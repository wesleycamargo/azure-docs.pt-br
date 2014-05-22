<properties pageTitle="Introdução aos hubs de notificação por push usando os Serviços Móveis do tempo de execução do .NET" metaKeywords="" description="Saiba como usar os Serviços Móveis do tempo de execução do .NET do Azure para enviar notificações por push a seu aplicativo da Windows Store." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Introdução às notificações por push nos Serviços Móveis" authors="wesmc" solutions="" manager="" editor="" />


# Introdução às notificações por push nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push" title="C# da Windows Store" class="current">C# da Windows Store#</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push" title="JavaScript da Windows Store">JavaScript da Windows Store</a><!--<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>--></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push" title="Back-end do .NET" class="current">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/"  title="Back-end do JavaScript">Back-end do JavaScript</a></div>

Este tópico mostra como usar os Serviços Móveis do Azure com um back-end do .NET para enviar notificações por push a um aplicativo da Windows Store. Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure para o projeto quickstart. Ao concluir, seu serviço móvel enviará uma notificação por push do back-end do .NET usando os Hubs de Notificação sempre que um registro for inserido. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

>[WACOM.NOTE]A integração dos Serviços Móveis com os Hubs de Notificação está no modo de visualização e disponível apenas para as plataformas Windows. Enquanto isso, você ainda pode enviar notificações por push para dispositivos Android e iOS do seu serviço de back-end do .NET usando o hub de notificação conectado, conforme demonstrado em **Introdução aos Hubs de notificação** ([iOS](/pt-br/documentation/articles/notification-hubs-ios-get-started)/[Android](/pt-br/documentation/articles/notification-hubs-android-get-started)). 

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Registrar seu aplicativo com os WNS e configurar os Serviços Móveis](#register)
2. [Atualizar o aplicativo para registrar-se para notificações](#update-app)
3. [Atualizar o servidor para enviar notificações por push](#update-server)
3. [Inserir dados para receber notificações por push](#test)

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis] ou a [Introdução aos dados] para conectar seu projeto ao serviço móvel. Quando um serviço móvel não é conectado, o assistente Adicionar Notificação por Push cria essa conexão para você. 

##<a id="register"></a> Registrar seu aplicativo com os WNS e configurar os Serviços Móveis

[WACOM.INCLUDE [mobile-services-javascript-backend-register-windows-store-app](../includes/mobile-services-javascript-backend-register-windows-store-app.md)]

O serviço móvel e seu aplicativo agora estão configurados para trabalhar com os WNS e os Hubs de Notificação. Em seguida, você irá atualizar o aplicativo da Windows Store para registrar-se para notificações.

##<a id="update-app"></a> Atualizar o aplicativo para registrar-se para notificações

Para que seu aplicativo possa receber notificações por push, você deve registrar um canal de notificação.

1. No Visual Studio, abra o arquivo App.xaml.cs e adicione a seguinte instrução `using`:

        using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

2. Adicione o seguinte método `InitNotificationAsync` à classe **App** para criar um canal de notificação por push e registrar-se para notificações por push: 
	
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

4. No Visual Studio, abra o arquivo Package.appxmanifest e verifique se **Compatível com Toast**, está definido como **Sim** na guia **Interface do Usuário do Aplicativo**. Salve o arquivo.

   	![][1]

   	Isso garante que seu aplicativo possa gerar notificações de toast. 

##<a id="update-server"></a> Atualizar o servidor para enviar notificações por push


[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="test"></a>Testar notificações por push no seu aplicativo

1. No Visual Studio, pressione F5 para executar o aplicativo.

2. No aplicativo, digite um texto em **Inserir um TodoItem** e clique em **Salvar**.

   	![][2]

   	Observe que, após a inserção, o aplicativo recebe uma notificação por push dos WNS.

   	![][3]

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação de um aplicativo da Windows Store para trabalhar com dados nos Serviços Móveis. Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

+ [Introdução aos Hubs de Notificação]
  <br/>Saiba como aproveitar os Hubs de Notificação no seu aplicativo da Windows Store.

+ [Enviar notificações para assinantes]
	<br/>Saiba como os usuários podem registrar e receber notificações por push para categorias de interesse.

+ [Enviar notificações para usuários]
	<br/>Saiba como enviar as notificações por push de um Serviço Móvel para usuários específicos em qualquer dispositivo.

+ [Enviar notificações entre plataformas aos usuários]
	<br/>Saiba usar modelos para enviar notificações por push de um Serviço Móvel, sem precisar colocar cargas específicas de plataforma no seu back-end.

Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis do tempo de execução do .NET.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários de seu aplicativo com diferentes tipos de conta usando os Serviços Móveis do tempo de execução do .NET.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como registrar e usar scripts de servidor.

* [Referência conceitual do tutorial do .NET dos Serviços Móveis]
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
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-push

[Introdução aos Hubs de Notificação]: /pt-br/manage/services/notification-hubs/getting-started-windows-dotnet/
[O que são Hubs de Notificação?]: /pt-br/develop/net/how-to-guides/service-bus-notification-hubs/
[Enviar notificações para assinantes]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet/
[Enviar notificações para usuários]: /pt-br/manage/services/notification-hubs/notify-users/
[Enviar notificações entre plataformas aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Referência conceitual do tutorial do .NET dos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library

