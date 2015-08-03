<properties 
	pageTitle="Implantar e configurar um aplicativo de API de conector SaaS" 
	description="Saiba como configurar um conector de SaaS que você instala em sua assinatura do Azure por meio do Azure Marketplace." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="tdykstra"/>

# Implantar e configurar um aplicativo de API de conector SaaS em um Serviço de Aplicativo do Azure

## Visão geral

Este tutorial mostra como instalar, configurar e testar um [conector de SaaS (Software como Serviço )](../app-service-logic-what-are-bizTalk-api-apps.md) no [Serviço de Aplicativo do Azure](/documentation/services/app-service/) para chamá-lo programaticamente, por exemplo, de um aplicativo móvel. Um conector de SaaS é um [aplicativo de API](app-service-api-apps-why-best-platform.md) que simplifica a interação com uma plataforma SaaS, como o Office 365, Salesforces, Facebook e Dropbox. Se, em vez de usar um conector predefinido, você quiser criar um aplicativo de API .NET personalizado, consulte [Conectar-se a uma plataforma SaaS a partir de um aplicativo de API ASP.NET](app-service-api-dotnet-connect-to-saas.md).

Por exemplo, para codificar solicitações de HTTP com a finalidade de ler e gravar arquivos em sua conta do Dropbox, o processo de autenticação para trabalhar diretamente com Dropbox é complicado. Um conector de Dropbox cuida da complexidade da autenticação para que você possa se concentrar em escrever seu código específico ao negócio.

> [AZURE.NOTE]Não é requerido seguir as instruções expostas aqui se você quiser usar um conector de SaaS em um aplicativo lógico. Consulte [Criar um novo aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md) para obter informações sobre como usar conectores de SaaS em aplicativos lógicos.
 
Este tutorial usa um conector de DropBox como um exemplo e orienta você durante as etapas a seguir:

* Instale o conector do Dropbox em um [grupo de recursos](../resource-group-overview.md) em sua assinatura do Azure. 
* Configure o conector do Dropbox para que ele possa se conectar ao serviço Dropbox. (Para concluir esta etapa será necessário uma conta do Dropbox.)
* Configure o grupo de recursos para que somente usuários autenticados possam acessar aplicativos de API incluídos no grupo de recursos.
* Teste para verificar se autenticação do de usuário e do Dropbox funciona.

Para obter mais informações sobre a autenticação no Serviço de Aplicativo, consulte [Autenticação para aplicativos de API e aplicativos móveis](../app-service/app-service-authentication-overview.md).

## Instalar o conector do Dropbox

1. Vá para a home page do [Portal de visualização do Azure] e clique em **Marketplace**.

	![Marketplace no portal de visualização do Azure](./media/app-service-api-connnect-your-app-to-saas-connector/marketplace.png)

2. Procure pelo Dropbox e clique no ícone **Conector do Dropbox**.

	![Clique em Conector do Dropbox](./media/app-service-api-connnect-your-app-to-saas-connector/searchdb.png)
 
3. Clique em **Criar**.

	![Clicar em Criar](./media/app-service-api-connnect-your-app-to-saas-connector/clickcreate.png)
 
5. Na folha **Conector do Dropbox**, em **Plano do Serviço de Aplicativo** clique em **Criar Novo**, e, na caixa **Criar novo plano de Serviço de Aplicativo** insira DropBoxPlan.

	Para obter mais informações sobre os planos do Serviço de Aplicativo, consulte [Visão geral dos planos de Serviço de Aplicativo do Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Em **Grupo de Recursos**, clique em **Criar Novo**, e, em seguida, na caixa **Criar Novo Grupo de Recursos** insira DropboxRG.

	Para obter mais informações sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../resource-group-overview.md).

7. Selecione a **Camada de Preços** gratuita. (Se você não a vir na lista, clique em **Exibir Tudo**. Depois de clicar em **F1 Gratuito**, clique no botão **Selecionar**.)

	Você pode usar uma camada de preços paga, mas não é necessário para este tutorial.
 
11. Escolha um **Local** perto de você.

9. <a id="gateway"></a>Mantenha o padrão "DropboxConnector" como o **Nome** do conector e clique **Criar**.

	![Clique em criar](./media/app-service-api-connnect-your-app-to-saas-connector/createdropbox.png)

	O Serviço de Aplicativo do Azure cria um grupo de recursos e, no grupo de recursos, cria um aplicativo de API conector do Dropbox e um aplicativo Web de *gateway*. A função do gateway é gerenciar o acesso a todos os aplicativos de API no grupo de recursos.

	Você pode verificar o andamento da criação do recurso clicando em **Notificações** na home page do portal de visualização do Azure.

3. Quando o Azure terminar de criar o conector, clique em **Procurar > Grupos de recursos > DropboxRG**.
 
	A folha **Grupo de Recursos** do DropboxRG mostra o conector e o gateway no grupo de recursos.

	![Diagrama do grupo de recursos](./media/app-service-api-connnect-your-app-to-saas-connector/rgdiagram.png)

## Configurar sua conta do Dropbox e o conector do Dropbox

Para habilitar o acesso da API à sua conta do Dropbox, você precisa criar um aplicativo Dropbox no site do desenvolvedor do Dropbox. Em seguida, copie a ID do cliente e os valores de segredo do cliente daquele aplicativo Dropbox para seu conector do Dropbox e defina o conector para aceitar somente solicitações autenticadas.

### <a id="createdbapp"></a>Criar um aplicativo do Dropbox

[AZURE.INCLUDE [app-service-api-create-dropbox-app](../../includes/app-service-api-create-dropbox-app.md)]

### <a id="copysettings"></a>Copiar as configurações de aplicativo do Dropbox no conector de Dropbox do Azure e vice-versa 

[AZURE.INCLUDE [app-service-api-exchange-dropbox-settings](../../includes/app-service-api-exchange-dropbox-settings.md)]

### Definir o conector do Dropbox para exigir acesso autenticado

Por padrão, o **Nível de acesso** do conector é definido como **Interno**, o que significa que só pode ser chamado por outros aplicativos de API e da Web no mesmo grupo de recursos. Mas o Dropbox permite que somente usuários autenticados acessem sua conta do Dropbox, assim, você precisará alterar a configuração do nível de acesso para exigir autenticação do usuário.

1. Volte para a folha **Configurações** e clique em **Configurações do aplicativo**.

2. Na folha **Configurações do aplicativo**, defina o **Nível de acesso** para **público (autenticado)**e, em seguida, clique em **Salvar**.
	
	![Definir como Público (autenticado)](./media/app-service-api-connnect-your-app-to-saas-connector/pubauth.png)

Você configurou o conector do Dropbox para que as chamadas de saída possam acessar sua conta do Dropbox e as chamadas de entrada devem ser de usuários autenticados. Na próxima seção, você especifica qual provedor de autenticação você quer usar para autenticar usuários.

## Configurar o gateway

Conforme explicado [anteriormente](#gateway), o gateway é um aplicativo Web especial que gerencia o acesso a todos os aplicativos de API em um grupo de recursos. Para configurar o gateway para autenticar usuários, você pode escolher um provedor de autenticação, como o Active Directory do Azure, Google, ou Twitter. Os usuários deverão, em seguida autenticar com o provedor escolhido antes que possam chamar com êxito o conector do Dropbox.

- Para realizar esta etapa, vá para a seção [Configurar o gateway](app-service-api-dotnet-add-authentication.md#configure-the-gateway) do tutorial [Proteger um aplicativo de API](app-service-api-dotnet-add-authentication.md) e siga as orientações lá para configurar o gateway em seu grupo de recursos DropboxRG.

## Testar para verificar usuário e autenticação do Dropbox

Depois de configurar o gateway em seu grupo de recursos DropboxRG para usar um provedor de autenticação, você pode testar seu conector de Dropbox.

Na maioria das vezes você usará um conector chamando-o do código; além disso, estamos escrevendo tutoriais que lhe mostrará como fazer isso. Mas, às vezes, você quer confirmar se o conector está funcionando antes de conectar outras partes de um aplicativo. Este tutorial mostra como usar um navegador e uma ferramenta de cliente REST simples para verificar se você pode interagir com o serviço Dropbox por meio do conector de Dropbox que você acabou de instalar e configurar.

As instruções a seguir mostram como executar essas etapas usando as ferramentas de desenvolvimento do navegador Chrome e a ferramenta de cliente Postman REST. Isso é apenas um exemplo, e você pode fazer os mesmos procedimentos com outros navegadores e ferramentas. "Cliente REST Avançado" é outro complemento do Chrome que você pode usar.

### Fazer logon como um usuário final

Siga as etapas a seguir em uma nova janela do navegador. Dependendo do provedor de autenticação que você estiver usando, perceberá que precisa usar uma janela particular ou incógnita.

2. Vá para a URL de logon para o gateway e o provedor de autenticação que você configurou. A URL segue este padrão: 

    	http://[gatewayurl]/login/[providername]

	Você pode obter a URL do gateway na folha **Gateway** no [Portal de visualização do Azure]. (Para obter a folha **Gateway**, clique em gateway no diagrama mostrado na folha **Grupo de recursos**.)

	![URL de gateway](./media/app-service-api-connnect-your-app-to-saas-connector/gatewayurl.png)

	O valor de [providername] é "facebook" para o Facebook, "twitter" para o Twitter, "aad" para o Active Directory do Azure, etc.

	Aqui está um exemplo de URL de logon para o Active Directory do Azure:

		https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/login/aad/

3. Insira suas credenciais quando o navegador exibir uma página de logon.
 
	Se você tiver configurado logon do Active Directory do Azure, faça logon como um dos usuários listados na guia **Usuários** para o aplicativo criado na guia do Active Directory do Azure no [Portal Azure], como admin@contoso.onmicrosoft.com.

	Quando o logon for bem-sucedido, você obterá uma página de "Logon concluído".

	![](./media/app-service-api-connnect-your-app-to-saas-connector/logindone.png)

### Fornecer a identidade do usuário para Dropbox

Para obter autorização do Dropbox para usar a API do Dropbox, você precisa fornecer a ele as credenciais do usuário. O Azure iniciará o processo para você, mas, para disparar o processo, você precisa acessar uma URL especial de gateway no navegador. Para obter essa URL você faz uma solicitação HTTP Post para o gateway.

A solicitação HTTP Post para o gateway deve incluir o token de autenticação que o Azure forneceu quando você fez o logon. Para solicitações do navegador, a inclusão do token é automático porque ele é armazenado em um cookie, mas para uma solicitação HTTP Post usando uma ferramenta de cliente REST você precisa obter o token do cookie e colocá-lo no cabeçalho da solicitação HTTP Post.

1. Na janela do navegador com a mensagem "Logon concluído", vá para ferramentas de desenvolvimento do navegador e localize o `x-zumo-auth` cookie. Mantenha essa janela aberta para que você possa copiar o valor do cookie na próxima etapa.
 
	Para obter o valor do cookie no Chrome, execute as seguintes etapas:

	- Pressione F12 para abrir as ferramentas de desenvolvedor.
	- Vá para a guia **Recursos**.
	- Localiza os cookies para o seu site de gateway e clique três vezes no **Valor** do `x-zumo-auth` cookie para selecionar tudo. (Certifique-se de obter todos os valores de cookie. Se você clicar duas vezes, poderá receber apenas a primeira parte deles.)  

	![Copiar token](./media/app-service-api-connnect-your-app-to-saas-connector/copytoken.png)

4. Em uma nova janela ou guia do navegador, crie e envie uma solicitação HTTP Post para o gateway, para solicitar uma URL de autorização. Inclua o `x-zumo-auth` token como um cabeçalho HTTP.

	A URL segue este padrão:

		[gatewayurl]/api/consent/list?api-version=2015-01-14&redirecturl=[a URL you want the browser to go to after you authenticate]

	Por exemplo:

		https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/api/consent/list?api-version=2015-01-14&redirecturl=https://portal.azure.com

	Para enviar a solicitação em Postman no Chrome, execute as seguintes etapas:

	- Insira a **URL de solicitação** descrita acima.
	- Defina o método como **Post**.
	- Adicione um cabeçalho denominado `x-zumo-auth`.
	- No campo **Valor** do cabeçalho, cole o valor que você copiou do `x-zumo-auth` cookie.
	- Clique em **Enviar**.
	 
	A ilustração a seguir mostra a ferramenta Postman no Chrome:

	![Enviar para a URL de autorização](./media/app-service-api-connnect-your-app-to-saas-connector/sendforconsent.png)

	A resposta inclui uma URL a ser usada para iniciar o processo de logon do usuário no Dropbox (se você receber uma resposta de erro que indica que não há suporte ao método Get, embora o menu suspenso do método esteja definido como **Post**, verifique se a URL do gateway é HTTPS, não HTTP).

	![URL de autorização](./media/app-service-api-connnect-your-app-to-saas-connector/getconsenturl.png)

2. Vá para a URL que você recebeu em resposta à solicitação HTTP Post.

	A resposta para essa URL redireciona o navegador para o site do Dropbox, onde o usuário faz logon e concede permissão para o aplicativo acessar a conta do usuário.
	
	Quando o processo de logon e consentimento for concluído, o Dropbox redirecionará o navegador para a URL de redirecionamento especificada (por exemplo, o Portal de Visualização do Azure se você tiver seguido o exemplo e usado https://portal.azure.com)). Se a chamada fosse realizada de um aplicativo Web, seria a próxima página a ser exibida nesse aplicativo. O aplicativo deve verificar a URL, porque, se ocorrer um erro no processo de logon ou de consentimento, a URL de redirecionamento poderá incluir uma variável de cadeia de consulta `error`.

3. Mantenha esta janela do navegador aberta, já que você o usará na seção a seguir.

### Chamar a API de conector do Dropbox 

Agora, o Azure está gerenciamento três tokens de autenticação para você:

- Um do provedor de autenticação configurado para o gateway, por exemplo Active Directory do Azure.
- Um do Dropbox.
- Um criado pelo Azure (o token "zumo").

O único que você deve usar ao fazer solicitações HTTP para trabalhar com Dropbox é o token zumo. Em segundo plano, o Azure associou esse token a outros dois e o conector fornece os outros dois em seu nome, ao fazer chamadas para o Dropbox.

Nas etapas a seguir você pode fazer uma solicitação Get ao conector do Dropbox para examinar os arquivos de sua conta do Dropbox. Como você pode usar uma janela do navegador para uma solicitação Get e como a janela do navegador já tem o token zumo em um cookie, tudo o que você precisa fazer é ir a uma URL e obter de volta os dados do Dropbox.

1. Na janela do navegador com o portal de visualização do Azure aberto, volte para a folha **Aplicativo de API** do conector do Dropbox. 

2. Copie a URL do aplicativo de API.
 
4. Clique no ícone **Definição da API** para ver os métodos de API disponíveis no conector.

	![Folha Aplicativo de API](./media/app-service-api-connnect-your-app-to-saas-connector/apiappblade.png)

	![Definição de API](./media/app-service-api-connnect-your-app-to-saas-connector/apidef.png)

2. Na janela do navegador usado para autenticar para o gateway, chame o método GET que recupera nomes de arquivo de uma pasta. Este é o padrão da URL:

		[connectorurl]/folder/[foldername]
   
3. Por exemplo, se a URL do conector for https://dropboxrg784237ad3e7.azurewebsites.net e você quiser ver os arquivos na pasta de seu blog, a URL seria:

		https://dropboxrg784237ad3e7.azurewebsites.net/folder/blog

	Navegadores diferentes respondem a chamadas de API de forma diferente; se você estiver usando o Chrome, verá uma resposta semelhante ao exemplo a seguir:

	![Obter a resposta à solicitação de pasta](./media/app-service-api-connnect-your-app-to-saas-connector/dbresponse.png)

## Próximas etapas

Você viu como instalar, configurar e testar um conector de SaaS. Para obter mais informações, consulte estes recursos:

* [Usando conectores](../app-service-logic/app-service-logic-connectors-list.md)
* [Autenticação para aplicativos de API e aplicativos móveis](../app-service/app-service-authentication-overview.md)  

[Portal de visualização do Azure]: https://portal.azure.com/
[Portal Azure]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO4-->