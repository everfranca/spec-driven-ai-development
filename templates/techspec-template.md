# Technical Specification: {{FEATURE_NAME}}

**Scope:** Esta especificação cobre APENAS a feature {{FEATURE_NAME}} conforme descrita no PRD.
Qualquer comportamento fora deste escopo deve ser explicitamente rejeitado.

| Metadata | Details |
| :--- | :--- |
| **Status** | Draft |
| **Data** | {{DATA_ATUAL}} |
| **Referência PRD** | [Link PRD](./prd.md) |
<!-- Status: DRAFT → IN_PROGRESS → APPROVED -->

---

## 1. Introdução e contexto

<!-- INSTRUÇÕES DE PREENCHIMENTO:
OBJETIVO: Dar visão geral técnica da feature em 1-2 parágrafos
FORMATO: Texto conciso (máx 150 palavras)
CONTEÚDO OBRIGATÓRIO:
- Padrão arquitetural usado (ex: "Clean Architecture com CQRS")
- Tecnologias principais (ex: "PostgreSQL, RabbitMQ, Redis")
- Padrões de design aplicados (ex: "Saga pattern", "Repository pattern")

EXEMPLO BOM:
"Esta feature implementará o fluxo de checkout utilizando o padrão Saga para orquestração.
Usaremos RabbitMQ para processamento assíncrono de pagamentos (conforme padrão em PaymentService).
O estado do pedido será persistido no PostgreSQL seguindo estrutura estabelecida em OrderEntity."

ANTI-PATTERN:
"Esta feature é para checkout e vai usar banco de dados." (Muito vago, sem tecnologias específicas)

REGRA: Citar AGENTS.md ou Features similares quando aplicável
-->

{{INTRODUCTION_CONTENT}}

**Objetivo de Negócio:** ...
**Impacto no Usuário:** ...
**Non Goals:** ...
**Pressupostos:** ...

---

## 2. High-Level Architecture [Obrigatório]

### 2.1 Context Diagram

<!-- INSTRUÇÕES DE PREENCHIMENTO:
OBJETIVO: Mostrar componentes envolvidos nesta feature APENAS
FORMATO: Diagrama Mermaid (graph LR ou TD)
REGRAS:
- Refletir APENAS componentes mencionados no PRD
- Serviços especulativos são PROIBIDOS
- Prefira menos nós à completude
- Use rótulos nas arestas para indicar comunicação

EXEMPLO BOM:
graph LR
    User -->|HTTP POST /orders| API
    API -->|Publish OrderCreated| MessageBroker
    PaymentWorker -->|Consume OrderCreated| MessageBroker
    PaymentWorker -->|Update status| Database

EXEMPLO RUIM:
graph LR
    User --> API --> Cache --> MessageBroker --> Worker --> Database --> Analytics
(Cache e Analytics não estão no PRD - isso é especulação)

TIPOS DE NÓS COMUNS:
- User, Client, Frontend
- API, Gateway, Controller
- Service, Worker, Processor
- Database, Cache, Queue
- ExternalService, ThirdPartyAPI
-->

:::mermaid
{{CONTEXT_DIAGRAM_CONTENT}}
:::

<!-- NOTA: Substituir o placeholder acima pelo diagrama real -->

### 2.2 Design de Componentes (Alinhado com a Arquitetura da Aplicação) [Obrigatório]

<!-- INSTRUÇÕES DE PREENCHIMENTO:
OBJETIVO: Listar componentes que serão criados/modificados
FORMATO: Tabela com colunas obrigatórias
REGRAS:
- Todo componente DEVE estar em conformidade com a arquitetura declarada
- Todo componente DEVE pertencer a exatamente UMA camada arquitetural
- Nenhum componente PODE violar direção de dependências

CAMADAS ARQUITETURAIS COMUNS:
- Domain: Entidades, Value Objects, Domain Services
- Application: Use Cases, Commands, Queries, Handlers, DTOs
- Infrastructure: Repositories, External Services, Adapters
- Interface: Controllers, Presenters, Gateways

TIPOS DE COMPONENTES:
- Controller, Handler, Service, Repository, Entity, Adapter, Worker

EXEMPLO BOM:
| Nome | Camada | Tipo | Responsabilidade | Dependências Permitidas |
|:---|:---|:---|:---|:---|
| CreateOrderHandler | Application | Handler | Processa comando de criação de pedido | IOrderRepository, IEventBus |
| OrderRepository | Infrastructure | Repository | Persiste e busca ordens no DB | DbContext (Dapper) |
| Order | Domain | Entity | Entidade de domínio Order | Nenhuma (entidade) |

EXEMPLO RUIM:
| Nome | Camada | Tipo | Descrição |
|:---|:---|:---|:---|
| OrderService | Application | Service | Gerencia pedidos |
(Faltou: responsabilidade específica, dependências)

ANTI-PATTERN:
- Componentes que dependem de camadas "superiores" (Domain dependendo de Application)
- Misturar responsabilidades (Handler que acessa DB diretamente)
-->

| Nome | Camada | Tipo | Responsabilidade | Dependências (Apenas Permitidas) |
|:---|:---|:---|:---|:---|
{{COMPONENT_DESIGN_CONTENT}}
<!-- ADICIONAR LINHAS CONFORME NECESSÁRIO -->

---

## 3. Design e Persistência de Dados [Se aplicável]

### 3.1 Modelos de dados / Schema [Se aplicável]

<!-- INSTRUÇÕES DE PREENCHIMENTO:
OBJETIVO: Definir estrutura de dados EXATA
FORMATO: Lista de Entities ou Tables com campos especificados
REGRAS:
- Tipos de dados ESPECÍFICOS (VARCHAR(255), não "string")
- Constraints explícitas (PK, FK, Unique, Not Null)
- Índices quando aplicável
- Relacionamentos documentados

EXEMPLO BOM:
Entity: Order
- id (UUID, PK): Identificador único
- customer_id (UUID, FK -> User.id, Not Null, Indexed): Cliente
- total_amount (DECIMAL(10,2), Not Null): Valor total
- status (VARCHAR(50), Not Null): [PENDING, PAID, FAILED, CANCELLED]
- created_at (TIMESTAMP, Default: NOW): Data criação

Constraints:
- CHECK (total_amount >= 0)
- INDEX idx_customer_id ON customer_id
- INDEX idx_status ON status

EXEMPLO RUIM:
Entity: Order
- id: int
- customer: reference to user
- amount: decimal
- status: enum
(Tipos genéricos, sem tamanhos, sem constraints)

ANTI-PATTERNS:
- "string" em vez de "VARCHAR(255)"
- "number" em vez de "DECIMAL(10,2)"
- Não especificar nullable/not null
- Não documentar índices
-->

{{DATA_MODELS_CONTENT}}
<!-- ADICIONAR ENTITIES/TABLES CONFORME NECESSÁRIO -->

### 3.2 Estratégia de armazenamento [Se aplicável]

<!-- INSTRUÇÕES DE PREENCHIMENTO:
OBJETIVO: Explicar COMO os dados serão armazenados
FORMATO: Texto curto ou bullet points
CONTEÚDO:
- Tipo de banco (relacional, document, key-value)
- Estratégias de particionamento (se aplicável)
- Políticas de retenção
- Backup/restore considerations

EXEMPLO BOM:
"Orders serão armazenados em PostgreSQL (conforme padrão do projeto).
Dados de auditoria (OrderEvents) serão particionados por mês (partitioning table).
Retenção: 5 anos em produção, conforme compliance."

-->

{{STORAGE_STRATEGY_CONTENT}}

### 3.3 Banco de Dados e Infraestrutura [Se aplicável]

<!-- INSTRUÇÕES DE PREENCHIMENTO:
OBJETIVO: Documentar stack de banco e infraestrutura EXATA
FORMATO: Tabela ou lista estruturada
CONTEÚDO OBRIGATÓRIO:
- Tipo de banco de dados (com versão)
- ORM/Query Builder usado
- Ferramenta de migrations
- Serviços de infraestrutura (Docker, mensageria, cache, storage)
- Padrões de nomenclatura de tabelas/colunas

EXEMPLO BOM:
**Database Stack:**
- Type: PostgreSQL 15+
- ORM: Prisma (conforme padrão do projeto)
- Migrations: Prisma Migrate (/prisma/migrations)
- Naming: snake_case for tables/columns
- FK Pattern: {table}_id (user_id, order_id)
- PK Pattern: UUID with gen_random_uuid()

**Infrastructure Stack:**
- Containerization: Docker Compose (docker-compose.yml)
- Services: postgres, redis, rabbitmq
- Messaging: RabbitMQ with amqplib
- Cache: Redis with ioredis (TTL: 5min default)
- Storage: S3 with CloudFront CDN
- CI/CD: GitHub Actions (test, lint, deploy)

EXEMPLO RUIM:
- Banco SQL
- ORM padrão
- Docker com alguns serviços
(Faltam: versões, nomes específicos, tecnologias exatas)

ANTI-PATTERNS:
- "banco de dados relacional" (qual? PostgreSQL? MySQL?)
- "ORM moderno" (Prisma? TypeORM? Sequelize?)
- Não especificar versões
- Não documentar serviços de docker-compose
-->

{{DATABASE_INFRASTRUCTURE_CONTENT}}

---

## 4. Especificações da interface (Contracts) [Se aplicável]

### 4.1 Public Interfaces

<!-- INSTRUÇÕES DE PREENCHIMENTO:
OBJETIVO: Definir contratos de API EXATOS
FORMATO: Endpoint completo com request/response
REGRAS:
- Método HTTP e rota completos
- Request payload com tipos
- Response para TODOS os status codes possíveis (200, 201, 400, 404, 500)
- Error responses formatados

EXEMPLO BOM:
POST /api/v1/orders
Content-Type: application/json
Authorization: Bearer {token}

Request:
{
  "items": [
    {
      "productId": "uuid",
      "quantity": 1
    }
  ]
}

Response 201 Created:
{
  "orderId": "uuid",
  "status": "PENDING",
  "totalAmount": 99.99,
  "createdAt": "2024-03-01T10:00:00Z"
}

Response 422 Unprocessable Entity:
{
  "code": "INSUFFICIENT_STOCK",
  "message": "Item xyz out of stock",
  "details": {
    "itemId": "xyz",
    "requested": 5,
    "available": 2
  }
}

Response 500 Internal Server Error:
{
  "code": "INTERNAL_ERROR",
  "message": "Unexpected error occurred",
  "requestId": "correlation-id-here"
}

EXEMPLO RUIM:
POST /orders
Request: { order data }
Response: { order }
(Faltam: versão da API, tipos, status codes, error cases)

ANTI-PATTERNS:
- Não documentar error cases
- Não especificar content-type
- Status codes genéricos (sem 422, 400, 404)
- Request sem tipos de dados
-->

{{PUBLIC_INTERFACES_CONTENT}}
<!-- ADICIONAR ENDPOINTS CONFORME NECESSÁRIO -->

### 4.2 Interações internas (Events/Messages) [Se aplicável]

<!-- INSTRUÇÕES DE PREENCHIMENTO:
OBJETIVO: Definir eventos/mensagens internas
FORMATO: Evento com payload estruturado
REGRAS:
- Nome do evento (Passado, ex: OrderCreated)
- Source (quem publica)
- Payload com tipos
- Correlation ID (sempre)

EXEMPLO BOM:
Event: OrderCreated
Source: CreateOrderHandler (Application Layer)
Queue/Topic: orders.events

Payload:
{
  "orderId": "uuid",
  "customerId": "uuid",
  "totalAmount": 99.99,
  "items": [
    {
      "productId": "uuid",
      "quantity": 1,
      "unitPrice": 99.99
    }
  ],
  "createdAt": "2024-03-01T10:00:00Z",
  "correlationId": "uuid"
}

EXEMPLO RUIM:
Event: OrderCreated
Payload: { order data }
(Faltam: source, tipos específicos, correlationId)

ANTI-PATTERN:
- Eventos sem correlation ID (impossível tracing)
- Payloads não estruturados
-->

{{INTERNAL_INTERACTIONS_CONTENT}}
<!-- ADICIONAR EVENTOS/MESSAGES CONFORME NECESSÁRIO -->

---

## 5. Lógica de negócio e algoritmos principais [Obrigatório]

### 5.1 Fluxo principal [Obrigatório]

<!-- INSTRUÇÕES DE PREENCHIMENTO:
OBJETIVO: Descrever passo a passo do "caminho feliz"
FORMATO: Lista numerada de passos
REGRAS:
- Passos observáveis e testáveis
- Decisões binárias claras (se X então Y)
- Validações explicitadas
- Ordem lógica preservada

EXEMPLO BOM:
1. Receber requisição de criação de pedido.
2. **Validação:** Verificar se todos os produtos existem no catálogo (via ProductService).
   - *Se algum produto não existir:* Retornar Erro `ProductNotFound`.
3. **Validação:** Verificar disponibilidade de estoque para cada item (via InventoryService).
   - *Se estoque insuficiente:* Retornar Erro `InsufficientStock`.
4. Calcular valor total do pedido (soma de quantity * unitPrice).
5. **Validação:** Se total > 10.000, exigir aprovação manual.
   - *Se sem aprovação:* Status = `REVIEW_PENDING`.
   - *Se aprovado ou total < 10.000:* Status = `PENDING_PAYMENT`.
6. Salvar pedido no banco (via OrderRepository).
7. Publicar evento `OrderCreated` no message broker.
8. Retornar 201 Created com orderId e status.

EXEMPLO RUIM:
1. Criar pedido
2. Validar
3. Salvar no banco
4. Publicar evento
(Passos genéricos, sem decisões, sem validações específicas)

ANTI-PATTERNS:
- "Processar pedido" (muito vago)
- "Fazer validações" (quais validações?)
- Não documentar tratamentos de erro
-->

{{MAIN_FLOW_CONTENT}}

### 5.2 Casos extremos [Obrigatório]

<!-- INSTRUÇÕES DE PREENCHIMENTO:
OBJETIVO: Documentar edge cases e exceções
FORMATO: Lista de regras com condições e ações
REGRAS:
- Regras de negócio específicas
- Tratamento de falhas
- Retry policies
- Timeout strategies

EXEMPLO BOM:
- **Regra de Negócio:** Se valor total > 10.000, status = `REVIEW_PENDING` e requer aprovação manual.
- **Concorrência:** Se múltiplas requisições para mesmo produto simultaneamente, usar optimistic locking (version field).
- **Retry:** Se PaymentService falhar (5xx), tentar 3 vezes com backoff exponencial (1s, 2s, 4s).
- **Timeout:** Se InventoryService não responder em 3s, falhar com `InventoryTimeout` error.
- **Data Consistency:** Se pagamento falhar após pedido criado, publicar `OrderFailed` e reverter estoque (compensating transaction).

EXEMPLO RUIM:
- Tratar erros
- Tentar novamente se falhar
- Validar inputs
(Genérico, sem números, sem ações específicas)

ANTI-PATTERNS:
- "Tratar gracefulmente" (o que isso significa?)
- "Fazer retry" (quantas vezes? com que backoff?)
- Não documentar compensating transactions
-->

{{EDGE_CASES_CONTENT}}
<!-- ADICIONAR CASOS EXTREMOS CONFORME NECESSÁRIO -->

---

## 6. Observability & Operational Readiness [Se aplicável]

### 6.1 Logging & Tracing

<!-- INSTRUÇÕES DE PREENCHIMENTO:
OBJETIVO: Definir o que deve ser logado
FORMATO: Lista de eventos de log com nível e contexto
REGRAS:
- Níveis apropriados (ERROR, WARN, INFO, DEBUG)
- Contexto relevante (quais dados incluir)
- NUNCA logar dados sensíveis (senha, token, cartão)
- Sempre incluir correlationId

EXEMPLO BOM:
- **Level:** INFO | **When:** Pedido criado com sucesso | **Context:** orderId, customerId, totalAmount, correlationId
- **Level:** ERROR | **When:** Falha no pagamento | **Context:** orderId, errorCode, errorMessage, correlationId (NUNCA logar detalhes do cartão)
- **Level:** WARN | **When:** Estoque baixo (< 10 unidades) | **Context:** productId, currentStock, correlationId
- **Level:** DEBUG | **When:** Início do processamento | **Context:** orderId, step, correlationId

EXEMPLO RUIM:
- Logar erros
- Logar quando pedido for criado
(Faltam: nível, contexto, o que especificamente logar)

ANTI-PATTERNS:
- Logar dados sensíveis (password, token, credit card)
- Logar sem contexto (qual orderId?)
- Usar nivel errado (ERROR para algo esperado)
-->

{{LOGGING_CONTENT}}

### 6.2 Métricas (KPIs) [Se aplicável]

<!-- INSTRUÇÕES DE PREENCHIMENTO:
OBJETIVO: Definir métricas a serem emitidas
FORMATO: Lista de métricas com tipo e descrição
REGRAS:
- Tipos: Counter (incrementos), Gauge (valor atual), Histogram (distribuição)
- Nomes descritivos
- Units quando aplicável

EXEMPLO BOM:
- `orders_created_total` (Counter): Total de pedidos criados
- `order_amount_histogram` (Histogram): Distribuição de valores de pedidos (em USD)
- `payment_processing_duration_seconds` (Histogram): Tempo de processamento de pagamento
- `inventory_check_failures_total` (Counter): Total de falhas em checagem de estoque

EXEMPLO RUIM:
- Métricas de pedidos
- Tempo de processamento
(Faltam: tipo, nome específico, unidade)

ANTI-PATTERNS:
- Métricas sem tipo
- Nomes genéricos ("metrics", "data")
- Não especificar unidade (seconds vs milliseconds)
-->

{{METRICS_CONTENT}}

---

## 7. Segurança & Compliance [Se aplicável]

<!-- INSTRUÇÕES DE PREENCHIMENTO:
OBJETIVO: Documentar requisitos de segurança
FORMATO: Lista de requisitos ou tabela
REGRAS:
- Autenticação/autorização necessária
- Dados sensíveis identificados
- Criptografia aplicada onde necessário
- Input sanitization

EXEMPLO BOM:
- **Authentication:** Endpoints requerem JWT Bearer token com role `customer` ou `admin`.
- **Authorization:** DELETE /orders/{id} requer role `admin` ou ser o dono do pedido (customerId == userId).
- **Data Privacy:** Campos `cpf` e `creditCardNumber` devem ser criptografados em repouso (AES-256).
- **Input Sanitization:** Todos os campos de texto devem ser sanitizados contra XSS e SQL Injection.
- **Criticidade:** Alta (envolve dados financeiros).
- **Recomendação:** Rate limiting de 10 req/min por IP para criação de pedidos.

EXEMPLO RUIM:
- Requer autenticação
- Criptografar dados sensíveis
- Sanitizar inputs
(Faltam: qual tipo de auth? quais dados? como sanitizar?)

ANTI-PATTERNS:
- "Segurança necessária" (o quê?)
- "Criptografar tudo" (o que é criptografável?)
- Não especificar roles ou permissions
-->

{{SECURITY_CONTENT}}

---

## 8. Plano de Implementação [Obrigatório]

<!-- INSTRUÇÕES DE PREENCHIMENTO:
OBJETIVO: Lista de passos técnicos implementáveis
FORMATO: Lista numerada de passos específicos
REGRAS:
- Máximo 10 passos
- Cada passo deve ser INDEPENDENTE (pode virar uma task)
- Passos devem ser MODULARES (não misturar camadas)
- Ordem lógica de execução
- Dependências entre passos explícitas

EXEMPLO BOM:
1. Create migration `20240301_create_orders_table.sql` with schema defined (section 3.1).
2. Create entity `Order.cs` in `/src/Domain/Orders/` following pattern from `User` entity.
3. Create interface `IOrderRepository.cs` in `/src/Infrastructure/Orders/`.
4. Implement `OrderRepository.cs` with Dapper (following pattern from `UserRepository`).
5. Create `CreateOrderCommand.cs` and `CreateOrderHandler.cs` using MediatR pattern (like `CreateUserHandler`).
6. Add FluentValidation validator `CreateOrderCommandValidator.cs`.
7. Create `OrdersController.cs` with POST /api/v1/orders endpoint.
8. Configure Serilog logging for Order flow (INFO on success, ERROR on failure).
9. Write integration tests for `OrderRepository` (following pattern from `UserRepositoryTests`).
10. Write unit tests for `CreateOrderHandler`.

EXEMPLO RUIM:
1. Criar models e repository
2. Criar endpoint
3. Adicionar validação
4. Escrever testes
(Passos genéricos, não modulares, misturam coisas)

ANTI-PATTERNS:
- "Implementar feature X" (muito vago)
- Misturar Database + Backend no mesmo passo (viola atomicidade)
- Não mencionar arquivos ou caminhos
- Passos dependentes não ordenados
- Mais de 10 passos (quebrar em sub-passos)

REGRAS DE ATOMICIDADE:
- Cada passo = UMA camada tecnológica
- Database (migrations, entities)
- Application (commands, handlers, validators)
- Infrastructure (repositories, external services)
- Interface (controllers, endpoints)
- Tests (unit, integration)
-->

{{IMPLEMENTATION_PLAN_CONTENT}}

---

**Template Version:** 0.2.0
