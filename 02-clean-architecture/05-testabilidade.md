# Testabilidade

## Por que Clean Architecture facilita testes

Se a regra de negócio (Entities + Use Cases) não depende de banco, framework ou rede, ela pode ser testada **sem subir infraestrutura nenhuma**. Isso muda o tipo de teste que você consegue escrever.

## Pirâmide de testes na prática

1. **Testes de unidade do domínio**: testam `Entities` e `Use Cases` isoladamente, usando implementações fake das interfaces (`ports`). Rápidos (milissegundos), sem banco, sem rede, rodam a cada `save` do editor.
2. **Testes de integração dos adapters**: testam se `RepositorioDePedidosPostgres` realmente conversa certo com o Postgres. Mais lentos, precisam de banco (real ou em container).
3. **Testes de contrato**: garantem que uma implementação de adapter realmente cumpre o que o Port promete — essencial quando há mais de uma implementação da mesma interface (ex: Postgres em produção, in-memory em teste).

## Exemplo: testando um Use Case sem banco

```
// Fake que implementa a interface RepositorioDePedidos
class RepositorioDePedidosFake implements RepositorioDePedidos {
  private pedidos = new Map()
  async buscarPorId(id) { return this.pedidos.get(id) }
  async salvar(pedido) { this.pedidos.set(pedido.id, pedido) }
}

test("aprova crédito quando saldo suficiente", async () => {
  const repositorio = new RepositorioDePedidosFake()
  await repositorio.salvar({ id: "1", saldo: 1000 })

  const useCase = new AprovarCredito(repositorio)
  const resultado = await useCase.executar({ pedidoId: "1", valor: 500 })

  expect(resultado.status).toBe("aprovado")
})
```

Note que nenhuma linha desse teste conhece Postgres, HTTP ou qualquer detalhe de infraestrutura — só a interface.

## O que isso NÃO significa

Não significa abandonar testes de integração — eles continuam necessários pra garantir que a implementação real (o adapter) funciona contra o banco/API real. A diferença é que agora você **escolhe** quando pagar o custo de subir infraestrutura, em vez de ser obrigado a isso pra testar toda regra de negócio.

## Sinal de alerta

Se testar uma regra de negócio simples exige subir um banco de dados, um container Docker ou mockar uma biblioteca externa inteira, é sinal de que a regra está acoplada a um detalhe de infraestrutura que deveria estar por trás de uma interface.
