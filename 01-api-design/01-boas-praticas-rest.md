# Boas Práticas REST

## Conceito

REST não é um protocolo, é um estilo arquitetural. Os princípios centrais:

- **Recursos, não ações**: a URL representa um substantivo (`/pedidos`), não um verbo (`/criarPedido`).
- **Métodos HTTP carregam a semântica da ação**: `GET` lê, `POST` cria, `PUT` substitui, `PATCH` atualiza parcialmente, `DELETE` remove.
- **Stateless**: cada requisição carrega tudo que o servidor precisa para processá-la. Nada de sessão guardada no servidor entre chamadas.
- **Uso correto dos status codes**: o corpo da resposta não deveria ser a única fonte de verdade sobre sucesso ou falha.

## Regra prática

| Método | Uso | Idempotente? |
|---|---|---|
| GET | Ler um recurso | Sim |
| POST | Criar um recurso novo | Não |
| PUT | Substituir um recurso inteiro | Sim |
| PATCH | Atualizar parte de um recurso | Não (geralmente) |
| DELETE | Remover um recurso | Sim |

## Exemplo

**Ruim** (ação no verbo, ignora semântica HTTP):
```
POST /getPedidoPorId
POST /deletarPedido/123
```

**Bom** (recurso + método):
```
GET    /pedidos/123
DELETE /pedidos/123
```

## Quando aplicar

Sempre que a API for consumida por múltiplos times/parceiros — é isso que garante que qualquer desenvolvedor entenda o contrato sem ler documentação extra. Em integrações internas simples, o rigor pode ser relaxado, mas a convenção de nomes de recursos deve se manter.

## Referência cruzada

Ver `06-idempotencia.md` para o detalhe de por que PUT é idempotente e POST não.
