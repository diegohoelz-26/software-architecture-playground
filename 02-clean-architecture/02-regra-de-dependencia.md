# Regra de Dependência

## O princípio central

> Dependências de código-fonte só podem apontar para dentro, nunca para fora.

Nada na camada mais interna (Entities) pode saber que existe algo na camada mais externa (Frameworks & Drivers). O nome de uma classe, função ou variável declarada em um círculo externo nunca é mencionado no código de um círculo mais interno.

## Como isso é possível na prática: Inversão de Dependência

Se a camada de negócio precisa persistir dados, mas não pode depender do banco — quem resolve isso é o **Dependency Inversion Principle** (o "D" do SOLID):

1. A camada interna **define uma interface** (ex: `RepositorioDePedidos`), pensando no que ela precisa, não em como será implementado.
2. A camada externa **implementa essa interface** (ex: `RepositorioDePedidosPostgres`), sabendo dos detalhes de banco.
3. Em tempo de execução, a implementação concreta é injetada — mas em tempo de compilação/design, a dependência aponta pra dentro (a implementação depende da interface, não o contrário).

```
[Use Case] ---> depende de ---> [Interface RepositorioDePedidos]  (definida na camada interna)
                                        ^
                                        | implementa
                        [RepositorioDePedidosPostgres]  (camada externa)
```

## Exemplo de código (conceitual)

**Errado** — Use Case conhece o Postgres diretamente:
```
class AprovarCredito {
  executar(pedido) {
    const conexao = new PostgresConnection(...)
    conexao.query("UPDATE pedidos SET status = 'aprovado' ...")
  }
}
```

**Certo** — Use Case depende de uma abstração:
```
class AprovarCredito {
  constructor(repositorio: RepositorioDePedidos) { ... }
  executar(pedido) {
    this.repositorio.atualizarStatus(pedido.id, "aprovado")
  }
}
```

## Por que isso importa na prática

- Trocar Postgres por outro banco = escrever uma nova implementação da interface. Zero mudança na regra de negócio.
- Testar a regra de negócio = injetar uma implementação fake/in-memory da interface, sem precisar de banco real.
- Isso é exatamente o motivo pelo qual a camada de abstração de dados (ex: `src/lib/db/`) importa: ela é a fronteira que implementa a interface que o resto do sistema espera, isolando o banco específico das camadas de cima.

## Quando essa regra é violada silenciosamente

O erro mais comum: importar um ORM (ex: Entity Framework, Prisma) direto dentro de uma classe de domínio "porque é mais rápido agora". Funciona no curto prazo, mas cria acoplamento que só aparece caro quando você precisa migrar de banco ou testar isoladamente.
