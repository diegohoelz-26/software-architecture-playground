# Convenções de URLs

## Conceito

A URL é a interface pública do seu domínio. Inconsistência aqui gera atrito imediato para quem consome.

## Regras

1. **Substantivos no plural**: `/clientes`, não `/cliente`.
2. **kebab-case** para múltiplas palavras: `/ordens-de-pagamento`, não `/ordensDePagamento` ou `/ordens_de_pagamento`.
3. **Hierarquia representa relação de posse (ownership)**, não qualquer relação:
   - `/clientes/123/pedidos` → pedidos QUE PERTENCEM ao cliente 123. Correto.
   - `/pedidos/clientes/123` → não faz sentido, pedido não "contém" cliente.
4. **Filtros, ordenação e busca vão na query string, não no path**:
   - Bom: `/pedidos?status=pendente&ordenar=data_criacao`
   - Ruim: `/pedidos/status/pendente/ordenar/data_criacao`
5. **Verbos só existem quando a ação não mapeia para CRUD** (ex: `/pedidos/123/cancelar` via POST é aceitável quando cancelar não é um simples PATCH de status — envolve regras de negócio, side effects, etc.)

## Exemplo comparativo

**Ruim:**
```
GET /getAllClientesAtivos
GET /cliente?id=123
POST /cliente/123/update
```

**Bom:**
```
GET  /clientes?status=ativo
GET  /clientes/123
PATCH /clientes/123
```

## Quando quebrar a regra

APIs de comando (CQRS, orientadas a eventos) frequentemente usam verbos de propósito porque a ação em si é o recurso (ex: `/pedidos/123/aprovar`). Nesse caso, documente a exceção — não deixe implícito.
