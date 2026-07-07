# OpenAPI / Swagger

## O que é

**OpenAPI** é a especificação (um formato YAML/JSON padronizado) que descreve os endpoints, parâmetros, request/response schemas e autenticação de uma API. **Swagger** é o nome histórico da ferramenta (Swagger UI, Swagger Editor) que renderiza essa especificação de forma visual e interativa — hoje o termo "Swagger" é usado quase como sinônimo de "documentação interativa de API", mas o padrão em si se chama OpenAPI (versão atual: 3.1).

## Por que importa

- **Contrato único**: a especificação é a fonte de verdade do que a API faz — não a implementação, não um documento Word separado que desatualiza.
- **Contract-first development**: você desenha o contrato antes de escrever código, e times de frontend/parceiros podem começar a integrar usando mocks gerados a partir da spec, sem esperar o backend terminar.
- **Geração automática**: SDKs de cliente, documentação navegável, testes de contrato — tudo pode ser gerado a partir do arquivo OpenAPI.

## Estrutura básica de um arquivo

```yaml
openapi: 3.1.0
info:
  title: API de Pedidos
  version: "1.0"
paths:
  /pedidos/{id}:
    get:
      summary: Busca um pedido por ID
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
      responses:
        "200":
          description: Pedido encontrado
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Pedido"
        "404":
          description: Pedido não encontrado
components:
  schemas:
    Pedido:
      type: object
      properties:
        id:
          type: string
        status:
          type: string
          enum: [pendente, confirmado, cancelado]
```

## Boas práticas

1. **Documente os erros também** — não só o `200`. Um consumidor precisa saber o formato de erro esperado (ver `05-tratamento-de-erros.md`).
2. **Use `$ref` para reaproveitar schemas** — evita duplicar a definição de `Pedido` em cada endpoint.
3. **Versione o arquivo junto com a API** — o arquivo OpenAPI da v1 não deveria mudar depois de publicado.
4. **Gateways como Azure APIM importam a spec OpenAPI diretamente** para gerar automaticamente o roteamento e a documentação exposta ao consumidor — reduz trabalho manual duplicado.

## Ferramentas úteis

- **Swagger Editor**: escrever/validar a spec visualmente.
- **Swagger UI / Redoc**: renderizar a spec como documentação navegável.
- **Postman**: importar a spec para gerar automaticamente uma coleção de testes.
