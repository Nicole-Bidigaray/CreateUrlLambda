<div id="top"></div>

<div align="center">

  <h1>APP — URL Shortener Serverless (Java + AWS)</h1>

<em>Transforming URLs, Empowering Connections</em>

  <!-- BADGES -->
  <p>
    <img alt="Java" src="https://img.shields.io/badge/Java-17-007396?logo=openjdk&logoColor=white">
    <img alt="AWS Lambda" src="https://img.shields.io/badge/AWS-Lambda-FF9900?logo=awslambda&logoColor=white">
    <img alt="API Gateway" src="https://img.shields.io/badge/AWS-API_Gateway-FF4F8B?logo=amazonapigateway&logoColor=white">
    <img alt="S3" src="https://img.shields.io/badge/AWS-S3-569A31?logo=amazons3&logoColor=white">
    <img alt="Maven" src="https://img.shields.io/badge/Build-Maven-C71A36?logo=apachemaven&logoColor=white">
    <img alt="License" src="https://img.shields.io/badge/License-MIT-2ea44f">
  </p>

  <!-- LINKS RÁPIDOS -->
  <p>
    <a href="#overview">Visão Geral</a> •
    <a href="#features">Funcionalidades</a> •
    <a href="#getting-started">Como Rodar</a> •
    <a href="#api">API</a> •
    <a href="#deploy">Deploy</a> •
    <a href="#security--costs">Segurança & Custos</a>
  </p>
</div>

---

## Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Arquitetura](#arquitetura)
- [Tecnologias](#tecnologias)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
    - [Pré-requisitos](#pré-requisitos)
    - [Build](#build)
    - [Execução local (Lambda Runtime)](#execução-local-lambda-runtime)
- [API](#api)
    - [Criar URL Curta — `POST /create`](#criar-url-curta--post-create)
    - [Redirecionar — `GET /{code}`](#redirecionar--get-code)
- [Deploy](#deploy)
- [Variáveis de Ambiente](#variáveis-de-ambiente)
- [Troubleshooting](#troubleshooting)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgments](#acknowledgments)

---

## Overview
Encurtador de URLs **serverless** com **AWS Lambda + API Gateway + S3** escrito em **Java 17**.  
Gera um **código curto** para cada URL e armazena os metadados no S3, com **expiração configurável**.

---

## Features
- 🚀 **Serverless**: sem servidores para gerenciar.
- ⏱️ **Expiração de links**: TTL configurável por requisição.
- 📦 **Storage barato**: metadados persistidos em **Amazon S3**.
- 🧰 **Empacotamento leve**: build com **maven-shade-plugin**.
- 🧪 **Pronto para testes**: exemplos de requests/responses e status codes.

---

## Arquitetura
<div align="center">
  <!-- troque por sua imagem -->
  <img src="assets/diagram.png" alt="Diagrama da Arquitetura" width="720">
</div>

```plaintext
Client → Amazon API Gateway
         ├─ POST /create → Lambda (CreateURLShortLambda) ──┐
         └─ GET  /{code} → Lambda (RedirectShortURLLambda) ─┴─ S3 (URLs + TTL)
```
## ⚙️ Tecnologias

| Tecnologia | Função |
|-----------:|:------|
| ☁️ **AWS Lambda** | Execução serverless das funções |
| 🌐 **Amazon API Gateway** | Exposição dos endpoints HTTP |
| 🗄️ **Amazon S3** | Armazenamento das URLs e metadados |
| 💻 **Java 17** | Linguagem principal |
| 📦 **Maven** | Build e dependências |
| 🧩 **Jackson Databind** | (De)serialização JSON |
| 🧠 **Lombok** | Redução de boilerplate |

---

## 🗂️ Project Structure

```plaintext
src/
 └─ main/java/com/rocketseat/createUrlShortner/
    ├─ Main.java              # Handler (Create URL)
    └─ UrlData.java           # Modelo (url original + expirationTime)
pom.xml
dependency-reduced-pom.xml
```
> 💡 O pacote `createUrlShortner` deve coincidir com o **Handler** configurado na Lambda.

---

## 🚀 Getting Started

### ✅ Pré-requisitos
- **AWS CLI** configurado (`aws configure`)
- **Java 17** instalado
- **Maven** instalado
- Permissões para **Lambda**, **API Gateway** e **S3**

### 🧱 Build
```bash
mvn clean package
# gera o .jar com dependências em target/
```

### 🖥️ Execução local (Lambda Runtime)

Se desejar simular localmente com o AWS Lambda Java Runtime:

```bash
# exemplo genérico — ajuste conforme seu handler/classe principal
java -jar target/CreateUrlLambda-1.0-SNAPSHOT.jar
```
## 🧩 API

### Criar URL Curta — `POST /create`

**Request (JSON)**
```json
{
  "originalUrl": "https://seusite.com/minha-pagina",
  "expirationTime": "1704067200"
}
```
**Response 201 (JSON)**
```json
{
  "code": "a1b2c3d4",
  "shortUrl": "https://api.seu-dominio.com/a1b2c3d4",
  "expiresAt": "2024-12-31T00:00:00Z"
}
```

## ❗ Erros comuns

- `400 Bad Request` – JSON inválido ou `originalUrl` ausente
- `422 Unprocessable Entity` – `expirationTime` inválido
- `500 Internal Server Error` – erro não mapeado

---

## 🔀 Redirecionar — `GET /{code}`

### Exemplo
```http
GET /a1b2c3d4
```
### Comportamento

- `302 Found` → redireciona para `originalUrl`
- `404 Not Found` → código inexistente
- `410 Gone` → link expirado

---

## ☁️ Deploy

Você pode fazer o deploy manual pelo Console ou via automação (SAM/Serverless).  
Abaixo, um **checklist** minimalista para o deploy manual.

### Bucket S3
- Crie um bucket (`NOME_DO_BUCKET`) para armazenar os registros.

### Lambda(s)
- Crie **CreateURLShortLambda** e **RedirectShortURLLambda**.
- **Runtime**: Java 17 (Amazon Corretto 17).
- **Handler**: `com.rocketseat.createUrlShortner.Main::handleRequest` (ajuste se necessário).
- **Env Vars**: ver [Variáveis de Ambiente](#-variáveis-de-ambiente).
- **Permissões (IAM)**: conceder `s3:PutObject`, `s3:GetObject`, `s3:ListBucket` no `NOME_DO_BUCKET`.

### API Gateway
- `POST /create` → integração com **CreateURLShortLambda**
- `GET /{shortCode}` → integração com **RedirectShortURLLambda**
- Habilite **Lambda Proxy Integration** (se aplicável).

### Function URL (opcional)
- Ative **Function URL** e proteja com `AuthType` conforme sua necessidade.

---

## 🔧 Variáveis de Ambiente

| Variável              | Exemplo                     | Descrição                                   |
|-----------------------|-----------------------------|---------------------------------------------|
| `URLS_BUCKET`         | `meu-bucket-encurtador`     | Bucket de storage das URLs                  |
| `REGIAO_AWS`          | `us-east-1`                 | Região de deploy                            |
| `BASE_REDIRECT_URL`   | `https://api.seu-dominio.com` | Base usada para montar a `shortUrl`       |
| `DEFAULT_TTL_SECONDS` | `2592000`                   | TTL padrão (30 dias), caso não informado    |

> Se não utilizar algumas variáveis, remova do código/console para evitar confusão.

---

## 🧰 Troubleshooting

- **Handler não encontrado**: verifique se o nome do pacote/classe bate com o JAR empacotado (`shade`) e o valor em *Handler*.
- **403 / AccessDenied no S3**: confirme a política IAM anexada à Lambda e o `Resource` apontando para seu bucket.
- **HTTP 500**: verifique os logs no **CloudWatch Logs** para o `stacktrace`.

---

## 🗺️ Roadmap

- [ ] Implementar `RedirectShortURLLambda` (handler de redirecionamento)
- [ ] Adicionar testes (JUnit + Mockito)
- [ ] Adicionar validações (URL válida, TTL mínimo/máximo)
- [ ] Pipeline CI (GitHub Actions) com `mvn verify` + upload de artefato
- [ ] Template de IaC (AWS SAM / Serverless Framework / Terraform)

---

## 🤝 Contributing

Contribuições são bem-vindas!

1. Faça um **fork**
2. Crie uma **branch** (`feat/x`)
3. Abra um **PR** com descrição clara

---

## 📜 License

Distribuído sob a licença **MIT**. Veja `LICENSE` para mais detalhes.

---

## 🙏 Acknowledgments

- Curso **Java na Prática by AWS** – inspiração do projeto
- Comunidade que mantém **AWS Lambda Java** e **Maven Shade Plugin**

<div align="right"><a href="#top">🔝 Voltar ao topo</a></div>

