<properties 
	pageTitle="Adicionar notificações por push ao seu aplicativo universal para Windows 8.1 | Microsoft Azure" 
	description="Saiba como enviar notificações por push ao seu aplicativo universal para Windows 8.1 do serviço móvel de back-end .NET usando os Hubs de Notificação do Azure." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="glenga"/>

# Adicionar notificações por push a seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Visão geral
Este tópico mostra como usar os Serviços Móveis do Azure com um back-end do .NET para enviar notificações por push a um aplicativo do Windows universal. Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure em um projeto do aplicativo Windows universal. Ao concluir, seu serviço móvel enviará uma notificação por push do back-end do .NET para todos os aplicativos do Windows Store e Windows Phone Store sempre que um registro é inserido na tabela TodoList. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

>[AZURE.NOTE]Esse tópico lhe mostra como usar as ferramentas no Visual Studio Professional 2013 com atualização 3 para adicionar suporte para notificações por push dos Serviços Móveis para um aplicativo Windows universal. As mesmas etapas podem ser usadas para adicionar notificações por push dos Serviços Móveis para um aplicativo da Windows Store ou Windows Phone Store 8.1. Para adicionar notificações por push a um aplicativo Windows Phone 8 ou Windows Phone Silverlight 8.1, consulte esta versão de [Introdução à autenticação dos Serviços Móveis](mobile-services-dotnet-backend-windows-phone-get-started-push.md).

Para concluir este tutorial, você precisará do seguinte:

* Uma [conta ativa da Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Comunidade do Visual Studio 2013</a>. 

##<a id="register"></a>Registrar seu aplicativo para notificações por push

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../../includes/mobile-services-create-new-push-vs2013.md)]

&nbsp;&nbsp;6. Navegue na pasta de projeto `\Services\MobileServices\your_service_name`, abra o arquivo de código push.register.cs gerado e inspecione o método **UploadChannel** que registra a URL do canal do dispositivo com o hub de notificação.
 
&nbsp;&nbsp;7. Abra o arquivo de código App.xaml.cs compartilhado e avise que uma chamada para o novo método **UploadChannel** foi adicionada no manipulador de evento **OnLaunched**. Isso garante a tentativa do registro do dispositivo sempre que o aplicativo for iniciado.

&nbsp;&nbsp;8. Repita as etapas anteriores para adicionar notificações por push ao projeto do aplicativo Windows Phone Store e, em seguida, no arquivo App.xaml.cs compartilhado, remova a chamada extra para **UploadChannel** e o wrapper condicional `#if...#endif` restante. Ambos os projetos podem agora compartilhar uma única chamada para **UploadChannel**.

> [AZURE.NOTE]Você também pode simplificar o código gerado ao unificar as definições ajustadas do `#if...#endif` [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) em uma única definição não ajustada usada por ambas as versões do aplicativo.

Agora que as notificações por push estão ativadas no aplicativo, você deve atualizar o serviço móvel para enviar notificações por push.

##<a id="update-service"></a>Atualizar o serviço para enviar notificações por push

As seguintes etapas atualizam a classe TodoItemController existente para enviar uma notificação por push para todos os dispositivos registrados quando um novo item é inserido. Você pode implementar código semelhante em [ApiController](https://msdn.microsoft.com/library/system.web.http.apicontroller.aspx), [TableController](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx) ou em qualquer lugar nos seus serviços de back-end.

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a>Habilitar notificações por push para teste local

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

As etapas restantes nesta seção são opcionais. Elas permitem que você teste seu aplicativo em relação ao seu serviço móvel em execução em um computador local. Se você planeja testar as notificações por push usando o serviço móvel em execução no Azure, você pode apenas pular para a última seção. Isso ocorre porque o assistente Adicionar Notificação por Push já configurou seu aplicativo para se conectar ao seu serviço em execução no Azure.

>[AZURE.NOTE]Nunca use um serviço móvel de produção para testar e desenvolver o trabalho. Sempre publique seu projeto de serviço móvel para um serviço de preparação para testes.

&nbsp;&nbsp;5. Abra o arquivo de projeto App.xaml.cs compartilhado e localize qualquer uma das linhas do código que cria uma nova instância da classe [MobileServiceClient] para acessar o serviço móvel em execução no Azure.

&nbsp;&nbsp;6. Comente este código e adicione o código que cria um novo [MobileServiceClient] com o mesmo nome, mas usando a URL do host local no construtor, semelhante ao seguinte:

	// This MobileServiceClient has been configured to communicate with your local
	// test project for debugging purposes.
	public static MobileServiceClient todolistClient = new MobileServiceClient(
		"http://localhost:4584"
	);

&nbsp;&nbsp;Usando este [MobileServiceClient], o aplicativo conectará ao serviço local ao invés da versão hospedada no Azure. Quando quiser voltar e executar o aplicativo no serviço móvel hospedado no Azure, mude de volta para as definições [MobileServiceClient] originais.

##<a id="test"></a> Testar notificações por push no seu aplicativo

[AZURE.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação do aplicativo da Windows Store para usar os Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir o tutorial seguinte, [Enviar notificações por push para usuários autenticados], que permite mostrar como usar marcas para enviar notificações por push a partir de um Serviço Móvel apenas para um usuário autenticado.

Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

* [Adicionar os Serviços Móveis a um aplicativo existente][Get started with data] <br/>Saiba mais sobre como armazenar e consultar dados usando os serviços móveis.

* [Introdução à autenticação do seu aplicativo][Get started with authentication] <br/>Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

* [O que são Hubs de Notificação?] <br/>Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

* [Depuração de aplicativos de Hubs de Notificação](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Obtenha diretrizes de solução de problemas e depuração de soluções de Hubs de Notificação.

* [Como usar um cliente .NET para os Serviços Móveis do Azure] <br/>Saiba mais sobre como usar os Serviços Móveis a partir dos aplicativos em C# do Windows.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md

[Enviar notificações por push para usuários autenticados]: mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md

[O que são Hubs de Notificação?]: ../notification-hubs-overview.md

[Como usar um cliente .NET para os Serviços Móveis do Azure]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx

<!---HONumber=August15_HO7-->