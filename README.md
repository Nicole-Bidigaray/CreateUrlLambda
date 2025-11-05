# ☁️ Java na Prática by AWS — Encurtador de URLs Serverless

![Java](https://img.shields.io/badge/Java-17-blue?logo=openjdk)
![AWS Lambda](https://img.shields.io/badge/AWS-Lambda-orange?logo=awslambda)
![Maven](https://img.shields.io/badge/Maven-Build-red?logo=apachemaven)
![License](https://img.shields.io/badge/License-MIT-green)

---

## 📖 Sobre o projeto
Este projeto foi desenvolvido como parte do curso **Java na Prática by AWS** (18 a 21 de novembro de 2024), com o objetivo de demonstrar, na prática, como construir uma aplicação **serverless** utilizando **AWS Lambda**, **Amazon API Gateway** e **Amazon S3**.

A aplicação implementa um **encurtador de URLs** escalável e totalmente sem servidor, permitindo que usuários criem links curtos que redirecionam para URLs originais, com um tempo de expiração configurável.

---

## 🏗️ Arquitetura do Sistema

O sistema é composto por **duas funções Lambda** principais:

### 🔹 1. CreateURLShortLambda
- Recebe uma requisição HTTP via **API Gateway**.
- Gera um código único (UUID) para a URL curta.
- Armazena no **Amazon S3** as informações da URL original e o tempo de expiração.

### 🔹 2. RedirectShortURLLambda
- Recebe o código da URL curta.
- Busca os dados correspondentes no **S3**.
- Valida o tempo de expiração.
- Redireciona o usuário para a URL original, caso ainda esteja válida.

---

## 🧩 Arquitetura AWS

```plaintext
Cliente → Amazon API Gateway → Lambda (CreateURLShortLambda)
                                     ↳ salva dados no Amazon S3

Cliente → Amazon API Gateway → Lambda (RedirectShortURLLambda)
                                     ↳ busca e valida dados no Amazon S3

## ⚙️ Tecnologias utilizadas

| Tecnologia | Função |
|-------------|--------|
| ☁️ **AWS Lambda** | Execução serverless das funções |
| 🌐 **Amazon API Gateway** | Exposição dos endpoints HTTP |
| 🗄️ **Amazon S3** | Armazenamento seguro das URLs |
| 💻 **Java 17** | Linguagem principal |
| 📦 **Maven** | Gerenciador de dependências |
| 🧰 **AWS SDK for Java v2** | Comunicação com serviços AWS |
| 🧩 **Jackson Databind** | Serialização e desserialização de JSON |
| 🧠 **Lombok** | Redução de boilerplate no código Java |

---

## 🚀 Como executar o projeto

### 🔧 Pré-requisitos

- Conta **AWS** configurada com credenciais válidas.  
- **Java 17** ou superior instalado.  
- **Maven** instalado.  
- Permissões para criar funções **Lambda** e **buckets S3**.

---

### 📦 Passo a passo

#### 1️⃣ Clone o repositório

```bash
git clone https://github.com/seu-usuario/java-aws-url-shortener.git
cd java-aws-url-shortener

#### 2️⃣ Compile e empacote o projeto

```bash
mvn clean package

### 3️⃣ Faça o deploy da função Lambda

1. Acesse o **AWS Lambda Console**.  
2. Crie uma nova função chamada **CreateURLShortLambda**.  
3. Faça upload do arquivo **.jar** gerado na pasta `/target`.  
4. Configure o **Handler** com o nome completo da classe:

   ```java
   com.rocketseat.createUrlShortner.Main::handleRequest

### 🔁 Repita o processo para a função RedirectShortURLLambda

Depois de configurar a primeira função, repita os mesmos passos para criar e implantar a função **RedirectShortURLLambda**.

---

### 4️⃣ Crie um bucket S3

1. Acesse o console do **Amazon S3**.  
2. Crie um bucket com um nome único.  
3. Permita acesso às funções **Lambda** configuradas.

---

### 5️⃣ Configure o API Gateway

1. Crie endpoints para:  
   - `/create`  
   - `/{shortCode}`  
2. Integre cada um com sua respectiva função **Lambda**.

---

## 🧪 Testes

### 🔹 Teste de criação da URL curta

**Método:** `POST`  
**Endpoint:** `/create`

**Body (JSON):**
```json
{
  "originalUrl": "https://fernandakipper.com/links",
  "expirationTime": "29494949"
}

### ✅ Resposta esperada

```json
{
  "code": "a1b2c3d4"
}

### 🔹 Teste de redirecionamento

**Método:** `GET`  
**Endpoint:** `/{code}`  

- Se o link ainda estiver dentro do tempo de expiração, o usuário será **redirecionado** para a URL original.  
- Caso contrário, retornará um erro de **link expirado**.

---

## 🧠 O que você aprende neste projeto

✅ Criar e configurar funções **AWS Lambda** com Java  
✅ Integrar **Lambda + API Gateway + S3**  
✅ Trabalhar com **UUIDs** e **JSON parsing**  
✅ Implementar uma **arquitetura serverless escalável**  
✅ Entender o fluxo completo de **encurtamento e redirecionamento de URLs**

---

## 💬 Créditos

Projeto desenvolvido durante o curso **Java na Prática by AWS**, ministrado por **Fernanda Kipper**.  
Curso gratuito e 100% prático, com foco em **Java + AWS Lambda + Arquitetura Serverless**.

---

## 🏷️ Licença

Este projeto é distribuído sob a licença **MIT**.  
Sinta-se à vontade para usar, estudar e aprimorar! 😄

---

## 🖼️ Diagrama da Arquitetura

> (Adicione aqui a imagem do diagrama do projeto, por exemplo)

![Arquitetura Serverless AWS](assets/diagram.png)

---

## 🔗 Acesse a função Lambda

👉 [Clique aqui para testar a Function URL da Lambda](https://SEU-ENDPOINT-LAMBDA.lambda-url.us-east-2.on.aws/)

---

### 💡 Dica

- Substitua o trecho  
  `https://SEU-ENDPOINT-LAMBDA.lambda-url.us-east-2.on.aws/`  
  pela sua **URL real da função Lambda**.  

- Substitua o caminho da imagem do diagrama (`assets/diagram.png`)  
  pelo nome do arquivo que você subir no repositório (por exemplo:  
  `diagram.png` na raiz do projeto).