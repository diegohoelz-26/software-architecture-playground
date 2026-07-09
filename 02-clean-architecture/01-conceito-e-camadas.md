# Conceito e Camadas

## O problema que resolve

Sem uma arquitetura explícita, é comum a regra de negócio ficar espalhada e misturada com framework, banco de dados e detalhes de infraestrutura. O resultado: trocar um banco, um framework web ou uma lib externa vira uma reforma no sistema inteiro, porque a lógica de negócio "gruda" nesses detalhes.

Clean Architecture (Robert C. Martin) resolve isso organizando o código em **camadas concêntricas**, onde a regra de negócio fica no centro, isolada de tudo que é detalhe técnico.

## As camadas (de dentro pra fora)

1. **Entities**: as regras de negócio mais fundamentais e estáveis do domínio. Não sabem nada sobre banco de dados, API ou UI. Ex: a regra "um pedido não pode ser cancelado depois de despachado" vive aqui.
2. **Use Cases (Application)**: orquestram as Entities pra realizar uma ação específica do sistema. Ex: `CancelarPedido`, `AprovarCredito`. Sabem *o quê* fazer, não *como* persistir ou expor.
3. **Interface Adapters**: convertem dados entre o formato conveniente para Use Cases/Entities e o formato conveniente para agentes externos (controllers, gateways de banco, apresentadores). Ex: um `PedidoController` que recebe HTTP e chama o Use Case.
4. **Frameworks & Drivers**: a camada mais externa — banco de dados, framework web, filas de mensageria, bibliotecas de terceiros. É a camada mais volátil e a que menos deveria conter regra de negócio.

## Regra de ouro

> Círculos internos não sabem nada sobre círculos externos.

O código de `Entities` e `Use Cases` **nunca** importa nada de `Frameworks & Drivers`. É o inverso que acontece — detalhes de fora dependem do que está definido dentro (ver `02-regra-de-dependencia.md`).

## Exemplo mental

**Ruim**: uma classe `Pedido` (Entity) que importa `SqlConnection` do Entity Framework pra se salvar sozinha.

**Bom**: a classe `Pedido` só conhece suas próprias regras. Quem sabe salvar no banco é uma implementação de repositório, numa camada externa, que implementa uma interface definida pela camada de dentro.

## Quando aplicar

Vale o investimento em sistemas com regra de negócio não-trivial e vida útil longa — onde você espera trocar peças (banco, framework, forma de expor a API) sem reescrever a lógica central. Para scripts pequenos ou provas de conceito descartáveis, o overhead de camadas pode não valer a pena.
