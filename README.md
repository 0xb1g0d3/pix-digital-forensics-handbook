# Mapa Técnico-Jurídico de Investigação  
## Pix / SPB / SPI / DICT / Gateways de Pagamento

**Autor:** Paulo A.  
**Objetivo:** Inventário técnico e jurídico de informações úteis para investigações envolvendo Pix, SPB, gateways de pagamento e eventuais conversões para criptoativos, com foco em **evidência, retenção de logs, rastreabilidade e cadeia de custódia**, no nível exigido por **DFIR financeiro, auditoria e persecução penal**.

---

## 1. FUNDAMENTO REGULATÓRIO (BANCO CENTRAL DO BRASIL)

### 1.1 Princípio central exigido pelo Bacen
O Banco Central do Brasil **não trata logs como mero requisito operacional**, mas como **instrumentos de rastreabilidade, auditoria e reconstrução de eventos**.

> O requisito regulatório central é a **capacidade de reconstruir integralmente uma transação Pix**, seus acessos e decisões associadas.

#### Fonte oficial
Manual de Segurança do Pix – Banco Central do Brasil  
Seção 7 – *Dados de Auditoria*  
https://www.bcb.gov.br/content/estabilidadefinanceira/pix/Regulamento_Pix/Manual_de_Seguranca_do_Pix.pdf

---

### 1.2 Regulamento do Pix (força normativa)
O Regulamento do Pix incorpora explicitamente os manuais técnicos como **parte obrigatória do arcabouço regulatório**.

#### Fonte oficial
Resolução BCB nº 1, de 12/08/2020 – Regulamento do Pix  
Art. 1º e Anexo (documentos integrantes)  
https://www.bcb.gov.br/estabilidadefinanceira/pix_regulamento

---

## 2. ONDE CADA EVIDÊNCIA EXISTE (RESPONSABILIDADE OBJETIVA)

| Tipo de evidência | Onde deve existir |
|------------------|------------------|
| Logs de autenticação e acesso | Instituição financeira / PSP |
| Logs de autorização e perfis | Instituição financeira / PSP |
| Mensagens Pix (ICOM / SPI) | Instituição financeira (envio e recebimento) |
| Logs do SPI (liquidação) | Banco Central (registros centrais) |
| Logs de API Pix | Instituição financeira e/ou gateway |
| Logs do DICT | Instituição financeira participante |
| Logs de gateway | Gateway de pagamento |
| Infraestrutura (SO, firewall, IAM) | Onde o sistema Pix é operado |

**Importante:** o Banco Central **não é repositório primário de logs operacionais do participante**.  
Cada **PSP é responsável por armazenar seus próprios registros**, ainda que o SPI seja centralizado.

---

## 3. LOGS E REGISTROS TÉCNICOS (DFIR)

### 3.1 Autenticação e Acesso
**Finalidade investigativa:** identificar uso legítimo indevido, engenharia social, abuso de privilégio.

**Devem existir em:**  
- Instituição financeira / PSP  
- Gateway (se houver console ou APIs administrativas)

**Conteúdo mínimo esperado:**
- Login / logout
- MFA
- IP de origem
- Timestamp (UTC)
- Identificador de dispositivo
- Contas técnicas e administrativas

**Base regulatória:**  
Manual de Segurança do Pix – Seção 7.1  
https://www.bcb.gov.br/content/estabilidadefinanceira/pix/Regulamento_Pix/Manual_de_Seguranca_do_Pix.pdf

---

### 3.2 Autorização e Perfis (RBAC)
**Finalidade investigativa:** identificar fraude sem malware ou exploit.

**Devem existir em:**  
- Instituição financeira  
- Gateway (se operar funções críticas)

**Registros esperados:**
- Criação/remoção de perfis
- Alterações de privilégio
- Delegações
- Service accounts
- Aprovações associadas

**Base:** princípios de segregação e controle exigidos pelo Bacen para participantes do SPB.

---

### 3.3 Logs do Pix / SPI (mensagens financeiras)
**Finalidade:** reconstrução financeira da transação.

**Devem existir em:**
- Instituição financeira pagadora
- Instituição financeira recebedora

**Conteúdo:**
- Mensagens XML `<envelope>`
- Identificadores (`PI-EndToEndId`, `PI-PayerId`)
- Timestamp
- Valor
- Status de liquidação

**Retenção obrigatória:**
- **10 anos** para mensagens ICOM enviadas e recebidas

**Fonte oficial:**  
Manual de Segurança do Pix – Seção 7.2  
https://www.bcb.gov.br/content/estabilidadefinanceira/pix/Regulamento_Pix/Manual_de_Seguranca_do_Pix.pdf

---

### 3.4 Logs de API Pix / Gateways
**Altíssimo valor probatório.**

**Devem existir em:**
- Instituição financeira
- Gateway de pagamento (se envolvido)

**Conteúdo esperado:**
- Request / response
- Payload ou hash
- Headers HTTP
- IP de origem
- mTLS / certificados

**Retenção:**
- **2 anos** para consultas
- **10 anos** para eventos de incidente/fraude

**Fonte:**  
Manual de Segurança do Pix – Seção 7.4  
https://www.bcb.gov.br/content/estabilidadefinanceira/pix/Regulamento_Pix/Manual_de_Seguranca_do_Pix.pdf

---

### 3.5 DICT (Diretório de Identificadores)
**Finalidade:** rastrear titularidade e alterações de chave Pix.

**Devem existir em:**  
- Instituição financeira participante

**Retenção:**
- Consultas: **2 anos**
- Escritas (criação, portabilidade, reivindicação, marcação de fraude, MED): **10 anos**

**Fonte:**  
Manual de Segurança do Pix – Seção 7.3  
https://www.bcb.gov.br/content/estabilidadefinanceira/pix/Regulamento_Pix/Manual_de_Seguranca_do_Pix.pdf

---

## 4. LGPD – ENQUADRAMENTO JURÍDICO

### Base legal predominante
- **Art. 7º, II** – Cumprimento de obrigação legal ou regulatória  
- **Art. 16, I** – Conservação para cumprimento de obrigação legal  
- **Art. 46** – Segurança da informação  
- **Art. 6º** – Necessidade, finalidade e minimização

**Lei Geral de Proteção de Dados – Lei 13.709/2018**  
https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm

➡️ Logs Pix **não são opcionais**, nem dependem de consentimento.

---

## 5. CADEIA DE CUSTÓDIA (PROVA DIGITAL)

A coleta de logs deve respeitar a **cadeia de custódia**, garantindo integridade e rastreabilidade.

**Exigir sempre:**
- Extração por responsável técnico identificado
- Hash criptográfico (ex.: SHA-256)
- Registro de data/hora (UTC)
- Descrição do sistema de origem
- Registro de transferência e armazenamento

**Base legal:**  
Código de Processo Penal – Art. 158-A  
https://www.planalto.gov.br/ccivil_03/_ato2019-2022/2019/lei/L13964.htm

---

## 6. CHECKLIST OPERACIONAL PARA ORDEM JUDICIAL

### 6.1 Instituição financeira pagadora e recebedora
Solicitar:

- Logs de autenticação e autorização (usuários e admins)
- Logs de API Pix relacionados à transação
- Mensagens ICOM/SPI (XML `<envelope>`)
- Registros DICT associados à chave
- Hash e metadados de integridade
- Janela temporal delimitada (UTC)

---

### 6.2 Gateway de pagamento (se aplicável)
Solicitar:

- Logs de API e roteamento
- Logs administrativos
- Correlação entre requisição do cliente e chamada Pix
- Certificados e mTLS utilizados

---

### 6.3 Banco Central do Brasil
Solicitar via ofício:

- Confirmação de liquidação no SPI
- Metadados institucionais da transação

---

## 7. OBSERVAÇÃO FINAL

Este documento foi estruturado para **uso direto em investigações criminais, auditorias regulatórias e perícias forenses**, alinhando:

- Exigências do Banco Central
- LGPD
- Cadeia de custódia penal
- Boas práticas de DFIR financeiro

Não se trata de interpretação doutrinária, mas de **aplicação objetiva de normas vigentes**.

---
