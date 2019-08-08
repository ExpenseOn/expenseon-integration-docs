# API Pendências

A API de pendências tem como objetivo disponibilizar ao sistema integrado ao ExpenseOn informações de pendências de um usuário específico.

Para executar a chamada, o sistema deve utilizar um usuário com perfil `Integrador` com a seguinte rota:


```HTTP
GET /api/integration/pendencies/{idType}/{id}
```
- O parâmetro `idType` definirá o tipo de id a ser buscado na base.
- O parâmetro `id` deve ser criptografado usando o [algoritmo MD5](https://en.wikipedia.org/wiki/MD5).

|Parâmetro|Tipo|Descrição|
|---|---|---|
|`idType`|`integer`|Campo a ser utilizado na pesquisa de pendências. Pode ser utilizado conforme a necessidade do sistema integrado.|
|`id`|`string`|ID do usuário referente à propriedade `idType`|

Onde o tipo de identificador `idType` poderá ser:

|Valor|Descrição|
|---|---|
|`1`|ID no sistema integrado ao ExpenseOn|
|`2`|E-mail|

Um exemplo de chamada usando o ID do sistema integrado:

|string|MD5|
|---|---|
|`COLAB001`|`e621b1ba4fe69a3ee9e8cc2bc36f98f8`|
|`colaborador@empresa.com`|`f67e5e515fc048b98baa3f9cb0a14ef6`|

```HTTP
GET /api/integration/pendencies/1/e621b1ba4fe69a3ee9e8cc2bc36f98f8
```

E um outro exemplo usando o e-mail cadastrado no ExpenseOn.

```HTTP
GET /api/integration/pendencies/2/f67e5e515fc048b98baa3f9cb0a14ef6
```

O sistema retornará um [objeto de resposta](./General.md#objeto-de-resposta) e o atributo `data` com a seguinte estrutura:

|Atributo|Tipo|Descrição|
|---|---|---|
|`ExpensesViolationCount`|[Pendency](#pendency)|Despesas com violações|
|`ReportsOpenCount`|[Pendency](#pendency)|Relatórios abertos e rejeitados|
|`AdvPaymentsOpenCount`|[Pendency](#pendency)|Adiantamentos abertos|
|`AdvPaymentsWaitingApprovalCount`|[Pendency](#pendency)|Adiantamentos aguardando aprovação|
|`ReportWaitingApprovalCount`|[Pendency](#pendency)|Relatórios aguardando aprovação|
|`ApprovalCount`|[Pendency](#pendency)|Documentos aguardando aprovação|
|`AdvPaymentsWaitingFinancialApprovalCount`|[Pendency](#pendency)|Adiantamentos aguardando contabilização|
|`ReportWaitingFinancialApprovalCount`|[Pendency](#pendency)|Relatórios aguardando contabilização|
|`FinancialApprovalCount`|[Pendency](#pendency)|Documentos aguardando contabilização|

E cada objeto de pendência terá a estrutura abaixo:

#### Pendency

|Atributo|Tipo|Descrição|
|---|---|---|
|`Value`|string|Quantidade de pendências|
|`Tooltip`|string|Descrição da pendência|


[Voltar ao índice](./README.md)
