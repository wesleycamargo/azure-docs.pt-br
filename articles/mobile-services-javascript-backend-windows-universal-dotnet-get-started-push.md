<properties pageTitle="Get started with push notification using a JavaScript backend mobile service" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/11/2014" ms.author="glenga"></tags>

# Introdução às notificações por push nos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-push][mobile-services-selector-get-started-push]]

Este tópico mostra como usar os Serviços Móveis do Azure com um back-end do JavaScript para enviar notificações por push a um aplicativo do Windows universal. Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure em um projeto do aplicativo Windows universal. Ao concluir, seu serviço móvel enviará uma notificação por push do back-end do JavaScript para todos os aplicativos do Windows Store e Windows Phone Store sempre que um registro é inserido na tabela TodoList. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

> [WACOM.NOTE]Esse tópico lhe mostra como usar as ferramentas no Visual Studio 2013 com atualização 3 para adicionar suporte para notificações por push dos Serviços Móveis para um aplicativo Windows universal. As mesmas etapas podem ser usadas para adicionar notificações por push dos Serviços Móveis para um aplicativo da Windows Store ou Windows Phone Store 8.1. Para adicionar notificações por push a um aplicativo Windows Phone 8 ou Windows Phone Silverlight 8.1, consulte esta versão de [Introdução à autenticação dos Serviços Móveis][Introdução à autenticação dos Serviços Móveis].

> Se você não pode atualizar para o Visual Studio 2013 Atualização 3 ou prefere adicionar manualmente seu projeto de serviço móvel a uma solução de aplicativo da Windows Store, veja [esta versão][esta versão] do tópico.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1.  [Registrar seu aplicativo para notificações por push][Registrar seu aplicativo para notificações por push]
2.  [Atualizar o serviço para enviar notificações por push][Atualizar o serviço para enviar notificações por push]
3.  [Testar notificações por push no seu aplicativo][Testar notificações por push no seu aplicativo]

Para concluir este tutorial, você precisará do seguinte:

-   Uma [conta ativa da Microsoft Store][conta ativa da Microsoft Store].
-   [Visual Studio 2013 Express para Windows][Visual Studio 2013 Express para Windows] com atualização 3 ou uma versão posterior

## <span id="register"></span></a>Registrar seu aplicativo para notificações por push

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013][mobile-services-create-new-push-vs2013]]

1.  Navegue na pasta de projeto `\Services\MobileServices\your_service_name`, abra o arquivo de código push.register.cs gerado e inspecione o método **UploadChannel** que registra a URL do canal do dispositivo com o hub de notificação.

2.  Abra o arquivo de código App.xaml.cs compartilhado e avise que uma chamada para o novo método **UploadChannel** foi adicionada no manipulador de evento **OnLaunched**.

    Isso garante a tentativa do registro do dispositivo sempre que o aplicativo for iniciado.

3.  Repita as etapas anteriores para adicionar notificações por push ao projeto do aplicativo Windows Phone Store e, em seguida, no arquivo App.xaml.cs compartilhado, remova a chamada extra para **UploadChannel** e o wrapper condicional `#if...#endif` restante.

    Ambos os projetos podem agora compartilhar uma única chamada para **UploadChannel**.

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Voc&ecirc; tamb&eacute;m pode simplificar o c&oacute;digo gerado ao unificar as defini&ccedil;&otilde;es ajustadas do <code data-inline="1">#if...#endif</code> <a href="http://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> em uma &uacute;nica defini&ccedil;&atilde;o n&atilde;o ajustada usada por ambas as vers&otilde;es do aplicativo.</p></div>

Agora que as notificações por push estão ativadas no aplicativo, você deve atualizar o serviço móvel para enviar notificações por push.

## <span id="update-service"></span></a>Atualizar o serviço para enviar notificações por push

As etapas a seguir mostram a atualização do script de inserção registrado para a tabela TodoItem. Você pode implementar código semelhante em qualquer script de servidor ou em qualquer lugar nos seus serviços de back-end.

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs][mobile-services-javascript-update-script-notification-hubs]]

## <span id="test"></span></a>Testar notificações de push no seu aplicativo

[WACOM.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push][mobile-services-javascript-backend-windows-universal-test-push]]

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação do aplicativo da Windows Store para usar os Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir o tutorial seguinte, [Enviar notificações por push para usuários autenticados][Enviar notificações por push para usuários autenticados], que permite mostrar como usar marcas para enviar notificações por push a partir de um Serviço Móvel apenas para um usuário autenticado.

Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

-   [Introdução aos dados][Introdução aos dados]<br/>
    Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][Introdução à autenticação]<br/>
    Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

-   [O que são Hubs de Notificação?][O que são Hubs de Notificação?]<br/>
    Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

-   [Como usar um cliente .NET para os Serviços Móveis do Azure][Como usar um cliente .NET para os Serviços Móveis do Azure]<br/>
    Saiba mais sobre como usar os Serviços Móveis a partir dos aplicativos do C# do Windows.



  [mobile-services-selector-get-started-push]: ../includes/mobile-services-selector-get-started-push.md
  [Introdução à autenticação dos Serviços Móveis]: /en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push
  [esta versão]: /en-us/documentation/articles/mobile-services-javscript-backend-windows-store-dotnet-get-started-push
  [Registrar seu aplicativo para notificações por push]: #register
  [Atualizar o serviço para enviar notificações por push]: #update-service
  [Testar notificações por push no seu aplicativo]: #test
  [conta ativa da Microsoft Store]: http://go.microsoft.com/fwlink/p/?LinkId=280045
  [Visual Studio 2013 Express para Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [MobileServiceClient]: http://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [mobile-services-javascript-update-script-notification-hubs]: ../includes/mobile-services-javascript-update-script-notification-hubs.md
  [mobile-services-javascript-backend-windows-universal-test-push]: ../includes/mobile-services-javascript-backend-windows-universal-test-push.md
  [Enviar notificações por push para usuários autenticados]: /en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/
  [Introdução aos dados]: /en-us/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
  [Introdução à autenticação]: /en-us/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-users
  [O que são Hubs de Notificação?]: /en-us/documentation/articles/notification-hubs-overview/
  [Como usar um cliente .NET para os Serviços Móveis do Azure]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
