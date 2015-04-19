<properties 
	pageTitle="Introdução à notificação por push usando um serviço móvel de back-end do JavaScript" 
	description="Saiba como usar os serviços móveis do Azure e Hubs de notificação para enviar notificações por push para seu aplicativo Windows universal." 
	services="mobile-services, notification-hubs" 
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
	ms.date="09/27/2014" 
	ms.author="glenga"/>


# Adicionar notificações por push ao seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Este tópico mostra como usar os Serviços Móveis do Azure com um back-end do JavaScript para enviar notificações por push a um aplicativo do Windows universal. Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure em um projeto do aplicativo Windows universal. Ao concluir, seu serviço móvel enviará uma notificação por push do back-end do JavaScript para todos os aplicativos do Windows Store e Windows Phone Store sempre que um registro é inserido na tabela TodoList. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

>[AZURE.NOTE]Esse tópico lhe mostra como usar as ferramentas no Visual Studio 2013 Update 3 para adicionar suporte para notificações por push dos Serviços Móveis para um aplicativo Windows universal. As mesmas etapas podem ser usadas para adicionar notificações por push dos Serviços Móveis para um aplicativo da Windows Store ou Windows Phone Store 8.1. Para adicionar notificações por push a um aplicativo Windows Phone 8 ou Windows Phone Silverlight 8.1, consulte esta versão de [Introdução às notificações por push nos Serviços Móveis](mobile-services-javascript-backend-windows-phone-get-started-push.md).

> Se você não pode atualizar para o Visual Studio 2013 Update 3 ou prefere adicionar manualmente seu projeto de serviço móvel a uma solução de aplicativo da Windows Store, veja [esta versão](mobile-services-javscript-backend-windows-store-dotnet-get-started-push.md) do tópico.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Registrar seu aplicativo para notificações por push](#register)
2. [Atualizar o serviço para enviar notificações por push](#update-service)
3. [Testar notificações por push em seu aplicativo](#test)

Para concluir este tutorial, você precisará do seguinte:

* Uma [conta da Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) ativa.
* [Visual Studio 2013 Express para Windows](http://go.microsoft.com/fwlink/?LinkId=257546) Update 3 ou versão posterior

##<a id="register"></a>Registrar seu aplicativo para notificações por push

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Navegue até a pasta de projeto <code>\Services\MobileServices\seu_nome_de_serviço</code>, abra o arquivo de código push.register.cs gerado e inspecione o método <strong>UploadChannel</strong> que registra a URL do canal do dispositivo com o hub de notificação.</p></li> 
<li><p>Abra o arquivo de código App.xaml.cs compartilhado e avise que uma chamada para o novo método <strong>UploadChannel</strong> foi adicionada no manipulador de evento <strong>OnLaunched</strong>.</p> <p>Isso garante a tentativa do registro do dispositivo sempre que o aplicativo for iniciado.</p></li>
<li><p>Repita as etapas anteriores para adicionar notificações por push ao projeto do aplicativo Windows Phone Store e, em seguida, no arquivo App.xaml.cs compartilhado, remova a chamada extra para <strong>UploadChannel</strong> e o wrapper condicional <code>#if...#endif</code> restante.</p> <p>Ambos os projetos podem agora compartilhar uma única chamada para <strong>UploadChannel</strong>.</p>
<p>Observe que você também pode simplificar o código gerado ao unificar as definições contidas em um wrapper <code>#if...#endif</code> do <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> em uma única definição sem wrapper, usada por ambas as versões do aplicativo.</p></li>
</ol>

Agora que as notificações por push estão habilitadas no aplicativo, você deve atualizar o serviço móvel para enviar notificações por push. 

##<a id="update-service"></a>Atualizar o serviço para enviar notificações por push

As etapas a seguir mostram a atualização do script de inserção registrado para a tabela TodoItem. Você pode implementar código semelhante em qualquer script de servidor ou em qualquer lugar nos seus serviços de back-end. 

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]


##<a id="test"></a> Testar notificações por push no seu aplicativo

[AZURE.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação do aplicativo da Windows Store para usar os Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir o próximo tutorial, [Enviar notificações por push para usuários autenticados], que permite mostrar como usar marcas para enviar notificações por push de um Serviço Móvel para um único usuário autenticado.

Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os serviços móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários de seu aplicativo com diferentes tipos de conta usando serviços móveis.

* [O que são Hubs de Notificação?]
  <br/>Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

* [Depurar aplicativos de Hubs de Notificação](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obter orientação ao solucionar problemas ou ao depurar soluções de hubs de notificação. 

* [Como usar um cliente .NET para os Serviços Móveis do Azure]
  <br/>Saiba mais sobre como usar os Serviços Móveis de aplicativos C# do Windows.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-users

[Enviar notificações por push para usuários autenticados]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/

[O que são Hubs de Notificação?]: /pt-br/documentation/articles/notification-hubs-overview/

[Como usar um cliente .NET para os Serviços Móveis do Azure]: /pt-br/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/

<!--HONumber=42-->
