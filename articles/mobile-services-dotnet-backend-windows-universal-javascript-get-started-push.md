<properties pageTitle="Get started with push notification using a .NET backend mobile service" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/15/2014" ms.author="glenga"></tags>

# Introdução às notificações por push nos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-push][mobile-services-selector-get-started-push]]

Este tópico mostra como usar os Serviços Móveis do Azure com um back-end do .NET para enviar notificações por push a um aplicativo do Windows universal. Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure em um projeto do aplicativo Windows universal. Ao concluir, seu serviço móvel enviará uma notificação por push do back-end do .NET para todos os aplicativos do Windows Store e Windows Phone Store sempre que um registro é inserido na tabela TodoList. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

> [WACOM.NOTE]Esse tópico lhe mostra como usar as ferramentas no Visual Studio Professional 2013 com atualização 3 para adicionar suporte para notificações por push dos Serviços Móveis para um aplicativo Windows universal. As mesmas etapas podem ser usadas para adicionar notificações por push dos Serviços Móveis para um aplicativo da Windows Store ou Windows Phone Store 8.1. Se você não pode atualizar para o Visual Studio Professional 2013 Atualização 3 ou prefere adicionar manualmente seu projeto de serviço móvel a uma solução de aplicativo da Windows Store, veja [esta versão][esta versão] do tópico.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1.  [Registrar seu aplicativo para notificações por push][Registrar seu aplicativo para notificações por push]
2.  [Atualizar o serviço para enviar notificações por push][Atualizar o serviço para enviar notificações por push]
3.  [Habilitar notificações por push para teste local][Habilitar notificações por push para teste local]
4.  [Testar notificações por push no seu aplicativo][Testar notificações por push no seu aplicativo]

Para concluir este tutorial, você precisará do seguinte:

-   Uma [conta ativa da Microsoft Store][conta ativa da Microsoft Store].
-   [Visual Studio Professional 2013][Visual Studio Professional 2013] (Atualização 3 ou versão posterior).<br/>
    Uma versão de avaliação gratuita está disponível.

## <span id="register"></span></a>Registrar seu aplicativo para notificações por push

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013][mobile-services-create-new-push-vs2013]]

1.  Navegue para a pasta do projeto `\services\mobileServices\scripts`, copie o arquivo de script \<*your\_service\_name*\>.push.register.js gerado na pasta `\js` compartilhada e, em seguida, exclua este arquivos dos projetos Windows individual e aplicativo WindowsPhone.

2.  Abra esse arquivo de script na pasta do projeto `\js` compartilhado, localize o código no ouvinte de eventos *ativado*, que registra o canal URL do dispositivo com o hub de notificação e exclua a função de comprometimento **feita**.

    Esse tutorial envia notificações quando um novo item é inserido, não quando um API personalizado é chamado.

3.  No projeto do aplicativo Windows, abra o arquivo default.html e mude o caminho da referência do arquivo de script para a pasta do projeto `\js` compartilhada, para que se pareça com o seguinte:

        <script src="/js/your_service_name.push.register.js"></script>

4.  Repita essa etapa para o projeto do aplicativo WindowsPhone.

    Agora, ambos os projetos estão usando uma versão compartilhada do script de registro em push.

Agora que as notificações por push estão ativadas no aplicativo, você deve atualizar o serviço móvel para enviar notificações por push.

## <span id="update-service"></span></a>Atualizar o serviço para enviar notificações por push

As seguintes etapas atualizam a classe TodoItemController existente para enviar uma notificação por push para todos os dispositivos registrados quando um novo item é inserido. Você pode implementar código semelhante em [ApiController], [TableController] ou em qualquer lugar nos seus serviços de back-end.

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push][mobile-services-dotnet-backend-update-server-push]]

## <span id="local-testing"></span></a> Habilitar notificações por push para teste local

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013][mobile-services-dotnet-backend-configure-local-push-vs2013]]

As etapas restantes nesta seção são opcionais. Elas permitem que você teste seu aplicativo em relação ao seu serviço móvel em execução em um computador local. Se você planeja testar as notificações por push usando o serviço móvel em execução no Azure, você pode apenas pular para a última seção. Isso ocorre porque o assistente Adicionar Notificação por Push já configurou seu aplicativo para se conectar ao seu serviço em execução no Azure.

> [WACOM.NOTE]Nunca use um serviço móvel de produção para testar e desenvolver o trabalho. Sempre publique seu projeto de serviço móvel para um serviço de preparação para testes.

1.  Navegue para a pasta do projeto `\services\mobileServices\settings`, copie o arquivo de script \<*your\_service\_name*\>.js gerado na pasta de projeto `\js` compartilhada e, em seguida, exclua este arquivo dos projetos do aplicativo Windows individual e WindowsPhone. Também exclua este arquivo da pasta `\services\mobileServices\scripts` em cada projeto de aplicativo, se existir lá também.

2.  Abra este arquivo de script na pasta de projeto `\js` compartilhada e comente o código existente que define o [objeto MobileServiceClient][objeto MobileServiceClient] usado para acessar o serviço móvel em execução no Azure.

3.  Adicione uma nova definição de objeto **MobileServiceClient** com o mesmo nome, mas usando a URL do host local no construtor, selelhante à seguinte:

        // This MobileServiceClient has been configured to communicate with your local
        // test project for debugging purposes.
        var todolistClient = new WindowsAzure.MobileServiceClient(
            "http://localhost:4584");

    Usando o objeto **MobileServiceClient**, o aplicativo se conectará ao serviço local ao invés da versão hospedada no Azure. Quando você quiser retornar e executar o aplicativo em relação ao serviço móvel hospedado no Azure, mude de volta para as definições do objeto **MobileServiceClient** originais.

## <span id="test"></span></a>Testar notificações de push no seu aplicativo

[WACOM.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push][mobile-services-dotnet-backend-windows-universal-test-push]]

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
  [esta versão]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
  [Registrar seu aplicativo para notificações por push]: #register
  [Atualizar o serviço para enviar notificações por push]: #update-service
  [Habilitar notificações por push para teste local]: #local-testing
  [Testar notificações por push no seu aplicativo]: #test
  [conta ativa da Microsoft Store]: http://go.microsoft.com/fwlink/p/?LinkId=280045
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=391934
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push-vs2013]: ../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md
  [objeto MobileServiceClient]: http://msdn.microsoft.com/en-us/library/azure/jj554219.aspx
  [mobile-services-dotnet-backend-windows-universal-test-push]: ../includes/mobile-services-dotnet-backend-windows-universal-test-push.md
  [Enviar notificações por push para usuários autenticados]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/
  [Introdução aos dados]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
  [Introdução à autenticação]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-users
  [O que são Hubs de Notificação?]: /en-us/documentation/articles/notification-hubs-overview/
  [Como usar um cliente HTML/JavaScript para os Serviços Móveis do Azure]: /en-us/documentation/articles/mobile-services-html-how-to-use-client-library
