# Ports and Adapters (Arquitetura Hexagonal)

## Relação com Clean Architecture

Ports and Adapters (também chamada de Arquitetura Hexagonal, criada por Alistair Cockburn) e Clean Architecture resolvem o mesmo problema com vocabulário diferente. Ambas isolam a regra de negócio de detalhes técnicos através de interfaces — a diferença é mais de ênfase e representação visual do que de princípio.

| Clean Architecture | Hexagonal | Papel |
|---|---|---|
| Entities + Use Cases | Domínio (hexágono central) | Regra de negócio |
| Interface definida na camada de aplicação | Port | Contrato que o domínio expõe/consome |
| Implementação na camada externa | Adapter | Implementação concreta do contrato |

## Dois tipos de Port

1. **Driving Port (ou Primary)**: como o mundo externo aciona o domínio. Ex: uma interface `CasoDeUsoAprovarCredito` que um controller HTTP chama.
2. **Driven Port (ou Secondary)**: o que o domínio precisa do mundo externo. Ex: a interface `RepositorioDePedidos` que o domínio usa pra persistir dados.

## Adapters correspondentes

- **Driving Adapter**: o controller REST, o handler de fila, a CLI — quem traduz uma entrada externa numa chamada ao Port.
- **Driven Adapter**: a implementação Postgres do repositório, o client HTTP do parceiro bancário, o publisher do Service Bus.

## Por que pensar em "hexágono"

O desenho do hexágono é só uma forma de dizer "o domínio tem múltiplas faces de entrada e saída, todas elas são apenas Ports — não importa se é HTTP, fila, CLI ou teste automatizado batendo direto na interface." Isso reforça que o domínio não sabe (nem deveria saber) quem está do outro lado do port.

## Exemplo prático: trocar de canal sem tocar no domínio

Se hoje seu Use Case `AprovarCredito` é chamado via API REST, e amanhã você precisa que a mesma regra seja acionada por uma mensagem de fila (Service Bus) — você só cria um novo **Driving Adapter** (um consumer de fila) que chama o mesmo Port. A regra de negócio em si não muda uma linha.

## Quando usar esse vocabulário em vez de "Clean Architecture"

Na prática, são intercambiáveis. Use "Ports and Adapters" quando quiser enfatizar a simetria de entrada/saída (múltiplos adapters plugando no mesmo domínio) — é uma forma mais visual de explicar o conceito para quem está vendo pela primeira vez, inclusive em entrevistas.
