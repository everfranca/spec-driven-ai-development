# Spec-Driven AI Development

Sistema de comandos e templates para desenvolvimento de software guiado por especificações, otimizado para uso com IA (OpenCode, Claude, ChatGPT).

## Objetivo

Transformar ideias vagas em código production-ready através de um fluxo estruturado de especificações que blinda o desenvolvimento contra ambiguidades e garante alinhamento entre negócio e técnica.

## Fluxo de Trabalho

### Fluxo para Projetos Greenfield (novos projetos)

0. **Início de Projeto Greenfield** - Ideia de produto/sistema
    - **gerar-visao.md** - Define FUNDACAO (Contexto + Stack) - Role: Product Manager + Tech Lead - Foco: Visao macro
1. **Input do Usuario** - Ideia ou requisito vago
2. **gerar-prd.md** - Define O QUE (requisitos funcionais) - Role: Product Manager - Foco: Funcionalidade
    - **LÊ**: specs/core/product_vision.md para contexto de negócio (se existir)
3. **gerar-techspec.md** - Define COMO (arquitetura tecnica) - Role: Arquiteto de Software - Foco: Implementacao
    - **LÊ**: specs/core/architecture.md para validação de stack (se existir)
4. **gerar-tasks.md** - Define QUANDO (tarefas atomicas) - Role: Tech Lead - Foco: Executabilidade
5. **executar-task.md** - FAZER (implementacao e validacao) - Role: Engenheiro Senior - Foco: Codigo funcional
6. Codigo Production-Ready

### Fluxo para Projetos Brownfield (legados)

0. **Início de Projeto Brownfield** - Projeto existente sem documentação
    - **gerar-contexto.md** - Analisa código e INFERE (Contexto + Stack) - Role: Engenheiro Senior + Arquiteto - Foco: Análise automática
    - Gera: `specs/core/architecture.md` (APENAS técnica, com níveis de confiança)
    - Gera: `specs/core/product_vision.md` (APENAS negócio, inferida do código)
1. **Input do Usuario** - Nova feature ou requisito para projeto legado
2. **gerar-prd.md** - Define O QUE (requisitos funcionais) - Role: Product Manager - Foco: Funcionalidade
    - **LÊ**: specs/core/product_vision.md (inferido) para contexto de negócio
3. **gerar-techspec.md** - Define COMO (arquitetura tecnica) - Role: Arquiteto de Software - Foco: Implementacao
    - **LÊ**: specs/core/architecture.md (inferido) para validação de stack
4. **gerar-tasks.md** - Define QUANDO (tarefas atomicas) - Role: Tech Lead - Foco: Executabilidade
5. **executar-task.md** - FAZER (implementacao e validacao) - Role: Engenheiro Senior - Foco: Codigo funcional
6. Codigo Production-Ready

**Diferenças entre Greenfield e Brownfield:**
- **Greenfield**: Começa do zero, entrevista usuário, 100% confiança nas decisões
- **Brownfield**: Analisa código existente, infere decisões, níveis de confiança variáveis (90%, 75%, 35%)

## Estrutura do Projeto

```
spec-driven-ai-development/
|-- commands/
|   |-- gerar-visao.md (v0.1.0) - Gerar Visão do Projeto (Inception Greenfield)
|   |-- gerar-contexto.md (v0.1.0) - Analisar Projeto Legado (Brownfield Inference)
|   |-- gerar-prd.md (v0.0.5) - Gerar Product Requirements Document
|   |-- gerar-techspec.md (v0.2.0) - Gerar Technical Specification
|   |-- gerar-tasks.md (v0.0.3) - Quebrar em tarefas executaveis
|   +-- executar-task.md (v0.0.4) - Executar tarefa especifica
|
+-- templates/
    |-- product_vision-template.md - Estrutura da Visão de Produto
    |-- architecture-template.md - Estrutura da Definição de Arquitetura
    |-- prd-template.md - Estrutura do PRD
    |-- techspec-template.md - Blueprint tecnico
    |-- task-template.md - Formato de tarefa individual
    +-- tasks-template.md - Lista mestre de tarefas
```

## Comandos

### 0. `commands/gerar-visao.md` (v0.1.0)
**Role:** Product Manager Sênior + Tech Lead Sênior

Cria os **artefatos fundacionais do projeto** (APENAS para projetos greenfield).

**Características:**
- Entrevista única cobrindo duas fases (Produto + Técnica)
- Gera DOIS arquivos separados:
  - `specs/core/product_vision.md`: Visão de negócio (ZERO técnica)
  - `specs/core/architecture.md`: Definição técnica (ZERO negócio)
- Checkpoint entre fases para aprovação
- Estabelece memória de longo prazo para o projeto
- Previne contaminação técnica em PRDs e perda de contexto em features

**Saída:**
- `specs/core/product_vision.md` (APENAS negócio)
- `specs/core/architecture.md` (APENAS técnica)

**Quando usar:**
- Primeira vez que iniciar um projeto greenfield
- Quando quiser redefinir a direção estratégica do projeto

---

### 0.5. `commands/gerar-contexto.md` (v0.1.0)
**Role:** Engenheiro de Software Sênior + Arquiteto de Software

Analisa **projetos existentes (brownfield)** e inferir regras de negócio e arquitetura técnica automaticamente.

**Características:**
- Análise proativa de código, configurações e estrutura de diretórios
- Sistema de níveis de confiança para cada inferência (90-100%, 60-89%, < 60%)
- Abordagem iterativa: gera `architecture.md` primeiro, valida, depois `product_vision.md`
- Máximo de 2 perguntas por categoria (apenas itens baixa confiança)
- Detecção de integrações externas, maturidade de testes e padrões DDD
- Gera os mesmos artefatos que `gerar-visao.md` mas via análise de código

**Saída:**
- `specs/core/architecture.md` (APENAS técnica, com níveis de confiança)
- `specs/core/product_vision.md` (APENAS negócio, inferida do código)

**Diferenças vs `gerar-visao.md`:**
| Aspecto | `gerar-visao.md` | `gerar-contexto.md` |
|:---|:---|:---|
| **Tipo de projeto** | Greenfield (novo) | Brownfield (legado) |
| **Abordagem** | Entrevista usuário | Analisa código automaticamente |
| **Interação** | 8-25 perguntas | Máx 6 perguntas (2 por categoria) |
| **Fonte de verdade** | Input do usuário | Evidências no código |
| **Níveis de confiança** | N/A (100%) | Sim (90%, 75%, 35%, etc.) |
| **Validação** | Checkpoint entre fases | Validação rápida (2 min) |

**Quando usar:**
- Primeira vez que aplicar Spec-Driven Development em projeto legado
- Quando a documentação está desatualizada ou ausente
- Quando quiser entender a arquitetura e domínio de um projeto existente
- Antes de refatorar ou migrar sistema legado

---

### 1. `commands/gerar-prd.md` (v0.0.5)
**Role:** Product Manager Sênior

Cria o **Product Requirements Document (PRD)** - o contrato entre stakeholders e desenvolvedores.

**Características:**
- Foco exclusivo em funcionalidade (Zero-Code)
- Loop de clarificação antes de gerar documento
- Validação em 3 camadas (consistência, ambiguidade, completude)
- Critérios de aceitação mensuráveis e testáveis

**Saída:** `specs/features/[nome-da-funcionalidade]/prd.md`

**Nota:** Se `specs/core/product_vision.md` existir, o comando o lerá para garantir consistência com a visão global do produto.

### 2. `commands/gerar-techspec.md` (v0.0.4)
**Role:** Arquiteto de Software Sênior

Traduz requisitos de negócio em **especificação técnica detalhada** - o blueprint de implementação.

**Características:**
- Análise de padrões do projeto (README.md, AGENTS.md)
- Define stack, bibliotecas, contratos de API, schemas
- Plano de implementação granular e modular
- Usa Context7 para documentação de frameworks

**Saída:** `specs/features/[nome-da-funcionalidade]/techspec.md`

**Nota:** Se `specs/core/architecture.md` existir, o comando o lerá para garantir que as decisões técnicas respeitem a arquitetura global do projeto.

### 3. `commands/gerar-tasks.md` (v0.0.3)
**Role:** Tech Lead Sênior

Quebra a techspec em **tarefas atômicas e executáveis** que um desenvolvedor júnior pode seguir sem dúvidas.

**Características:**
- Isolamento de contexto tecnológico (Database, Backend, Frontend, Infra)
- Cada task = 1 PR
- Sub-tarefas detalhadas (passo-a-passo explícito)
- Human-in-the-loop (aprovação do plano)

**Saída:** 
- `specs/features/[nome-da-funcionalidade]/tasks.md`
- `specs/features/[nome-da-funcionalidade]/task-1.md`
- `specs/features/[nome-da-funcionalidade]/task-2.md`
- ...

---

### 4. `commands/executar-task.md` (v0.0.4)
**Role:** Engenheiro de Software Sênior

**Executa** itens pendentes de uma task, criando/modificando arquivos reais do projeto.

**Características:**
- Contrato de execução obrigatório
- Código persistido (não apenas descrição)
- Validação com evidências objetivas
- Atualização de checkboxes comprovada

**Entrada:** Arquivo de task específico
**Saída:** Código funcional + task atualizada

## Templates

### `templates/prd-template.md`
Estrutura padrão para documentos de requisito contendo:
- Contexto e Problema
- Escopo (In/Out)
- Personas e User Stories
- Requisitos Funcionais (com Gherkin)
- Fluxos de Exceção
- Requisitos Não-Funcionais
- Critérios de Aceite

### `templates/techspec-template.md`
Blueprint técnico com:
- High-Level Architecture (diagramas Mermaid)
- Design de Componentes (camadas arquiteturais)
- Modelos de Dados / Schema
- Contratos de API (Request/Response)
- Lógica de Negócio e Algoritmos
- Plano de Implementação

### `templates/task-template.md`
Formato de tarefa executável com:
- Contexto e Objetivo
- Requisitos (mapeados ao PRD)
- Plano de Execução (sub-tarefas)
- Detalhes de Implementação (snippets)
- Critérios de Aceite

### `templates/tasks-template.md`
Lista mestre simples de todas as tarefas da feature.

## Como Usar

### Fluxo Completo de Desenvolvimento

1. **Iniciar nova feature:**
   ```bash
   # Use o comando gerar-prd.md com sua ideia
   Input: "Quero um sistema de autenticação com login social"
   ```

2. **Gerar PRD:**
   - A IA fará perguntas de clarificação
   - Responda até não haver ambiguidades
   - PRD salvo em: `specs/features/autenticacao/prd.md`

3. **Gerar Tech Spec:**
   - Use o comando gerar-techspec.md
   - A IA analisará patterns do seu projeto
   - Tech Spec salva em: `specs/features/autenticacao/techspec.md`

4. **Gerar Tasks:**
   - Use o comando gerar-tasks.md
   - Revise o plano proposto
   - Tasks salvas em: `specs/features/autenticacao/task-*.md`

5. **Executar Tasks:**
   - Use executar-task.md para cada task
   - Cada execução gera código real
   - Marca checkboxes como concluídos

## Estrutura de Diretórios Esperada

```
seu-projeto/
|-- specs/
|   +-- core/
|       |-- product_vision.md (Visão de produto - Apenas para greenfield)
|       +-- architecture.md (Definição de arquitetura - Apenas para greenfield)
|   +-- features/
|       +-- [nome-da-funcionalidade]/
|           |-- prd.md (Requisitos funcionais)
|   |-- techspec.md      (Especificação técnica)
|           |-- tasks.md (Lista de todas as tasks)
|           |-- task-1.md (Task 1 detalhada)
|           |-- task-2.md (Task 2 detalhada)
|           +-- ...
|
+-- [codigo da aplicacao]
```

## Princípios Fundamentais

### Atomicidade
- Cada task foca em **UMA** camada tecnológica
- Tasks misturadas (DB + Backend) são proibidas
- Cada task deve ser testável independentemente

### Explicitação
- Nada de "configure o banco" → "Crie arquivo X com conteúdo Y"
- Caminhos completos e relativos
- Passos sequenciais numerados

### Validacao
- Critérios de aceitação binários (passa/falha)
- Evidências objetivas obrigatórias
- Zero sucesso sem artefatos reais

### Zero Ambiguidade
- Termos mensuráveis (< 2s, não "rápido")
- Comportamentos observáveis
- Contratos explícitos (JSON, status codes)

## Status dos Documentos

### PRD Status Flow
```
DRAFT → IN_PROGRESS → IN_REVIEW → APPROVED
```

### TechSpec Status Flow
```
DRAFT → IN_PROGRESS → APPROVED
```

### Task Status Flow
```
TODO → IN_PROGRESS → DONE
```

## Integrações

- **Context7:** Para consultar documentação de frameworks e bibliotecas
- **Mermaid:** Para diagramas de arquitetura
- **Gherkin:** Para cenários de teste BDD

## Notas Importantes

- Todos os comandos usam system-instructions para orientar a IA
- Templates garantem consistência entre features
- Versionamento semântico em cada comando (ex: v0.0.5)
- Foco em desenvolvimento guiado por especificações, não apenas código

## Contribuindo

Este sistema é iterativo. Sugestões de melhoria nos comandos e templates são bem-vindas.

**Versão Atual:** Framework v0.0.5
**Última Atualização:** Março 2026
