# 📚 User Health BFF - Documentação Completa

> **Versão:** 1.0 | **Status:** ✅ Produção Ready | **Data:** 09/02/2026

---

## 📑 Índice

1. [Visão Geral](#-visão-geral)
2. [Quick Start](#-quick-start)
3. [Autenticação JWT](#-autenticação-jwt)
4. [Arquitetura](#-arquitetura)
5. [Como Executar](#-como-executar)
6. [API Endpoints](#-api-endpoints)
7. [Testes](#-testes)
8. [Testes Manuais via API](#-testes-manuais-via-api)
9. [Troubleshooting](#-troubleshooting)
10. [Roadmap](#-roadmap)

---

## 🎯 Visão Geral

### Descrição
Backend for Frontend (BFF) para gerenciamento de usuários com CRUD completo, autenticação JWT, validações robustas, tratamento de exceções e documentação Swagger integrada.

### Tecnologias
- **Java 21**
- **Spring Boot 4.0.2**
- **Spring Data JPA**
- **Spring Security** com OAuth2 Resource Server
- **PostgreSQL 42.7.3**
- **JWT com RSA (RS256)**
- **BCrypt** para hash de senhas
- **Lombok**
- **SpringDoc OpenAPI 2.7.0** (Swagger)
- **Bean Validation**
- **Docker & Docker Compose**
- **JUnit 5 + Mockito** para testes

### Funcionalidades
✅ CRUD completo de usuários  
✅ **Autenticação JWT com RSA (RS256)**  
✅ **Criptografia de senhas com BCrypt**  
✅ **Spring Security com endpoints públicos e protegidos**  
✅ **Atualização de senha** (PATCH /api/v1/auth/password)  
✅ Validações de entrada (Bean Validation)  
✅ Tratamento de exceções centralizado  
✅ Documentação Swagger/OpenAPI completa  
✅ Docker Compose para desenvolvimento e produção  
✅ Health checks e métricas (Spring Actuator)  
✅ Persistência com PostgreSQL  
✅ **Testes unitários e de integração**  

---

## 🚀 Quick Start

### 🔧 Modo 1: Desenvolvimento Local (Perfil LOCAL)

```bash
# 1. Subir infraestrutura (PostgreSQL)
docker-compose -f docker-compose-local.yml up -d

# 2. Executar aplicação na IDE
# - Abra UserHealthMain.java no IntelliJ
# - Pressione Shift+F10 (Run)
# - Perfil 'local' será ativado automaticamente
```

### 🐳 Modo 2: Produção no Docker (Perfil PROD)

```bash
# Subir tudo com um comando
docker-compose up -d
```

### Acessar Aplicação
- **Swagger UI:** http://localhost:8080/swagger-ui.html
- **API REST:** http://localhost:8080/api/users
- **Health Check:** http://localhost:8080/actuator/health
- **Metrics:** http://localhost:8080/actuator/metrics

### Teste Rápido
```bash
# Health check
curl http://localhost:8080/actuator/health

# Criar usuário (público - não requer autenticação)
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{"nome":"João Silva","email":"joao@test.com","login":"joaosilva","senha":"senha12345678"}'

# Login e obter JWT token
curl -X POST http://localhost:8080/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"joao@test.com","password":"senha12345678"}'

# Listar usuários (protegido - requer token)
curl -H "Authorization: Bearer SEU_TOKEN_AQUI" http://localhost:8080/api/users
```

---

## 🔐 Autenticação JWT

### Visão Geral

A aplicação possui autenticação completa com **JWT (JSON Web Tokens)** usando **Spring Security** e assinatura **RSA (RS256)**.

### Componentes de Segurança
- **Spring Security** - Framework de autenticação e autorização
- **OAuth2 Resource Server** - Validação de tokens JWT
- **JWT com RSA** - Tokens assinados com chaves RSA (RS256)
- **BCrypt** - Hash de senhas com salt automático (10 rounds)
- **Session Stateless** - Sem estado de sessão (escalável)

### Endpoints de Autenticação

#### 1. Login (Público)
```bash
POST /api/v1/auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "senha123456"
}
```

**Resposta:** Token JWT válido
```json
{
  "accessToken": "eyJhbGciOiJSUzI1NiJ9...",
  "expiresIn": 3600
}
```

#### 2. Atualizar Senha (Público)
```bash
PATCH /api/v1/auth/password
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "novaSenha123456"
}
```

**Resposta:** 204 No Content

#### 3. Usar Token em Requisições Protegidas
```bash
GET /api/users
Authorization: Bearer eyJhbGciOiJSUzI1NiJ9...
```

### Proteção de Endpoints

| Endpoint | Público | Protegido | Descrição |
|----------|---------|-----------|-----------|
| POST /api/v1/auth/login | ✅ Sim | ❌ Não | Login de usuário |
| PATCH /api/v1/auth/password | ✅ Sim | ❌ Não | Atualizar senha |
| POST /api/users | ✅ Sim | ❌ Não | Criar usuário |
| GET /api/users | ❌ Não | ✅ Sim | Listar usuários |
| GET /api/users/{id} | ❌ Não | ✅ Sim | Buscar usuário |
| PUT /api/users/{id} | ❌ Não | ✅ Sim | Atualizar usuário |
| DELETE /api/users/{id} | ❌ Não | ✅ Sim | Deletar usuário |
| GET /actuator/health | ✅ Sim | ❌ Não | Health check |
| GET /swagger-ui.html | ✅ Sim | ❌ Não | Documentação |

### Segurança Implementada

✅ **Senhas criptografadas** com BCrypt (10 rounds)  
✅ **JWT assinado** com RSA (RS256)  
✅ **Token expira** em 1 hora  
✅ **Session stateless** (escalável)  
✅ **CORS configurado**  
✅ **Exception handling** completo  
✅ **Chaves RSA** em arquivos separados (app.pub e app.key)

### Fluxo de Autenticação

```
1. Criar usuário (POST /api/users)
   → Senha criptografada com BCrypt
   → Usuário salvo no banco

2. Login (POST /api/v1/auth/login)  
   → Valida email/senha
   → Gera JWT token com RSA
   → Retorna token + expiração

3. Requisições protegidas
   → Header: Authorization: Bearer <token>
   → Spring Security valida assinatura RSA
   → Extrai informações do token
   → Permite ou nega acesso
```

### Exemplo Completo

```bash
# 1. Criar usuário
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{
    "nome": "João Silva",
    "email": "joao@example.com",
    "login": "joaosilva",
    "senha": "senha12345678"
  }'

# 2. Fazer login
TOKEN=$(curl -X POST http://localhost:8080/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "joao@example.com",
    "password": "senha12345678"
  }' | jq -r '.accessToken')

# 3. Usar token em requisições protegidas
curl -H "Authorization: Bearer $TOKEN" \
  http://localhost:8080/api/users
```

---

## 🏗️ Arquitetura

### Estrutura de Camadas

```
┌─────────────────────────────────────────┐
│         Controller Layer                │  ← REST API
│  - Endpoints HTTP                       │
│  - Validação de entrada (@Valid)        │
│  - Documentação Swagger                 │
└──────────────┬──────────────────────────┘
               │
               ↓
┌─────────────────────────────────────────┐
│         Service Layer                   │  ← Lógica de Negócio
│  - Regras de negócio                    │
│  - Validações customizadas              │
│  - Transações (@Transactional)          │
│  - Autenticação JWT                     │
└──────────────┬──────────────────────────┘
               │
               ↓
┌─────────────────────────────────────────┐
│         Repository Layer                │  ← Acesso a Dados
│  - JPA Repository                       │
│  - Queries customizadas                 │
└──────────────┬──────────────────────────┘
               │
               ↓
┌─────────────────────────────────────────┐
│         Database (PostgreSQL)           │
│  - Tabela: usuarios                     │
└─────────────────────────────────────────┘
```

### Estrutura de Arquivos

```
src/main/java/com/fiap/user/health/bff/
├── UserHealthMain.java              # Main application
├── config/
│   ├── OpenApiConfig.java          # Swagger configuration
│   └── SecurityConfig.java         # Spring Security + JWT
├── controller/
│   ├── UserController.java         # CRUD endpoints
│   ├── AuthController.java         # Authentication endpoints
│   └── docs/                       # Swagger documentation
├── dto/
│   ├── request/
│   │   ├── UserRequestDto.java     # Create user DTO
│   │   ├── UserUpdateRequestDto.java # Update user DTO
│   │   ├── UserCredentialsRequestDto.java # Login DTO
│   │   └── UserAuthRequestDto.java # Token response DTO
│   └── response/
│       └── UserResponseDto.java     # User response DTO
├── exception/
│   ├── ApiErrorMessage.java         # Error response structure
│   ├── EmailAlreadyExistsException.java
│   ├── UserNotFoundException.java
│   ├── JwtAuthenticationEntryPoint.java
│   ├── JwtAccessDeniedHandler.java
│   └── GlobalExceptionHandler.java  # Exception handler
├── mapper/
│   └── UserMapper.java              # DTO/Entity conversions
├── model/
│   ├── User.java                    # Domain model
│   └── Token.java                   # JWT token model
├── persistence/
│   ├── entity/
│   │   └── UserEntity.java         # JPA entity
│   └── repository/
│       └── UserRepository.java      # JPA repository
└── service/
    ├── user/
    │   ├── UserServiceInterface.java
    │   └── UserServiceImpl.java     # User business logic
    └── auth/
        ├── AuthServiceInterface.java
        └── AuthServiceImpl.java     # Authentication logic
```

### Padrões de Projeto Utilizados

| Padrão | Aplicação |
|--------|-----------|
| **Layered Architecture** | Separação Controller/Service/Repository |
| **DTO Pattern** | Separação de objetos de transferência |
| **Repository Pattern** | Abstração de acesso a dados |
| **Mapper Pattern** | Conversões entre camadas |
| **Builder Pattern** | Construção de objetos (Lombok) |
| **Dependency Injection** | Injeção via construtor |
| **Exception Handler Pattern** | Tratamento centralizado |
| **Strategy Pattern** | Services com interfaces |

---

## 🏃 Como Executar

### Opção 1: Docker (Recomendado)

**Pré-requisitos:**
- Docker Desktop instalado
- Docker Compose instalado

**Passos:**
1. Clone o repositório
2. Execute: `docker-compose up -d`
3. Aguarde ~30 segundos
4. Acesse: http://localhost:8080/swagger-ui.html

### Opção 2: Execução Local

**Pré-requisitos:**
- Java 21 instalado
- Maven instalado
- PostgreSQL rodando

**Passos:**

1. **Configurar PostgreSQL**
```sql
CREATE DATABASE postgres;
CREATE USER postgres WITH PASSWORD 'postgres';
GRANT ALL PRIVILEGES ON DATABASE postgres TO postgres;
```

2. **Configurar application.properties**
```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/postgres
spring.datasource.username=postgres
spring.datasource.password=postgres
```

3. **Compilar e Executar**
```bash
# Compilar
./mvnw clean install

# Executar
./mvnw spring-boot:run

# Ou executar JAR
java -jar target/user-health-bff-0.0.1-SNAPSHOT.jar
```

---

## 📡 API Endpoints

### Visão Geral

| Método | Endpoint | Descrição | Status Sucesso | Status Erro |
|--------|----------|-----------|----------------|-------------|
| POST | `/api/users` | Criar usuário | 201 Created | 400, 409 |
| GET | `/api/users` | Listar todos | 200 OK | - |
| GET | `/api/users/{id}` | Buscar por ID | 200 OK | 404 |
| PUT | `/api/users/{id}` | Atualizar | 200 OK | 400, 404, 409 |
| DELETE | `/api/users/{id}` | Deletar | 204 No Content | 404 |

### Detalhamento

#### 1. Criar Usuário
```http
POST /api/users
Content-Type: application/json

{
  "nome": "João Silva",
  "email": "joao@example.com",
  "login": "joaosilva",
  "senha": "senha12345678"
}
```

**Validações:**
- `nome`: 2-50 caracteres, apenas letras
- `email`: formato válido, único no sistema
- `login`: 5-20 caracteres
- `senha`: 8-100 caracteres

**Respostas:**
- `201 Created`: Usuário criado com sucesso
- `400 Bad Request`: Dados inválidos
- `409 Conflict`: Email já cadastrado

#### 2. Listar Todos
```http
GET /api/users
```

**Resposta:**
```json
[
  {
    "id": 1,
    "nome": "João Silva",
    "email": "joao@example.com",
    "login": "joaosilva"
  }
]
```

#### 3. Buscar por ID
```http
GET /api/users/1
```

**Respostas:**
- `200 OK`: Usuário encontrado
- `404 Not Found`: Usuário não existe

#### 4. Atualizar
```http
PUT /api/users/1
Content-Type: application/json

{
  "email": "novoemail@example.com",
  "login": "novologin",
  "senha": "novasenha123"
}
```

**Respostas:**
- `200 OK`: Atualizado com sucesso
- `400 Bad Request`: Dados inválidos
- `404 Not Found`: Usuário não existe
- `409 Conflict`: Email já usado por outro usuário

#### 5. Deletar
```http
DELETE /api/users/1
```

**Respostas:**
- `204 No Content`: Deletado com sucesso
- `404 Not Found`: Usuário não existe

### Estrutura de Erro

```json
{
  "timestamp": "2026-02-08T10:30:00",
  "status": 404,
  "error": "Not Found",
  "message": "User not found with id: 1",
  "path": "/api/users/1",
  "errors": [
    {
      "field": "email",
      "message": "must be a well-formed email address"
    }
  ]
}
```

---

## 🧪 Testes

### Testes Manuais via cURL

#### Criar Usuário
```bash
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{
    "nome": "Maria Silva",
    "email": "maria@example.com",
    "login": "mariasilva",
    "senha": "senha12345678"
  }'
```

#### Listar Todos
```bash
curl http://localhost:8080/api/users
```

#### Buscar por ID
```bash
curl http://localhost:8080/api/users/1
```

#### Atualizar
```bash
curl -X PUT http://localhost:8080/api/users/1 \
  -H "Content-Type: application/json" \
  -d '{
    "email": "maria.nova@example.com",
    "login": "marianova",
    "senha": "novasenha123"
  }'
```

#### Deletar
```bash
curl -X DELETE http://localhost:8080/api/users/1
```

### Cenários de Erro

#### Email Duplicado (409)
```bash
# Criar primeiro usuário
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{"nome":"Pedro","email":"pedro@test.com","login":"pedro1","senha":"senha12345678"}'

# Tentar criar com mesmo email
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{"nome":"Pedro2","email":"pedro@test.com","login":"pedro2","senha":"senha12345678"}'
```

#### Validação de Dados (400)
```bash
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{"nome":"A","email":"invalido","login":"ab","senha":"123"}'
```

### Swagger UI
Acesse http://localhost:8080/swagger-ui.html para testar interativamente.

---

## 🧪 Testes

### Estrutura de Testes

A aplicação possui cobertura completa de testes:

```
src/test/java/com/fiap/user/health/bff/
├── UserHealthBffE2ETest.java              # Testes E2E completos
├── controller/
│   ├── AuthControllerIntegrationTest.java # Testes do controller de autenticação
│   └── UserControllerIntegrationTest.java # Testes do controller de usuários
├── service/
│   ├── auth/
│   │   └── AuthServiceImplTest.java       # Testes unitários do serviço de auth
│   └── user/
│       └── UserServiceImplTest.java       # Testes unitários do serviço de usuário
├── persistence/repository/
│   └── UserRepositoryIntegrationTest.java # Testes de integração do repositório
├── mapper/
│   └── UserMapperTest.java                # Testes do mapper
└── integration/
    └── RealIntegrationTest.java           # Testes de integração reais
```

### Tipos de Testes

| Tipo | Quantidade | Testes | Descrição |
|------|-----------|--------|-----------|
| **Testes E2E** | 1 classe | 12 testes | Testes completos de ponta a ponta |
| **Testes de Integração** | 4 classes | 47 testes | Controllers, Repository, Integration |
| **Testes Unitários** | 3 classes | 26 testes | Services e Mappers |
| **TOTAL** | **8 classes** | **85 testes** | Cobertura completa da aplicação |

### Executar Testes

#### Todos os testes
```bash
# Maven Wrapper (Windows)
./mvnw.cmd test

# Maven Wrapper (Linux/Mac)
./mvnw test

# Maven instalado
mvn test
```

#### Testes específicos
```bash
# Apenas testes unitários
./mvnw test -Dtest=*ServiceImplTest

# Apenas testes de integração
./mvnw test -Dtest=*IntegrationTest

# Apenas testes E2E
./mvnw test -Dtest=UserHealthBffE2ETest

# Teste específico
./mvnw test -Dtest=UserServiceImplTest#shouldCreateUserSuccessfully
```

#### Testes com relatórios
```bash
# Gerar relatório de cobertura
./mvnw clean test jacoco:report

# Ver relatório
# target/site/jacoco/index.html
```

### Tecnologias de Teste

- **JUnit 5** - Framework de testes
- **Mockito** - Mocks e stubs
- **Spring Boot Test** - Testes de integração
- **MockMvc** - Testes de controllers
- **H2 Database** - Banco em memória para testes
- **@SpringBootTest** - Contexto completo da aplicação
- **@WebMvcTest** - Testes focados em controllers
- **@DataJpaTest** - Testes focados em JPA

### Perfil de Teste

Os testes usam o perfil `test` com H2 in-memory:

**application-test.yml:**
```yaml
spring:
  datasource:
    url: jdbc:h2:mem:testdb
    driver-class-name: org.h2.Driver
  jpa:
    hibernate:
      ddl-auto: create-drop
```

### Exemplos de Testes Implementados

✅ **CRUD completo** - Criar, Listar, Buscar, Atualizar, Deletar  
✅ **Validações** - Campos obrigatórios, formatos, tamanhos  
✅ **Exceções** - Email duplicado, usuário não encontrado  
✅ **Autenticação** - Login, tokens JWT, endpoints protegidos  
✅ **Segurança** - Acesso negado, autenticação obrigatória  
✅ **Repository** - Queries customizadas, findByEmail  
✅ **Mapper** - Conversões DTO ↔ Entity  
✅ **Integration** - Fluxos completos de ponta a ponta

---

## 📋 Testes Manuais via API

### Testes via cURL

### Problema: Porta 8080 em uso

**Windows:**
```bash
netstat -ano | findstr :8080
taskkill /PID <PID> /F
```

**Linux/Mac:**
```bash
lsof -i :8080
kill -9 <PID>
```

**Solução alternativa:**
Mudar porta em `docker-compose.yml`:
```yaml
ports: ["8081:8080"]
```

### Problema: Container não inicia

```bash
# Ver logs
docker-compose logs user-health-bff

# Rebuild limpo
docker-compose down -v
docker system prune -f
docker-compose up -d --build
```

### Problema: PostgreSQL não conecta

```bash
# Verificar se banco está healthy
docker-compose ps

# Reiniciar banco
docker-compose restart app-db

# Ver logs do banco
docker-compose logs app-db
```

### Problema: Build Maven falha

```bash
# Limpar e rebuildar
./mvnw clean install -U

# Pular testes temporariamente
./mvnw clean install -DskipTests
```

### Problema: Lombok não funciona na IDE

- **IntelliJ IDEA:** File → Settings → Plugins → Lombok
- **Eclipse:** Instalar lombok.jar
- **VS Code:** Instalar extensão Lombok

---

## 🎯 Roadmap

### ✅ Implementado (v1.0)
- CRUD completo de usuários
- Autenticação JWT com RSA (RS256)
- Criptografia de senhas com BCrypt
- Spring Security com endpoints protegidos
- Validações de entrada (Bean Validation)
- Tratamento de exceções centralizado
- Documentação Swagger/OpenAPI completa
- Docker e Docker Compose (prod e local)
- Health checks e métricas
- Testes unitários e de integração

### 🔵 Curto Prazo (v1.1 - v1.2)
- [ ] Paginação e ordenação nos endpoints
- [ ] Filtros de busca avançados
- [ ] Cobertura de código 90%+
- [ ] CI/CD pipeline (GitHub Actions)
- [ ] Auditoria (created_at, updated_at)

### 🟢 Médio Prazo (v2.0)
- [ ] Cache com Redis
- [ ] Soft delete
- [ ] Rate limiting
- [ ] Integração com OpenTelemetry e Jaeger
- [ ] Métricas Prometheus customizadas

### 🟡 Longo Prazo (v3.0+)
- [ ] Event sourcing
- [ ] CQRS pattern
- [ ] Mensageria (Kafka/RabbitMQ)
- [ ] Observabilidade avançada
- [ ] LGPD/GDPR compliance

---

## 📊 Boas Práticas Implementadas

✅ **Arquitetura em camadas** (Controller/Service/Repository)  
✅ **DTOs** para entrada e saída (separação de concerns)  
✅ **Validação Bean Validation** (@Valid, @NotNull, @Email, etc)  
✅ **Tratamento centralizado de exceções** (GlobalExceptionHandler)  
✅ **Transações** com @Transactional  
✅ **Documentação OpenAPI/Swagger** completa e interativa  
✅ **Lombok** para reduzir boilerplate  
✅ **Design Patterns** (Builder, Repository, Strategy)  
✅ **Senha não exposta** nas respostas JSON  
✅ **Validação de email único** no banco  
✅ **Mensagens de erro padronizadas** (ApiErrorMessage)  
✅ **Docker multi-stage build** otimizado  
✅ **Health checks** em todos os containers  
✅ **Código limpo e manutenível**  
✅ **Autenticação JWT com RSA (RS256)**  
✅ **Senhas criptografadas com BCrypt (10 rounds)**  
✅ **Spring Security** configurado corretamente  
✅ **Endpoints públicos e protegidos** bem definidos  
✅ **CORS** configurado  
✅ **Session stateless** (escalável)  
✅ **Testes unitários e de integração** (JUnit 5 + Mockito)  

---

## 🆘 Suporte e Contato

### Documentação
- **Swagger UI:** http://localhost:8080/swagger-ui.html
- **OpenAPI JSON:** http://localhost:8080/v3/api-docs

### Links Úteis
- **Repositório:** [GitHub]
- **Issues:** [GitHub Issues]
- **Wiki:** [Confluence/Wiki]

### Equipe
- **Desenvolvido por:** Tech Challenge Team - FIAP
- **Versão:** 1.0
- **Data:** 09/02/2026

---

## 📄 Licença

Este projeto é parte de um desafio técnico educacional - FIAP Tech Challenge.

---

**🎉 README único e objetivo - Documentação completa da aplicação!**

_Desenvolvido com ❤️ pela equipe Tech Challenge_

