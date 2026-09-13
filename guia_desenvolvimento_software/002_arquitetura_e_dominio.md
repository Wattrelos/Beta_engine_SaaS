# Arquitetura e Domínio

Como estruturar o projeto e garantir que o negócio seja bem modelado

**O próximo passo depende de estarmos olhando para a Visão Macro (como o sistema funciona) ou para a Visão Micro (o que vai ser codificado primeiro).**

Para entender com clareza, pense na metáfora da **Construção de uma Casa**:

```
1. Terreno e Plantas Gerais  ➔  Estrutura de Pastas & Diagrama de Arquitetura
2. Fundação e Estrutura      ➔  Modelo de Domínio & Diagrama EER (Banco de Dados)
3. Alvenaria e Encanamento   ➔  Contratos (DTOs / Migrations)
4. Acabamento e Elétrica     ➔  Código (Controllers, Casos de Uso, Telas)
```

Aqui está a sequência recomendada pela engenharia de software moderna:

---

### Passo 1: O Diagrama de Arquitetura (A "Planta Baixa" do Sistema)
*Se você ainda não tem clareza de como as peças conversam entre si.*

O **Diagrama de Arquitetura** (exatamente o que você tem aberto em [`architectureDiagram.puml`](file:///var/www/html/Beta_engine_SaaS/docs/architecture/architectureDiagram.puml)) responde à pergunta:
> *"Quando uma requisição chega da internet, por quais canos ela passa até devolver a resposta?"*

Ele define:
- Quem intercepta a requisição primeiro (o Front Controller `index.php`).
- Quais Middlewares são executados (Sessão, Autenticação, CSRF).
- Quem resolve as dependências (Injeção de Dependência / Container PSR-11).
- Quem chama o quê (Action ➔ Repository ➔ Mapper ➔ DAO ➔ MySQL/Redis).

> [!TIP]
> Como você **já tem esse diagrama de arquitetura desenhado**, a visão macro de como seu sistema funciona já está clara!

---

### Passo 2: O Diagrama de Domínio / Diagrama EER (A "Fundação de Concreto") — 👉 **ESTE É O SEU PRÓXIMO PASSO!**
*Se você quer começar a dar vida às regras de negócio e ao banco.*

O **Diagrama EER (Entidade-Relacionamento Estendido)** ou o **Diagrama de Classes de Domínio** responde à pergunta:
> *"Quais são os conceitos reais do meu negócio e como eles se relacionam?"*

Você **não consegue escrever a primeira classe** em `src/Domain/` ou a primeira migration em `database/migrations/` sem antes responder a coisas como:
1. O que é um `Cliente`? Quais campos ele tem? (Nome, E-mail, CPF/CNPJ, Senha hash).
2. Um `Cliente` pode ter mais de um `Endereço`? (Relação 1 para N).
3. O `Pedido` se relaciona com o `Cliente` ou com uma `Sessão de Venda`?
4. Um `Produto` possui múltiplos `Preços` e `Variações` (cor, tamanho)?

Se você tentar sair programando antes de ter o EER/Domínio modelado:
- Você vai criar tabelas no banco e, depois de 3 dias, descobrir que esqueceu uma chave estrangeira e terá que reescrever código.
- Suas classes e métodos ficarão mudando a cada hora porque você não sabe se o cliente tem um ou múltiplos contatos.

---

### O Fluxo Ideal de Desenvolvimento (Passo a Passo)

Para nunca mais ficar perdido sobre "o que fazer agora", siga este roteiro consolidado na indústria:

```
┌────────────────────────────────────────────────────────────────────────┐
│                        A JORNADA DO DESENVOLVIMENTO                    │
├────────────────────────────────────────────────────────────────────────┤
│ 1. Pastas Organizadas (Estrutura física do projeto)               [OK] │
│                                                                        │
│ 2. Diagrama de Arquitetura (Fluxo macro: Router ➔ Action ➔ DB)    [OK] │
│                                                                        │
│ 3. 🎯 DIAGRAMA EER / MODELO DE DOMÍNIO (O seu foco agora!)             │
│    ↳ Desenhar tabelas, chaves, relacionamentos (1:1, 1:N, N:N).        │
│                                                                        │
│ 4. Migrations do Banco de Dados                                        │
│    ↳ Transformar o diagrama EER em scripts SQL versionados.            │
│                                                                        │
│ 5. Entidades e Value Objects no Código (src/Domain/)                   │
│    ↳ Criar as classes PHP/Java que espelham as entidades do negócio.   │
│                                                                        │
│ 6. Contratos e Repositórios (src/Infrastructure/)                      │
│    ↳ Criar os métodos de salvar, buscar e atualizar no banco.          │
│                                                                        │
│ 7. Casos de Uso e Controllers (src/Application/ e Presentation/)       │
│    ↳ Conectar a interface ou API com as regras de negócio.             │
└────────────────────────────────────────────────────────────────────────┘
```

---

### Resumo: O que fazer exatamente agora?

Como suas pastas já estão organizadas e você já possui a visão arquitetural, o seu próximo passo obrigatório é:

👉 **Validar ou criar o Diagrama EER / Modelo de Dados (Banco de Dados)**.

Com o EER pronto em mãos:
1. Você cria os arquivos de migration em `database/migrations/`.
2. Cria as entidades puras em `src/Domain/`.
3. E o código começa a fluir com naturalidade, sem retrabalho!