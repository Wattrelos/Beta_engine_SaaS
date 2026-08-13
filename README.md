# 1. Objetivo e Diretrizes

O objetivo desta fase é obter uma versão " beta " (1.0) do sistema, onde a arquitetura esteja consolidada e as principais funcionalidades estejam implementadas, com foco em: **qualidade de código, estrutura limpa, performance e boas práticas de desenvolvimento (Clean Architecture, DDD, ADR)**.

O sistema será entregue em um repositório acessível publicamente (Github) para futuras auditorias e manutenção.

# 2. Estrutura do Projeto

### 2.1 Estrutura de Diretórios

A estrutura do projeto deverá seguir o padrão: 
```
project-root/
├── backend/                # Lógica principal da aplicação
│   ├── config/             # Configurações globais (routes, containers, etc.)
│   ├── src/                # Código fonte PHP (Core, Admin, Infrastructure, etc.)
│   └── resources/          # Templates Twig, Locales, etc.
├── public_html/            # Webroot (servida pelo servidor, ex: Apache)
├── vendor/                 # Dependências Composer
├── composer.json           # Dependências do projeto
├── database/               # Migrações SQL e Seeds
├── docs/                   # Documentação (Diagramas, ADRs, Requisitos)
└── tests/                  # Testes automatizados (Unit, Integration, Feature)
```

### 2.2 Arquitetura Interna

- **Backend**: Deve seguir a arquitetura **ADR (Action-Domain-Responder)** combinada com **Clean Architecture**.
- **Controllers (Actions)**: Devem ser simples e orquestrar as camadas superiores.
- **Domain**: Deve conter as regras de negócio puras, entidades, objetos de valor e fábricas.
- **Infraestrutura**: Camada de implementação (ex: DataMapper, Repositórios, Serviços Externos).
- **Views**: Twig.

# 3. Principais Funcionalidades e Módulos

### 3.1 Módulo Core

O core é o coração da aplicação, composto por:

1.  **Sistema de Autenticação e Segurança**:
    * Autenticação por senha (hashing com bcrypt/Argon2).
    * Sessões PHP seguras.
    * Controle de permissões e roles (Admin vs. Usuário).
    * Proteção de rotas com Middleware (PSR-15).

2.  **Gerenciamento de Sessão e Idiomas**:
    * Suporte a múltiplos idiomas (pt-br, en-us, fr-fr).
    * Armazenamento de dados de sessão seguro.

3.  **Utilitários Gerais**:
    * Helpers de string, cache, data, etc.
    * Formatação de preços e datas.

### 3.2 Módulo Administrativo (Admin)

O backoffice deve incluir as seguintes funcionalidades:

1.  **Gerenciamento de Clientes e Endereços**:
    * Criação, edição, listagem e exclusão de clientes.
    * Gerenciamento de endereços múltiplos por cliente.
    * Controle de status (Ativo, Inativo, etc.).

2.  **Gestão de Catálogo**:
    * **Produtos**: Cadastro completo com nome, descrição, preço, estoque, imagens e SEO.
    * **Categorias**: Hierarquia de categorias (subcategorias).
    * **Fabricantes**: Cadastro e gestão de fabricantes.
    * **Fornecedores**: Cadastro e gestão de fornecedores.

3.  **Gestão de Vendas e Pedidos**:
    * Histórico de pedidos.
    * Status de pedidos (Processando, Enviado, Cancelado, Concluído).
    * Emissão de Vouchers/Cupons.

4.  **Configurações Gerais**:
    * Configurações da loja (nome, moeda, fuso, etc.).
    * Configurações de sistemas externos (Gateway de Pagamento, Frete).

5.  **Dashboard**:
    * Visão geral de vendas, produtos mais vendidos, novos clientes, etc.

### 3.3 Módulo de E-commerce e Vendas

1.  **Autenticação de Clientes**:
    * Login, registro, recuperação de senha.
    * Painel "Minha Conta".

2.  **Carrinho e Checkout**:
    * Adicionar/Remover/Atualizar produtos no carrinho.
    * Cálculo de frete (Integração com serviços externos).
    * Cálculo de impostos.
    * Finalização de compra com integração de pagamento.

3.  **Produtos e Categorias**:
    * Visualização de produtos por categoria.
    * Detalhes do produto.
    * Busca e filtros.

4.  **Páginas de Conteúdo**:
    * Páginas informativas (Sobre nós, Contato, Termos, FAQ).

### 3.4 POS (Point of Sale)

- **Layouts**: `cashier` (Caixa), `sales-rep` (Vendedor) e `shared` (Páginas compartilhadas).
- Funcionalidades básicas de venda presencial.

# 4. Aspectos Técnicos e Boas Práticas

### 4.1 Padrões de Código

- **PSR-15**: Uso de Middleware para controle de acesso e sessões.
- **PSR-4**: autoloading do Composer.
- **DTOs**: Uso de Data Transfer Objects para transferência de dados entre camadas.
- **Interfaces**: Utilização de interfaces para dependências (Inversão de Controle).

### 4.2 Segurança

- ** nunca armazene senhas em texto plano**, use `password_hash()`.
- **Proteja rotas** do Admin com Middleware.
- Validação de todos os inputs.

### 4.3 Performance

- Utilizar cache onde for apropriado (ex: cache de categorias, configurações).
- Consultas otimizadas ao banco de dados (evitar N+1).
- Paginação em listas longas.

### 4.4 Internacionalização

- Suporte a 3 idiomas: **pt-br, en-us, fr-fr**.
- Utilizar arquivos de tradução (`.po`, `.mo` ou JSON) na pasta `locales/`.
- O idioma deve ser armazenado na sessão ou perfil do usuário.

### 4.5 Banco de Dados

- O banco deve estar em **MySQL 8.x**.
- Migrações devem estar organizadas na pasta `database/migrations/`.
- Seeds para dados iniciais devem estar em `database/seeds/`.
- Seguir o padrão **DDD** na modelagem (Entities, Value Objects, Repositórios).

### 4.6 Testes

- Cobertura mínima de **70%** dos testes.
- Testes Unitários para regras de negócio.
- Testes de Integração para fluxo de autenticação e fluxo de checkout.
- Testes de Integração para sistema de permissões e admin.

# 5. Processo de Desenvolvimento e Entrega

### 5.1 Agentes de Desenvolvimento (IA)

Para a execução desta fase, serão utilizados os seguintes agentes de IA:

1. **Gerente de Projeto (Project Manager)**:
   * Responsável por planejar as tasks, monitorar o progresso e garantir a qualidade.
   * Deve aprovar ou rejeitar Pull Requests (PRs).

2. **Arquiteto de Software (Software Architect)**:
   * Responsável por garantir que as boas práticas de Clean Architecture, ADR, PSR-15 e DDD sejam seguidas.
   * Deve revisar a estrutura de diretórios e padrões de código.

3. **Desenvolvedor Back-end (Back-end Developer)**:
   * Responsável por implementar a lógica do negócio, banco de dados e APIs.

4. **Desenvolvedor Front-end (Front-end Developer)**:
   * Responsável por implementar as views Twig, AJAX e integração com o back-end.

5. **Engenheiro de Qualidade (QA Engineer)**:
   * Responsável por escrever testes automatizados e realizar testes manuais.

6. **DevOps Engineer**:
   * Responsável por configurar o ambiente, CI/CD e versionamento (Git/GitHub).

### 5.2 Fluxo de Trabalho (Workflow)

1. O **Gerente de Projeto** criará uma `task list` com todas as funcionalidades prioritárias.
2. Para cada funcionalidade, o **Arquiteto** definirá a melhor abordagem e estrutura de código.
3. O **Back-end** implementará a lógica e as APIs.
4. O **Front-end** implementará as views e integrações.
5. O **QA** escreverá