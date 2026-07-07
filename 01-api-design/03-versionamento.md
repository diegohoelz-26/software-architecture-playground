# Versionamento de API

## Por que existe

Uma API evolui. Em algum momento você precisa mudar um contrato de forma que quebra quem já consome — versionamento é como você faz isso sem derrubar ninguém em produção.

## Estratégias

### 1. Versionamento na URL (mais comum)
```
GET /v1/pedidos/123
GET /v2/pedidos/123
```
- **Prós**: explícito, fácil de cachear, fácil de rotear no gateway/APIM.
- **Contras**: "polui" a URL; tecnicamente o mesmo recurso passa a ter duas URLs diferentes.

### 2. Versionamento por header
```
GET /pedidos/123
Accept-Version: 2
```
- **Prós**: URL permanece limpa, o recurso é "o mesmo" conceitualmente.
- **Contras**: menos visível, mais fácil o consumidor esquecer de enviar o header.

### 3. Versionamento por media type (content negotiation)
```
GET /pedidos/123
Accept: application/vnd.suaempresa.v2+json
```
- **Prós**: mais "puro" do ponto de vista REST.
- **Contras**: maior curva de aprendizado para os consumidores, mais raro no mercado.

## Recomendação prática

Para APIs expostas a parceiros externos: **versionamento na URL**. É o que a maioria do mercado espera e o que gateways como Azure APIM lidam melhor nativamente (roteamento por path).

## Regra de ouro

Nunca quebre um contrato de versão já publicada. Se precisa mudar algo incompatível, sobe a versão — não altera a v1 em produção.

## Depreciação

Toda versão antiga deve ter uma data de fim de vida comunicada com antecedência, geralmente via header de resposta:
```
Deprecation: true
Sunset: Sat, 31 Dec 2026 23:59:59 GMT
```
