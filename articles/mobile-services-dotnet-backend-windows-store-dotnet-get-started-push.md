<properties 
	pageTitle="Introdução aos hubs de notificação por push usando os serviços móveis de tempo de execução do .NET" 
	description="Saiba como usar serviços móveis do Azure e Hubs de notificação para enviar notificações por push para seu aplicativo da Windows Store." 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="wesmc,ricksal"/>


# Adicionar notificações por push ao seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Este tópico mostra como usar os Serviços Móveis do Azure com um back-end do .NET para enviar notificações por push a um aplicativo do Windows universal. Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure para o projeto universal de início rápido do Windows. Ao concluir, seu serviço móvel enviará uma notificação por push do back-end do .NET usando os Hubs de Notificação sempre que um registro for inserido. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

>[AZURE.NOTE]Este tópico mostra como configurar manualmente as notificações por push usando o Serviço de Notificação do Windows (WNS) para um aplicativo da Windows Store. Você pode usar as ferramentas do Visual Studio 2013 para configurar automaticamente as mesmas notificações por push em um projeto de aplicativo universal. Para obter mais informações, consulte a [versão universal do aplicativo Windows](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push) deste tutorial.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Registrar seu aplicativo com os WNS e configurar os Serviços Móveis](#register)
2. [Atualizar o aplicativo para se registrar para notificações](#update-app)
3. [Atualizar o aplicativo para enviar notificações por push](#update-server)
4. [Habilitar notificações por push para teste local](#local-testing)
3. [Inserir dados para receber notificações por push](#test)

Este tutorial baseia-se no guia de início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis] ou a [Introdução aos dados] para conectar seu projeto ao serviço móvel. 

>[AZURE.NOTE]Este tópico dá suporte aos aplicativos do Windows Phone Store 8.1. Para adicionar notificações por push a um aplicativo Windows Phone 8 ou Windows Phone Silverlight 8.1, consulte esta versão de [Introdução a notificações nos Serviços Móveis](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push).

##<a id="register"></a> Registrar seu aplicativo com o WNS e configurar os Serviços Móveis

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

O serviço móvel e seus aplicativos agora estão configurados para trabalhar com o WNS e os Hubs de Notificação. Em seguida, você atualizará o aplicativo universal do Windows para registrar-se para notificações.

##<a id="update-app"></a> Atualizar o aplicativo para se registrar para notificações

Para que seu aplicativo possa receber notificações por push, você deve registrar um canal de notificação.

1. No Visual Studio, abra o arquivo App.xaml.cs e adicione as seguintes `using` instruções:

        using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

2. Adicione o seguinte método `InitNotificationAsync` à classe de **Aplicativo** para criar um canal de notificação por push e registrar-se para notificações por push: 
	
        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            System.Exception exception = null;
            try
            {
                await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
            }
            catch (System.Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                var dialog = new MessageDialog(exception.Message, "Registering Channel URI");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


    Esse código recupera o ChannelURI do aplicativo nos WNS e registra esse ChannelURI para notificações por push.
    
3. Na parte superior do manipulador de eventos **OnLaunched** no App.xaml.cs, adicione a seguinte chamada para o novo método **InitNotificationsAsync**:

        InitNotificationsAsync();

	Isso garante que o registro seja solicitado sempre que a página for carregada. Em seu aplicativo, convém fazer esse registro periodicamente apenas para garantir que o registro seja atual. 

4. No Visual Studio, abra o arquivo Package.appxmanifest e verifique se **Compatível com toast** é definido como **Sim** na guia **Interface de Usuário do Aplicativo**. Salve o arquivo.

   	![][1]

   	Isso garante que seu aplicativo possa gerar notificações de toast. 

##<a id="update-server"></a> Atualizar o servidor para enviar notificações por push

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Habilitar notificações por push para testes locais

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

##<a id="test"></a> Testar notificações por push no seu aplicativo

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação do aplicativo da Windows Store para usar os Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir o tutorial seguinte, [Enviar notificações por push para usuários autenticados], que permite mostrar como usar marcas para enviar notificações por push de um Serviço Móvel para um único usuário autenticado.

<!--+ [Enviar notificações por push para usuários autenticados]
	<br/>Saiba como usar marcas para enviar notificações por push de um Serviço Móvel para apenas um usuário autenticado.

+ [Enviar notificações de difusão para assinantes]
	<br/>Saiba como os usuários podem registrar e receber notificações por push para categorias de interesse.

+ [Enviar notificações baseadas em modelo aos assinantes]
	<br/>Saiba usar modelos para enviar notificações por push de um Serviço Móvel, sem precisar colocar cargas específicas de plataforma no seu back-end.
-->
Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os serviços móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários de seu aplicativo com diferentes tipos de conta usando serviços móveis.

* [O que são Hubs de Notificação?]
  <br/>Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

* [Depurar aplicativos de Hubs de Notificação](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obter orientação ao solucionar problemas ou ao depurar soluções de hubs de notificação. 

* [Referência conceitual do tutorial do .NET de Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com o .NET.

<!-- Anchors. -->

<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[Introdução aos dados]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Introdução à autenticação]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users

[Enviar notificações por push para usuários autenticados]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[O que são Hubs de Notificação?]: /en-us/documentation/articles/notification-hubs-overview/
[Enviar notificações de transmissão aos assinantes]: /en-us/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Enviar notificações baseadas em modelo aos assinantes]: /en-us/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/


[Referência conceitual do tutorial do .NET de Serviços Móveis]: /en-us/documentation/articles/mobile-services-html-how-to-use-client-library
\n<!--HONumber=42-->
