# Comparação com Outras Arquiteturas

## Clean Architecture vs. MVC tradicional (em camadas simples)

| | MVC tradicional | Clean Architecture |
|---|---|---|
| Onde fica a regra de negócio | Geralmente dentro do Controller ou do Model (que já conhece o ORM) | Isolada em Entities/Use Cases, sem conhecer ORM ou framework |
| Direção da dependência | Model depende do ORM/framework diretamente | Regra de negócio não depende de nada externo (Dependency Rule) |
| Troca de banco/framework | Impacta a regra de negócio | Impacta só a camada de infraestrutura |
| Curva de entrada | Baixa, direto ao ponto | Mais alta, exige disciplina de camadas |

MVC tradicional não é "errado" — é adequado para CRUDs simples onde a regra de negócio é rasa (basicamente validação de formulário). Clean Architecture compensa quando a regra de negócio é rica e muda com frequência independente da infraestrutura.

## Clean Architecture vs. Arquitetura Hexagonal (Ports and Adapters)

Já coberto em `04-ports-and-adapters.md` — são, na prática, a mesma ideia com vocabulário diferente. Hexagonal enfatiza a simetria entrada/saída; Clean Architecture enfatiza as camadas concêntricas e a regra de dependência.

## Clean Architecture vs. Onion Architecture

Onion Architecture (Jeffrey Palermo) é praticamente a mesma proposta, com uma camada extra explícita para "Domain Services" entre Entities e Application Services. Na prática, a maioria dos times trata Clean, Onion e Hexagonal como a mesma família de arquitetura — "arquitetura em camadas com a regra de negócio no centro e dependência apontando pra dentro".

## Quando escolher cada uma

- **CRUD simples, vida útil curta, time pequeno**: MVC tradicional resolve mais rápido, sem overhead de camadas.
- **Domínio rico, múltiplos consumidores (API, fila, CLI), vida útil longa, múltiplas integrações externas**: Clean/Hexagonal/Onion — o investimento em isolamento se paga quando o sistema precisa evoluir sem reescrever a regra de negócio.
- **Times grandes com fronteiras de equipe bem definidas**: a separação explícita de camadas facilita paralelismo — um time mexe no domínio, outro no adapter de banco, outro no controller, com menor risco de conflito.

## Armadilha de escolher a arquitetura errada

Aplicar Clean Architecture completa (com todas as camadas e interfaces) num CRUD trivial de 3 telas cria mais boilerplate do que valor — overhead sem benefício real. A decisão de arquitetura deveria ser proporcional à complexidade e ao tempo de vida esperado do sistema.
