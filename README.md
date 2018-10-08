# Documentação de Integração

Essa documentação online tem como objetivo descrever o uso da API ExpenseOn para Integrar nosso sistema com sistemas parceiros, bem como sistemas ERPs.

## Índice

- [Introdução](#introdução)
  - [Requisitos para Utilização](#requisitos-para-utilização)
  - [REST](#rest)
  - [Objeto de Resposta](#objeto-de-resposta)
  - [JSON](#json)
  - [Ambientes](#ambientes)
  - [Regras Gerais](#regras-gerais)
- [Autenticação](#autenticação)
  - [Básica](#básica)
- [Os dois macro processos](#os-dois-macro-processos)
- [Cadastros](#Cadastros)
  - [Estado das Integrações](#estado-das-integrações)
  - [Usuários](#usuários)
  - [Categorias e Contas Contábeis](#categorias-e-contas-contábeis)
  - [Centros de Custo](#centros-de-custo)
  - [Filiais](#filiais)
  - [Áreas](#áreas)
  - [Projetos](#projetos)
  - [Clientes e Contatos](#clientes-e-contatos)
- [Prestação de Contas](#Prestação-de-Contas)
  - [Adiantamento](#adiantamento)
    - [Obter Adiantamentos a pagar](#obter-adiantamentos-a-pagar)
    - [Atualizar status de adiantamento](#atualizar-status-de-adiantamento)
  - [Relatórios](#relatórios)
    - [Obter Relatórios a pagar](#obter-relatórios-a-pagar)
    - [Atualizar status de relatório](#atualizar-status-de-relatório)
- [Referência de Objetos](#referência-de-objetos)

## Introdução

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
|500|Server Error|Um erro inesperado aconteceu no servidor|

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
|Produção|`https://expenseon.com`|
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

#### Dados do tipo `string`

Todos os dados inseridos ou comparados em campos do tipo `string`, levam em consideração caracteres maiúsculos e minúsculos, bem como acentuações.

O desenvolvedor deve sempre se atentar, por exemplo, quando o valor aceito pelo campo deve ser `Usuario`, o sistema não aceitará o valor `usuario` ou `Usuário` acarretando assim, erros na sincronização dos dados.

#### Dados do tipo `datetime`

Os objetos que possuem dados do tipo date, terão o formato conforme exemplo abaixo:

`2018-08-28T21:54:17.908Z`

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

## Os Dois Macro Processos

Há dois macro processos envolvidos na integração do ExpenseOn com sistemas ERP.

O primeiro que tem o objetivo de, inicialmente facilitar a inserção dos cadastros no ExpenseOn, e em momentos seguintes, através de jobs , facilitar o sincronismo dos dados do ERP com a ferramenta. Dessa forma, a integração garante que os dados de domínio do ERP estarão sempre em dia com os utilizados no ExpenseOn.

O segundo processo tem como objetivo inicial a criação de objetos financeiros no ERP através da tradução dos elementos consultados no ExpenseOn. Em um momento seguinte, também é responsável por retornar à ferramenta o status de pagamento dos documentos financeiros criados no primeiro passo.

A seguir, vamos exemplificar os endpoints que são utilizados na execução de ambos os processos.

### Estado das Integrações

Essa é uma função chave para fazer as integrações funcionarem com melhor performance. O ERP deve utilizar essa função para verificar a data de última atualização de cada um das entidades de domínio.

Para exemplificar o uso desse endpoint, vamos utilizar a entidade [User](#user). 

Vamos então imaginar o caso de uso onde o ERP executará um _job_ para sincronizar os dados de usuário:

Para começar, o ERP faz uma requisição para consultar o estado da integração. Em caso de sucesso na requisição, o serviço irá retornar um [objeto de resposta](#objeto-de-resposta) com o campo `data` preenchido com um objeto do tipo [IntegrationState](#integrationstate). Tal chamada deve ser feita como descrito abaixo:

```HTTP
GET /api/integration/state
```

Com esse objeto, o ERP poderá fazer comparações com o campo `userLastUpdate` com os usuários do ERP e suas devidas datas de criação e atualização. O objetivo dessa comparação é selecionar somente os usuários novos e atualizados após a última atualização da base de dados do ExpenseOn. Dessa forma, o objeto JSON criado para enviar a listagem de usuários novos ou alterados será menor e mais performático.

É importante saber também que **os valores iniciais** de todos os atributos serão `0001-01-01 00:00:00` e serão atualizados conforme suas chamadas de sincronização são executadas.

A mesma lógica deve ser usada para sincronizar os demais dados de domínio da integração. Basta verificar os atributos que correspondem a cada objeto de domínio na definição do tipo [IntegrationState](#integrationstate).

### Usuários

Função utilizada para inserir ou atualizar usuários no sistema.

- Para que o registro seja atualizado, o campo `financeSystemId` será utilizado como referência.
- Caso o valor dos atributos `filial`, `area` ou `cargo` do usuário a cadastrar não existam no ExpenseOn, o sistema os cria automaticamente seguindo a descrição informada, seguindo as [regras gerais](#regras-gerais).
- Ao inserir um novo usuário no sistema, o mesmo receberá um e-mail com seu *login* e senha de acesso.

```HTTP
POST /api/integration/user
```

|Parâmetro|Tipo|Descrição|
|---|---|---|
|users|[User](#user)[]|Lista de objetos do tipo Usuário|

A chamada retorna no campo `data` a lista de usuários atualizados/inseridos.

### Categorias e Contas Contábeis

Função utilizada para inserir ou atualizar categorias no sistema.

- Para que o registro seja atualizado, o campo `categoryReferenceId` será utilizado como referência.
- O campo `parentCategoryName` deve ser preenchido com sua referência "pai" ou deixada com valor nulo.
- O campo `isMileage` deve ser usado para sinalizar qual categoria no ERP é utilizado para despesas do tipo Milhagem/Quilometragem. O ExpenseOn aceitará apenas uma categoria com essa informação. Caso seja informada outra categoria com esse atributo com o valor `true`, a categoria anterior será substituída. As despesas postadas anteriormente terão também suas referências atualizadas para o novo valor.

```HTTP
POST /api/integration/category
```

|Parâmetro|Tipo|Descrição|
|---|---|---|
|categories|[Category](#category)[]|Lista de objetos do tipo Categoria|

### Centros de Custo

Função utilizada para inserir ou atualizar centros de custo no sistema.

- Para que o registro seja atualizado, o campo `costCenterReferenceId` será utilizado como referência.

```HTTP
POST /api/integration/costcenter
```

|Parâmetro|Tipo|Descrição|
|---|---|---|
|costCenters|[CostCencer](#costcenter)[]|Lista de objetos do tipo Centro de Custo|

### Filiais

Função utilizada para inserir ou atualizar filiais no sistema.

- Para que o registro seja atualizado, o campo `subsidiaryReferenceId` será utilizado como referência.

```HTTP
POST /api/integration/subsidiary
```

|Parâmetro|Tipo|Descrição|
|---|---|---|
|subsidiaries|[Subsidiary](#subsidiary)[]|Lista de objetos do tipo Filial|

### Áreas

Função utilizada para inserir ou atualizar áreas no sistema.

- Para que o registro seja atualizado, o campo `areaReferenceId` será utilizado como referência.

```HTTP
POST /api/integration/area
```

|Parâmetro|Tipo|Descrição|
|---|---|---|
|areas|[Area](#area)[]|Lista de objetos do tipo Área|

### Projetos

Função utilizada para inserir ou atualizar projetos no sistema.

- Para que o registro seja atualizado, o campo `projectReferenceId` será utilizado como referência.

```HTTP
POST /api/integration/project
```

|Parâmetro|Tipo|Descrição|
|---|---|---|
|project|[Project](#project)[]|Lista de objetos do tipo Projeto|

### Clientes e Contatos

Função utilizada para inserir ou atualizar clientes e seus contatos no sistema.

- Para que o registro seja atualizado, o campo `clientReferenceId` será utilizado como referência.
- Os contatos sempre devem ser enviados, mesmo para atualização de cadastro. Caso não sejam enviados, os contatos serão apagados.

```HTTP
POST /api/integration/client
```

##### Objeto de Resposta em caso de sucesso

|Atributo|Tipo|Descrição|
|---|---|---|
|clients|[Client](#client)[]|Lista de objetos do tipo Cliente|

## Prestação de Contas

No processo de prestação de contras, o endpoint de *Adiantamentos* é responsável por listar os adiantamentos com o status `Approved`. Esses adiantamentos devem ser utilizados para criar objetos financeiros que serão pagos aos funcionários.

Já o endpoint de *Relatórios* é responsável por listar relatórios aprovados com despesas aprovadas e adiantamentos a serem compensados.

### Adiantamento

#### O processo de negócio

Para solicitar um adiantamento no Expenseon, o usuário cria e envia um documento para seu aprovador para que o mesmo seja aprovado. Após esse processo, o sistema financeiro pode iniciar o processo de pagamento.

Para fazer o pagamento desse adiantamento, o ERP deve selecionar os adiantamentos com o status `Approved` e deve criar documentos financeiros para efetuar posteriormente seu pagamento. Assim que os documentos financeiros referentes aos adiantamentos são pagos, o ERP sinaliza ao ExpenseOn os documentos de adiantamento que devem ter seus status modificados para `Payed` e também atualiza suas referências no ERP.

Após essa etapa de alteração de status, o ExpenseOn estará atualizado com as informações do sistema ERP e o usuário já poderá utilizar o valor pago.

Veja agora nos próximos passos como o processo pode ser facilmente executado através da API do ExpenseOn.

#### Obter Adiantamentos a pagar

```HTTP
GET /api/integration/advpayment
```

|Parâmetro|Tipo|Descrição|
|---|---|---|
|startDate|`datetime`|Data referência inicial|
|endDate|`datetime`|Data referência final|

##### Objeto de Resposta em caso de sucesso

|Atributo|Tipo|Descrição|
|---|---|---|
|advPayments|[AdvancedPayment](#advancedpayment)[]|Lista de objetos do tipo Adiantamento|

Os documentos listados no objeto acima, possuem o atributo `advPaymentStatusName` atribuidos com os status `Approved`.

Os objetos com o status `Approved` devem ser utilizados para criar documentos de _Contas a Pagar_ no sistema ERP.

#### Atualizar status de adiantamento

Essa chamada deve ser utilizada para atualizar o *Adiantamento* no ExpenseOn com os dados do documento financeiro e o status desejado:

- Na criação do documento *Contas a Pagar*, deve atualizar somente a referência do documento no ERP ao ExpenseOn.
- Na alteração do status do adiantamento para `Error` em caso de **falha** na criação dos documentos de _Contas a Pagar_ no sistema ERP.
- No pagamento do documento *Contas a Pagar*, deve atualizar somente o status para `Payed`.
- Na contabilização do Adiantamento no *Financeiro* do ERP. Sinalizando que o adiantamento foi compensando, então usando o status `Finished`.

```HTTP
POST /api/integration/advpayment
```

|Parâmetro|Tipo|Descrição|
|---|---|---|
|changes|[DocumentChange](#documentchange)[]|Lista de objetos de alteração de documentos|

O parâmetro `changes` na chamada acima deverá ter um array com os documentos que precisam ter seu stado moficiado conforme as possibilidades abaixo:

##### Em todas as situações

- O valor do atributo `documentId` o id do adiantamento no ExpenseOn.

##### Contas a pagar criado

- O valor do parâmetro `financeDocumentId` deve ser a referência do documento de **Contas a Pagar** no ERP.

##### Erro no processamento do ERP

- Deve-se preencher o atributo `status` com a string `Error` e o atributo `message` com o motivo do erro para futura depuração.

##### Contas a pagar pago

- Deve-se preencher o atributo `status` com a string `Payed`.

### Relatórios

O endpoint de relatórios funciona como se fosse um "extrato" dos relatórios submetidos pelos funcionários do sistema. Todos os relatórios terão suas despesas e adiantamentos listados e a lógica contábil de cada ERP deve ser aplicada conforme o processo da empresa dona do sistema.

#### Obter Relatórios a pagar

```HTTP
GET /api/integration/report
```

|Parâmetro|Tipo|Descrição|
|---|---|---|
|startDate|`datetime`|Data referência inicial|
|endDate|`datetime`|Data referência final|

##### Objeto de Resposta em caso de sucesso

|Atributo|Tipo|Descrição|
|---|---|---|
|reports|[Report](#report)[]|Lista de objetos do tipo Relatório|

Os documentos listados no objeto acima, possuem o atributo `reportStatusName` atribuidos com os status `Approved`.

Os objetos com o status `Approved` devem ser utilizados para criar documentos de _Contas a Pagar_ no sistema ERP através das despesas no atributo `expenses`.

#### Atualizar status de despesas em relatórios

Essa chamada deve ser utilizada para basicamente três ocasiões:

- Alteração do status de despesas para `Error` em caso de **falha** na criação dos documentos de _Contas a Pagar_ no sistema ERP.
- Alteração do status de despesas para `Payed` quando o documento de _Contas a Pagar_ foi pago no sistema ERP.

```HTTP
POST /api/integration/report
```

|Parâmetro|Tipo|Descrição|
|---|---|---|
|changes|[DocumentChange](#documentchange)[]|Lista de objetos de alteração de documentos|

A configuração do parâmetro `changes` na chamada acima deverá ser feita de acordo com cada uma das situações abaixo:

##### Em todas as situações

- O valor do parâmetro `documentId` deve ser o valor do atributo `id` no objeto [Expense](#expense) ou [AdvPayment](#AdvPayment) utilizado no processo.

##### Contas a pagar criado

- O valor do parâmetro `financeDocumentId` deve ser a referência do documento de **Contas a Pagar** no ERP.

##### Erro no processamento do ERP

- Deve-se preencher o atributo `status` com a string `Error` e o atributo `message` com o motivo do erro para futura depuração.

##### Contas a pagar Pago

- Deve-se preencher o atributo `status` com a string `Payed`.

## Referência de Objetos

### IntegrationState

|Atributo|Tipo|Descrição|
|---|---|---|
|`userLastUpdate`|datetime|Última atualização das entidades do tipo Usuário|
|`categoryLastUpdate`|datetime|Última atualização das entidades do tipo Categoria|
|`costCenterLastUpdate`|datetime|Última atualização das entidades do tipo Centro de Custo|
|`subsidiaryLastUpdate`|datetime|Última atualização das entidades do tipo Filial|
|`areaLastUpdate`|datetime|Última atualização das entidades do tipo Area|
|`projectLastUpdate`|datetime|Última atualização das entidades do tipo Projeto|
|`clientLastUpdate`|datetime|Última atualização das entidades do tipo Cliente|

### User

|Atributo|Tipo|Descrição|
|---|---|---|
|`financeSystemId`|string|Atributo usado para dar referência ao usuário em questão|
|`name`|string|Nome do usuário|
|`email`|string|Email utilizado para login e notificações|
|`profile`|string|Perfil do usuário, sendo os valores possíveis válidos: _Administrador_, _Financeiro_, _Aprovador_ e _Usuario_|
|`subsidiary`|string|Filial que o usuário pertence|
|`area`|string|Área que o usuário pertence|
|`jobPosition`|string|Cargo que o usuário exerce na empresa|
|`isActive`|boolean|Define se o usuário está ativo ou inativo|

### Category

|Atributo|Tipo|Descrição|
|---|---|---|
|`categoryReferenceId`|string|Atributo usado para dar referência à categoria em questão|
|`categoryName`|string|Nome da categoria|
|`parentCategoryName`|string|Nome da categoria superior|
|`glAccountReferenceId`|string|Referência da conta contábil|
|`glAccountName`|string|Nome da conta contábil|
|`isMileage`|boolean|Define se a categoria é de Milhagem/Quilometragem|
|`isActive`|boolean|Define se a categoria está ativa ou inativa|

### CostCenter

|Atributo|Tipo|Descrição|
|---|---|---|
|`costCenterReferenceId`|string|Atributo usado para dar referência ao centro de custo em questão|
|`costCenterName`|string|Nome do centro de custo|
|`isActive`|boolean|Define se o centro de custo está ativo ou inativo|

### Subsidiary

|Atributo|Tipo|Descrição|
|---|---|---|
|`subsidiaryReferenceId`|string|Atributo usado para dar referência à filial em questão|
|`subsidiaryName`|string|Atributo usado para dar referência à filial em questão|
|`isActive`|boolean|Define se a filial está ativa ou inativa|

### Area

|Atributo|Tipo|Descrição|
|---|---|---|
|`areaReferenceId`|string|Atributo usado para dar referência à área em questão|
|`areaName`|string|Atributo usado para dar referência à área em questão|
|`isActive`|boolean|Define se a área está ativa ou inativa|

### Project

|Atributo|Tipo|Descrição|
|---|---|---|
|`projectName`|string|Nome do projeto|
|`projectReferenceId`|string|Atributo usado para dar referência ao projeto em questão|
|`clientName`|string|Nome do cliente na qual esse projeto pertence|
|`costCenterReferenceId`|string|Centro de custo na qual o projeto se refere|
|`isActive`|boolean|Define se o projeto está ativo ou inativo|
|`users`|[User](#user)[]|Usuários atribuídos ao projeto|
|`categoryReferences`|string[]|Código de referência das categorias atreladas ao projeto|

### Client

|Atributo|Tipo|Descrição|
|---|---|---|
|`clientName`|string|Nome do cliente|
|`clientReferenceId`|string|Atributo usado para dar referência ao cliente em questão|
|`clientCurrencyCode`|string|Código da moeda utilizada pelo cliente. ex: `BRL`, `USD`, etc.|
|`isActive`|boolean|Define se o projeto está ativo ou inativo|
|`contacts`|[Contact](#contact)[]|Contatos atribuídos ao cliente|

### Contact

|Atributo|Tipo|Descrição|
|---|---|---|
|`contactName`|string|Nome do contato|
|`contactReferenceId`|string|Atributo usado para dar referência ao contato em questão|
|`contactEmail`|string|Email do contato|
|`contactPhone`|string|Telefone do contato|

### DocumentChange

|Atributo|Tipo|Descrição|
|---|---|---|
|`documentId`|integer|Identificação do documento no ExpenseOn|
|`financeDocumentId`|string|Atributo usado para dar referência ao documento em questão no contas a pagar no ERP|
|`date`|datetime|Data da alteração de status e/ou referência do documento|
|`status`|[DocumentPaymentStatus](#documentpaymentstatus)|Status do documento|
|`message`|string|Mensagem de resposta do ERP em casos de erro|

### DocumentPaymentStatus

|Valor|Enumeração|Descrição|
|---|---|---|
|`Approved`|1|Documento pronto para processar no ERP|
|`Error`|2|Erro na criação do documento de _Contas a Pagar_ no ERP|
|`Payed`|3|Documento financeiro foi pago com sucesso no _Contas a Pagar_ no ERP|
|`Finished`|4|Documento financeiro foi contabilizado com sucesso no módulo financeiro do ERP|

### Report

|Atributo|Tipo|Descrição|
|---|---|---|
|`id`|integer|Id do relatório|
|`reference`|string|Atributo usado para dar referência ao relatório em questão|
|`name`|string|Nome do relatório|
|`reportStatusId`|integer|Status do relatório|
|`reportStatusName`|string|Descrição do status do relatório|
|`startDate`|datetime|Data de início do relatório|
|`endDate`|datetime|Vencimento do relatório|
|`totalReimbursableExpense`|decimal|Montante total em despesas reembolsáveis|
|`totalNonReimbursableExpense`|decimal|Montante total em despesas não reembolsáveis|
|`totalAdvPayment`|decimal|Montante total em adiantamentos|
|`totalReimbursable`|decimal|Montante total reembolsável|
|`expenses`|[Expense](#expense)[]|Despesas atreladas ao relatório|
|`advPayments`|[AdvancedPayment](#advanced-payment)[]|Adiantamentos atrelados ao relatório|
|`comment`|string|Campo utilizado para inserir observações sobre o relatório|
|`creator`|[User](#user)[]|Usuário que criou o relatório|
|`approver`|[User](#user)[]|Usuário assinalado como aprovador do relatório|
|`creationDate`|datetima|Data de criação do relatório|
|`firstApproval`|datetime|Data da primeira aprovação do relatório|
|`lastApproval`|datetime|Data da última aprovação do relatório|
|`submittedDate`|datetime|Data do ultimo envio para aprovação|
|`reimbursedDate`|datetime|Data de reembolso do relatório|

### Expense

|Atributo|Tipo|Descrição|
|---|---|---|
|`id`|integer|Id da despesa|
|`reference`|integer|Referência da despesa|
|`categoryReferenceId`|string|Referência da categoria da despesa|
|`categoryDescription`|string|Nome da categoria da despesa|
|`creationDate`|datetime|Data de criação da despesa|
|`expenseDate`|datetime|Data de vencimento da despesa|
|`expenseStatus`|integer|Código do status da despesa|
|`expenseStatusName`|integer|Descrição do status da despesa|
|`local`|string|Local onde a despesa ocorreu|
|`receiptId`|string|Id do recibo da despesa|
|`currencyCode`|string|Código da moeda da despesa. Ex: `BRL`, `USD`, etc|
|`amount`|decimal|Valor da despesa|
|`reimbursable`|boolean|Atributo que define se a despesa é reembolsável|
|`billable`|boolean|Atributo que define se a despesa é cobrável do cliente|
|`clientReferenceId`|string|Atributo que atrela a despesa à um cliente específico|
|`projectReferenceId`|string|Atributo que atrela a despesa à um projeto específico|
|`exchangeRate`|decimal|Taxa de câmbio utilizada para despesas convertidas em moedas diferentes da padrão do sistema|
|`exchangeRateCurrencyCode`|string|Código da moeda a qual a despesa teve conversão|
|`costCenterReferenceId`|string|Atributo que atrela a despesa à um centro de custo específico|
|`comment`|string|Atributo utilizado para adicionar observações à despesa|
|`creator`|[User](#user)|Usuário criador da despesa|
|`submittedDate`|datetime|Data do envio para aprovação da despesa|
|`approvalDate`|datetime|Data da aprovação da despesa|
|`paymentDate`|datetime|Data de reembolso do adiantamento|

### AdvancedPayment

|Atributo|Tipo|Descrição|
|---|---|---|
|`id`|integer|Id do adiantamento|
|`reference`|string|Referência do adiantamento|
|`comment`|string|Atributo utilizado para adicionar observações ao adiantamento|
|`amount`|decimal|Valor do adiantamento|
|`currencyCode`|string|Código da moeda do adiantamento. Ex: `BRL`, `USD`, etc|
|`creationDate`|datetime|Data de criação do adiantamento|
|`advPaymentDate`|datetime|Data de vencimento do adiantamento|
|`advPaymentStatus`|integer|Código do status do adiantamento|
|`advPaymentStatusName`|integer|Descrição do status do adiantamento|
|`creator`|[User](#user)|Usuário criador do adiantamento|
|`approver`|[User](#user)|Usuário aprovador do adiantamento|
|`costCenterReferenceId`|string|Atributo que atrela o adiantamento à um centro de custo específico|
|`firstApproval`|datetime|Data da primeira aprovação do adiantamento|
|`lastApproval`|datetime|Data da última aprovação do adiantamento|
|`submittedDate`|datetime|Data do envio para aprovação|
|`paymentDate`|datetime|Data de reembolso do adiantamento|