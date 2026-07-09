# Armadilhas Comuns

## 1. Anemic Domain Model (Modelo de Domínio Anêmico)

As `Entities` viram apenas structs de dados (getters/setters, sem comportamento), e toda a regra de negócio migra pros `Use Cases`. Isso enfraquece o propósito da camada de domínio — Entities deveriam carregar comportamento e invariantes (ex: `pedido.cancelar()` que valida se pode cancelar), não só dados.

**Sinal de alerta**: Use Cases cheios de `if` validando regras que deveriam estar dentro da própria Entity.

## 2. Over-engineering em projetos pequenos

Aplicar todas as camadas (Entities, Use Cases, Ports, Adapters, Presenters) num sistema de 3 telas gera uma quantidade de arquivos e indireção que não se paga. O critério não é "seguir a arquitetura à risca sempre", é aplicar o **princípio** (isolar regra de negócio de detalhe técnico) na medida certa pro tamanho do problema.

## 3. Vazamento de framework pro domínio

Decorators de ORM (ex: `@Entity`, `@Column` do TypeORM/Prisma) direto nas classes de domínio. Tecnicamente funciona, mas acopla a Entity ao framework de persistência — a troca de ORM ou banco passa a exigir mexer na regra de negócio.

**Mitigação prática**: manter um objeto de domínio "puro" e mapear pra um objeto de persistência (com os decorators) na camada de infraestrutura.

## 4. Interfaces genéricas demais ou específicas demais

- **Genérica demais**: uma interface `Repositorio<T>` só com `save`/`find`/`delete` que não expressa a linguagem do domínio (ex: `RepositorioDePedidos.buscarPendentesPorCliente()` é mais expressivo que um repositório genérico com query builder exposto).
- **Específica demais**: uma interface por cada pequena variação de consulta, virando um emaranhado de métodos que ninguém lembra qual usar.

## 5. Confundir camadas com pastas

Ter as pastas certas (`domain/`, `application/`, `infrastructure/`) não garante a regra de dependência — é perfeitamente possível importar `infrastructure/` dentro de `domain/` mesmo com a estrutura de pastas correta. A disciplina de import é o que importa; a pasta é só o lembrete visual. Ferramentas de lint (ex: regras de import por camada no ESLint, ArchUnit no Java) ajudam a garantir isso automaticamente.

## 6. Use Cases fazendo trabalho de Controller

Um Use Case que recebe diretamente o objeto de request HTTP (`req.body`), em vez de receber um DTO já traduzido pelo controller. Isso acopla a camada de aplicação ao protocolo HTTP, quebrando a promessa de que o mesmo Use Case poderia ser chamado por uma fila ou uma CLI sem alteração.

## Resumo prático

A arquitetura existe pra servir a manutenibilidade do sistema, não o contrário. Se em algum ponto você está adicionando camadas só "porque é o padrão", pare e pergunte: essa camada está isolando algo que realmente muda com frequência, ou só adicionando indireção?
