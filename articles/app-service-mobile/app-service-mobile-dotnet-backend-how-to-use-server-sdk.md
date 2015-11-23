<properties
	pageTitle="Como trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis | Serviço de Aplicativo do Azure"
	description="Aprenda como trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Serviço de Aplicativo do Azure."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/18/2015"
	ms.author="glenga"/>

# Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure

Este tópico mostra como usar o SDK do servidor de back-end do .NET nos principais cenários dos Aplicativos Móveis do Serviço de Aplicativo do Azure. Os Aplicativos Móveis SDK do Azure ajuda você a trabalhar com clientes móveis de seu aplicativo ASP.NET.

>[AZURE.TIP]O [.NET server SDK para Aplicativos Móveis do Azure](https://github.com/Azure/azure-mobile-apps-net-server) é o software livre no GitHub. O repositório contém o conjunto de testes de unidade SDK de todo o servidor, bem como alguns projetos de exemplo.

## Como: Baixar e inicializar o SDK

O SDK está disponível em [NuGet.org]. Este pacote inclui a funcionalidade básica necessária para começar a usar o SDK. Para inicializar o SDK, você precisa executar ações no objeto **HttpConfiguration**.

###Instalar o SDK

Para instalar o SDK, clique com botão direito no projeto do servidor no Visual Studio, selecione **Gerenciar Pacotes NuGet**, pesquise pelo pacote [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) e clique em **Instalar**.

###<a name="server-project-setup"></a> Inicializar o projeto de servidor

Um projeto do servidor back-end .NET é inicializado de modo semelhante a outros projetos do ASP.NET, pela inclusão de uma classe de inicialização do OWIN. Para adicionar essa classe no Visual Studio, clique com o botão direito do mouse em seu projeto do servidor e selecione **Adicionar** -> **Novo Item**, depois **Web** -> **Geral** -> **Classe de Inicialização do OWIN**.

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

Para habilitar recursos individuais, você deve chamar os métodos de extensão no objeto **MobileAppConfiguration** antes de chamar **ApplyTo**. Por exemplo, o código a seguir adiciona as rotas padrão para todos os controladores de API durante a inicialização.

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

Muitos dos métodos de extensão de recurso estão disponíveis por meio de pacotes NuGet adicionais que você pode incluir, que são descritos na seção a seguir. O início rápido do servidor do Portal do Azure chama **UseDefaultConfiguration()**. Isso equivale à configuração a seguir:
    
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

- [Microsoft.Azure.Mobile.Server.Quickstart] dá suporte à configuração básica de Aplicativos Móveis. Adicionado à configuração chamando o método de extensão **UseDefaultConfiguration** durante a inicialização. Essa extensão inclui as seguintes extensões: Notificações, Autenticação, Entidade, Tabelas, pacotes Crossdomain e Home. Isso é equivalente ao projeto do servidor de início rápido que você baixa no portal do Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementa a página padrão *este aplicativo móvel está em execução* na raiz do site. Adicionar à configuração chamando o método de extensão **AddMobileAppHomeController**.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) inclui classes para trabalhar com dados e define o pipeline de dados. Adicionar à configuração chamando o método de extensão **AddTables**.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) permite que o Entity Framework acesse dados no Banco de Dados SQL. Adicionar à configuração chamando o método de extensão **AddTablesWithEntityFramework**.

- [Microsoft.Azure.Mobile.Server.Authentication] permite a autenticação e define o middleware OWIN usado para validar tokens. Adicionar à configuração chamando os métodos de extensão **AddAppServiceAuthentication** e **IAppBuilder**.**UseMobileAppAuthentication**.

- [Microsoft.Azure.Mobile.Server.Notifications] permite as notificações por push e define um ponto de extremidade de registro push. Adicionar à configuração chamando o método de extensão **AddPushNotifications**.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) cria um controlador que fornece dados para os navegadores da Web herdados do seu Aplicativo Móvel. Adicionar à configuração chamando o método de extensão **MapLegacyCrossDomainController**.

## Como definir um controlador da API personalizada

O controlador da API personalizada fornece as funções mais básicas de back-end do Aplicativo Móvel, expondo um ponto de extremidade. O controlador da API personalizada

1. No Visual Studio, clique com o botão direito na pasta Controladores e, em seguida, clique em **Adicionar** > **Controlador**, selecione **Web API 2 Controller&mdash;Empty** e clique em **Adicionar**.

2. Forneça um **Nome do Controlador**, como `CustomController`, e clique em **Adicionar**. Isso cria uma nova classe **CustomController** herdada do **ApiController**.

3. No novo arquivo classe de controlador, adicione a seguinte instrução de uso.

		using Microsoft.Azure.Mobile.Server.Config;

4. Aplique o **MobileAppControllerAttribute** à definição de classe de controlador de API, como no exemplo a seguir:

		[MobileAppController] 
		public class CustomController : ApiController
		{
		      //...
		}

4. No arquivo App\_Start/Startup.MobileApp.cs, adicione uma chamada para o método de extensão **MapApiControllers**, como no exemplo a seguir:

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);
    
	Observe que você não precisa chamar **MapApiControllers** quando, em vez disso, você chama **UseDefaultConfiguration**, que inicializa todos os recursos.

Qualquer controlador que não tenha **MobileAppControllerAttribute** instalado, ainda pode ser acessado por clientes, mas ele não será consumido corretamente por clientes que usem qualquer SDK cliente do Aplicativo Móvel.

## Como: definir um controlador de tabela

Um controlador de tabela fornece acesso aos dados de entidade em um armazenamento de dados com base em tabela, como o armazenamento de Banco de dados SQL ou da tabela do Azure. Controladores de tabela herdam da classe genérica **TableController**, em que o tipo genérico é uma entidade no modelo que representa o esquema da tabela, da seguinte maneira:

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

Controladores de tabela são inicializados usando o método de extensão **AddTables**. O exemplo a seguir inicializa um controlador de tabela que usa o Entity Framework para acessar dados:

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
Para obter um exemplo de um controlador de tabela que usa o Entity Framework para acessar dados de um Banco de Dados SQL do Azure, consulte a classe **TodoItemController** no download do projeto do servidor de início rápido do portal do Azure.

## Como: Adicionar autenticação a um projeto do servidor

Você pode adicionar autenticação ao seu projeto de servidor, estendendo o objeto **MobileAppConfiguration** e configurando o middleware OWIN. Quando você instala o pacote [Microsoft.Azure.Mobile.Server.Quickstart] e chamar o método de extensão **UseDefaultConfiguration**, você pode pular a etapa 3.

1. No Visual Studio, instale o pacote [Microsoft.Azure.Mobile.Server.Authentication]. 

2. No arquivo de projeto Startup.cs, adicione a seguinte linha de código no início do método **Configuração**:

		app.UseMobileAppAuthentication(config);

	Isso adiciona o componente de middleware OWIN que permite seu Aplicativo Móvel do Azure validar tokens emitidos pelo gateway de Serviço de Aplicativo associado.

3. Adicione o atributo `[Authorize]` a qualquer controlador ou método que exija autenticação. Os usuários agora devem ser autenticados para acessar o ponto de extremidade ou aquelas APIs específicas.

Para saber mais sobre como autenticar clientes para o back-end de Aplicativos Móveis, consulte [Adicionar autenticação ao seu aplicativo](app-service-mobile-dotnet-backend-ios-get-started-users.md).

## Adicionar notificações por push para um projeto do servidor

Você pode adicionar notificações por push para seu projeto do servidor, estendendo o objeto **MobileAppConfiguration** e criando um cliente de Hubs de Notificação. Quando você instala o pacote [Microsoft.Azure.Mobile.Server.Quickstart] e chama o método de extensão **UseDefaultConfiguration**, você pode pular a etapa 3.

1. No Visual Studio, clique com o botão direito do mouse no projeto do servidor e clique em **Gerenciar pacotes NuGet**, pesquise por Microsoft.Azure.Mobile.Server.Notifications` e clique em **Instalar**. Isso instala o pacote [Microsoft.Azure.Mobile.Server.Notifications].
 
3. Repita essa etapa para instalar o pacote `Microsoft.Azure.NotificationHubs`, que inclui a biblioteca do cliente de Hubs de Notificação.

2. Em App\_Start/Startup.MobileApp.cs, abra o arquivo de projeto WebApiConfig.cs e adicione uma chamada para o método de extensão **AddPushNotifications** durante a inicialização, que se parece com o seguinte:

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

Neste ponto, você pode usar o cliente de Hubs de Notificação para enviar notificações por push para dispositivos registrados. Para obter mais informações, consulte [Adicionar notificações por push ao seu aplicativo](app-service-mobile-ios-get-started-push.md). Para saber mais sobre tudo o que você pode fazer com os Hubs de Notificação, consulte [Visão Geral de Hubs de Notificação](../notification-hubs/notification-hubs-overview.md).

## Como publicar o projeto do servidor

Use as etapas a seguir para publicar o seu projeto do servidor no Azure.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]


[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=Nov15_HO3-->