## Integração SSO SAML com Active Directory do Azure

### Requisito Inicial: Copiar e salvar Metadados do SP
Nessa primeira parte o usuário deve acessar o módulo web com um usuário com perfil Administrador. O caminho deve ser acessado "Painel de Controle > Empresa > SSO". 

Copie o conteúdo do campo Metadata do SP e salve como um arquivo XML em seu disco rígido. Esse arquivo será utilizado nos próximos passos.

![Requisito Inicial: Copiar e salvar Metadados do SP](/imgs/sso/0.png?raw=true)

### Passo 1: Painel Azure
Acesse o Painel Azure da empresa e selecione o item do menu "Active Directory do Azure".

![Passo 1 - Painel Azure](/imgs/sso/1.png?raw=true)

### Passo 2: Active Directory do Azure
Acesse no menu o item "Aplicativos Empresariais" para visualizar a lista de aplicativos configurados.

![Passo 2 - Active Directory do Azure](/imgs/sso/2.png?raw=true)

### Passo 3: Aplicativos Empresariais
Clique em "+ Novo aplicativo" para visualizar a janela de edição de aplicatios empresariais.

![Passo 3 - Aplicativos Empresariais](/imgs/sso/3.png?raw=true)

### Passo 4: Novo Aplicativo
Clique em "+ Crie seu próprio aplicativo" para criar um aplicativo não listado na galeria.

![Passo 4 - Novo Aplicativo](/imgs/sso/4.png?raw=true)

### Passo 5: Novo Aplicativo
Digite "ExpenseOn" como nome do aplicativo e selecione a opção "Integre qualquer outro aplicativo que você não encontre na galeria". Clique em Criar.

![Passo 5 - Criar Aplicativo](/imgs/sso/5.png?raw=true)

## Passo 6: Aplicativo Criado
Após a criaçao do aplicativo, clique em "Configurar logon único".

![Passo 6 - Aplicativo Criado](/imgs/sso/6.png?raw=true)

### Passo 7: Selecionar SAML
Clique em SAML como opção de logon único da configuração.

![Passo 7 - Selecionar Logon ](/imgs/sso/7.png?raw=true)

### Passo 8: Configuração criada
A configuração então é criada e possui campos a serem preenchidos. Clique em "Carregar arquivo de metadados".

![Passo 8 - Logon SAML Criado](/imgs/sso/8.png?raw=true)

### Passo 9: Importar Metadados do SP
Selecione o arquivo de metadados criado no início do guia, "Requisito Inicial" e clique em "Adicionar".

![Passo 9 - Importar Metadados do SP](/imgs/sso/9.png?raw=true)

### Passo 10: Salvar configuração Básica
Ao finalizar a importação, a janela de configuração básica é mostrada e só é necessário clicar no botao "Salvar".

![Passo 10 - Salvar configuração Básica](/imgs/sso/10.png?raw=true)

### Passo 11: Editar atributo de Usuário
Na tela principal do aplicativo, na sessão "Atributos e Declarações do Usuário", clique em "Editar". Depois clique no primeiro atributo para editar o valor enviado.

![Passo 11 - Editar atributo de Usuário](/imgs/sso/11.png?raw=true)

### Passo 12: Usar registro de Funcionário
Caso seja utilizado o registro de funcionário como atributo de autenticação, utilize `user.employeeid`, caso seja o e-mail, utilize `user.mail`.

![Passo 12a - Usar Registro de Funcionário](/imgs/sso/12a.png?raw=true)

![Passo 12b - Usar e-mail](/imgs/sso/12b.png?raw=true)

### Passo 13: Acessar Metadados do IdP
Após a configuração, será disponibilizado o Certificado de Autenticação SAML na página principal do Aplicativo. Dessa forma, clique no link "URL de metadados de federação de aplicativos" e cole em uma nova aba do navegador para visualizar o Metadados do IdP.

![Passo 13a - Certificado SAML disponível](/imgs/sso/13a.png?raw=true)

![Passo 13b - Validar e salvar Metadados do IdP](/imgs/sso/13b.png?raw=true)


### Passo 14: Atribuir Usuários
Após a configuração da autenticação, é necessário atribuir usuários ou grupo de usuários à aplicação. Essa atribuição fica a critério da área de negócios da empresa. Basicamente são os usuários que acessarão a plataforma.

![Passo 14 - Atribuir Usuários](/imgs/sso/14.png?raw=true)

### Passo 15: Colar e Salvar Metadados do IdP
Agora que a plataforma está devidamente cadastrada no Active Directory do Azure, é necessário colar o Metadados do IdP na tela "Painel de Controle > SSO" no ExpenseOn. Ao salvar essa configuração e ativar o controle "SSO SAML 2.0", os próximos acessos serão feitos somente através do protocolo SAML.

![Passo 15 - Colar e Salvar Metadados do IdP](/imgs/sso/15.png?raw=true)

Caso existam dúvidas nesse processo, entre em contato com nossa equipe de suporte.