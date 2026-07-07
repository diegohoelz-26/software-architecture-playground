# Autenticação e Autorização

## Diferença fundamental

- **Autenticação**: quem é você? (validação de identidade)
- **Autorização**: o que você pode fazer? (validação de permissão)

Uma API pode autenticar perfeitamente um usuário e ainda assim recusar a ação por falta de autorização (403).

## OAuth2 + OIDC — o padrão de mercado

- **OAuth2** resolve autorização: emite um **access token** que representa "essa aplicação, em nome desse usuário, pode fazer X".
- **OIDC (OpenID Connect)** é uma camada sobre o OAuth2 que resolve autenticação: emite um **ID token** (JWT) que prova quem é o usuário.

Na prática, a maioria das APIs corporativas usa o fluxo **Client Credentials** (machine-to-machine) ou **Authorization Code** (usuário via navegador) para obter o access token, e então:

```
GET /pedidos/123
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
```

## Validação do JWT na API

1. Verificar assinatura (contra a chave pública do Authorization Server / JWKS endpoint).
2. Verificar `exp` (expiração) e `iat` (emissão).
3. Verificar `aud` (audience — o token foi emitido pra essa API específica?).
4. Verificar `scope` ou `roles` (o que esse token autoriza?).

## Onde validar: Gateway vs. Aplicação

| Camada | Responsabilidade |
|---|---|
| API Gateway (ex: Azure APIM) | Valida assinatura, expiração, formato do token — rejeita requisições inválidas antes de chegar no backend |
| Aplicação | Valida regras de autorização de negócio (ex: "esse usuário pode ver esse pedido específico?") |

Essa separação evita que toda API precise reimplementar validação de JWT — o gateway centraliza a parte genérica, e o backend foca na regra de negócio específica.

## Scopes vs. Roles

- **Scopes**: o que a aplicação/client pode fazer (`pedidos:leitura`, `pedidos:escrita`).
- **Roles**: quem é o usuário dentro do seu domínio (`admin`, `atendente`, `cliente`).

Uma API bem desenhada frequentemente combina os dois: o scope define a permissão da aplicação, o role (dentro do token ou buscado à parte) define a permissão do usuário.

## Erro comum

Confundir autenticação com autorização e devolver `401` quando na verdade o token é válido mas a ação não é permitida — nesse caso o correto é `403`.
