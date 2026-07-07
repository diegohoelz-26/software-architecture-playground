# Tratamento de Erros

## O problema

Cada equipe inventa seu próprio formato de erro, e o consumidor da API precisa tratar caso a caso. A solução é um **padrão único de erro**, independente do endpoint.

## Padrão recomendado: RFC 7807 (Problem Details for HTTP APIs)

```json
{
  "type": "https://suaempresa.com/erros/saldo-insuficiente",
  "title": "Saldo insuficiente",
  "status": 422,
  "detail": "A conta 123 possui saldo de R$ 50,00, insuficiente para a operação de R$ 200,00.",
  "instance": "/contas/123/saques",
  "trace_id": "a1b2c3d4-...."
}
```

- **type**: URI que identifica a categoria do erro (pode até ser uma página de documentação).
- **title**: resumo curto e estável (não muda entre ocorrências do mesmo erro).
- **status**: mesmo código do HTTP status.
- **detail**: mensagem específica dessa ocorrência.
- **instance**: qual recurso/endpoint gerou o erro.
- **trace_id**: essencial para observabilidade — permite achar o erro nos logs/traces.

## Mapeamento de status codes

| Código | Significado | Quando usar |
|---|---|---|
| 400 | Bad Request | Payload mal formado, campo obrigatório ausente |
| 401 | Unauthorized | Token ausente ou inválido |
| 403 | Forbidden | Token válido, mas sem permissão pro recurso |
| 404 | Not Found | Recurso não existe |
| 409 | Conflict | Conflito de estado (ex: recurso já existe, versão desatualizada) |
| 422 | Unprocessable Entity | Regra de negócio violada (payload é válido, mas a operação não pode ocorrer) |
| 429 | Too Many Requests | Rate limit estourado |
| 500 | Internal Server Error | Erro não tratado do lado do servidor |

## Erro comum

Retornar `200 OK` com um campo `"success": false` no corpo. Isso quebra qualquer client HTTP padrão, cache, e monitoramento baseado em status code. O status HTTP **é** a fonte de verdade.

## Conexão com Anti-Corruption Layer

Se sua API consome parceiros externos (ex: integrações bancárias), vale a pena normalizar os erros deles no seu próprio formato antes de repassar ao seu consumidor — isso evita vazar o formato de erro do parceiro para dentro do seu domínio.
