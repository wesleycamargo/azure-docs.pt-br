<properties
	pageTitle="Como trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis | Serviço de Aplicativo do Azure"
	description="Aprenda como trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Serviço de Aplicativo do Azure."
	keywords="serviço de aplicativo, serviço de aplicativo do azure, aplicativo móvel, serviço móvel, escala, escalonável, implantação de aplicativo, implantação de aplicativo do azure"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/02/2016"
	ms.author="glenga"/>

# Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Este tópico mostra como usar o SDK do servidor de back-end do .NET nos principais cenários dos Aplicativos Móveis do Serviço de Aplicativo do Azure. Os Aplicativos Móveis SDK do Azure ajuda você a trabalhar com clientes móveis de seu aplicativo ASP.NET.

>[AZURE.TIP] O [SDK do .NET Server para Aplicativos Móveis do Azure](https://github.com/Azure/azure-mobile-apps-net-server) tem código aberto no GitHub. O repositório contém o conjunto de testes de unidade SDK de todo o servidor, bem como alguns projetos de exemplo.

## Documentação de referência

A documentação de referência para o SDK do servidor está localizada aqui: [Referência do .NET dos Aplicativos Móveis do Azure](https://msdn.microsoft.com/library/azure/dn961176.aspx).

## <a name="create-app"></a>Como criar um back-end do aplicativo móvel do .NET

Se você estiver começando um novo projeto, será possível criar um aplicativo do Serviço de Aplicativo usando o [portal do Azure] ou o Visual Studio. Esta seção ajudará você a usar um destes métodos para criar um novo back-end de aplicativo móvel que hospeda uma API de lista de tarefas pendentes simples. Você pode executar isso localmente ou publicar o projeto em seu aplicativo móvel do Serviço de Aplicativo baseado em nuvem.

Se você estiver adicionando recursos móveis a um projeto existente, veja a seção [Baixar e inicializar o SDK](#install-sdk) abaixo.

### Criar um back-end do .NET usando o Portal do Azure

Você pode criar um novo back-end de aplicativo móvel diretamente no [Portal do Azure].

Você pode seguir as etapas abaixo ou criar um novo cliente e um novo servidor juntos acompanhando o tutorial [Criar um aplicativo móvel](app-service-mobile-ios-get-started.md). O tutorial contém uma versão simplificada dessas instruções e funciona melhor para projetos de prova de conceito; o tutorial pode criar apenas um back-end Node.js.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

De volta à folha _Introdução_, em **Criar uma API de tabela**, escolha **C#** como a **Linguagem de back-end**. Clique em **Download**, extraia os arquivos compactados do projeto em seu computador local e abra a solução no Visual Studio.

### Criar um back-end do .NET usando o Visual Studio 2013 e o Visual Studio 2015

Para criar um projeto dos Aplicativos Móveis no Visual Studio, será necessário instalar o [SDK do Azure para .NET](https://azure.microsoft.com/downloads/), versão 2.8.1 ou posterior. Depois de instalar o SDK, crie um novo aplicativo ASP.NET:

1. Abra o diálogo **Novo Projeto** (de *Arquivo* > **Novo** > **Projeto...**).

2. Expanda **Modelos** > **Visual C#** e selecione **Web**.

3. Selecione **Aplicativo Web do ASP.NET**.

4. Preencha o nome do projeto. Em seguida, clique em **OK**.

5. Em _Modelos do ASP.NET 4.5.2_, selecione **Aplicativo Móvel do Azure**. Marque **Host na nuvem** para criar um novo aplicativo móvel na nuvem no qual você possa publicar esse projeto.

6. Clique em **OK**. Seu aplicativo será criado e exibido no Gerenciador de Soluções.

## <a name="install-sdk"></a>Como baixar e inicializar o SDK

O SDK está disponível em [NuGet.org]. Este pacote inclui a funcionalidade básica necessária para começar a usar o SDK. Para inicializar o SDK, você precisa executar ações no objeto **HttpConfiguration**.

###Instalar o SDK

Para instalar o SDK, clique com botão direito do mouse no projeto do servidor no Visual Studio, selecione **Gerenciar Pacotes NuGet**, pesquise pelo pacote [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) e clique em **Instalar**.

###<a name="server-project-setup"></a> Inicializar o projeto de servidor

Um projeto do servidor back-end .NET é inicializado de modo semelhante a outros projetos do ASP.NET, pela inclusão de uma classe de inicialização do OWIN. Não se esqueça de referenciar o pacote NuGet `Microsoft.Owin.Host.SystemWeb`. Para adicionar essa classe no Visual Studio, clique com o botão direito do mouse em seu projeto do servidor e escolha **Adicionar** > **Novo Item**, **Web** > **Geral** > **Classe de inicialização OWIN**.

Isso vai gerar uma classe com o seguinte atributo:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

No método `Configuration()` de sua classe de inicialização do OWIN, configure o projeto de servidor usando um objeto **HttpConfiguration** que representa as opções de configuração para o serviço. O exemplo a seguir inicializa o projeto do servidor, sem nenhum recurso adicional:

	// in OWIN startup class
	public void Configuration(IAppBuilder app)
	{
	    HttpConfiguration config = new HttpConfiguration();

	    new MobileAppConfiguration()
	        // no added features
	        .ApplyTo(config);

	    app.UseWebApi(config);
	}

Para habilitar recursos individuais, você deve chamar os métodos de extensão no objeto **MobileAppConfiguration** antes de chamar **ApplyTo**. Por exemplo, o código a seguir adiciona as rotas padrão para todos os controladores de API que têm o atributo `[MobileAppController]` durante a inicialização:

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

Observe que `MapApiControllers` mapeia apenas controladores com o atributo `[MobileAppController]`. Para mapear outros controladores, use o método [MapHttpAttributeRoutes].

Muitos dos métodos de extensão de recurso estão disponíveis por meio de pacotes NuGet adicionais que você pode incluir, que são descritos na seção a seguir.

O início rápido do servidor do portal do Azure chama **UseDefaultConfiguration()**. Isso equivale à configuração a seguir:

		new MobileAppConfiguration()
			.AddMobileAppHomeController()             // from the Home package
			.MapApiControllers()
			.AddTables(                               // from the Tables package
				new MobileAppTableConfiguration()
					.MapTableControllers()
					.AddEntityFramework()             // from the Entity package
				)
			.AddPushNotifications()                   // from the Notifications package
			.MapLegacyCrossDomainController()         // from the CrossDomain package
			.ApplyTo(config);


### Extensões SDK

Os seguintes pacotes com base em extensão no NuGet fornecem vários recursos móveis que podem ser usados pelo seu aplicativo. Habilitar extensões durante a inicialização usando o objeto **MobileAppConfiguration**.

- [Microsoft.Azure.Mobile.Server.Quickstart] Dá suporte à configuração básica dos Aplicativos Móveis. Adicionado à configuração chamando o método de extensão **UseDefaultConfiguration** durante a inicialização. Essa extensão inclui as seguintes extensões: Notificações, Autenticação, Entidade, Tabelas, pacotes Crossdomain e Home. Isso é equivalente ao projeto do servidor de início rápido que você baixa no portal do Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementa a página padrão *este aplicativo móvel está em execução* na raiz do site. Adicione à configuração chamando o método de extensão **AddMobileAppHomeController**.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) inclui classes para trabalhar com dados e define o pipeline de dados. Adicione à configuração chamando o método de extensão **AddTables**.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) permite que o Entity Framework acesse dados no Banco de Dados SQL. Adicionar à configuração chamando o método de extensão **AddTablesWithEntityFramework**.

- [Microsoft.Azure.Mobile.Server.Authentication] Permite a autenticação e define o middleware OWIN usado para validar tokens. Adicionar à configuração chamando os métodos de extensão **AddAppServiceAuthentication** e **IAppBuilder**.**UseAppServiceAuthentication**.

- [Microsoft.Azure.Mobile.Server.Notifications] permite as notificações por push e define um ponto de extremidade de registro push. Adicionar à configuração chamando o método de extensão **AddPushNotifications**.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) Cria um controlador que fornece dados para os navegadores da Web herdados do seu Aplicativo Móvel. Adicione à configuração chamando o método de extensão **MapLegacyCrossDomainController**.

- [Microsoft.Azure.Mobile.Server.Login] dá suporte à autenticação personalizada por meio do método AppServiceLoginHandler.CreateToken(). Esse é um método estático e não precisa ser habilitado na configuração.

## <a name="publish-server-project"></a>Como publicar o projeto do servidor

Essa seção mostra como publicar seu projeto de back-end do .NET a partir do Visual Studio. Você também pode implantar seu projeto de back-end usando Git ou qualquer um dos outros métodos abordados na [documentação de implantação do Serviço de Aplicativo do Azure](../app-service-web/web-sites-deploy.md).

1. No Visual Studio, recompile o projeto para restaurar os pacotes do NuGet.

2. No Gerenciador de Soluções, clique com o botão direito no projeto e clique em **Publicar**. Na primeira vez que você publicar, precisará definir um perfil de publicação. Quando já tiver um perfil definido, basta selecioná-la e clicar em **Publicar**.

2. Se solicitado a selecionar um destino de publicação, clique em **Serviço de Aplicativo do Microsoft Azure** > **Próximo** e (se necessário) entre com suas credenciais do Azure. O Visual Studio baixa e armazena suas configurações de publicação com segurança diretamente do Azure.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)

3. Escolha sua **Assinatura**, selecione o **Tipo de Recurso** em **Exibição**, expanda **Aplicativo Móvel**, clique no back-end do aplicativo móvel e então clique em **OK**.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)

4. Verifique as informações de perfil de publicação e clique em **Publicar**.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

	Quando o back-end do aplicativo móvel foi publicado com êxito, você verá uma página de aterrissagem indicando êxito.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

##<a name="define-table-controller"></a> Como definir um controlador de tabela

Um controlador de tabela fornece acesso aos dados de entidade em um armazenamento de dados com base em tabela, como o armazenamento de Banco de dados SQL ou da tabela do Azure. Controladores de tabela herdam da classe genérica **TableController**, em que o tipo genérico é uma entidade no modelo que representa o esquema da tabela, da seguinte maneira:

	public class TodoItemController : TableController<TodoItem>
    {
		//...
	}

Os controladores de tabela são inicializados com o método de extensão **AddTables**. Isso adiciona rotas em `/tables/` para todas as subclasses de `TableController`.

O exemplo a seguir inicializa um controlador de tabela que usa o Entity Framework para acessar dados:

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);

Para obter um exemplo de um controlador de tabela que usa o Entity Framework para acessar dados de um Banco de Dados SQL do Azure, veja a classe **TodoItemController** no download do projeto do servidor de início rápido do portal do Azure.

### Como ajustar o tamanho de paginação da tabela

Por padrão, os Aplicativos Móveis do Azure retornam 50 registros por solicitação. Isso garante que o cliente não associará o thread de interface do usuário nem o servidor por muito tempo, garantindo uma boa experiência do usuário. Você deve aumentar o “tamanho da consulta permitido” do lado servidor e o tamanho da página do lado cliente para fazer uma alteração no tamanho da paginação de tabela. Para aumentar o tamanho da paginação, ajuste seu controlador de tabela com esta linha:

    [EnableQuery(PageSize = 500)]

Certifique-se de que PageSize seja igual ou maior do que o tamanho que será solicitado pelo cliente. Consulte a documentação de instruções específicas do cliente para obter detalhes sobre a alteração do tamanho de página do cliente.

## Como definir um controlador da API personalizada

O controlador da API personalizada fornece as funções mais básicas de back-end do Aplicativo Móvel, expondo um ponto de extremidade. Você pode registrar um controlador da API específico do dispositivo móvel usando o atributo [MobileAppController]. Esse atributo registra a rota, configura o serializador JSON dos Aplicativos Móveis e ativa a o [verificação de versão de cliente](app-service-mobile-client-and-server-versioning.md).

1. No Visual Studio, clique com o botão direito do mouse na pasta Controladores, clique em **Adicionar** > **Controlador**, selecione **Controlador da API Web 2 &mdash; Vazio** e clique em **Adicionar**.

2. Forneça um **Nome do controlador**, como `CustomController`, e clique em **Adicionar**. Isso cria uma nova classe **CustomController** herdada do **ApiController**.

3. No novo arquivo classe de controlador, adicione a seguinte instrução de uso.

		using Microsoft.Azure.Mobile.Server.Config;

4. Aplique o atributo **[MobileAppController]** à definição de classe do controlador de API, como no exemplo a seguir:

		[MobileAppController]
		public class CustomController : ApiController
		{
		      //...
		}

4. No arquivo App\_Start/Startup.MobileApp.cs, adicione uma chamada ao método de extensão **MapApiControllers**, como no seguinte exemplo:

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);

	Observe que você não precisará chamar **MapApiControllers** se chamar **UseDefaultConfiguration**, que inicializa todos os recursos.

Qualquer controlador que não tenha o **MobileAppControllerAttribute** aplicado ainda poderá ser acessado pelos clientes, mas não será consumido corretamente por clientes que usem qualquer SDK do cliente do Aplicativo Móvel.

## Como trabalhar com autenticação

Aplicativos móveis usam os recursos de autenticação do serviço de aplicativo e do ASP.NET para simplificar o processo de habilitar a autenticação para seus aplicativos. Esta seção mostra como executar as seguintes tarefas relacionadas à autenticação em seu projeto de servidor de back-end do .NET:

+ [Como: Adicionar autenticação a um projeto do servidor](#add-auth)
+ [Como usar a autenticação personalizada para o seu aplicativo](#custom-auth)
+ [Como recuperar informações do usuário autenticado](#user-info)
+ [Como restringir o acesso a dados para usuários autorizados](#authorize)

### <a name="add-auth"></a>Como adicionar autenticação a um projeto do servidor

Você pode adicionar autenticação ao seu projeto de servidor estendendo o objeto **MobileAppConfiguration** e configurando o middleware OWIN. Quando você instala o pacote [Microsoft.Azure.Mobile.Server.Quickstart] e chamar o método de extensão **UseDefaultConfiguration**, você pode pular a etapa 3.

1. No Visual Studio, instale o pacote [Microsoft.Azure.Mobile.Server.Authentication].

2. No arquivo de projeto Startup.cs, adicione a seguinte linha de código ao início do método **Configuração**:

		app.UseAppServiceAuthentication(config);

	Isso adiciona o componente de middleware OWIN que permite seu Aplicativo Móvel do Azure validar tokens emitidos pelo gateway de Serviço de Aplicativo associado.

3. Adicione o atributo `[Authorize]` a qualquer controlador ou método que exija autenticação. Agora, os usuários devem ser autenticados para acessar o ponto de extremidade ou APIs específicas.

Para saber como autenticar clientes no back-end dos Aplicativos Móveis, veja [Adicionar autenticação ao seu aplicativo](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Como usar a autenticação personalizada para o seu aplicativo

Você pode optar por fornecer seu próprio sistema de logon, caso não queria usar um dos provedores de Autenticação/Autorização do Serviço de Aplicativo. Para fazer isso, instale o pacote [Microsoft.Azure.Mobile.Server.Login].

Será necessário fornecer sua própria lógica para determinar se um usuário deve estar conectado. Por exemplo, você pode comparar com senhas com sal e hash aplicados em um banco de dados. No exemplo abaixo, o método `isValidAssertion()` é responsável por essas verificações e é definido em outro lugar.

A autenticação personalizada é exposta criando um novo ApiController e expondo ações de registro e logon, como a mostrada abaixo. O cliente pode tentar fazer logon coletando as informações relevantes do usuário e enviando um POST HTTPS para a API com as informações do usuário no corpo. Depois que o servidor valida a asserção, um token pode ser emitido usando o método `AppServiceLoginHandler.CreateToken()`.

Observe que essa ApiController **não deve** usar o atributo `[MobileAppController]`, pois causará falha nas solicitações de logon do cliente. O atributo `[MobileAppController]` requer o cabeçalho de solicitação [ZUMO-API-VERSION](app-service-mobile-client-and-server-versioning.md) e esse cabeçalho **não** é enviado pelo SDK do cliente para rotas de logon.

Um exemplo de ação de logon poderia ser:

		public IHttpActionResult Post([FromBody] JObject assertion)
		{
			if (isValidAssertion(assertion)) // user-defined function, checks against a database
			{
				JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
					mySigningKey,
					myAppURL,
					myAppURL,
					TimeSpan.FromHours(24) );
				return Ok(new LoginResult()
				{
					AuthenticationToken = token.RawData,
					User = new LoginResultUser() { UserId = userName.ToString() }
				});
			}
			else // user assertion was not valid
			{
				return this.Request.CreateUnauthorizedResponse();
			}
		}

No exemplo acima, LoginResult e LoginResultUser são objetos simples que expõem as propriedades mostradas. O cliente espera que as respostas de logon voltem como objetos JSON na forma:

		{
			"authenticationToken": "<token>",
			"user": {
				"userId": "<userId>"
			}
		}

O método `AppServiceLoginHandler.CreateToken()` inclui um parâmetro _audience_ e um parâmetro _issuer_. Ambos são normalmente definidos para a URL da raiz do seu aplicativo, usando o esquema HTTPS. Da mesma forma, você deve definir _secretKey_ como o valor da chave de conexão do seu aplicativo. Esse é um valor confidencial que nunca deve ser compartilhado ou incluído em um cliente. Você pode obter esse valor enquanto estiver hospedado no Serviço de Aplicativo consultando a variável de ambiente _WEBSITE\_AUTH\_SIGNING\_KEY_. Se for necessário em um contexto de depuração local, siga as instruções na seção [Depuração local com autenticação](#local-debug) para recuperar a chave e armazená-la como uma configuração de aplicativo.

Também é necessário fornecer um tempo de vida para o token emitido, bem como quaisquer declarações que quiser incluir. Você precisa fornecer uma declaração de entidade, conforme mostrado no exemplo de código.

Você também pode simplificar o código do cliente para usar o método `loginAsync()` (a nomeação pode variar entre plataformas), em vez de um HTTP POST manual. Você usaria a sobrecarga que aceita um parâmetro de token adicional, que se correlaciona com o objeto de asserção de que você faria um POST. Nesse caso, o provedor deve ser um nome personalizado de sua escolha. Em seguida, no servidor, a ação de logon deve estar no caminho _/.auth/login/{customProviderName}_, que inclui esse nome personalizado. Para colocar o controlador nesse caminho, adicione uma rota para o HttpConfiguration antes de aplicar o MobileAppConfiguration.

		config.Routes.MapHttpRoute("CustomAuth", ".auth/login/CustomAuth", new { controller = "CustomAuth" });

Substitua a cadeia de caracteres “CustomAuth” acima pelo nome do controlador que hospeda a ação de logon.

>[AZURE.TIP] Usar a abordagem loginAsync() garante que o token de autenticação está conectado a todas as chamadas subsequentes ao serviço.

###<a name="user-info"></a>Como recuperar informações do usuário autenticado

Quando um usuário é autenticado pelo serviço de aplicativo, você pode acessar a ID de usuário atribuída e outras informações no seu código de back-end do .NET. Isso é útil para tomar decisões de autorização para um determinado usuário no back-end, por exemplo, se um usuário específico pode acessar uma linha da tabela ou outro recurso. O código a seguir mostra como obter a ID de usuário para um usuário conectado:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

A SID é derivado da ID de usuário específica do provedor e é estática para um determinado usuário e o provedor de logon. Quando um usuário acessa um ponto de extremidade anonimamente, a propriedade User retorna null.

O Serviço de Aplicativo também permite solicitar declarações específicas do provedor de logon. Isso permite solicitar mais informações do provedor, por exemplo, usando Graph APIs do Facebook. Você pode especificar declarações na folha do provedor no portal. Algumas declarações exigem configuração adicional com o provedor.

O código a seguir chama o método de extensão **GetAppServiceIdentityAsync** para obter as credenciais de logon, que incluem o token de acesso necessário para fazer solicitações na Graph API do Facebook:

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Observe que você deve adicionar uma instrução de uso a `System.Security.Principal` para fazer o método de extensão **GetAppServiceIdentityAsync** funcionar.

### <a name="authorize"></a>Como restringir o acesso a dados para usuários autorizados

Na seção anterior, mostramos como recuperar a ID de usuário de um usuário autenticado. Você pode restringir o acesso a dados e outros recursos com base nesse valor. Por exemplo, adicionar uma coluna userId às tabelas e filtrar os resultados da consulta do usuário segundo a ID de usuário é uma maneira simples de limitar os dados retornados apenas aos usuários autorizados. O código a seguir retorna linhas de dados somente quando a ID do usuário atual corresponde ao valor na coluna UserId na tabela TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

Dependendo do seu cenário específico, você também poderá criar tabelas de Usuários ou Funções para monitorar informações mais detalhadas de autorização do usuário, como quais pontos de extremidade um determinado usuário tem permissão para acessar.

## Adicionar notificações por push para um projeto do servidor

Você pode adicionar notificações por push ao seu projeto do servidor estendendo o objeto **MobileAppConfiguration** e criando um cliente dos Hubs de Notificação. Ao instalar o pacote [Microsoft.Azure.Mobile.Server.Quickstart] e chamar o método de extensão **UseDefaultConfiguration**, você poderá pular para a Etapa 3.

1. No Visual Studio, clique com o botão direito no projeto do servidor, clique em **Gerenciar Pacotes NuGet**, pesquise por Microsoft.Azure.Mobile.Server.Notifications e clique em **Instalar**. Isso instala o pacote [Microsoft.Azure.Mobile.Server.Notifications].

3. Repita essa etapa para instalar o pacote `Microsoft.Azure.NotificationHubs`, que inclui a biblioteca do cliente dos Hubs de Notificação.

2. Em App\_Start/Startup.MobileApp.cs, adicione uma chamada ao método de extensão **AddPushNotifications** durante a inicialização, que se parece com o seguinte:

		new MobileAppConfiguration()
			// other features...
			.AddPushNotifications()
			.ApplyTo(config);

	Isso cria o ponto de extremidade do registro de notificação por push em seu projeto de servidor. Esse ponto de extremidade é usado pelos clientes para se registrarem com o hub de notificação associado. Agora você precisa adicionar o cliente do Hub de notificação que é usado para enviar notificações.

3. Em um controlador do qual você deseja enviar notificações por push, adicione a seguinte instrução de uso:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;

4. Adicione o código a seguir que cria um cliente de Hubs de Notificação:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

Neste ponto, você pode usar o cliente de Hubs de Notificação para enviar notificações por push para dispositivos registrados. Para obter mais informações, veja [Adicionar notificações por push ao seu aplicativo](app-service-mobile-ios-get-started-push.md). Para saber mais sobre tudo o que você pode fazer com os Hubs de Notificação, confira [Visão geral dos Hubs de Notificação](../notification-hubs/notification-hubs-overview.md).

##<a name="tags"></a>Como adicionar marcações à instalação de um dispositivo para habilitar o envio por push direcionado

Os Hubs de Notificação permitem que você envie notificações direcionadas para registros específicos usando marcas. Uma marca que é criada automaticamente é a ID de instalação, que é específica de uma instância do aplicativo em um determinado dispositivo. Um registro com ID de instalação também é chamado de *instalação*. Você pode usar a ID de instalação para gerenciar a instalação, por exemplo, para adicionar marcas. A ID de instalação pode ser acessada na propriedade **installationId** do **MobileServiceClient**.

O exemplo a seguir mostra como usar uma ID de instalação para adicionar uma marca a uma instalação específica nos Hubs de Notificação:

	hub.PatchInstallation("my-installation-id", new[]
	{
	    new PartialUpdateOperation
	    {
	        Operation = UpdateOperationType.Add,
	        Path = "/tags",
	        Value = "{my-tag}"
	    }
	});

Observe que qualquer marca fornecida pelo cliente durante o registro de notificação por push é ignorada pelo back-end ao criar a instalação. Para habilitar um cliente a adicionar marcas à instalação, você deverá criar uma nova API personalizada que adiciona marcas usando o padrão acima. Para obter um exemplo de um controlador de API personalizado que permite que os clientes adicionem marcações a uma instalação, confira [Marcações de notificação por push adicionadas por cliente](https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags) no exemplo de início rápido concluído para os Aplicativos Móveis do Serviço de Aplicativo para back-end do .NET.

##<a name="push-user"></a>Como enviar notificações por push para um usuário autenticado

Quando um usuário autenticado se registra para notificações por push, uma marca de ID de usuário é adicionada automaticamente ao registro. Usando essa marca, você pode enviar notificações por push para todos os dispositivos registrados por um usuário específico. O código abaixo obtém a SID do usuário que fez a solicitação e envia um modelo de notificação por push para cada registro de dispositivo do usuário:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Ao se registrar para notificações por push de um cliente autenticado, verifique se a autenticação foi concluída antes de tentar o registro. Para saber mais, confira [Enviar por push para usuários](https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users) no exemplo de início rápido dos Aplicativos Móveis do Serviço de Aplicativo para back-end do .NET.

## Como depurar e solucionar problemas do SDK do .NET Server

O Serviço de Aplicativo do Azure fornece várias técnicas de depuração e de solução de problemas para aplicativos ASP.NET.

- [Monitorando um Serviço de Aplicativo do Azure](../app-service-web/web-sites-monitor.md)
- [Habilitar o registro em log de diagnósticos no Serviço de Aplicativo do Azure](../app-service-web/web-sites-enable-diagnostic-log.md)
- [Solucionar problemas de um Serviço de Aplicativo do Azure no Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### Registro em log

É possível gravar em logs de diagnóstico do Serviço de Aplicativo usando a gravação de rastreamento padrão do ASP.NET. Antes de gravar os logs, habilite o diagnóstico no back-end do aplicativo móvel.

Para habilitar o diagnóstico e gravar logs:

1. Siga as etapas em [Como habilitar o diagnóstico](../app-service-web/web-sites-enable-diagnostic-log.md#enablediag).

2. Adicione a seguinte instrução de uso em seu arquivo de código:

		using System.Web.Http.Tracing;

3. Crie um gravador de rastreamento para gravar de back-end do .NET para os logs de diagnóstico da seguinte maneira:

		ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
		traceWriter.Info("Hello, World");

4. Republique seu projeto de servidor e acesse o back-end do aplicativo móvel para executar o caminho de código com o registro em log.

5. Baixe e avalie os logs, conforme descrito em [Como baixar logs](../app-service-web/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Depuração local com autenticação

Você pode executar seu aplicativo localmente a fim de testar as alterações antes de publicá-lo na nuvem. Para vários aplicativos, basta pressionar *F5* no Visual Studio. No entanto, há algumas considerações adicionais ao usar a autenticação.

Você deve ter um aplicativo móvel baseado em nuvem com a Autenticação/Autorização do Serviço de Aplicativo configurado, e o cliente deve ter o ponto de extremidade de nuvem especificado como o host de logon alternativo. Veja a documentação da plataforma cliente escolhida ([iOS](app-service-mobile-ios-how-to-use-client-library.md) e [Windows/Xamarin](app-service-mobile-dotnet-how-to-use-client-library.md)) para conhecer as etapas específicas necessárias.

Verifique se seu aplicativo tem o [Microsoft.Azure.Mobile.Server.Authentication] instalado. Em seguida, na classe de inicialização OWIN do aplicativo, adicione o seguinte, após aplicar `MobileAppConfiguration` ao seu `HttpConfiguration`:

		app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
		{
			SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
			ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
			ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
			TokenHandler = config.GetAppServiceTokenHandler()
		});

No exemplo acima, você deve definir as configurações de aplicativo _authAudience_ e _authIssuer_ no arquivo Web.config para que cada uma seja a URL da raiz do aplicativo usando o esquema HTTPS. Da mesma forma, você deve definir _authSigningKey_ como o valor da chave de autenticação de seu aplicativo. Esse é um valor confidencial que nunca deve ser compartilhado ou incluído em um cliente. Para obtê-lo, navegue até o aplicativo no [portal do Azure] e clique em **Ferramentas**. Em seguida, selecione **Kudu** e clique em **Ir**. Você será levado até o ponto de extremidade de gerenciamento Kudu de seu site. Clique em **Ambiente** e encontre o valor em _WEBSITE\_AUTH\_SIGNING\_KEY_. Esse é o valor que você deve usar para _authSigningKey_ em sua configuração de aplicativo local.

Agora, seu servidor em execução local está equipado para validar tokens que o cliente obtém do ponto de extremidade baseado em nuvem.


[portal do Azure]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx

<!---HONumber=AcomDC_0817_2016-->