# Idempotência

## Conceito

Uma operação é idempotente quando executá-la múltiplas vezes produz o mesmo resultado que executá-la uma vez. Isso importa porque em sistemas distribuídos, **timeouts acontecem, e o cliente não sabe se a requisição foi processada ou não** — ele só sabe que não recebeu resposta.

## Métodos e idempotência nativa

- `GET`, `PUT`, `DELETE` são idempotentes por definição do protocolo HTTP.
- `POST` **não é idempotente** por padrão — chamar duas vezes cria dois recursos.

## O problema real

```
Cliente → POST /pagamentos {"valor": 500} → timeout (sem resposta)
Cliente, sem saber se processou, tenta de novo → POST /pagamentos {"valor": 500}
```
Resultado sem proteção: **dois pagamentos de R$500 processados.**

## Solução: Idempotency Key

O cliente gera uma chave única por operação (geralmente um UUID) e envia num header:

```
POST /pagamentos
Idempotency-Key: 7b3e4f2a-9c1d-4e5f-8a2b-1c3d4e5f6a7b
{"valor": 500}
```

O servidor:
1. Recebe a chave, verifica se já processou essa chave antes.
2. Se sim, retorna a **mesma resposta** da primeira vez (sem reprocessar).
3. Se não, processa normalmente e guarda o resultado associado à chave (geralmente com um TTL de 24h).

## Onde isso aparece no dia a dia

Esse é exatamente o problema que webhooks de callback de pagamento resolvem (ex: recebimento duplicado de notificação Pix) — o mesmo princípio de idempotência se aplica tanto ao lado de quem envia quanto de quem recebe o callback.

## Regra prática

Qualquer endpoint `POST` que representa uma operação financeira, de criação de recurso crítico, ou que dispara efeito colateral irreversível (envio de email, débito, criação de pedido) **deveria** suportar Idempotency-Key.
