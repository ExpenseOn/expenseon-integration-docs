# Integração UBER Business

Essa documentação online tem como objetivo descrever o uso da API ExpenseOn para integrar nosso sistema com a API do UBER.

Para utilizar essas funções, é necessário entender o [processo de autenticação](General.md) para então consumir os dados nos serviços listados a seguir.

## Índice

- [Como funciona a integração](#como-funciona-a-integração)
- [Contas UBER Business](#contas-uber-business)
    - [Chaves pública e privada](#chaves-pública-e-privada)
- [Como funciona o serviço](#como-funciona-o-servico)
    - [Uber Daily](#uber-daily)
    - [Uber Trips](#uber-trips)
- [Referências](#referências)
- [Referência de Objetos](#referência-de-objetos)

## Como funciona a integração

A integração tem o objetivo de facilitar a prestação de contas dos serviços utilizados pelo Uber através de rotinas que consumam os recursos da API do Uber e assim sejam transformadas em despesas para manipulação no ExpenseOn.

Para que o serviço seja utilizado é necessário primeiro ativá-lo. Em seguida, deve-se cadastrar os dados da conta que será vinculada a conta Uber da empresa,lembrando que é possível realizar mais de um cadastro desde que respeite a granularidade por filial, ou seja, pode-se cadastrar uma conta Uber para a empresa matriz e outras contas para cada filial desejada. O cadastro deve ser realizado de acordo com a política da empresa.

Com a conta cadastrada, o sistema irá gerar uma chave pública de autenticação que combinada a chave privada manterão seguros os dados e o canal de comunicação entre o ExpenseOn e a API do Uber.

Por fim, através da rotina de importação, o sistema com base nos dados cadastrados e corretamente informados poderá acessar aos serviços utilizados pela empresa, importá-los e também gerar as respectivas despesas.

## Contas Uber Business

As contas Uber Business são cadastradas no ExpenseOn e responsáveis pelo armazenamento dos dados que, posteriormente, enviados para a API do Uber permitem a importação dos serviços utilizados.

### Para inserir ou atualizar as contas Uber no sistema.

```HTTP
POST /api/uberaccount
```

```HTTP
PUT /api/uberaccount
```

|Parâmetro|Tipo|Descrição|
|---|---|---|
|entry|[UberAccountRequest](#uberaccountrequest)|Objeto do tipo UberAccountRequest|

##### Objeto de resposta em caso de sucesso

|Atributo|Tipo|Descrição|
|---|---|---|
|response|[UberAccount](#uberaccount)|Objeto do tipo UberAccount|

### Para obter a listagem de todas as contas da empresa.

```HTTP
GET /api/uberaccount/list
```

##### Objeto de resposta em caso de sucesso

|Atributo|Tipo|Descrição|
|---|---|---|
|response|[UberAccount](#uberaccount)[]|Lista de objetos do tipo UberAccount|

### Para obter uma única conta.

```HTTP
GET /api/uberaccount/{id}
```

|Parâmetro|Tipo|Descrição|
|---|---|---|
|id|`int`|Id da conta Uber|

##### Objeto de resposta em caso de sucesso

|Atributo|Tipo|Descrição|
|---|---|---|
|response|[UberAccount](#uberaccount)|Objeto do tipo UberAccount|

### Para exclusão de uma conta.

```HTTP
DELETE /api/uberaccount/{id}
```

|Parâmetro|Tipo|Descrição|
|---|---|---|
|id|`int`|Id da conta Uber|

##### Objeto de resposta em caso de sucesso

|Atributo|Tipo|Descrição|
|---|---|---|
|response|boolean|Objeto do tipo boolean. True se conta foi excluída e False se conta não foi excluída|

## Chaves Pública e Privada

As chaves pública e privada são geradas na criação de uma nova conta. As chaves são criptografadas e para segurança só pode-se ter acesso a chave pública. É possível também atualizá-las se necessário.

### Para obter a chave pública da conta.

```HTTP
GET /api/uberaccount/key/{id}
```

|Parâmetro|Tipo|Descrição|
|---|---|---|
|id|`int`|Id da conta Uber|

##### Objeto de resposta em caso de sucesso

|Atributo|Tipo|Descrição|
|---|---|---|
|response|string|Objeto do tipo string. Chave pública descriptografada|

### Para atualizar chaves pública e privada.

```HTTP
PUT /api/uberaccount/key
```

|Parâmetro|Tipo|Descrição|
|---|---|---|
|entry|[UberAccountKeyRequest](#uberaccountkeyrequest)|Objeto do tipo UberAccountKeyRequest|

##### Objeto de resposta em caso de sucesso

|Atributo|Tipo|Descrição|
|---|---|---|
|response|[UberAccount](#uberaccount)|Objeto do tipo UberAccount|

## Como funciona o serviço

O serviço de das respectivas contas ativas e válidas cadastradas no ExpenseOn fará a leitura, importação e persistência dos dados disponíveis para cada empresa permitindo posteriormente a manutenção e visualização dos dados.

### Para execução do serviço.

```HTTP
POST /api/worker/ProcessMobility
```

##### Objeto de resposta em caso de sucesso

|Atributo|Tipo|Descrição|
|---|---|---|
|response|[SaveTripsResponse](#savetripsresponse)|Objeto do tipo SaveTripsResponse|

## Uber Daily

Os arquivos Uber Daily são responsáveis pela listagem dos serviços utilizaods pela empresa em conjunto com o Uber. Todas as informações necessárias para a integração com o ExpenseOn são lidas neste arquivo com os dados informados nas contas. A cada dia, sendo utilizado ou não serviços, um novo arquivo Daily é criado e possibilita a leitura e interpretação do ExpenseOn.

## Uber Trips

As Uber Trips são registros que singularmente representam o uso dos recursos do Uber pela empresa. Qualquer serviço disponível pelo Uber é considerado uma nova Uber Trip. Com base nesses dados que o ExpenseOn realiza a criação das despesas para prestação de contas.

## Referências

|Ambiente|URI|
|---|---|
|Uber para desenvolvedores|`https://developer.uber.com/`|
|Uber para empresas|`https://developer.uber.com/docs/businesses/introduction`|

## Referência de Objetos

### SaveTripsResponse

|Atributo|Tipo|Descrição|
|---|---|---|
|`ProviderName`|string|Nome do provedor|
|`Errors`|string[]|Lista de erros|
|`HasErrors`|boolean|Define se houve erros no serviço ou não|
|`SucceedCount`|int|Quantidade de ações com sucesso|

### UberAccount

|Atributo|Tipo|Descrição|
|---|---|---|
|`id_uber_account`|int|Id da conta Uber|
|`uber_account`|string|Usuário sFTP Uber Business|
|`id_company`|int|Id da empresa|
|`ref_level`|int|Nível de referência. Opções: 1 - Empresa, 2 - Filial|
|`ref_level_id`|int|Id da referência (Id da Empresa ou Id da Filial)|
|`user_reference_field`|int|Identificação do usuário. Opções: 0 - CPF, 1 - E-mail, 2 - Registro do Funcionário|
|`initial_date`|datetime|Início de coleta dos dados pela integração|
|`is_active`|boolean|Define se a conta está ativa ou inativa|
|`public_key`|string|Chave pública criptografada|
|`private_key`|string|Chave privada criptografada|
|`company`|Empresa|Empresa atribuída à conta|
|`subsidiary`|Filial|Filial atribuída à conta|

### UberAccountKeyRequest

|Atributo|Tipo|Descrição|
|---|---|---|
|`id_uber_account`|int|Id da conta Uber|

### UberAccountRequest

|Atributo|Tipo|Descrição|
|---|---|---|
|`id_uber_account`|int|Id da conta Uber (0 para método POST)|
|`uber_account`|string|Usuário sFTP Uber Business|
|`id_company`|int|Id da empresa|
|`id_subsidiary`|int|Id da filial (se houver granularidade)|
|`user_reference_field`|int|Identificação do usuário. Opções: 0 - CPF, 1 - E-mail, 2 - Registro do Funcionário|
|`initial_date`|datetime|Início de coleta dos dados pela integração|
|`is_active`|boolean|Define se a conta está ativa ou inativa|