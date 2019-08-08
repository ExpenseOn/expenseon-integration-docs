# Informações Gerais

As informações abaixo devem ser utilizadas e consideradas como preliminares para implementar integrações com o sistema ExpenseOn. 

- [Requisitos para Utilização](#requisitos-para-utilização)
- [REST](#rest)
- [Objeto de Resposta](#objeto-de-resposta)
- [JSON](#json)
- [Ambientes](#ambientes)
- [Regras Gerais](#regras-gerais)
- [Autenticação](#autenticação)
  - [Básica](#básica)

### Requisitos para Utilização

Para que exista uma melhor absorção do conhecimento dessa documentação, é necessário que o desenvolvedor tenha recebido ao menos uma explicação dos processos do ExpenseOn. Caso ainda não tenha participado de nenhuma demo da ferramenta, você pode solicitar entrando em [contato conosco por email](mailto:contato@expenseon.com).

### REST

A API foi implementada de forma que as chamadas sempre retornam uma resposta nos seguintes formatos:

|Código|Descrição| Explicação|
|---|---|---|
|200|Success|A chamada foi executada com sucesso.|
|400|Bad Request|Um erro inesperado aconteceu na API|
|401|Unauthorized|Usuário não autenticado ou sessão expirada|
|403|Forbidden|Usuário não possui acesso ao conteúdo solicitado|
|404|Not Found|Objeto solicitado não foi encontrado|
|50X|Server Error|Um erro inesperado aconteceu no servidor|

### Objeto de Resposta

O objeto abaixo é um objeto padrão onde todas as respostas serão retornadas. O que diferenciará em caso de sucesso e falha, além do padrão HTTP `2xx` para sucesso e `4xx` e `5xx` para falha, será descrito nos dois próximos itens.

|Atributo|Tipo|Descrição|
|---|---|---|
|`success`|boolean|Campo que confirma se há erros|
|`message`|string|Descreve uma mensagem de retorno|
|`data`|object|Objeto variável, normalmente utilizado para retornar objetos atualizados e/ou inseridos|
|`error_code`|string|Código de erro|
|`error_type`|string|Tipo de erro|
|`error_description`|string|Descrição do erro|
|`more_info`|object|Atributo usado caso o usuário necessite de mais informações para apurar um problema.|

#### Sucesso

Em caso de sucesso (`HTTP 200`), o [objeto de resposta](#objeto-de-resposta) terá os seguintes campos afetados:

|Atributo|Valor|
|---|---|
|`success`|`true`|

O campo `data: object` terá normalmente objetos definidos em cada método e é descrito em cada chamada durante esse documento.

#### Erro

Em caso de erro (HTTP `4xx`) o [objeto de resposta](#objeto-de-resposta) terá os seguintes campos afetados:

|Atributo|Valor|
|---|---|
|`success`|`false`|

O motivo de cada erro também será detalhado nos demais campos com o prefixo `error_` do [objeto de resposta](#objeto-de-resposta).

### JSON

Todas as chamadas na API devem ser feitas utilizando o [formato JSON](https://www.json.org). As respostas também são sempre retornadas nesse mesmo formato.
  
### Ambientes

A _API_ ExpenseOn está disponibilizada em 2 ambientes: **Produção** e **Qualidade**. Todas as implementações de parceiros e demais sistemas devem utilizar o ambiente de testes e depois de passar por nossa validação interna, passa a funcionar em ambiente de produção.
Para isso as chamadas oficiais devem ser feitas conforme a tabela abaixo:

|Ambiente|URI|
|---|---|
|Produção|`https://app.expenseon.com`|
|Qualidade|`https://qa.expenseon.com`|

Os usuários de acesso ao sistema são criados separadamente, portanto, qualquer sistema deve solicitar primeiramente o _login_ de Qualidade e após sua aprovação, nossa equipe disponibilizará uma outra conta em ambiente produção.

Quando citado um endpoint nessa documentação com notação como:

```HTTP
POST /api/integration/user
```
Trata-se então, na prática de:
```HTTP
POST https://qa.expenseon.com.br/api/integration/user
```

### Regras Gerais

#### Dados do tipo alphanumérico

Todos os dados inseridos ou comparados em campos do tipo `string`, levam em consideração caracteres maiúsculos e minúsculos, bem como acentuações.

O desenvolvedor deve sempre se atentar, por exemplo, quando o valor aceito pelo campo deve ser `Usuario`, o sistema não aceitará o valor `usuario` ou `Usuário` acarretando assim, erros na sincronização dos dados.

#### Dados do tipo Data e Hora

Os objetos que possuem dados do tipo datetime, terão o formato seguindo a norma [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) conforme exemplo abaixo:

|Tipo|Formato|
|---|---|
|`datetime`|`2018-08-28T21:54:17.908Z`|
|`date`|`2018-08-28`|

#### Dados numéricos

Todos os números serão formatados como `integer`, `decimal` ou `float`.

#### Dados monetários

Todos os dados referentes à moedas seguirão a norma [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217).

## Autenticação

O ExpenseOn pode ser utilizado através de um processo de autenticação criado internamente. Para obter um login para essa autenticação, [fale com nossa equipe](mailto:contato@expenseon.com) e ela irá auxiliar na obtenção do acesso.

### Básica

Com usuário e senha em mãos, a autenticação básica pode ser feita através da chamada do seguinte endpoint:

```HTTP
POST https://expenseon.com/api/login
```

O objeto de autenticação deve ser conforme o JSON abaixo:

```JSON
{
  "email": "seuemail@dominio.com",
  "senha": "suasenhasupersecretaesegura"
}
```

Em caso de sucesso no login, o objeto de retorno será um `Ticket` com diversas informações do usuário logado. O atributo `ticket_hash` é o que deve ser gravado e chamado nas requests seguintes. Ele pode ser encontrado no seguinte caminho:

```JSON
{
  ...
  "ticket_hash": "6378523e-dadb-427a-8605",
  ...
}
```

Após gerar o ticket de acesso, agora você está apto a utilizar a API da ExpenseOn. Vale lembrar que o ticket gerado perde sua validade de 20 minutos **em caso de inatividade**, ou seja, caso o ticket seja utilizado e esteja válido, ele se valida por mais 20 minutos. As chamadas devem ser feitas com o Header `Ticket` adicionado com o valor do ticket obtido no processo de autenticação. Veja o exemplo de chamada:

```HTTP
POST /api/usuario HTTP/1.1
Host: expenseon.com
Content-Type: application/json; charset=utf8
Content-Length: 123
Ticket: 6378523e-dadb-427a-8605
{
  ...
}
```

Caso o acesso aos endpoints sejam feitos sem o uso do `Ticket` ou com o mesmo expirado após 20 minutos de uso, o servidor enviará uma resposta com o código `401 Unauthorized`.

Será então necessário uma nova autenticação para continuar utilizando a API.

[Voltar ao Índice](README.md)