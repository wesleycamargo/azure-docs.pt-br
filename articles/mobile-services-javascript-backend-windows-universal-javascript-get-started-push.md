<properties pageTitle="Get started with push notification using a JavaScript backend mobile service" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile-services,notification-hubs" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/15/2014" ms.author="glenga" />

# Introdução às notificações por push nos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Este tópico mostra como usar os Serviços Móveis do Azure com um back-end do JavaScript para enviar notificações por push a um aplicativo do Windows universal. Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure em um projeto do aplicativo Windows universal. Ao concluir, seu serviço móvel enviará uma notificação por push do back-end do JavaScript para todos os aplicativos do Windows Store e Windows Phone Store sempre que um registro é inserido na tabela TodoList. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

> [WACOM.NOTE]Esse tópico lhe mostra como usar as ferramentas no Visual Studio 2013 com atualização 3 para adicionar suporte para notificações por push dos Serviços Móveis para um aplicativo Windows universal. As mesmas etapas podem ser usadas para adicionar notificações por push dos Serviços Móveis para um aplicativo da Windows Store ou Windows Phone Store 8.1. Se você não pode atualizar para o Visual Studio 2013 Atualização 3 ou prefere adicionar manualmente seu projeto de serviço móvel a uma solução de aplicativo da Windows Store, veja [esta versão][esta versão] do tópico.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1.  [Registrar seu aplicativo para notificações por push][Registrar seu aplicativo para notificações por push]
2.  [Atualizar o serviço para enviar notificações por push][Atualizar o serviço para enviar notificações por push]
3.  [Testar notificações por push no seu aplicativo][Testar notificações por push no seu aplicativo]

Para concluir este tutorial, você precisará do seguinte:

-   Uma [conta ativa da Microsoft Store][conta ativa da Microsoft Store].
-   [Visual Studio 2013 Express para Windows][Visual Studio 2013 Express para Windows] com atualização 3 ou uma versão posterior

## <span id="register"></span></a>Registrar seu aplicativo para notificações por push

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  Navegue para a pasta do projeto `\services\mobileServices\scripts`, copie o arquivo de script \<*your\_service\_name*\>.push.register.js gerado na pasta `\js` compartilhada e, em seguida, exclua este arquivos dos projetos Windows individual e aplicativo WindowsPhone.

2.  Abra esse arquivo de script na pasta do projeto `\js` compartilhado, localize o código no ouvinte de eventos *ativado*, que registra o canal URL do dispositivo com o hub de notificação e exclua a função de comprometimento **feita**.

    Esse tutorial envia notificações quando um novo item é inserido, não quando um API personalizado é chamado.

3.  No projeto do aplicativo Windows, abra o arquivo default.html e mude o caminho da referência do arquivo de script para a pasta do projeto `\js` compartilhada, para que se pareça com o seguinte:

        <script src="/js/your_service_name.push.register.js"></script>

4.  Repita essa etapa para o projeto do aplicativo WindowsPhone.

    Agora, ambos os projetos estão usando uma versão compartilhada do script de registro em push.

Agora que as notificações por push estão ativadas no aplicativo, você deve atualizar o serviço móvel para enviar notificações por push.

## <span id="update-service"></span></a>Atualizar o serviço para enviar notificações por push

As etapas a seguir mostram a atualização do script de inserção registrado para a tabela TodoItem. Você pode implementar código semelhante em qualquer script de servidor ou em qualquer lugar nos seus serviços de back-end.

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

## <span id="test"></span></a>Testar notificações de push no seu aplicativo

[WACOM.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação dos aplicativos da Windows Store para usar os Serviços Míveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir o tutorial seguinte, [Enviar notificações por push para usuários autenticados][Enviar notificações por push para usuários autenticados], que permite mostrar como usar marcas para enviar notificações por push a partir de um Serviço Móvel apenas para um usuário autenticado.

Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

-   [Introdução aos dados][Introdução aos dados]<br/>
    Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][Introdução à autenticação]<br/>
    Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

-   [O que são Hubs de Notificação?][O que são Hubs de Notificação?]<br/>
    Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

-   [Como usar um cliente HTML/JavaScript para os Serviços Móveis do Azure][Como usar um cliente HTML/JavaScript para os Serviços Móveis do Azure]<br/>
    Saiba mais sobre como usar os Serviços Móveis dos aplicativos HTML e JavaScript.



  [mobile-services-selector-get-started-push]: ../includes/mobile-services-selector-get-started-push.md
  [esta versão]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
  [Registrar seu aplicativo para notificações por push]: #register
  [Atualizar o serviço para enviar notificações por push]: #update-service
  [Testar notificações por push no seu aplicativo]: #test
  [conta ativa da Microsoft Store]: http://go.microsoft.com/fwlink/p/?LinkId=280045
  [Visual Studio 2013 Express para Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [mobile-services-javascript-update-script-notification-hubs]: ../includes/mobile-services-javascript-update-script-notification-hubs.md
  [mobile-services-javascript-backend-windows-universal-test-push]: ../includes/mobile-services-javascript-backend-windows-universal-test-push.md
  [Enviar notificações por push para usuários autenticados]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/
  [Introdução aos dados]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-users
  [Como usar um cliente HTML/JavaScript para os Serviços Móveis do Azure]: /pt-br/documentation/articles/mobile-services-html-how-to-use-client-library
