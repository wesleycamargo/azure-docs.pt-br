<properties
   pageTitle="Integrando aplicativos ao Active Directory do Azure | Microsoft Azure"
	description="Detalhes sobre como adicionar, atualizar ou remover um aplicativo no AD do Azure (Active Directory do Azure)."
	services="active-directory"
	documentationCenter=""
	authors="msmbaldwin"
	manager="mbaldwin"
	editor="mbaldwin"/>
<tags
   ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="08/25/2015"
	ms.author="mbaldwin"/>

# Integrando aplicativos ao Active Directory do Azure
Os desenvolvedores corporativos e provedores de SaaS (software como serviço) podem desenvolver serviços de nuvem comerciais ou aplicativos de linha de negócios que podem ser integrados ao AD do Azure (Active Directory do Azure) para fornecer conexão segura e autorização para os respectivos serviços. Para integrar um aplicativo ou serviço ao AD do Azure, um desenvolvedor deve primeiramente registrar os detalhes sobre o aplicativo no AD do Azure usando o Portal de Gerenciamento do Azure.

Este artigo mostra como adicionar, atualizar ou remover um aplicativo no AD do Azure. Você aprenderá sobre os diferentes tipos de aplicativo que podem ser integrados ao AD do Azure, como configurar aplicativos para acessar outros recursos, como APIs Web, e muito mais.

Para obter mais informações sobre as propriedades do aplicativo, consulte [Objetos de aplicativo e de entidade de serviço](active-directory-application-objects.md); para saber mais sobre as diretrizes de identidade visual que você deve usar ao desenvolver aplicativos com o Active Directory do Azure, consulte [Diretrizes de identidade visual para aplicativos integrados](active-directory-branding-guidelines.md).

## Adicionando um aplicativo

Para que você possa usar os recursos do AD do Azure em qualquer aplicativo desejado, é preciso primeiro registrar o aplicativo em um diretório. Esse processo de registro envolve fornecer ao AD do Azure detalhes sobre seu aplicativo, como a URL em que ele está localizado, a URL à qual enviar respostas depois que um usuário é autenticado, o URI que identifica o aplicativo, etc.

Se estiver criando um aplicativo Web que precise apenas de logon para usuários no AD do Azure, você pode simplesmente seguir as instruções abaixo. Se o aplicativo precisar de acesso a uma API Web, isso significa que você está criando um aplicativo nativo que precisa acessar uma API Web, ou se deseja transformar o aplicativo em multilocatário, você precisará ler a seção [Atualizando um aplicativo](#updating-an-application) para continuar configurando o aplicativo.

Se quer disponibilizar o aplicativo para outras organizações, você também precisará habilitar o acesso externo assim que o aplicativo for adicionado.

### Para registrar um aplicativo no Portal de Gerenciamento do Azure

1. Entre no Portal de Gerenciamento do Azure.

1. Clique no ícone do Active Directory no menu esquerdo e clique no diretório desejado.

1. No menu superior, clique em Aplicativos. Se nenhum aplicativo tiver sido adicionado ao diretório, essa página mostrará apenas o link Adicionar um Aplicativo. Clique no link ou, se preferir, você pode clicar no botão Adicionar, na barra de comandos.

1. Na página O que você deseja fazer, clique no link Adicionar um aplicativo que minha organização esteja desenvolvendo.

1. Na página Conte-nos sobre o seu aplicativo, você deve especificar um nome para o aplicativo, bem como indicar o tipo de aplicativo que está registrando no AD do Azure. Você pode escolher um aplicativo Web e/ou API Web (padrão), ou aplicativo cliente nativo, que representa um aplicativo que está instalado em um dispositivo como telefone ou computador. Depois que fizer isso, clique no ícone de seta no canto inferior direito da página.

1. Na página Propriedades do aplicativo, forneça a URL de Logon e o URI da ID do Aplicativo do aplicativo Web (ou apenas o URI de Redirecionamento para um aplicativo cliente nativo), em seguida, clique na caixa de seleção no canto inferior direito da página.

1. O aplicativo foi adicionado e você será levado para a página Início Rápido do seu aplicativo. Dependendo do tipo de aplicativo, Web ou nativo, você verá diferentes opções sobre como adicionar mais recursos ao aplicativo. Depois que o aplicativo tiver sido adicionado, você poderá começar a atualizá-lo para permitir que os usuários se conectem, acessem APIs Web em outros aplicativos, ou a configurar o aplicativo multilocatário (o que permite que outras organizações acessem o aplicativo).

>[AZURE.NOTE]Por padrão, o registro de aplicativo recentemente criado é configurado para permitir que os usuários do seu diretório entrem no aplicativo.

## Atualizando um aplicativo

Depois que o aplicativo tiver sido registrado no AD do Azure, talvez ele tenha que ser atualizado para fornecer acesso a APIs Web, disponibilizado para outras organizações e muito mais. Esta seção descreve como realizar outras configurações no aplicativo. Para obter mais informações sobre como funciona a autenticação no AD do Azure, consulte [Cenários de autenticação do AD do Azure.](active-directory-authentication-scenarios.md).

### Visão geral da estrutura de consentimento

A nova estrutura de consentimento do AD do Azure facilita o desenvolvimento de aplicativos Web e nativos que precisam acessar APIs Web protegidas pelo AD do Azure. Essas APIs Web incluem a Graph API, o Office 365 e outros serviços Microsoft, além das suas próprias APIs Web. A estrutura se baseia em um usuário ou administrador que dá consentimento a um aplicativo para que ele seja registrado no diretório, o que pode envolver acesso aos dados do diretório. Por exemplo, se um aplicativo Web precisar chamar a API Web do Office 365 para ler as informações de calendário sobre o usuário, esse usuário precisará dar consentimento ao aplicativo. Depois que o consentimento for dado, o aplicativo Web poderá chamar a API Web do Office 365 em nome do usuário e usar as informações de calendário conforme a necessidade.

A estrutura de consentimento foi criada com base no OAuth 2.0 e seus vários fluxos, como concessão de código de autorização e concessão de credenciais de cliente, usando clientes públicos ou confidenciais. Com o OAuth 2.0, o AD do Azure permite criar muitos tipos diferentes de aplicativo cliente, como em um telefone, tablet, servidor, ou um aplicativo Web, assim como obter acesso aos recursos necessários.

Para obter informações detalhadas sobre a estrutura de consentimento, consulte [OAuth 2.0 no AD do Azure](https://msdn.microsoft.com/library/azure/dn645545.aspx), [Cenários de autenticação do AD do Azure](active-directory-authentication-scenarios.md) e o tópico [Autenticação e autorização usando a estrutura de consentimento comum](https://msdn.microsoft.com/library/office/dn605895(v=office.15).aspx) do Office 365.

#### Exemplo da experiência de consentimento

As etapas a seguir mostrarão como funciona a experiência de consentimento para o desenvolvedor e o usuário do aplicativo.

1. Na página de configuração do aplicativo no Portal de Gerenciamento do Azure, defina as permissões que o aplicativo exige usando os menus suspensos no controle Permissões para outros aplicativos.

    ![Permissões para outros aplicativos](./media/active-directory-integrating-applications/permissions.png)

1. Pense na possibilidade de que as permissões do aplicativo tenham sido atualizadas, de que o aplicativo esteja em execução e de que um usuário esteja prestes a usá-lo pela primeira vez. Se o aplicativo ainda não tiver adquirido um token de atualização ou acesso, ele precisará ir até o ponto de extremidade de autorização do AD do Azure para obter um código de autorização que possa ser usado para adquirir um novo token de atualização e acesso.

1. Se o usuário ainda não estiver autenticado, será solicitado que ele entre no AD do Azure.

    ![Conexão do usuário ou administrador no AD do Azure](./media/active-directory-integrating-applications/useradminsignin.png)

1. Depois que o usuário tiver se conectado, o AD do Azure determinará se o usuário precisará ver uma página de consentimento. Essa decisão depende do fato de o usuário (ou do administrador da organização) já ter dado o consentimento de aplicativo. Se o consentimento ainda não tiver sido dado, o AD do Azure solicitará o consentimento ao usuário e exibirá as permissões necessárias que ele precisa para funcionar. O conjunto de permissões que é exibido na caixa de diálogo de consentimento é o mesmo que foi selecionado no controle Permissões para outros aplicativos no Portal de Gerenciamento do Azure.

    ![Experiência de consentimento do usuário](./media/active-directory-integrating-applications/userconsent.png)

1. Depois que o usuário dá consentimento, um código de autorização é retornado ao aplicativo, que pode ser resgatado para aquisição de um token de acesso e de atualização. Para saber mais sobre esse fluxo, consulte a [seção Aplicativo Web para API Web](active-directory-authentication-scenarios.md#web-application-to-web-api) em [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md).

### Acessando APIs Web em outros aplicativos

Usando a estrutura de consentimento descrita acima, você pode configurar o aplicativo de modo a exigir permissões para acessar dados expostos pelas APIs Web em seu diretório. Por padrão, todos os aplicativos podem escolher permissões no Active Directory do Azure (Graph API) e na API de Gerenciamento de Serviços do Azure, com a permissão "Permitir logon e leitura do perfil do usuário" do AD do Azure já selecionada por padrão. Se o diretório também tiver uma assinatura do Office 365, as APIs Web e permissões para o SharePoint e o Exchange Online também estarão disponíveis para seleção. Você pode selecionar entre dois tipos de permissão nos menus suspensos ao lado da API Web desejada:

- Permissões de Aplicativo: o aplicativo precisa acessar a API Web diretamente por si só (sem contexto de usuário). Esse tipo de permissão requer o consentimento do administrador e também não está disponível para aplicativos cliente nativos.

- Permissões de Delegação: o aplicativo precisa acessar a API Web como o usuário conectado, mas com acesso limitado pela permissão selecionada. Esse tipo de permissão pode ser concedido por um usuário, a menos que a permissão esteja configurada para pedir consentimento do administrador.

#### Para adicionar acesso a APIs da Web a outros aplicativos

1. Entre no Portal de Gerenciamento do Azure.

1. Clique no ícone do Active Directory no menu esquerdo e clique no diretório desejado.

1. No menu superior, clique em Aplicativos e no aplicativo que deseja configurar. A página Início Rápido aparecerá com o logon único e outras informações de configuração.

1. Expanda Acessar APIs Web na seção Outros Aplicativos de Início Rápido e clique no link Configurá-la agora na seção Selecionar permissões. A página de propriedades do aplicativo será exibida.

1. Role para baixo até a seção Permissões para outros aplicativos. A primeira coluna permite selecionar dentre os aplicativos disponíveis no diretório que expõem uma API Web. Depois de selecionado, você pode selecionar as permissões de aplicativo e delegação que a API Web expõe.

1. Depois de selecionadas, clique no botão Salvar na barra de comandos.

>[AZURE.NOTE]Clicar no botão Salvar também define automaticamente as permissões para o aplicativo no seu diretório com base nas Permissões para outros aplicativos que você configurou. É possível ver essas permissões de aplicativo na guia Propriedades do aplicativo.

### Expondo APIs Web para outros aplicativos

Você pode desenvolver uma API Web e disponibilizá-la para outras organizações expondo os escopos de permissão para outros desenvolvedores de aplicativo. Uma API Web corretamente configurada é disponibilizada assim como outras APIs Web da Microsoft, incluindo a Graph API e as APIs do Office 365 Sua API Web é disponibilizada pela configuração de um manifesto de aplicativo, que é um arquivo JSON que representa a configuração de identidade do seu aplicativo. Você pode expor os escopos de permissão navegando até o aplicativo no Portal de Gerenciamento do Azure e clicando no botão Manifesto do Aplicativo na barra de comandos.

#### Para expor uma API da Web para outros aplicativos

1. Entre no Portal de Gerenciamento do Azure.

1. Clique no ícone do Active Directory no menu esquerdo e clique no diretório desejado.

1. No menu superior, clique em Aplicativos e no aplicativo que deseja configurar. A página Início Rápido aparecerá com o logon único e outras informações de configuração.

1. Clique no botão Gerenciar manifesto, na barra de comandos, e selecione Baixar manifesto.

1. Abra o arquivo de manifesto do aplicativo JSON e substitua o nó "oauth2Permissions" pelo trecho JSON a seguir. Esse trecho é um exemplo de como expor um escopo de permissão conhecido como uma representação de usuário. Certifique-se de alterar o texto e os valores pelos do seu próprio aplicativo:

		"oauth2Permissions": [
		{
			"adminConsentDescription": "Allow the application full access to the Todo List service on behalf of the signed-in 	user",
			"adminConsentDisplayName": "Have full access to the Todo List service",
			"id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
			"isEnabled": true,
			“origin”: “Application”
			"type": "User",
			"userConsentDescription": "Allow the application full access to the todo service on your behalf",
			"userConsentDisplayName": "Have full access to the todo service",
			"value": "user_impersonation"
			}
		],

    O valor da id deve ser um novo GUID gerado que você cria usando uma ferramenta de geração de GUID ou de modo programático. Ele representa um identificador exclusivo para a permissão que é exposto pela API Web. Depois que o cliente estiver configurado corretamente para solicitar acesso à sua API Web e chamar a API Web, ele apresentará um token JWT do OAuth 2.0 que tem a declaração de escopo (scp) definida com o valor acima que, nesse caso, é user\_impersonation.

	>[AZURE.NOTE]É possível expor escopos de permissão adicionais posteriormente conforme a necessidade. Lembre-se de que a API Web pode expor várias permissões associadas a uma variedade de funções diferentes. Agora, você pode controlar o acesso à API Web usando a declaração de escopo (scp) no token JWT do OAuth 2.0 recebido.

1. Salve o arquivo JSON atualizado e carregue-o clicando no botão Gerenciar manifesto, na barra de comandos, selecionando Carregar manifesto, navegando até o arquivo de manifesto atualizado e selecionando-o. Depois de carregada, a API Web estará configurada para ser usada por outros aplicativos do seu diretório.

#### Para verificar se a API da Web é exposta a outros aplicativos em seu diretório

1. No menu superior, clique em Aplicativos, selecione o aplicativo para o qual deseja configurar o acesso à API Web e clique em Configurar.

1. Role para baixo até a seção Permissões para outros aplicativos. Clique no menu suspenso Selecionar aplicativo e você poderá selecionar a API Web para a qual acabou de expor uma permissão. No menu suspenso Permissões de Delegação, selecione a nova permissão.

![Permissões da lista de tarefas são mostradas](./media/active-directory-integrating-applications/listpermissions.png)

#### Esquema AppPermissions para o arquivo JSON do Manifesto do Aplicativo

A tabela a seguir lista os possíveis valores para a parte oauth2Permissions do arquivo JSON do Manifesto do Aplicativo.

|Elemento|Descrição|
|---|---|
|adminConsentDescription|A descrição de ajuda exibida quando o ponteiro do mouse é passado sobre a caixa de diálogo de consentimento do administrador e na página de propriedades do aplicativo com consentimento.|
|adminConsentDisplayName|O nome amigável para a permissão mostrada nos menus suspensos do escopo de permissão de delegação e/ou aplicativo, e para administradores durante o consentimento, e na página de propriedades do aplicativo.|
|ID|Representa um identificador interno exclusivo para o escopo de permissão. Ele deve ser exclusivo entre todas as permissões para o aplicativo, bem como deve ser um GUID.|
|isEnabled|Ao criar ou atualizar uma permissão OAuth2, sempre defina esse elemento como true. Se quiser excluir uma permissão, primeiramente você precisará definir esse valor para false e carregar o manifesto. Em seguida, você pode remover a permissão em um carregamento de manifesto subsequente.|
|origin|Reservado para uso futuro. Sempre definido como "application".|
|type|Pode ser um dos seguintes valores: "User": pode ser consentido pelos usuários finais. "Admin": deve ser consentido por uma administrador da empresa|
|userConsentDescription|A descrição da ajuda exibida ao passar o mouse sobre a caixa de diálogo de consentimento do usuário.|
|userConsentDisplayName|O nome amigável para a permissão mostrada aos usuários finais durante o consentimento, bem como na página de propriedades do aplicativo no Painel de Acesso do Aplicativo.|
|valor|Esse valor será colocado na declaração scp do token de acesso do OAuth 2.0 se essa permissão específica tiver sido consentida pelo usuário. A API Web pode usar esse valor para focar o nível de acesso que o aplicativo tem ao representar o usuário. Ele não deve conter espaços em branco e deve ser exclusivo entre todas as permissões no aplicativo.|

### Acessando a Graph API

Esta seção descreve como atualizar o aplicativo para acessar a Graph API, que é chamada de "Active Directory do Azure" na lista de Permissões para outros aplicativos. Ela é disponibilizada por padrão a todos os aplicativos que são registrados no AD do Azure. Você tem as seguintes permissões na Graph API para selecionar:

|Nome da permissão|Descrição|Tipo|
|---|---|---|
|Permitir logon e leitura dos perfis dos usuários|Permita que os usuários entrem no aplicativo com as respectivas contas organizacionais e que o aplicativo leia os perfis dos usuários conectados, como endereço de email e informações de contato.|Somente permissão de delegação. Pode ser consentida pelos usuários.|
|Acessar diretório da sua organização|Permita que o aplicativo acesse o diretório da organização em nome dos usuários conectados.|Somente permissão de delegação. Pode ser consentida pelos usuários em um cliente nativo e somente por um administrador de aplicativos Web.|
|Ler dados do diretório|Permita que o aplicativo leia os dados no diretório da organização, como usuários, grupos e aplicativos.|Permissão de aplicativo e delegação. Deve ser consentida por um administrador.|
|Ler e gravar dados do diretório|Permita que o aplicativo leia e grave dados no diretório da organização, como usuários e grupos.|Permissão de aplicativo e delegação. Deve ser consentida por um administrador.|

Para usuários existentes do Portal de Gerenciamento do Azure, definir as permissões de aplicativo Ler dados do diretório e Ler e gravar dados do diretório por meio do controle Permissões para outros aplicativos é como usar o assistente anterior Gerenciar Acesso. Para ver os escopos de permissão expostos pelo Office 365, consulte o tópico [Autenticação e autorização usando a estrutura de consentimento comum](https://msdn.microsoft.com/office/office365/howto/common-app-authentication-tasks).

>[AZURE.NOTE]Devido a uma limitação atual, os aplicativos cliente nativos só poderão chamar a Graph API do AD do Azure se eles usarem a permissão "Acessar diretório da sua organização". Essa restrição não se aplica a aplicativos Web.

### Configurando aplicativos multilocatários

Ao adicionar um aplicativo ao AD do Azure, talvez você queira que ele seja acessado apenas por usuários da sua organização. Ou talvez você queira que o aplicativo seja acessado por usuários em organizações externas. Esses dois tipos de aplicativo são chamados de aplicativos de locatário único e multilocatários. Você pode modificar a configuração de um aplicativo de locatário único para torná-lo um aplicativo multilocatário, que é discutido nesta seção.

É importante observar as diferenças entre um aplicativo de locatário único e multilocatário. Um aplicativo de locatário único é destinado para uso em uma organização. Normalmente, trata-se de um aplicativo LoB (linha de negócios) escrito por um desenvolvedor corporativo. Um aplicativo de locatário único só precisa ser acessado por usuários em um diretório e, como resultado, ele só precisa ser provisionado em um diretório. Um aplicativo multilocatário é destinado para uso em muitas organizações. Geralmente, trata-se de um aplicativo SaaS (software como serviço) escrito por um ISV (fornecedor independente de software). Aplicativos multilocatários precisam ser provisionados em cada diretório em que serão usados, o que requer o consentimento do usuário ou administrador para registrá-los.

#### Permitindo que usuários externos concedam acesso

Se estiver escrevendo um aplicativo que queira disponibilizar para seus clientes ou parceiros fora da organização, você precisará atualizar a definição do aplicativo no Portal de Gerenciamento do Azure.

>[AZURE.NOTE]Ao habilitar o acesso externo, você deve garantir que o URI da ID do Aplicativo do seu aplicativo pertença a um domínio verificado. Além disso, a URL de Retorno deve começar com https://. Para saber mais, consulte [Objetos de aplicativo e objetos de entidade de serviço](active-directory-application-objects.md).

##### Para habilitar o acesso ao seu aplicativo para usuários externos

1. Entre no Portal de Gerenciamento do Azure.

1. Clique no ícone do Active Directory no menu esquerdo e clique no diretório desejado.

1. No menu superior, clique em Aplicativos e no aplicativo que deseja configurar. A página Início Rápido aparecerá com o logon único e outras informações de configuração.

1. Expanda a seção Configurar aplicativo multilocatário de Início Rápido e clique no link Configurá-lo agora na seção Habilitar Acesso. A página de propriedades do aplicativo será exibida.

1. Clique no botão Sim ao lado de O aplicativo é multilocatário e clique no botão Salvar na barra de comandos.

Depois de ter feito a alteração acima, os usuários e administradores em outras organizações poderão conceder ao aplicativo acesso aos respectivos diretórios e outros dados.

### Concedendo acesso usando a estrutura de consentimento

Para conceder acesso usando a estrutura de consentimento, o aplicativo cliente deve solicitar autorização usando o OAuth 2.0. Os [códigos de exemplo](https://github.com/AzureADSamples) estão disponíveis para mostrar como um aplicativo Web, um aplicativo nativo ou um aplicativo de servidor/daemon solicita códigos de autorização e tokens de acesso para chamar APIs Web.

Seu aplicativo Web pode proporcionar uma experiência de inscrição para usuários. Se você oferecer uma experiência de inscrição, é esperado que o usuário clique em um botão de inscrição (ou de entrada) que redirecionará o navegador para o ponto de extremidade de autorização OAuth2.0 do AD do Azure ou um ponto de extremidade userinfo do OpenID Connect. Esses pontos de extremidade permitem ao aplicativo obter informações sobre o novo usuário inspecionando o id\_token.

Como alternativa, o aplicativo Web também pode oferecer uma experiência que permita aos administradores "inscreverem-se em minha empresa". Essa experiência também redireciona o usuário para o ponto de extremidade de autorização OAuth 2.0. Nesse caso, você também pode passar um parâmetro prompt=admin\_consent para disparar a experiência de consentimento do administrador, onde o administrador dará consentimento em nome da respectiva organização. No consentimento bem-sucedido, a resposta conterá admin\_consent=true. Ao resgatar um token de acesso, você também receberá um id\_token que fornecerá informações sobre a organização e o administrador que se inscreveu no seu aplicativo.

#### Habilitando a concessão implícita do OAuth 2.0 para Aplicativos de Uma Página

Os Aplicativos de Uma Página (SPAs) normalmente são estruturados com um front-end pesado de JavaScript executado no navegador, que chama o back-end da API Web do aplicativo para executar sua lógica de negócios. Para SPAs hospedados no Azure AD, use concessão implícita do OAuth 2.0 para autenticar o usuário no Azure AD e obter um token que pode ser usado para proteger chamadas do cliente JavaScript do aplicativo para sua API Web de back-end. Depois que o usuário tiver dado consentimento, esse mesmo protocolo de autenticação poderá ser usado para obter tokens para proteger chamadas entre o cliente e outros recursos de API Web configurados para o aplicativo. Por padrão, a Concessão Implícita do OAuth 2.0 está desabilitada para aplicativos. Você pode habilitar a Concessão Implícita do OAuth 2.0 para seu aplicativo definindo o valor `oauth2AllowImplicitFlow`”` em seu manifesto de aplicativo, que é um arquivo JSON que representa a configuração da identidade do seu aplicativo.

##### Para habilitar a Concessão Implícita do OAuth 2.0

1. Entre no Portal de Gerenciamento do Azure.
1. Clique no ícone do **Active Directory** no menu esquerdo e clique no diretório desejado.
1. No menu superior, clique em **Aplicativos** e no aplicativo que deseja configurar. A página Início Rápido aparecerá com o logon único e outras informações de configuração.
1. Clique no botão **Gerenciar manifesto** na barra de comandos e selecione **Baixar manifesto**. Abra o arquivo de manifesto de aplicativo JSON e defina o valor de "oauth2AllowImplicitFlow" como "true". Por padrão, é "false".

       "oauth2AllowImplicitFlow": true,

1. Salve o arquivo JSON atualizado e carregue-o clicando no botão **Gerenciar manifesto** na barra de comandos, selecionando **Carregar manifesto**, navegando até o arquivo de manifesto atualizado e selecionando-o. Uma vez carregada, a API Web agora está configurada para usar a Concessão Implícita do OAuth 2.0 para autenticar usuários.


### Experiências herdadas de concessão de acesso

Esta seção descreve a experiência de consentimento herdada antes de 12 de março de 2014. Essa experiência ainda tem suporte e é descrita abaixo. Antes da nova funcionalidade, você podia conceder apenas as seguintes permissões:

- Logon de usuários da respectiva organização

- Logon de usuários e leitura dos dados de diretório (como apenas aplicativo) da respectiva organização

- Logon de usuários e leitura e gravação dos dados de diretório (como apenas aplicativo) da respectiva organização

Você pode seguir as etapas em [Desenvolvendo aplicativos Web multilocatários com o Azure AD](https://msdn.microsoft.com/library/azure/dn151789.aspx) para conceder acesso para novos aplicativos registrados no Azure AD. É importante observar que a nova estrutura de consentimento permite aplicativos muito mais poderosos e também permite que os usuários deem consentimento a esses aplicativos, e não apenas os administradores.

#### Criando o link que concede acesso a usuários externos (herdado)

Para que os usuários externos se inscrevam em seu aplicativo usando as respectivas contas organizacionais, você precisará atualizar o aplicativo para mostrar um botão que se vincule à página no AD do Azure e os permita conceder acesso. As diretrizes de identidade visual desse botão de inscrição são discutidas no tópico [Diretrizes de identidade visual para aplicativos integrados](active-directory-branding-guidelines.md). Depois que o usuário concede ou nega acesso, a página de concessão de acesso do AD do Azure redireciona o navegador de volta para seu aplicativo com uma resposta. Para saber mais sobre propriedades do aplicativo, consulte [Objetos do aplicativo e das entidades de serviço](active-directory-application-objects.md).

A página de concessão de acesso é criada pelo AD do Azure, e você pode encontrar um link para ela na página Configuração do seu aplicativo no Portal de Gerenciamento. Para acessar a página Configuração, clique no link Aplicativos, no menu superior do seu locatário do AD Azure, clique no aplicativo que deseja configurar e, em Configurar, no menu superior da página Início Rápido.

O link para seu aplicativo se parecerá com este: `http://account.activedirectory.windowsazure.com/Consent.aspx?ClientID=058eb9b2-4f49-4850-9b78-469e3176e247&RequestedPermissions=DirectoryReaders&ConsentReturnURL=https%3A%2F%2Fadatum.com%2FExpenseReport.aspx%3FContextId%3D123456`. A tabela a seguir descreve as partes do link:

|Parâmetro|Descrição|
|---|---|
|ClientId|Obrigatório. A ID do Cliente que você obteve como parte da adição do aplicativo.|
|RequestedPermissions|Opcional. Nível de acesso que o aplicativo está solicitando, que será exibido para o usuário que está concedendo acesso ao aplicativo. Se não for especificado, o nível de acesso solicitado será padronizado apenas para logon único. As outras opções são DirectoryReaders e DirectoryWriters. Consulte Níveis de Acesso do Aplicativo para obter mais detalhes sobre esses níveis de acesso.|
|ConsentReturnUrl|Opcional. A URL para a qual deseja que a resposta de concessão de acesso seja retornada. Esse valor deve ser codificado por URL e estar sob o mesmo domínio que a URL de Resposta configurada na definição do aplicativo. Se não for fornecido, a resposta da concessão de acesso será redirecionada para a sua URL de Resposta configurada.|

Especificar uma ConsentReturnUrl separada da URL de Resposta permitirá ao aplicativo implementar lógica separada que possa processar a resposta em uma URL diferente da URL de Resposta (que normalmente processa tokens SAML para logon). Também é possível especificar parâmetros adicionais na URL codificada ConsentReturnURL; eles serão passados de volta como parâmetros de cadeia de consulta para seu aplicativo no redirecionamento. Esse mecanismo pode ser usado para manter informações adicionais ou associar a solicitação do aplicativo para uma concessão de acesso à resposta do AD do Azure.

#### Conceder acesso à experiência do usuário e à resposta (herdado)

Quando um aplicativo redireciona para o link de concessão de acesso, as imagens a seguir demonstram qual será a experiência do usuário.

Se o usuário ainda não tiver se conectado, ele será solicitado a fazê-lo:

![Entrar no AAD](./media/active-directory-integrating-applications/signintoaad.png)

Depois que o usuário for autenticado, o AD do Azure o redirecionará para a página de concessão de acesso:

![Conceder Acesso](./media/active-directory-integrating-applications/grantaccess.png)

>[AZURE.NOTE]Somente o administrador da empresa da organização externa pode conceder acesso ao aplicativo. Se o usuário não for um administrador da empresa, ele terá a opção de enviar um email ao administrador de sua empresa para solicitar que o acesso seja concedido a esse aplicativo.

Depois que o cliente tiver recebido acesso para o aplicativo clicando em Conceder Acesso, ou se ele tiver negado o acesso clicando em Cancelar, o AD do Azure enviará uma resposta à ConsentReturnUrl ou à URL de Resposta configurada. Essa resposta contém os seguintes parâmetros:

|Parâmetro|Descrição|
|---|---|
|TenantId|A ID exclusiva da organização no AD do Azure que concedeu acesso para seu aplicativo. Esse parâmetro só poderá ser especificado se o cliente tiver concedido acesso.|
|Consentimento|O valor será definido para Concedido se acesso tiver sido concedido ao aplicativo ou Negado se a solicitação tiver sido rejeitada.|

Os parâmetros adicionais serão retornados ao aplicativo se eles tiverem sido especificados como parte da URL codificada ConsentReturnUrl. Veja a seguir um exemplo de resposta a uma solicitação de concessão de acesso que indica que o aplicativo foi autorizado e inclui uma ContextID que foi fornecida na solicitação de concessão de acesso: `https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Granted&TenantId=f03dcba3-d693-47ad-9983-011650e64134`.

>[AZURE.NOTE]A resposta de concessão de acesso não conterá um token de segurança para o usuário; o aplicativo deve conectar o usuário separadamente.

A seguir, um exemplo de resposta para uma solicitação de concessão de acesso que foi negada: `https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Denied`

#### Rolando chaves do aplicativo para acesso ininterrupto ao Graph API (herdado)

Durante o tempo de vida do seu aplicativo, talvez seja necessário alterar as chaves que você usa quando chama o AD do Azure para adquirir um token de acesso para chamar a Graph API. Em geral, a alteração dessas chaves acontece por dois motivos: substituição de emergência, no caso de comprometimento da chave, ou uma substituição quando a chave atual está prestes a expirar. O procedimento a seguir deve ser seguido para fornecer ao aplicativo acesso ininterrupto enquanto você atualiza as chaves (basicamente para o segundo caso).

1. No Portal de Gerenciamento do Azure, clique no locatário do diretório, clique em Aplicativos no menu superior e clique no aplicativo que deseja configurar. A página Início Rápido aparecerá com o logon único e outras informações de configuração.

1. Clique em Configurar, no menu superior, para ver uma lista de propriedades do aplicativo, e você verá uma lista de suas chaves.

1. Em Chaves, clique no menu suspenso Selecionar duração e escolha 1 ou 2 anos. Clique em Salvar na barra de comandos. Isso gera uma nova chave de senha para o aplicativo. Copie essa nova chave de senha. Nesse ponto, tanto a chave existente quanto a nova podem ser usadas pelo aplicativo para obter um token de acesso do AD do Azure.

1. Volte para o aplicativo e atualize a configuração para iniciar usando a nova chave de senha. Consulte [Usando o Graph API para consultar o Azure AD](https://msdn.microsoft.com/library/azure/dn151791.aspx) para obter um exemplo de onde essa atualização deverá acontecer.

1. Agora você deve implementar essa alteração no ambiente de produção — verificando-a primeiro em um nó de serviço, antes de implementá-la no restante.

1. Depois que a atualização é concluída na implantação de produção, você fica livre para voltar ao Portal de Gerenciamento do Azure e remover a chave antiga.

#### Alterando propriedades do aplicativo após a habilitação de acesso (herdado)

Depois de permitir que usuários externos acessem seu aplicativo, você ainda pode continuar fazendo alterações nas propriedades do aplicativo no Portal de Gerenciamento do Azure. No entanto, os clientes que já tinham acesso concedido ao aplicativo antes de você fazer as alterações não verão essas mudanças refletidas ao exibir detalhes sobre o aplicativo no Portal de Gerenciamento do Azure. Depois que o aplicativo é disponibilizado para os clientes, você precisa ter bastante cuidado ao fazer determinadas mudanças. Por exemplo, se você atualizar o URI da ID do Aplicativo, os clientes existentes com acesso concedido antes dessa mudança não poderão fazer logon no aplicativo usando as respectivas contas corporativas ou de estudante.

Se você fizer uma alteração em RequestedPermissions para solicitar um nível mais alto de acesso, os clientes existentes usando a funcionalidade de aplicativo que agora aproveitam novas chamadas à API usando esse nível de acesso elevado podem obter uma resposta de acesso negado da Graph API. O aplicativo deve tratar esses casos e pedir ao cliente para conceder acesso ao aplicativo com a solicitação para um nível de acesso elevado.

## Removendo um aplicativo

Esta seção descreve como remover um aplicativo de seu diretório para aplicativos de locatário único e multilocatário.

### Para remover um aplicativo de locatário único do diretório

1. Entre no Portal de Gerenciamento do Azure.

1. Clique no ícone do Active Directory no menu esquerdo e clique no diretório desejado.

1. No menu superior, clique em Aplicativos e no aplicativo que deseja configurar. A página Início Rápido aparecerá com o logon único e outras informações de configuração.

1. Clique no botão Excluir na barra de comandos.

1. Clique em Sim na mensagem de confirmação.

### Para remover um aplicativo multilocatário do diretório

1. Entre no Portal de Gerenciamento do Azure.

1. Clique no ícone do Active Directory no menu esquerdo e clique no diretório desejado.

1. No menu superior, clique em Aplicativos e no aplicativo que deseja configurar. A página Início Rápido aparecerá com o logon único e outras informações de configuração.

1. Na seção O aplicativo é multilocatário, clique em Não. Isso converte o aplicativo em locatário único, mas o aplicativo ainda permanecerá em uma organização que já tenha sido consentida para ele.

1. Clique no botão Excluir na barra de comandos.

1. Clique em Sim na mensagem de confirmação.

Para um administrador de empresa remover o acesso de um aplicativo ao seu diretório (depois ter dado consentimento), ele deverá ter uma assinatura do Azure para remover o acesso por meio do Portal de Gerenciamento do Azure. Como alternativa, o administrador de empresa pode usar os [Cmdlets do PowerShell do Azure AD](http://go.microsoft.com/fwlink/?LinkId=294151) para remover o acesso.

## Próximas etapas

- Consulte [Diretrizes de identidade visual para aplicativos integrados](active-directory-branding-guidelines.md)

- Saiba mais sobre [Objetos de aplicativo e de entidade de serviço](active-directory-application-objects.md)

- Visite o [Guia do desenvolvedor do Active Directory](active-directory-developer's guide.md)

<!---HONumber=September15_HO1-->