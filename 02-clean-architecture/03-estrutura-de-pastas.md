# Estrutura de Pastas na Prática

## O objetivo

A estrutura de pastas deveria comunicar a arquitetura — alguém deveria conseguir entender as camadas só olhando o `tree` do projeto, sem ler uma linha de código.

## Estrutura de referência

```
src/
├── domain/                    # Entities — regra de negócio pura
│   ├── pedido.ts
│   └── cliente.ts
│
├── application/                # Use Cases — orquestram o domínio
│   ├── aprovar-credito.ts
│   ├── cancelar-pedido.ts
│   └── ports/                  # Interfaces que a aplicação precisa (definidas aqui, implementadas fora)
│       ├── repositorio-de-pedidos.ts
│       └── notificador.ts
│
├── infrastructure/              # Frameworks & Drivers — implementações concretas
│   ├── db/
│   │   └── repositorio-de-pedidos-postgres.ts   # implementa a interface de application/ports
│   ├── messaging/
│   │   └── notificador-service-bus.ts
│   └── http/
│       └── cliente-http-parceiro.ts
│
└── interface-adapters/          # Controllers, presenters — traduzem entre externo e Use Cases
    ├── controllers/
    │   └── pedido-controller.ts
    └── presenters/
        └── pedido-presenter.ts
```

## Regra prática de importação

- `domain/` **nunca** importa nada de `infrastructure/` ou `interface-adapters/`.
- `application/` importa de `domain/`, define interfaces em `application/ports/`, mas nunca importa a implementação concreta de `infrastructure/`.
- `infrastructure/` importa de `application/ports/` (pra implementar as interfaces) e de bibliotecas externas (ORM, SDK de fila, HTTP client).
- A "amarração" entre interface e implementação concreta acontece num único lugar: o ponto de composição (ex: um arquivo `main.ts` ou um container de injeção de dependência).

## Ponto de composição (Composition Root)

```
// main.ts — único lugar que conhece a implementação concreta
const repositorio = new RepositorioDePedidosPostgres(conexao)
const useCase = new AprovarCredito(repositorio)
```

Esse é o único arquivo do sistema que "vê" tanto a interface quanto a implementação ao mesmo tempo — todo o resto do código conhece só a interface.

## Erro comum

Organizar pastas por tipo técnico genérico (`controllers/`, `models/`, `services/`) sem separar por camada de responsabilidade — isso é organização de MVC tradicional, não de Clean Architecture, e não impõe a regra de dependência (nada impede um "service" de importar direto o ORM).
