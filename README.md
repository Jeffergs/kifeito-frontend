# Kifeito Frontend

O **Kifeito Frontend** é a interface web da aplicação, responsável por disponibilizar ao usuário as funcionalidades do Kifeito como um gerenciador pessoal de tarefas.

O frontend será desenvolvido em **Angular** e consumirá exclusivamente a API do **Kifeito BFF**.

O frontend não acessa diretamente os microsserviços **User**, **Tasks** ou **Notification**.

---

## 📋 Índice

1. [🎯 Responsabilidade](#-responsabilidade)
2. [🔐 Autenticação](#-autenticação)
3. [📊 Dashboard](#-dashboard)
4. [📋 Tarefas](#-tarefas)
5. [🔄 Ciclo de vida da tarefa](#-ciclo-de-vida-da-tarefa)
6. [👤 Perfil](#-perfil)
7. [🧭 Navegação](#-navegação)
8. [🔒 Autenticação e sessão](#-autenticação-e-sessão)
9. [⚠️ Estados da interface](#️-estados-da-interface)
10. [🧩 Componentes](#-componentes)
11. [🛠️ Tecnologias](#️-tecnologias)
12. [📁 Estrutura](#-estrutura)
13. [⚙️ Configuração](#️-configuração)
14. [🐳 Docker](#-docker)
15. [🧪 Testes](#-testes)
16. [🚫 Fora do escopo da versão 1](#-fora-do-escopo-da-versão-1)
17. [📄 Licença](#-licença)

---

# 🎯 Responsabilidade

O frontend possui uma responsabilidade específica:

> **Fornecer a interface de interação do usuário com o Kifeito.**

O frontend é responsável por:

- Apresentar as telas da aplicação;
- Capturar os dados fornecidos pelo usuário;
- Realizar validações de interface;
- Consumir a API do BFF;
- Apresentar os resultados das operações;
- Controlar a navegação entre as telas;
- Manter o estado da interface;
- Armazenar o token de acesso conforme a estratégia definida para a aplicação;
- Controlar a exibição das ações de acordo com o estado da tarefa;
- Tratar erros retornados pela API.

O frontend **não possui as regras de negócio dos domínios User ou Tasks**.

As regras de negócio permanecem nos respectivos microsserviços.

### Comunicação

```text
Frontend
   │
   │ HTTP
   ▼
 BFF
   │
   ├──────────► User
   │
   └──────────► Tasks
````

O frontend não acessa diretamente:

```
User
Tasks
Notification
PostgreSQL
RabbitMQ
```

⬆️ Voltar ao índice

---

# 🔐 Autenticação

A versão 1 possui as telas necessárias para criação da conta e autenticação do usuário.

---

## 1.1. Cadastro

Tela para criação de uma nova conta.

### Campos

-  Nome; 
-  E-mail; 
-  Senha; 
-  Confirmação de senha. 

### Funcionalidades

-  Validação dos campos; 
-  Validação de e-mail; 
-  Validação da senha; 
-  Confirmação de senha; 
-  Indicação de erros de validação; 
-  Envio para `POST /api/auth/register`; 
-  Tratamento de erros retornados pela API; 
-  Redirecionamento para a tela de login após o cadastro. 

### Fluxo

```
Preenchimento
     ↓
Validação
     ↓
POST /api/auth/register
     ↓
Sucesso
     ↓
Redirecionamento para Login
```

---

## 1.2. Login

Tela para autenticação do usuário.

### Campos

-  E-mail; 
-  Senha. 

### Funcionalidades

-  Validação dos campos; 
-  Autenticação; 
-  Armazenamento do token de acesso; 
-  Tratamento de credenciais inválidas; 
-  Indicação de erros; 
-  Redirecionamento para o Dashboard após login. 

### Endpoint

```
POST /api/auth/login
```

### Fluxo

```
E-mail + senha
      ↓
POST /api/auth/login
      ↓
JWT
      ↓
Armazenamento do token
      ↓
Dashboard
```

⬆️ Voltar ao índice

---

# 📊 Dashboard

O Dashboard é a tela inicial após a autenticação.

### Endpoint

```
GET /api/dashboard
```

O Dashboard apresenta uma visão resumida da situação das tarefas do usuário.

### Informações

Deve apresentar, pelo menos:

-  Total de tarefas; 
-  Tarefas pendentes; 
-  Tarefas concluídas; 
-  Tarefas canceladas; 
-  Próximas tarefas; 
-  Tarefas atrasadas. 

### Exemplo de organização

```
┌─────────────────────────────────────────────┐
│ Dashboard                                   │
│                                             │
│ Total     Pendentes   Concluídas Canceladas │
│   20          8           10         2      │
│                                             │
│ Próximas tarefas                            │
│ ─────────────────────────────────────────── │
│ Estudar Spring          04/09 19:00         │
│ Fazer exercício         05/09 07:00         │
│                                             │
│ Tarefas atrasadas                           │
│ ─────────────────────────────────────────── │
│ Revisar Java            02/09 20:00         │
└─────────────────────────────────────────────┘
```

O Dashboard é uma **visão da aplicação**, e não uma entidade ou domínio próprio.

Os dados são fornecidos pelo BFF.

⬆️ Voltar ao índice

---

# 📋 Tarefas

A aplicação disponibiliza uma área para gerenciamento das tarefas do usuário.

---

## 3.1. Listagem

Tela **Minhas tarefas**.

### Endpoint

```
GET /api/tasks
```

Deve permitir visualizar:

-  Título; 
-  Descrição; 
-  Data de agendamento; 
-  Status. 

### Funcionalidades

-  Listar as tarefas do usuário; 
-  Indicar visualmente o status; 
-  Permitir filtros básicos por status; 
-  Acessar os detalhes de uma tarefa; 
-  Acessar a edição de uma tarefa; 
-  Disponibilizar as ações permitidas para cada estado. 

### Exemplo

```
Minhas tarefas

[ Todas ] [ Pendentes ] [ Concluídas ] [ Canceladas ]

────────────────────────────────────────────

Estudar Spring Boot
04/09/2026 19:00
PENDING

[Visualizar] [Editar] [Concluir] [Cancelar]

────────────────────────────────────────────

Revisar Java
02/09/2026 20:00
COMPLETED

[Visualizar] [Reabrir]

────────────────────────────────────────────
```

---

## 3.2. Visualização

Tela para visualizar uma tarefa individual.

### Endpoint

```
GET /api/tasks/{id}
```

### Informações

Deve exibir:

-  Título; 
-  Descrição; 
-  Data de agendamento; 
-  Status; 
-  Data de criação; 
-  Data de alteração. 

A tela deve apresentar as ações permitidas de acordo com o status atual da tarefa.

---

## 3.3. Criação

Tela ou formulário para criação de uma nova tarefa.

### Campos

-  Título; 
-  Descrição; 
-  Data de agendamento. 

### Endpoint

```
POST /api/tasks
```

### Funcionalidades

-  Validação dos campos; 
-  Indicação de erros; 
-  Criação da tarefa; 
-  Tratamento de erros retornados pela API; 
-  Redirecionamento para a listagem ou visualização da tarefa após sucesso. 

### Fluxo

```
Formulário
    ↓
Validação
    ↓
POST /api/tasks
    ↓
Sucesso
    ↓
Listagem ou detalhes
```

---

## 3.4. Edição

Tela ou formulário para alteração de uma tarefa.

### Campos

-  Título; 
-  Descrição; 
-  Data de agendamento. 

### Endpoint

```
PUT /api/tasks/{id}
```

### Funcionalidades

-  Carregar os dados atuais da tarefa; 
-  Permitir alteração dos campos disponíveis; 
-  Validar os dados; 
-  Enviar a atualização; 
-  Apresentar erros; 
-  Atualizar a interface após sucesso. 

⬆️ Voltar ao índice

---

# 🔄 Ciclo de vida da tarefa

A interface deve disponibilizar as ações correspondentes ao estado atual da tarefa.

### Endpoints

| AçãoMétodoEndpoint |        |                              |
| ------------------ | ------ | ---------------------------- |
| Concluir           | PATCH  | `/api/tasks/{id}/complete`   |
| Reabrir            | PATCH  | `/api/tasks/{id}/reopen`     |
| Cancelar           | PATCH  | `/api/tasks/{id}/cancel`     |
| Reativar           | PATCH  | `/api/tasks/{id}/reactivate` |
| Excluir            | DELETE | `/api/tasks/{id}`            |

A interface deve mostrar somente ações coerentes com o estado atual da tarefa.

### PENDING

```
PENDING
├── Complete
├── Cancel
├── Edit
└── Delete
```

### COMPLETED

```
COMPLETED
├── Reopen
└── Delete
```

### CANCELLED

```
CANCELLED
├── Reactivate
└── Delete
```

### Confirmações

Operações destrutivas ou que alteram significativamente o estado da tarefa devem possuir confirmação quando apropriado.

Principalmente:

```
Delete
Cancel
```

A confirmação deve informar claramente a ação que será realizada.

### Atualização da interface

Após uma ação de ciclo de vida:

```
Ação
 ↓
Requisição
 ↓
Sucesso
 ↓
Atualiza estado da interface
 ↓
Exibe novo status
```

O frontend não altera o status localmente antes de receber a confirmação da API.

⬆️ Voltar ao índice

---

# 👤 Perfil

Tela para gerenciamento da própria conta.

### Consultar perfil

```
GET /api/user
```

Deve permitir visualizar:

-  Nome; 
-  E-mail. 

### Editar perfil

```
PUT /api/user
```

Deve permitir alterar:

-  Nome; 
-  E-mail. 

### Excluir conta

```
DELETE /api/user
```

A exclusão deve exigir uma confirmação explícita na interface.

### Fluxo de exclusão

```
Excluir conta
      ↓
Confirmação
      ↓
DELETE /api/user
      ↓
Sucesso
      ↓
limpa sessão
      ↓
Login
```

⬆️ Voltar ao índice

---

# 🧭 Navegação

A aplicação deve possuir uma navegação adequada aos diferentes estados de autenticação.

### Usuário não autenticado

```
Login
  │
  └──→ Cadastro
```

### Usuário autenticado

```
Dashboard
   │
   ├──→ Minhas tarefas
   │       │
   │       ├──→ Criar tarefa
   │       ├──→ Visualizar tarefa
   │       └──→ Editar tarefa
   │
   └──→ Perfil
```

### Rotas protegidas

As telas que exigem autenticação devem ser protegidas por um mecanismo de controle de acesso no frontend.

Um usuário não autenticado não deve conseguir navegar diretamente para áreas protegidas da aplicação.

### Exemplo

```
/dashboard
/tasks
/tasks/new
/tasks/:id
/tasks/:id/edit
/profile
```

Caso não exista uma sessão válida:

```
Rota protegida
      ↓
não autenticado
      ↓
/login
```

⬆️ Voltar ao índice

---

# 🔒 Autenticação e sessão

O frontend deve incluir o token de acesso nas requisições que exigem autenticação.

### Header

```
Authorization: Bearer <access-token>
```

A inclusão do token deve ser centralizada em um **HTTP Interceptor**.

### Fluxo

```
Frontend
   ↓
HTTP Request
   ↓
HTTP Interceptor
   ↓
adiciona Authorization
   ↓
BFF
```

O frontend não deve adicionar manualmente o token em cada chamada HTTP.

### Sessão expirada

Quando a API indicar que a sessão não é mais válida:

```
requisição
   ↓
401 Unauthorized
   ↓
Limpa sessão
   ↓
Redireciona para Login
```

Na versão 1, não haverá fluxo de Refresh Token.

⬆️ Voltar ao índice

---

# ⚠️ Estados da interface

A versão 1 deve tratar os estados básicos das requisições.

```
Carregando
    ↓
 ┌───────┐
 │       │
 ▼       ▼
Sucesso  Erro
  ↓        ↓
dados    mensagem
```

### Loading

Durante uma requisição:

-  Indicar que a operação está em andamento; 
-  Evitar ações duplicadas; 
-  Impedir múltiplos envios acidentais de um mesmo formulário. 

### Sucesso

Após uma operação realizada com sucesso:

-  Atualizar a interface; 
-  Apresentar os dados atualizados; 
-  Redirecionar quando necessário; 
-  Apresentar feedback ao usuário quando apropriado. 

### Erro de validação

Apresentar os erros próximos aos campos correspondentes.

### Não encontrado

Quando o recurso não existir:

```
404 Not Found
```

A interface deve apresentar uma mensagem adequada e permitir retornar para uma tela válida.

### Não autorizado

Quando a sessão não for válida:

```
401 Unauthorized
```

A interface deve limpar a sessão e redirecionar para o login.

### Acesso negado

Quando a operação não for permitida:

```
403 Forbidden
```

A interface deve informar que a operação não está autorizada.

### Conflito

Quando ocorrer um conflito de dados:

```
409 Conflict
```

A interface deve apresentar uma mensagem adequada ao contexto.

### Erro inesperado

Para erros não previstos:

```
500 Internal Server Error
```

A interface deve apresentar uma mensagem genérica, sem expor detalhes técnicos da aplicação.

### Estado vazio

As telas de listagem também devem tratar o caso em que não existem dados.

Exemplo:

```
Nenhuma tarefa encontrada.

[ Criar primeira tarefa ]
```

⬆️ Voltar ao índice

---

# 🧩 Componentes

A aplicação deverá utilizar componentes reutilizáveis para evitar duplicação de interface e comportamento.

### Componentes principais

```
Navbar
Sidebar
Button
Input
Textarea
DateTimePicker
Modal
ConfirmDialog
Loading
Toast
Alert
TaskCard
TaskStatusBadge
TaskForm
EmptyState
```

### Responsabilidades

**Navbar** — Navegação principal e acesso ao perfil/logout.

**Sidebar** — Navegação entre as principais áreas da aplicação, quando utilizada no layout.

**TaskCard** — Apresentação resumida de uma tarefa.

**TaskStatusBadge** — Representação visual do status da tarefa.

**TaskForm** — Formulário reutilizável para criação e edição.

**ConfirmDialog** — Confirmação de ações destrutivas ou relevantes.

**Loading** — Indicação visual de carregamento.

**Toast / Alert** — Feedback de sucesso, erro ou informação.

**EmptyState** — Representação de listas sem dados.

Os componentes devem permanecer focados em apresentação e interação da interface.

As regras de negócio pertencem ao backend.

⬆️ Voltar ao índice

---

# 🛠️ Tecnologias

| TecnologiaUtilização |                          |
| -------------------- | ------------------------ |
| Angular              | Framework frontend       |
| TypeScript           | Linguagem                |
| HTML                 | Estrutura das interfaces |
| CSS                  | Estilização              |
| Angular Router       | Navegação                |
| Angular HttpClient   | Comunicação HTTP         |
| Reactive Forms       | Formulários e validação  |
| RxJS                 | Programação reativa      |
| Docker               | Containerização          |
| GitHub Actions       | CI                       |


⬆️ Voltar ao índice

---

# 📁 Estrutura

Estrutura inicial:

```
kifeito-frontend
│
├── src
│   ├── app
│   │   │
│   │   ├── core
│   │   │   ├── guards
│   │   │   │   └── auth.guard.ts
│   │   │   ├── interceptors
│   │   │   │   └── auth.interceptor.ts
│   │   │   ├── services
│   │   │   │   ├── auth.service.ts
│   │   │   │   ├── user.service.ts
│   │   │   │   ├── task.service.ts
│   │   │   │   └── dashboard.service.ts
│   │   │   └── models
│   │   │
│   │   ├── shared
│   │   │   ├── components
│   │   │   ├── pipes
│   │   │   └── directives
│   │   │
│   │   ├── features
│   │   │   ├── auth
│   │   │   │   ├── login
│   │   │   │   └── register
│   │   │   │
│   │   │   ├── dashboard
│   │   │   │
│   │   │   ├── tasks
│   │   │   │   ├── task-list
│   │   │   │   ├── task-detail
│   │   │   │   ├── task-form
│   │   │   │   └── task-card
│   │   │   │
│   │   │   └── profile
│   │   │
│   │   ├── app.routes.ts
│   │   └── app.config.ts
│   │
│   ├── assets
│   │
│   ├── environments
│   │   ├── environment.ts
│   │   └── environment.prod.ts
│   │
│   ├── index.html
│   └── styles.css
│
├── public
│
├── .github
│   └── workflows
│       └── pull-request.yml
│
├── .gitignore
├── Dockerfile
├── angular.json
├── package.json
├── package-lock.json
├── tsconfig.json
└── README.md
```

⬆️ Voltar ao índice

---

# ⚙️ Configuração

O endereço da API do BFF deve ser configurado por ambiente.

Exemplo:

```
BFF_API_URL
```

Exemplo de utilização:

```
http://localhost:8080
```

As configurações específicas de ambiente não devem ser armazenadas diretamente no código-fonte.

> Informações sensíveis não devem ser incluídas no frontend, pois o código frontend é distribuído para o navegador.

⬆️ Voltar ao índice

---

# 🐳 Docker

O Kifeito Frontend possui seu próprio `Dockerfile` e pode ser executado junto aos demais serviços através do Docker Compose.

O Docker garante um ambiente de execução padronizado e permite executar o frontend de forma isolada e reproduzível.

O serviço pode ser executado juntamente com:

```
Kifeito BFF
Kifeito User
Kifeito Tasks
Kifeito Notification
PostgreSQL
RabbitMQ
```

Em produção, o frontend poderá ser servido por um servidor web como **Nginx**, enquanto o BFF permanece responsável pela API.

⬆️ Voltar ao índice

---

# 🧪 Testes

O serviço terá testes unitários para as regras de interface e testes de integração para suas principais integrações.

- **Testes unitários:** validação de formulários, componentes, navegação, guards e estados da interface. 
- **Testes de integração:** BFF, autenticação e principais fluxos de interação da aplicação. 

⬆️ Voltar ao índice

---

# 🚫 Fora do escopo da versão 1

Para manter a complexidade proporcional à necessidade do sistema, a versão 1 não possui:

-  Aplicativo mobile; 
-  PWA; 
-  Notificações push; 
-  Funcionamento offline; 
-  Sincronização em tempo real; 
-  WebSocket; 
-  Múltiplos temas; 
-  Internacionalização; 
-  Acessibilidade avançada além dos requisitos básicos; 
-  Gerenciamento avançado de estado; 
-  NgRx; 
-  GraphQL; 
-  Múltiplos idiomas; 
-  Compartilhamento de tarefas; 
-  Colaboração entre usuários; 
-  Tarefas recorrentes; 
-  Múltiplos lembretes; 
-  Recuperação de senha; 
-  Troca de senha; 
-  Autenticação em dois fatores; 
-  Login social. 

Esses recursos poderão ser avaliados em versões futuras conforme a necessidade real do produto.

⬆️ Voltar ao índice

---

# 📄 Licença

O Kifeito está sendo desenvolvido inicialmente para uso próprio e para um grupo limitado de usuários.

Apesar do uso inicial restrito, o projeto está sendo desenvolvido com arquitetura, práticas e estrutura voltadas para um produto comercial, podendo futuramente ser disponibilizado de forma mais ampla.

O código-fonte, a aplicação, a identidade visual, a documentação e demais componentes do projeto são de propriedade do próprio autor.

A utilização, cópia, modificação, distribuição ou comercialização de qualquer parte do projeto depende de autorização expressa do detentor dos direitos.

⬆️ Voltar ao índice

```
```
