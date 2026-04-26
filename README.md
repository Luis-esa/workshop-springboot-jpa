# Web Services com Spring Boot e JPA / Hibernate

![Java](https://img.shields.io/badge/Java-25-blue?style=flat-square&logo=openjdk)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-latest-brightgreen?style=flat-square&logo=springboot)
![Maven](https://img.shields.io/badge/Maven-build-red?style=flat-square&logo=apachemaven)
![H2](https://img.shields.io/badge/H2-in--memory-blue?style=flat-square)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-production-316192?style=flat-square&logo=postgresql)

Projeto desenvolvido durante o **Curso Java COMPLETO** da [DevSuperior](https://devsuperior.com.br), ministrado pelo Prof. Dr. Nelio Alves. O objetivo é construir uma API RESTful completa com Spring Boot, JPA/Hibernate, boas práticas de arquitetura em camadas, tratamento de exceções e deploy em nuvem.

---

## 📋 Objetivos

- Criar projeto Spring Boot Java
- Implementar modelo de domínio
- Estruturar camadas lógicas: resource, service, repository
- Configurar banco de dados de teste (H2)
- Povoar o banco de dados
- CRUD — Create, Retrieve, Update, Delete
- Tratamento de exceções

---

## 🗂️ Modelo de Domínio

O sistema possui as seguintes entidades e relacionamentos:

| Entidade    | Atributos principais                                      |
|-------------|-----------------------------------------------------------|
| `User`      | id, name, email, phone, password                          |
| `Order`     | id, moment, orderStatus                                   |
| `Product`   | id, name, description, price, imgUrl                      |
| `Category`  | id, name                                                  |
| `OrderItem` | quantity, price (associação N:N entre Order e Product)    |
| `Payment`   | id, moment (associação 1:1 com Order)                     |

**OrderStatus (enum):** `WAITING_PAYMENT`, `PAID`, `SHIPPED`, `DELIVERED`, `CANCELED`

---

## 🏗️ Arquitetura — Camadas Lógicas

```
Application
     ↕
Resource Layer  (REST Controllers)
     ↓
Service Layer
     ↓
Data Access Layer  (Repositories / JPA)
               ↕
            Entities
```

---

## 🚀 Tecnologias Utilizadas

- **Java 25**
- **Spring Boot** (Spring Web, Spring Data JPA)
- **Maven**
- **H2 Database** (perfil de teste)
- **PostgreSQL** (perfil de desenvolvimento/produção)
- **Apache Tomcat** (embutido no Spring Boot)
- **Postman** (testes de API)
- **Heroku** (deploy — opcional)

---

## ⚙️ Como Executar Localmente

### Pré-requisitos

- Java 17+
- Maven
- (Opcional) PostgreSQL instalado para o perfil `dev`

### Clonar o repositório

```bash
git clone https://github.com/acenelio/workshop-springboot3-jpa.git
cd workshop-springboot3-jpa
```

### Executar com perfil de teste (H2)

O perfil padrão usa banco de dados H2 em memória, sem necessidade de configuração adicional.

```bash
./mvnw spring-boot:run
```

Acesse o console do H2 em: [http://localhost:8080/h2-console](http://localhost:8080/h2-console)

| Campo    | Valor                    |
|----------|--------------------------|
| JDBC URL | `jdbc:h2:mem:testdb`     |
| Username | `sa`                     |
| Password | *(vazio)*                |

---

## 🗄️ Configuração de Banco de Dados

### `application.properties`

```properties
spring.profiles.active=test
spring.jpa.open-in-view=true
```

### `application-test.properties`

```properties
# DATASOURCE
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.username=sa
spring.datasource.password=

# H2 CLIENT
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

# JPA, SQL
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.defer-datasource-initialization=true
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

---

## 📦 Dependências principais (`pom.xml`)

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-h2-console</artifactId>
</dependency>
```

---

## 🔁 Endpoints REST

### Usuários — `/users`

| Método   | Rota         | Descrição              |
|----------|--------------|------------------------|
| `GET`    | `/users`     | Listar todos           |
| `GET`    | `/users/{id}`| Buscar por ID          |
| `POST`   | `/users`     | Inserir novo usuário   |
| `PUT`    | `/users/{id}`| Atualizar usuário      |
| `DELETE` | `/users/{id}`| Deletar usuário        |

**Exemplo de body para inserção/atualização:**

```json
{
  "name": "Bob Brown",
  "email": "bob@gmail.com",
  "phone": "977557755",
  "password": "123456"
}
```

### Pedidos — `/orders`

| Método | Rota          | Descrição        |
|--------|---------------|------------------|
| `GET`  | `/orders`     | Listar todos     |
| `GET`  | `/orders/{id}`| Buscar por ID    |

### Produtos — `/products`

| Método | Rota            | Descrição        |
|--------|-----------------|------------------|
| `GET`  | `/products`     | Listar todos     |
| `GET`  | `/products/{id}`| Buscar por ID    |

### Categorias — `/categories`

| Método | Rota               | Descrição        |
|--------|--------------------|------------------|
| `GET`  | `/categories`      | Listar todas     |
| `GET`  | `/categories/{id}` | Buscar por ID    |

---

## ⚠️ Tratamento de Exceções

As seguintes classes foram criadas para padronizar o tratamento de erros da API:

- `services/exceptions/ResourceNotFoundException` — recurso não encontrado (HTTP 404)
- `services/exceptions/DatabaseException` — violação de integridade no banco (HTTP 400)
- `resources/exceptions/StandardError` — estrutura padrão de erro na resposta
- `resources/exceptions/ResourceExceptionHandler` — interceptador global de exceções

---

## ☁️ Deploy (Heroku + PostgreSQL) — Opcional

> ⚠️ O deploy no Heroku é **opcional** e pode exigir cartão de crédito.

### Perfil de desenvolvimento — `application-dev.properties`

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/springboot_course
spring.datasource.username=postgres
spring.datasource.password=1234567
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

### Perfil de produção — `application-prod.properties`

```properties
spring.datasource.url=${DATABASE_URL}
spring.jpa.hibernate.ddl-auto=none
spring.jpa.show-sql=false
spring.jpa.properties.hibernate.format_sql=false
jwt.secret=${JWT_SECRET}
jwt.expiration=${JWT_EXPIRATION}
```

### `system.properties`

```properties
java.runtime.version=17
```

### Deploy via Git

```bash
heroku login
heroku git:remote -a nome-do-seu-app
git add .
git commit -m "Deploy app to Heroku"
git push heroku main
```

---

## 📁 Repositórios de Referência

| Versão          | Link                                                                 |
|-----------------|----------------------------------------------------------------------|
| Spring Boot 4   | [workshop-springboot4-jpa](https://github.com/acenelio/workshop-springboot4-jpa) |
| Spring Boot 3   | [workshop-springboot3-jpa](https://github.com/acenelio/workshop-springboot3-jpa) |
| Spring Boot 2   | [workshop-springboot2-jpa](https://github.com/acenelio/workshop-springboot2-jpa) |

---

## 👨‍💻 Autor

Desenvolvido com base no curso **Java COMPLETO** — [devsuperior.com.br](https://devsuperior.com.br)  
Prof. Dr. Nelio Alves
