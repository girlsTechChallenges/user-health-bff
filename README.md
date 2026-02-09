# 📚 User Health BFF - Documentação Completa

> **Versão:** 1.0 | **Status:** ✅ Produção Ready | **Data:** 08/02/2026

---

## 📑 Índice

1. [Visão Geral](#-visão-geral)
2. [Quick Start](#-quick-start)
3. [Arquitetura](#-arquitetura)
4. [Docker](#-docker)
5. [Como Executar](#-como-executar)
6. [API Endpoints](#-api-endpoints)
7. [Testes](#-testes)
8. [Troubleshooting](#-troubleshooting)
9. [Roadmap](#-roadmap)

---

## 🎯 Visão Geral

### Descrição
Backend for Frontend (BFF) para gerenciamento de usuários com CRUD completo, validações, tratamento de exceções e documentação Swagger integrada.

### Tecnologias
- **Java 21**
- **Spring Boot 4.0.2**
- **Spring Data JPA**
- **PostgreSQL 42.7.3**
- **Lombok**
- **SpringDoc OpenAPI 2.7.0** (Swagger)
- **Bean Validation**
- **Docker & Docker Compose**
- **Micrometer Tracing** (Observabilidade)
- **Zipkin** (Distributed Tracing)

### Funcionalidades
✅ CRUD completo de usuários  
✅ **Autenticação JWT com RSA**  
✅ **Criptografia de senhas com BCrypt**  
✅ **Endpoints protegidos com Spring Security**  
✅ Validações de entrada (Bean Validation)  
✅ Tratamento de exceções centralizado  
✅ Documentação Swagger/OpenAPI  
✅ Docker configurado  
✅ Health checks e métricas  
✅ Persistência com PostgreSQL  
✅ **Observabilidade nativa Spring Boot com Zipkin**  
✅ **Distributed Tracing automático**  
✅ **Métricas Prometheus**  
✅ **Logging estruturado com TraceID**  

---

## 🚀 Quick Start

> ⚠️ **IMPORTANTE:** Se você acabou de fazer alterações nos arquivos de configuração, execute um **rebuild completo**:
> ```bash
> docker-compose down
> docker rmi user-health-bff-user-health-bff -f
> docker-compose build --no-cache user-health-bff
> docker-compose up -d
> ```
> Sem isso, o Docker pode usar uma imagem antiga com configuração incorreta!

### 🔧 Modo 1: Desenvolvimento na IDE (Perfil LOCAL)

```bash
# 1. Subir infraestrutura no Docker
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
- **Zipkin UI (Traces):** http://localhost:9411
- **Prometheus Metrics:** http://localhost:8080/actuator/prometheus

### Teste Rápido
```bash
# Health check
curl http://localhost:8080/actuator/health

# Criar usuário
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{"nome":"João Silva","email":"joao@test.com","login":"joaosilva","senha":"senha12345678"}'

# Listar usuários
curl http://localhost:8080/api/users
```

---

## 🔐 Autenticação JWT

### Autenticação Implementada

A aplicação possui autenticação completa com **JWT (JSON Web Tokens)** e **Spring Security**.

### Componentes
- **Spring Security** - Framework de autenticação e autorização
- **JWT com RSA** - Tokens assinados com chaves RSA
- **BCrypt** - Hash de senhas com salt automático
- **OAuth2 Resource Server** - Validação de tokens JWT

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

**Resposta:** Token JWT válido por 1 hora
```json
{
  "accessToken": "eyJhbGciOiJSUzI1NiJ9...",
  "expiresIn": 3600
}
```

#### 2. Usar Token em Requisições
```bash
GET /api/users
Authorization: Bearer eyJhbGciOiJSUzI1NiJ9...
```

### Proteção de Endpoints

| Endpoint | Público | Protegido |
|----------|---------|-----------|
| POST /api/v1/auth/login | ✅ Sim | ❌ Não |
| POST /api/v1/users | ✅ Sim | ❌ Não |
| GET /api/users | ❌ Não | ✅ Sim |
| PUT /api/users/{id} | ❌ Não | ✅ Sim |
| DELETE /api/users/{id} | ❌ Não | ✅ Sim |

### Segurança

✅ **Senhas criptografadas** com BCrypt (10 rounds)  
✅ **JWT assinado** com RSA (RS256)  
✅ **Token expira** em 1 hora  
✅ **Session stateless** (escalável)  
✅ **CORS configurado**  
✅ **Exception handling** completo  

### Fluxo de Autenticação

```
1. Criar usuário (POST /api/v1/users)
   → Senha criptografada com BCrypt

2. Login (POST /api/v1/auth/login)  
   → Valida email/senha
   → Gera JWT token
   → Retorna token + expiração

3. Requisições protegidas
   → Header: Authorization: Bearer <token>
   → Spring Security valida token
   → Permite ou nega acesso
```

📖 **Documentação completa:** [AUTENTICACAO.md](AUTENTICACAO.md)

---

## 🔍 Observabilidade

### Stack de Observabilidade Implementada

A aplicação possui observabilidade completa com:

- **OpenTelemetry** - Instrumentação e coleta de telemetria
- **OpenTelemetry Collector** - Processamento e roteamento de dados
- **Jaeger** - Visualização de traces distribuídos
- **Micrometer** - Bridge para métricas e tracing
- **Prometheus** - Métricas exportadas

### Arquitetura

```
Spring Boot App → Zipkin (Traces Visualization)
                → Prometheus Metrics (via Actuator)
```

### URLs de Observabilidade

- **Zipkin UI:** http://localhost:9411
- **Prometheus Metrics:** http://localhost:8080/actuator/prometheus

### Como Usar

1. **Iniciar com observabilidade:**
```bash
docker-compose up -d
```

2. **Gerar tráfego:**
```bash
# Criar usuários e fazer operações
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{"nome":"Test","email":"test@test.com","login":"test","senha":"password123"}'
```

3. **Visualizar traces:**
- Abra http://localhost:9411
- Clique em **"Run Query"** ou busque por service name
- Explore os traces das suas requisições

### O Que Você Verá

✅ **Traces completos** de cada requisição HTTP  
✅ **Spans** individuais (Controller → Service → Repository → Database)  
✅ **Latências** de cada operação  
✅ **Queries SQL** executadas  
✅ **Erros e exceções** com stack trace  
✅ **TraceID e SpanID** nos logs da aplicação  
✅ **Tags e logs** contextuais  

### Exemplo de Trace

```
POST /api/users (201 Created) - 150ms
  └─ Controller.createUser - 148ms
      └─ UserServiceImpl.createUser (@Observed) - 145ms
          ├─ UserRepository.findByEmail - 25ms
          │   └─ PostgreSQL: SELECT ... - 23ms
          └─ UserRepository.save - 115ms
              └─ PostgreSQL: INSERT ... - 112ms
```

### Configurações

**application.yml:**
```yaml
management:
  tracing:
    sampling:
      probability: 1.0  # 100% em dev, ajustar para 0.1 (10%) em produção
  zipkin:
    tracing:
      endpoint: http://zipkin:9411/api/v2/spans
```

### 📚 Documentação Completa de Observabilidade

- 📖 **[OBSERVABILITY.md](./OBSERVABILITY.md)** - Guia completo de observabilidade com Spring Boot e Zipkin
- 🔄 **[MIGRATION_GUIDE.md](./MIGRATION_GUIDE.md)** - Guia de migração de OpenTelemetry para Micrometer + Zipkin
management.otlp.tracing.endpoint=http://otel-collector:4318/v1/traces
logging.pattern.level=%5p [${spring.application.name:},%X{traceId:-},%X{spanId:-}]
```

**Sampling Rate:**
- Desenvolvimento: `1.0` (100% dos traces)
- Produção: `0.1` a `0.2` (10-20% dos traces)

### Métricas Disponíveis

- `http_server_requests_seconds` - Latência HTTP
- `jvm_memory_used_bytes` - Memória JVM
- `hikaricp_connections` - Pool de conexões
- `system_cpu_usage` - Uso de CPU

📖 **Documentação completa:** [OBSERVABILIDADE.md](OBSERVABILIDADE.md)

---

## 🏃 Como Executar

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
│   └── OpenApiConfig.java          # Swagger configuration
├── controller/
│   ├── Controller.java             # REST endpoints
│   └── UserControllerDocs.java     # Swagger documentation
├── dto/
│   ├── request/
│   │   ├── UserRequestDto.java     # Create user DTO
│   │   └── UserUpdateRequestDto.java # Update user DTO
│   └── response/
│       └── UserResponseDto.java     # Response DTO
├── exception/
│   ├── ApiErrorMessage.java         # Error response structure
│   ├── EmailAlreadyExistsException.java
│   ├── UserNotFoundException.java
│   └── GlobalExceptionHandler.java  # Exception handler
├── mapper/
│   └── UserMapper.java              # DTO/Entity conversions
├── model/
│   └── User.java                    # Domain model
├── persistence/
│   ├── entity/
│   │   └── UserEntity.java         # JPA entity
│   └── repository/
│       └── UserRepository.java      # JPA repository
└── service/
    ├── UserServiceInterface.java
    └── UserServiceImpl.java         # Business logic
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

---

## 🐳 Docker


### Comandos Docker

#### Básico
```bash
# Iniciar
docker-compose up -d

# Parar
docker-compose down

# Logs
docker-compose logs -f

# Status
docker-compose ps

# Reiniciar
docker-compose restart

# Rebuild
docker-compose up -d --build
```

#### Debug
```bash
# Entrar no container da aplicação
docker exec -it user-health-bff sh

# Entrar no PostgreSQL
docker exec -it user-health-db psql -U postgres

# Ver uso de recursos
docker stats

# Logs específicos
docker-compose logs -f user-health-bff
docker-compose logs -f app-db
```

#### Limpeza
```bash
# Parar e manter volumes
docker-compose down

# Parar e remover volumes (APAGA DADOS!)
docker-compose down -v

# Limpeza completa do Docker
docker system prune -a --volumes
```


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

### Testes via PowerShell (Windows)

```powershell
# Criar usuário
$body = @{
    nome = "João Santos"
    email = "joao@example.com"
    login = "joaosantos"
    senha = "senha12345678"
} | ConvertTo-Json

Invoke-RestMethod -Uri "http://localhost:8080/api/users" `
  -Method Post -ContentType "application/json" -Body $body

# Listar todos
Invoke-RestMethod -Uri "http://localhost:8080/api/users" -Method Get

# Buscar por ID
Invoke-RestMethod -Uri "http://localhost:8080/api/users/1" -Method Get
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

## 🐛 Troubleshooting

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
- Validações de entrada (Bean Validation)
- Tratamento de exceções centralizado
- Documentação Swagger/OpenAPI
- Docker e Docker Compose
- Health checks e métricas
- Documentação completa
- **Observabilidade com OpenTelemetry e Jaeger**
- **Distributed Tracing completo**
- **Métricas Prometheus**

### 🔵 Curto Prazo (v1.1 - v1.2)
- [ ] Testes unitários (JUnit + Mockito)
- [ ] Testes de integração (TestContainers)
- [ ] BCrypt para hash de senhas
- [ ] Spring Security básico
- [ ] Cobertura de código 80%+

### 🟢 Médio Prazo (v2.0)
- [ ] Paginação e ordenação
- [ ] Filtros de busca
- [ ] Cache com Redis
- [ ] Soft delete
- [ ] Auditoria (created_at, updated_at)
- [ ] JWT para autenticação

### 🟡 Longo Prazo (v3.0+)
- [ ] Event sourcing
- [ ] CQRS pattern
- [ ] Mensageria (Kafka/RabbitMQ)
- [ ] CI/CD completo
- [ ] Observabilidade avançada
- [ ] LGPD/GDPR compliance

---

## 📊 Boas Práticas Implementadas

✅ Separação de camadas (Controller/Service/Repository)  
✅ DTOs para entrada e saída  
✅ Validação em múltiplas camadas  
✅ Tratamento centralizado de exceções  
✅ Transações com @Transactional  
✅ Documentação OpenAPI/Swagger  
✅ Lombok para reduzir boilerplate  
✅ Builder pattern  
✅ Repository pattern  
✅ Senha não exposta nas respostas  
✅ Validação de email único  
✅ Mensagens de erro padronizadas  
✅ Docker para deploy fácil  
✅ Health checks  
✅ Código limpo e manutenível  
✅ **Autenticação JWT com RSA**  
✅ **Senhas criptografadas com BCrypt**  
✅ **Spring Security configurado**  
✅ **Endpoints protegidos**  
✅ **Observabilidade com OpenTelemetry**  
✅ **Distributed Tracing com Jaeger**  
✅ **Métricas com Prometheus**  

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
- **Data:** 08/02/2026

---

## 📄 Licença

Este projeto é parte de um desafio técnico educacional - FIAP Tech Challenge.

---

**🎉 Documentação completa em um único arquivo!**

_Desenvolvido com ❤️ pela equipe Tech Challenge_

