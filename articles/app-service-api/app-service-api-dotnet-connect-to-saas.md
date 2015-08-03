<properties 
	pageTitle="Aplicativo de API ASP.NET de conector de SaaS personalizado no Serviço de Aplicativo do Azure" 
	description="Saiba como escrever um código que se conecta a uma plataforma SaaS a partir de um aplicativo de API e como chamar o aplicativo de API a partir de um cliente .NET." 
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
	ms.date="07/01/2015" 
	ms.author="tdykstra"/>

# Conectar-se a uma plataforma SaaS de um aplicativo de API ASP.NET no Serviço de Aplicativo do Azure

## Visão geral

Este tutorial mostra como codificar e configurar um [aplicativo de API](app-service-api-apps-why-best-platform.md) que se conecta a uma [plataforma SaaS (Software como serviço)](../app-service/app-service-authentication-overview.md#obotosaas) usando o [SDK de aplicativo de API do Serviço de Aplicativo para .NET](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/). O tutorial também mostra como chamar o aplicativo de API de um cliente .NET usando o [SDK do Serviço de Aplicativo para .NET](http://www.nuget.org/packages/Microsoft.Azure.AppService). Ao final do tutorial, você terá um cliente de aplicativo do console .NET que chama um aplicativo de API .NET em execução no Serviço de Aplicativo do Azure. O aplicativo de API chama a API do Dropbox e retorna uma lista de arquivos e pastas em na conta de Dropbox do usuário.

Como alternativa para escrever código que chama uma API de SaaS diretamente de um aplicativo de API personalizado, você pode chamar um [aplicativo de API de conector](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md) pré-empacotado. Para obter informações sobre como fazer isso, consulte [Implantar e configurar um aplicativo de API de conector SaaS](app-service-api-connnect-your-app-to-saas-connector.md).

Este tutorial apresenta as seguintes etapas:

* Criar um projeto de aplicativo de API no Visual Studio. 
* Configurar o arquivo *apiapp.json* para permitir que o aplicativo de API se conecte ao serviço Dropbox.
* Adicionar o código que chama Dropbox e retorna os resultados.
* Criar um novo aplicativo de API no Azure.
* Implantar o projeto para o aplicativo de API.
* Configurar o aplicativo de API.
* Configurar o gateway.
* Criar um cliente de teste.
* Executar o cliente de teste.

## Pré-requisitos

O tutorial parte dos seguintes pressupostos:

* Você concluiu os tutoriais [Criar um aplicativo de API](app-service-dotnet-create-api-app.md) e [Implantar um aplicativo de API](app-service-dotnet-deploy-api-app.md).
  
* Você tem uma compreensão básica da arquitetura de gateway do Serviço de Aplicativo do Azure para autenticação, conforme apresentado em [Autenticação para aplicativos de API e aplicativos móveis](app-service-authentication-overview.md).

* Você sabe como trabalhar com aplicativos de API no Portal de Visualização do Azure, conforme explicado em [Como navegar até as folhas de aplicativo de API e de gateway](app-service-api-manage-in-portal.md#navigate).

## Criar o projeto de aplicativo de API
 
Quando houver instruções para inserir um nome para o projeto, digite *SimpleDropbox*.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

## Configurar o arquivo *apiapp.json*

Para que um aplicativo de API faça chamadas de saída para uma plataforma SaaS, essa plataforma deve ser especificada no arquivo *apiapp.json*.

1. Abra o arquivo *apiapp.json* e adicione uma propriedade `authentication`, como mostrado aqui (você também precisará adicionar uma vírgula depois da propriedade anterior):

		"authentication": [
		  {
		    "type": "dropbox"
		  }
		]

	O arquivo apiapp.json completo será semelhante a este exemplo:

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "SimpleDropBox",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "SimpleDropBox",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    },
		    "authentication": [
		      {
		        "type": "dropbox"
		      }
		    ]
		}

2. Salve o arquivo.

A configuração da propriedade `authentication` tem alguns efeitos:

* Faz com que o portal exiba a interface do usuário na folha de aplicativo de API, permitindo que você insira o ID do cliente da plataforma SaaS e os valores secretos do cliente.

	![](./media/app-service-api-dotnet-connect-to-saas/authblade.png)

* Permite que o aplicativo de API recupere o token de acesso do provedor de SaaS do gateway para uso ao chamar a API do provedor de SaaS.

A propriedade `authentication` é uma matriz, mas essa versão de visualização não oferece suporte à especificação de vários provedores.

Para obter uma lista de plataformas compatíveis, consulte [Obtendo o consentimento do usuário para acessar outras plataformas SaaS](../app-service/app-service-authentication-overview.md#obotosaas).

Você também pode especificar os escopos, como neste exemplo:

		"authentication": [
		  {
		    "type": "google",
		    "scopes": ["https://www.googleapis.com/auth/userinfo.email", "https://www.googleapis.com/auth/userinfo.profile"]
		  }
		]

Os escopos disponíveis são definidos de acordo com cada provedor de SaaS e podem ser encontrados no portal de desenvolvedor do provedor.

## Adicionar o código que chama Dropbox

1. Instale o pacote NuGet [DropboxRestAPI](https://www.nuget.org/packages/DropboxRestAPI) no projeto SimpleDropbox.

	* No menu **Ferramentas**, clique em **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.

	* Na janela do **Console do Gerenciador de Pacotes**, digite este comando:
	 
			install-package DropboxRestAPI  

1. Abra *Controllers\\ValuesController.cs* e substitua todo o código no arquivo pelo código a seguir.

		using DropboxRestAPI;
		using Microsoft.Azure.AppService.ApiApps.Service;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace SimpleDropBox2.Controllers
		{
		    public class ValuesController : ApiController
		    {
		        public async Task<IEnumerable<string>> Get()
		        {
		            // Retrieve the token from the gateway
		            var runtime = Runtime.FromAppSettings(Request);
		            var dropboxTokenResult = await runtime.CurrentUser.GetRawTokenAsync("dropbox");
		
		            // Create a Dropbox client object that will send the token
		            // with REST API calls to Dropbox.
		            var dropboxClient = new Client(
		                new Options
		                {
		                    AccessToken = dropboxTokenResult.Properties["AccessToken"]
		                }
		            );
		
		            // Call the Dropbox API
		            var metadata = await dropboxClient.Core.Metadata.MetadataAsync("/");
		
		            // Return a list of files and folders.
		            return metadata.contents.Select(md => md.path);
		        }
		    }
		}

	Antes de o cliente chamar esse método, o usuário fez logon no Dropbox e concedeu consentimento para o aplicativo de API acessar a conta de Dropbox do usuário. O Dropbox reconhece essa permissão fornecendo um token de acesso para o gateway do Serviço de Aplicativo. Esse código recupera o token do gateway e o inclui em uma chamada à API do Dropbox, conforme mostrado nas etapas 6 e 7 no diagrama a seguir.

	![](./media/app-service-api-dotnet-connect-to-saas/saastoken.png)

2. Compile o projeto.

## Criar um aplicativo de API no Azure

Nesta seção, use o assistente **Publicar Web** do Visual Studio para criar um aplicativo de API no Azure. Quando houver instruções para inserir um nome para o aplicativo de API, digite *SimpleDropbox*.

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## Implantar o código

Use o mesmo assistente **Publicar Web** para implantar o código do novo aplicativo de API.

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Configurar a autenticação de chamadas de entrada

Para que o Serviço de Aplicativo do Azure permita chamadas de saída autenticadas do aplicativo de API, esse aplicativo também deve exigir que as chamadas de entrada venham de usuários autenticados. Isso não é um requisito geral de OAuth 2.0, mas é um requisito da arquitetura de gateway do Serviço de Aplicativo implementada atualmente.

As capturas de tela desta seção mostram um aplicativo de API ContactsList, mas o processo é o mesmo para o aplicativo de API SimpleDropbox que você está criando neste tutorial.

### Configure o aplicativo de API para solicitar a autenticação das chamadas de entrada

[AZURE.INCLUDE [app-service-api-config-auth](../../includes/app-service-api-config-auth.md)]

### Configurar um provedor de identidade no gateway

[AZURE.INCLUDE [app-service-api-gateway-config-auth](../../includes/app-service-api-gateway-config-auth.md)]

## Configurar a autenticação de chamadas de saída

Para permitir que o aplicativo de API chame a API do Dropbox, você precisa trocar as configurações entre o aplicativo de API e um aplicativo Dropbox criado no site de desenvolvedor do Dropbox.

### Criar um aplicativo Dropbox no site Dropbox.com

[AZURE.INCLUDE [app-service-api-create-dropbox-app](../../includes/app-service-api-create-dropbox-app.md)]

### Trocar configurações entre o Dropbox e o aplicativo de API

As etapas a seguir referem-se a um aplicativo de API de conector do Dropbox, mas os procedimentos e a interface do usuário são os mesmos para o aplicativo de API SimpleDropbox que você está criando neste tutorial.

> **Observação:** se você não vir os campos de segredo e ID de cliente do Dropbox na folha **Autenticação** do aplicativo de API SimpleDropbox, conforme mostrado na captura de tela, certifique-se de que você reiniciou o gateway conforme as instruções após a implantação do projeto de aplicativo de API. O valor "dropbox" na propriedade `authentication` do arquivo *apiapp.json* implantado anteriormente é o que faz com que o portal exiba esses campos.

[AZURE.INCLUDE [app-service-api-exchange-dropbox-settings](../../includes/app-service-api-exchange-dropbox-settings.md)]

## Criar um cliente de teste

Nesta seção, você criará um projeto de aplicativo de console que usa o código de cliente gerado pelo Visual Studio para chamar o aplicativo de API SimpleDropbox. O aplicativo de console cria uma instância de um controle de navegador do Windows Forms para manipular a interação do usuário com o gateway e páginas da web de logon do Dropbox.

### Criar o projeto

1. No Visual Studio, crie um projeto de aplicativo de console e nomeie-o *SimpleDropboxTest*.

2. Defina uma referência para System.Windows.Forms.
 
	* No ** Gerenciador de Soluções**, clique com o botão direito do mouse em ** Referências** e, em seguida, clique em ** Adicionar Referência**.

	* Marque a caixa de seleção à esquerda de **System.Windows.Forms**e, em seguida, clique em **OK**.
	 
	![](./media/app-service-api-dotnet-connect-to-saas/setref.png)

	O aplicativo de console usará o conjunto do Windows Forms para instanciar um controle do navegador quando for necessário para permitir que o usuário faça logon no gateway e no Dropbox.

### Adicionar o código de cliente gerado

As capturas de tela desta seção mostram um projeto ContactsList e um aplicativo de API, mas, para este tutorial, selecione o projeto SimpleDropboxTest e o aplicativo de API SimpleDropbox.

[AZURE.INCLUDE [app-service-api-dotnet-add-generated-client](../../includes/app-service-api-dotnet-add-generated-client.md)]

### Adicione código para chamar o aplicativo de API

3. Abra *Program.cs* e substitua o código existente pelo seguinte.
		
		using Microsoft.Azure.AppService;
		using Newtonsoft.Json;
		using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Windows.Forms;
		
		namespace SimpleDropboxTest
		{
		    enum Step
		    {
		        GatewayLogin,
		        GetSaaSConsentLink,
		        GetUserConsent
		    }
		
		    class Program
		    {
		        private const string GATEWAY_URL = @"{gateway url}";
		        private const string URL_TOKEN = "#token=";
		        private const string SAAS_URL = "dropbox.com";
		        private static Form frm = new Form();
		        private static string responseURL = "";
		        private static Step step;
		        [STAThread]
		        static void Main(string[] args)
		        {
		            // Create the web browser control
		            WebBrowser browser = new WebBrowser();
		            browser.Dock = DockStyle.Fill;
		            browser.Navigated += CheckResponseURL;
		            frm.Controls.Add(browser);
		            frm.Width = 640;
		            frm.Height = 480;
		
		            // Create the gateway and API app clients.
		            AppServiceClient appServiceClient = new AppServiceClient(GATEWAY_URL);
		            SimpleDropbox simpleDropboxClient = appServiceClient.CreateSimpleDropbox();
		
		            // Navigate browser to gateway login URL for configured identity provider.
		            // Identity provider for this example is Azure Active Directory.
		            step = Step.GatewayLogin;
		            browser.Navigate(string.Format(@"{0}/login/aad", GATEWAY_URL));
		            frm.ShowDialog();
		            Console.WriteLine("Logged in to gateway, response URL=" + responseURL);
		
		            // Get user ID and Zumo token from return URL, then call 
		            // the gateway URL to log in the gateway client.
		            var encodedJson = responseURL.Substring(responseURL.IndexOf(URL_TOKEN) + URL_TOKEN.Length);
		            var decodedJson = Uri.UnescapeDataString(encodedJson);
		            var result = JsonConvert.DeserializeObject<dynamic>(decodedJson);
		            string userId = result.user.userId;
		            string userToken = result.authenticationToken;
		            appServiceClient.SetCurrentUser(userId, userToken);
		
		            // Call gateway API to get consent link URL for target SaaS platform.
		            // SaaS platform for this example is Dropbox.
		            // See the tutorial for an explanation of
		            // the redirectURL parameter for GetConsentLinkAsync
		            var gatewayConsentLink = appServiceClient.GetConsentLinkAsync("SimpleDropbox", GATEWAY_URL).Result;
		            Console.WriteLine("\nGot gateway consent link, URL=" + gatewayConsentLink);
		
		            // Navigate browser to consent link URL returned from gateway.
		            // Response URL will be the SaaS logon link
		            step = Step.GetSaaSConsentLink;
		            browser.Navigate(gatewayConsentLink);
		            frm.ShowDialog();
		            Console.WriteLine("\nGot SaaS consent link response, URL=" + responseURL);
		
		            // Navigate browser to login/consent link for SaaS platform.
		            step = Step.GetUserConsent;
		            browser.Navigate(responseURL);
		            frm.ShowDialog();
		            Console.WriteLine("\nGot Dropbox login response, URL=" + responseURL);
		
		            Console.WriteLine("\nResponse from SaaS Provider");
		            var response = simpleDropboxClient.Values.Get();
		            foreach (string s in response)
		            {
		                Console.WriteLine(s);
		            }
		            Console.Read();
		        }
		
		        static void CheckResponseURL(object sender, WebBrowserNavigatedEventArgs e)
		        {
		            if ((step == Step.GatewayLogin && e.Url.AbsoluteUri.IndexOf(URL_TOKEN) > -1)
		                || (step == Step.GetSaaSConsentLink && e.Url.AbsoluteUri.IndexOf(SAAS_URL) > -1)
		                || (step == Step.GetUserConsent && e.Url.AbsoluteUri.IndexOf(GATEWAY_URL) > -1))
		            {
		                responseURL = e.Url.AbsoluteUri;
		                frm.Close();
		            }
		        }
		
		    }
		}

1. Substitua {gateway url} pela URL real do seu gateway.
 
	Você pode obter a URL do gateway na folha **Gateway** no portal:

	![](./media/app-service-api-dotnet-connect-to-saas/gwurl.png)

		private const string GATEWAY_URL = @"https://sd1aeb4ae60b7cb4f3d966dfa43b660.azurewebsites.net";

	> **Importante**: verifique se a URL do gateway começa com `https://`, e não com `http://`. **Se copiar http:// do portal, você precisará alterar para https:// quando colar no código.**

### Explicação do código

O aplicativo de console foi projetado para usar uma quantidade mínima de código a fim de ilustrar as etapas pelas quais um aplicativo cliente tem de passar. Um aplicativo de produção geralmente não é um aplicativo de console e implementa logs e tratamento de erros.

Aqui está uma visão geral do que o código está fazendo:

* Abre um navegador para a URL de logon do gateway do provedor de identidade configurado; neste caso, o Active Directory do Azure. 
	 
* Trata a URL de resposta esperada depois que o usuário faz logon: extrair o token do Zumo e ID do usuário, fornecê-los ao objeto de cliente do Serviço de Aplicativo.

* Usa o objeto de cliente do Serviço de Aplicativo para recuperar uma URL de gateway que redirecionará o link do Dropbox para logon e consentimento. Etapa 1 no diagrama.

* Abre um navegador para a URL de consentimento de gateway. O navegador é redirecionado para o link de logon e consentimento do Dropbox. Etapa 2 no diagrama.
	 
* Fecha o navegador depois que o usuário faz logon e consente em Dropbox.com. Etapa 3 no diagrama.
 
* Chama o aplicativo de API. Etapa 5 no diagrama. (A etapa 4 é executada em segundo plano entre Dropbox.com e o gateway; as etapas 6 e 7 são executadas a partir do aplicativo de API, não do cliente.)

![](./media/app-service-api-dotnet-connect-to-saas/saastoken.png)

Observações adicionais:

* O atributo `STAThread` no método `Main` é exigido pelo controle de navegador da Web e não está relacionado à configuração ou à chamada do aplicativo de API.

* A URL de logon do gateway mostrada termina com `/aad` no caso do Active Directory do Azure.

		browser.Navigate(string.Format(@"{0}/login/aad", GATEWAY_URL));

	Veja os valores a serem usados com outros provedores: * "microsoftaccount" * "facebook" * "twitter" * "google" <br/><br/>

* O segundo parâmetro do método `GetConsentLinkAsync()` é a URL de retorno de chamada para a qual o servidor de consentimento redireciona depois que o usuário faz logon no Dropbox e concede consentimento para acessar a conta do usuário.

		var gatewayConsentLink = appServiceClient.GetConsentLinkAsync("SimpleDropbox", GATEWAY_URL).Result;

	Para esse parâmetro, você normalmente deve especificar a próxima página da Web para a qual o usuário deve ir no aplicativo cliente. Como esse código de demonstração está em um aplicativo de console, não há nenhuma página de aplicativo para acessar, e o código especifica a URL de gateway apenas como uma página de aterrissagem conveniente.

	O aplicativo cliente deve confirmar que será redirecionado para essa URL e que não há nenhuma mensagem de erro. Se o processo de logon/consentimento falhar, a URL de redirecionamento poderá conter uma mensagem de erro na cadeia de consulta. Para obter mais informações, consulte a seção [Solucionar problemas](#troubleshooting).

## Teste

1. Execute o aplicativo de console SimpleDropboxTest.

2. Na primeira página de logon, entre usando suas credenciais do Active Directory do Azure (ou credenciais de outro provedor de identidade, como Google ou Twitter, se for a configuração no gateway).

	![](./media/app-service-api-dotnet-connect-to-saas/aadlogon.png)

3. Na página de logon de Dropbox.com, entre usando suas credenciais do Dropbox.

	![](./media/app-service-api-dotnet-connect-to-saas/dblogon.png)

4. Na página de consentimento do Dropbox, conceda ao aplicativo permissão de acesso aos dados.

	![](./media/app-service-api-dotnet-connect-to-saas/dbconsent.png)

	Em seguida, o aplicativo de console chama o aplicativo de API e retorna uma lista de arquivos na sua conta do Dropbox.

	![](./media/app-service-api-dotnet-connect-to-saas/testclient.png)

## Solucionar problemas

Esta seção contém os seguintes tópicos:

* [Erro HTTP 405 após logon do gateway](#405)
* [Erro HTTP 400 em vez da página de logon do Dropbox](#400)
* [Erro HTTP 403 ao chamar o aplicativo de API](#403)

### <a id="405"></a> Erro HTTP 405 após o logon do gateway

Se você obtiver erros HTTP 405 quando o código chamar GetConsentLinkAsync, verifique se você usou https://, não http://, para a URL de gateway.

![](./media/app-service-api-dotnet-connect-to-saas/http405.png)

O erro 405 de método não permitido é emitido porque o cliente tenta fazer uma solicitação HTTP POST não relacionada a SSL, o gateway redireciona para *https://* e o redirecionamento causa uma solicitação GET. A URL de recuperação de um link de consentimento só aceita solicitações POST.

### <a id="400"></a>Erro HTTP 400 em vez da página de logon do Dropbox

Certifique-se de que você tem o **ID de cliente** correto na folha **Autenticação** do aplicativo de API e certifique-se de que não haja espaços à esquerda ou à direita.

### <a id="403"></a> Erro HTTP 403 ao chamar o aplicativo de API

* Verifique se o **nível de acesso** do aplicativo de API está configurado como **Público (autenticado)**, e não **Interno**.

* Certifique-se de que você tem o **segredo de cliente** correto na folha **Autenticação** do aplicativo de API e certifique-se de que não há espaços à esquerda ou à direita.

A URL de redirecionamento após o logo no Dropbox pode se parecer com este exemplo:

	https://sd1aeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/?error=RmFpbGVkIHRvIGV4Y2hhbmdlIGNvZGUgZm9yIHRva2VuLiBEZXRhaWxzOiB7ImVycm9yX2Rlc2NyaXB0aW9uIjogIkludmFsaWQgY2xpZW50X2lkIG9yIGNsaWVudF9zZWNyZXQiLCAiZXJyb3IiOiAiaW52YWxpZF9jbGllbnQifQ%3d%3d

Se você remover %3d%3d do final do valor da cadeia de consulta `error`, ela será uma cadeia codificada com base64 válida. Decodifique a cadeia para obter a mensagem de erro:

	Failed to exchange code for token. Details: {"error_description": "Invalid client_id or client_secret", "error": "invalid_client"}

## Próximas etapas

Você aprender a codificar e configurar um aplicativo de API que se conecta a uma plataforma SaaS. Para obter links para outros tutoriais sobre como lidar com a autenticação em aplicativos da API, consulte [Autenticação para aplicativos de API e aplicativos móveis - próximas etapas](../app-service/app-service-authentication-overview.md#next-steps).

[Azure preview portal]: https://portal.azure.com/
[Azure portal]: https://manage.windowsazure.com/

<!---HONumber=July15_HO4-->