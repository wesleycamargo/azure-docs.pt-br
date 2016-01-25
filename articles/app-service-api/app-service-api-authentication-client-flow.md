<properties 
	pageTitle="Chamar um aplicativo de API de um cliente autenticado" 
	description="Saiba como chamar um aplicativo de API do Azure de um cliente de aplicativo Web que foi autenticado pelo Active Directory do Azure." 
	keywords="serviço de aplicativo, serviço de aplicativo do Azure, autenticação, autenticação do Azure, api, api de autenticação"
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
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Chamar um aplicativo de API do Azure de um cliente de aplicativo Web autenticado pelo Active Directory do Azure

## Visão geral

Este tutorial mostra como chamar um aplicativo de API que é protegido pelo AAD (Active Directory do Azure) de um aplicativo Web que também é protegido pelo AAD. Ao concluir o tutorial, você terá um aplicativo Web e um aplicativo de API em execução na sua assinatura do Azure. O aplicativo Web exibirá dados que obtém chamando o aplicativo de API, conforme mostrado na captura de tela a seguir.

![](./media/app-service-api-authentication-client-flow/aboutpage.png)

Você aprenderá a transmitir credenciais do AAD do aplicativo Web para o aplicativo de API a fim de que o usuário não seja solicitado novamente pelo aplicativo de API a fazer logon.

Você executará as seguintes etapas:

- Criar um aplicativo de API e configurá-lo para usar a autenticação AAD.
- Criar um aplicativo Web e configurá-lo para usar a autenticação AAD.
- Adicionar código ao aplicativo Web para chamar o aplicativo de API protegido.
- Implantar o aplicativo Web e o aplicativo de API no Azure.
- Testar para verificar se o aplicativo Web pode chamar o aplicativo de API.

### Autenticação do fluxo de cliente

O código que você adiciona ao aplicativo Web implementará um processo chamado autenticação do [fluxo de cliente](../app-service/app-service-authentication-overview.md#client-flow). O diagrama a seguir ilustra o processo para obter o token de acesso do AAD e trocá-lo por um token do aplicativo de API (Zumo).

![](./media/app-service-api-authentication-client-flow/clientflow.png)

Caso você não esteja familiarizado com a função do gateway de aplicativo de API na autenticação para aplicativos de API, confira [Autenticação para aplicativos de API e aplicativos móveis](../app-service/app-service-authentication-overview.md).

## Pré-requisitos

Antes de iniciar este tutorial, verifique se o [Visual Studio 2015](https://www.visualstudio.com/) e o [Azure SDK para .NET](http://go.microsoft.com/fwlink/?linkid=518003) estão instalados. As mesmas instruções também funcionarão para o Visual Studio 2013.

O tutorial supõe que você saiba como trabalhar com projetos Web no Visual Studio.

## Criar e proteger um aplicativo de API usando o AAD

1. Crie ou baixe um projeto de aplicativo de API.
 
	* Para criar um projeto, siga as orientações em [Criar um aplicativo de API](app-service-dotnet-create-api-app.md).

	* Para baixar um projeto, obtenha-o no [Repositório ContactsList do GitHub](https://github.com/tdykstra/ContactsList).

	Agora você tem um projeto de API Web que retorna dados de contato.

	![](./media/app-service-api-authentication-client-flow/swaggerlocal.png)

2. Implante um projeto de aplicativo de API em um novo aplicativo de API no Azure.

	Siga as instruções em [Implantar um aplicativo de API](app-service-dotnet-deploy-api-app.md).

	No [Portal de Visualização do Azure], a folha **Definição de API** do aplicativo de API agora mostra os métodos Get e Post do projeto de API Web que você implantou.

	![](./media/app-service-api-authentication-client-flow/apiappinportal.png)

4. Proteja o aplicativo de API usando o AAD como o provedor de identidade.
 
	Siga as instruções do AAD em [Proteger um aplicativo de API](app-service-api-dotnet-add-authentication.md). Você pode pular a seção **Usar o Postman para enviar uma solicitação Post** do tutorial.

	Ao concluir, a folha **Active Directory do Azure** do aplicativo de API no [portal de visualização do Azure] terá a ID de cliente do aplicativo do AAD e seu locatário do AAD.

	![](./media/app-service-api-authentication-client-flow/aadblade.png)

	E a guia **Configurar** do aplicativo do AAD no [portal do Azure] terá a URL da ID do aplicativo e a URL de Resposta do aplicativo de API.

	![](./media/app-service-api-authentication-client-flow/aadconfig.png)

## Criar e proteger um aplicativo Web usando o AAD

Nesta seção, você vai baixar e configurar um projeto Web que está configurado para autenticação AAD. O projeto tem uma página **Sobre** que mostra informações de declarações do usuário conectado.

![](./media/app-service-api-authentication-client-flow/aboutpagestart.png)

Você modificará essa página posteriormente adicionando uma seção para exibir informações de contato recuperadas do aplicativo de API.

1. Baixe o projeto Web do [repositório WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/)
 
2. Siga as instruções de **Como executar o exemplo** no [arquivo Leiame](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/master/README.md), com as seguintes exceções:
 
	* Você pode usar o Visual Studio 2015, embora o arquivo Leiame recomende usar o Visual Studio 2013. 

	* Use o aplicativo do AAD que você já criou, em vez de criar outro.
 
	* Mantenha o mesmo **URI da ID do Aplicativo** que já tem para o aplicativo do AAD. (Não o altere para o formato especificado no arquivo Leiame.)
	
	* Altere outras configurações de aplicativo do AAD conforme orientado; em particular, defina o logon e as URLs de resposta para a URL base para o aplicativo de exemplo.

Você está mantendo o mesmo URI da ID do aplicativo que criou para o aplicativo de API, de modo que é possível usar o mesmo token de acesso do AAD para o aplicativo Web e o aplicativo de API. Se você alterou o URI da ID do aplicativo para o formato definido pelo leiame, ele funcionará para acessar o aplicativo Web, mas não para o aplicativo de API. Você não pode transmitir o token do AAD para o gateway do aplicativo de API para obter o token Zumo, pois o gateway espera um token para um URI da ID do aplicativo composto pela URL do gateway mais "/logon/aad".

## Adicionar o código de cliente gerado para o aplicativo de API

Nesta seção, você adiciona o código gerado automaticamente para uma interface tipada para chamar o aplicativo de API.

8.	No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no projeto WebApp-GroupClaims-DotNet e clique em **Adicionar > Cliente do Aplicativo de API do Azure**.

9.	Na caixa de diálogo **Adicionar Cliente do Aplicativo de API do Microsoft Azure**, selecione o aplicativo de API que você protegeu com o AAD.

	Após a conclusão da geração de código, você vê uma nova pasta **Gerenciador de soluções**, com o nome do aplicativo de API. Esta pasta contém o código que implementa os modelos de dados e classes de cliente.

10. Corrija referências ambíguas causadas pelo código gerado em *ContactsList/ContactsExtensions.cs*: altere as duas instâncias de `Task.Factory.StartNew` para `System.Threading.Tasks.Task.Factory.StartNew`.
 
## Adicionar código para trocar o token do AAD por um token Zumo

1.	Em HomeController.cs, adicione instruções `using` ao SDK do Serviço de Aplicativo e o serializador JSON.

		using Microsoft.Azure.AppService;
		using Newtonsoft.Json.Linq;

2. Adicione as constantes da URL do gateway e o URI da ID do aplicativo do aplicativo do AAD. Verifique se a URL do gateway é https, e não http.

		private const string GATEWAY_URL = "https://clientflowgroupaeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/";
		private const string APP_ID_URI = GATEWAY_URL + "login/aad";

2.	Adicione um método que obtenha um objeto de cliente para chamar o aplicativo de API.

		public async Task<AppServiceClient> GetAppServiceClient()
		{
		    var appServiceClient = new AppServiceClient(GATEWAY_URL);
		    string userObjectID = ClaimsPrincipal.Current.FindFirst
		        ("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		
		    var authContext = new AuthenticationContext
		        (ConfigHelper.Authority, new TokenDbCache(userObjectID));
		
		    ClientCredential credential = new ClientCredential
		        (ConfigHelper.ClientId, ConfigHelper.AppKey);
		
		    // Get the AAD token.
		    AuthenticationResult result = authContext.AcquireToken(APP_ID_URI, credential);
		    var aadToken = new JObject();
		    aadToken["access_token"] = result.AccessToken;
		
		    // Send the AAD token to the gateway and get a Zumo token
		    var appServiceUser = await appServiceClient.LoginAsync
		        ("aad", aadToken).ConfigureAwait(false);
		
		    return appServiceClient;
		}

	Nesse código, `ConfigHelper.Authority` é resolvido para "https://login.microsoftonline.com/{your tenant}", por exemplo: "https://login.microsoftonline.com/contoso.onmicrosoft.com".

2.	Adicione o código imediatamente antes da instrução `return View()` no fim do método `About` para chamar o aplicativo de API. (Na etapa seguinte, você adicionará o código à exibição `About` para exibir os dados retornados).

		var appServiceClient = await GetAppServiceClient();
		var client = appServiceClient.CreateContactsList();
		var contacts = client.Contacts.Get();
		ViewData["contacts"] = contacts;

3. Em *Views/Home/About.cshtml*, adicione o código logo depois do título `h2` para exibir as informações de contato.

		<h3>Contacts</h3>
		<table class="table table-striped table-bordered table-condensed table-hover">
		    <tr>
		        <th>Name</th>
		        <th>Email</th>
		    </tr>
		
		    @foreach (WebAppGroupClaimsDotNet.Models.Contact contact in (IList<WebAppGroupClaimsDotNet.Models.Contact>)ViewData["contacts"])
		    {
		        <tr>
		            <td>@contact.Name</td>
		            <td>@contact.EmailAddress</td>
		        </tr>
		    }
		
		</table>


3. No arquivo Web.config do aplicativo, adicione o redirecionamento de associação a seguir após a marca de abertura `<assemblyBinding>`.

		<dependentAssembly>
		  <assemblyIdentity name="System.Net.Http.Primitives" publicKeyToken="b03f5f7f11d50a3a" culture="neutral"/>
		  <bindingRedirect oldVersion="0.0.0.0-4.2.28.0" newVersion="4.2.28.0"/>
		</dependentAssembly>

	Sem esse redirecionamento de associação, o aplicativo falhará porque o SDK do Serviço de Aplicativo inclui uma dependência em System.Net.Http.Primitives versão 1.5.0.0, mas a versão usada pelo projeto é 4.2.28.0.
 
3. Siga as instruções em **Implantar este exemplo no Azure** no [arquivo Leiame](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/).

5. Execute o aplicativo.

	![](./media/app-service-api-authentication-client-flow/homepage.png)

6. Clique em **Entrar** e insira as credenciais de um usuário no domínio do AAD que você está usando para esse aplicativo.

	![](./media/app-service-api-authentication-client-flow/signedin.png)

7. Clique em **Sobre**.

	O código adicionado à exibição e ao controle Sobre é executado e mostra que você se autenticou com êxito no aplicativo de API e chamou o respectivo método Get.

	![](./media/app-service-api-authentication-client-flow/aboutpage.png)

## Solucionar problemas

### Erros HTTP 400 

Verifique se a url do gateway é https, e não http. Quando copiada diretamente do portal de visualização do Azure, ela pode especificar http.
 
### Erros do SQL Server

O aplicativo exige acesso a um banco de dados do SQL Server identificado pela cadeia de conexão GroupClaimContext. Verifique se a cadeia de conexão no site implantado aponta para uma instância do Banco de Dados SQL. Você pode colocar a cadeia de conexão correta no Web.config implantado ou nas definições de configuração do tempo de execução do Azure.

## Agradecimentos

Agradeço a Govind S. Yadav ([@govindsyadav](https://twitter.com/govindsyadav)) por me ajudar a desenvolver este tutorial.

## Próximas etapas

Você viu como fazer a autenticação do fluxo de cliente para aplicativos de API do Serviço de Aplicativo. Para obter informações sobre outros modos de lidar com a autenticação em aplicativos de API, consulte [Autenticação para aplicativos de API e aplicativos móveis](../app-service/app-service-authentication-overview.md).

[portal do Azure]: https://manage.windowsazure.com/
[Portal de Visualização do Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0114_2016-->