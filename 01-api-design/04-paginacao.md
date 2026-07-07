# Paginação

## Por que é obrigatória

Qualquer endpoint que retorna uma coleção vai, cedo ou tarde, ter que lidar com milhares de registros. Sem paginação, a API quebra sob carga ou devolve payloads gigantes.

## Estratégias

### 1. Offset-based (mais simples)
```
GET /pedidos?limit=20&offset=40
```
- **Prós**: simples de implementar, permite "pular para a página X".
- **Contras**: performance degrada em tabelas grandes (o banco ainda percorre as linhas puladas); resultados podem duplicar ou sumir se dados forem inseridos/removidos entre requisições.

### 2. Cursor-based (recomendado para APIs de alto volume)
```
GET /pedidos?limit=20&cursor=eyJpZCI6MTIzfQ==
```
- O cursor é um ponteiro opaco (geralmente o ID do último item da página anterior, codificado).
- **Prós**: performance constante, resultados estáveis mesmo com dados mudando.
- **Contras**: não permite pular direto pra "página 5"; navegação é sequencial.

## Formato de resposta padrão

```json
{
  "data": [ { "id": 1, "...": "..." } ],
  "pagination": {
    "next_cursor": "eyJpZCI6MTQzfQ==",
    "has_more": true
  }
}
```

## Quando usar cada uma

- **Offset**: painéis administrativos internos, volumes pequenos/médios, onde "pular pra página X" é útil.
- **Cursor**: feeds, APIs públicas de parceiros, qualquer coleção que cresce continuamente (ex: transações, eventos, logs) — este é o padrão usado por Stripe, GitHub e a maioria das APIs de grande escala.

## Erro comum

Não colocar limite máximo de itens por página. Sempre defina um teto (ex: 100) mesmo que o cliente peça mais — protege o backend de sobrecarga.
