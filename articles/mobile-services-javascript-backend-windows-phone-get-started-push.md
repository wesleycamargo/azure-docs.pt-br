<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# Introdução às notificações por push nos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações de push para um aplicativo do Windows Phone Silverlight. Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure para o projeto quickstart. Ao concluir, seu serviço móvel enviará uma notificação por push usando Hubs de Notificação sempre que um registro for inserido. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

> [WACOM.NOTE]Este tutorial demonstra a integração dos Serviços Móveis com os Hubs de Notificação que é como você envia notificações por push do seu serviço móvel. Se você estiver usando um serviço móvel antigo que está usando o push herdado e ainda não foi atualizado para usar os Hubs de Notificação, *recomendamos que você atualize* como parte deste tutorial. Se você escolher não atualizar agora, você deve seguir esta versão do tutorial: [Introdução às notificações por push (herdado)][Introdução às notificações por push (herdado)].

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1.  [Atualizar o aplicativo para registrar-se para notificações][Atualizar o aplicativo para registrar-se para notificações]
2.  [Atualizar scripts de servidor para enviar notificações por push][Atualizar scripts de servidor para enviar notificações por push]
3.  [Inserir dados para receber notificações por push][Inserir dados para receber notificações por push]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis] ou a [Introdução aos dados][Introdução aos dados] para conectar seu projeto ao serviço móvel. Quando um serviço móvel não é conectado, o assistente Adicionar Notificação por Push cria essa conexão para você.

> [WACOM.NOTE]Para enviar notificações por push para um aplicativo do Windows Phone 8.1, siga a versão do [aplicativo da Windows Store][aplicativo da Windows Store] deste tutorial.

## <span id="update-app"></span></a> Atualizar o aplicativo para registrar-se para notificações

Para que seu aplicativo possa receber notificações por push, você deve registrar um canal de notificação.

1.  No Visual Studio, abra o arquivo App.xaml.cs e adicione a seguinte instrução `using`:

        using Microsoft.Phone.Notification;

2.  Adicione o seguinte ao App.xaml.cs:

        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }

            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {

                    // Register for notifications using the new channel
                    await MobileService.GetPush()
                        .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                });
        }

    Esse código recupera o ChannelURI do aplicativo do Serviço de Notificação por Push da Microsoft usado pelo Windows Phone 8.x "Silverlight” e depois registra o ChannelURI para notificações por push.

    > [WACOM.NOTE]Neste tutorial, o serviço móvel envia uma notificação do sistema ao dispositivo. Ao enviar uma notificação de bloco, você deve chamar o método **BindToShellTile** no canal.

3.  Na parte superior do manipulador de eventos **Application\_Launching** no App.xaml.cs, adicione a seguinte chamada para o novo método **AcquirePushChannel**:

        AcquirePushChannel();

    Isso garante que o registro seja solicitado sempre que a página for carregada. Em seu aplicativo, convém fazer esse registro periodicamente apenas para garantir que o registro seja atual.

4.  Pressione a tecla **F5** para executar o aplicativo. Uma caixa de diálogo pop-up com a chave do registro é exibida.

5.  No Gerenciador de Soluções, expanda **Propriedades**, abra o arquivo WMAppManifest.xml, clique na guia **Recursos** e verifique se o recurso **ID___CAP___PUSH_NOTIFICATION** está marcado.

    ![][0]

    Isso garante que seu aplicativo possa gerar notificações de toast.

## <span id="update-scripts"></span></a> Atualizar scripts de servidor para enviar notificações por push

Finalmente, você deve atualizar o script registrado para a operação de inserção na tabela TodoItem para enviar notificações.

1.  Clique em **TodoItem**, clique em **Script** e selecione **Inserir**.

    ![][1]

2.  Substitua a função de inserção com o seguinte código e **Salvar**:

        function insert(item, user, request) {
        // Define a payload for the Windows Phone toast notification.
        var payload = '<?xml version="1.0" encoding="utf-8"?>' +
            '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
            '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
            '</wp:Text2></wp:Toast></wp:Notification>';

        request.execute({
            success: function() {
                // If the insert succeeds, send a notification.
                push.mpns.send(null, payload, 'toast', 22, {
                    success: function(pushResponse) {
                        console.log("Sent push:", pushResponse);
                        request.respond();
                        },              
                        error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
                            request.respond(500, { error: pushResponse });
                            }
                        });
                    }
                });      
        }

    Esse script de inserção envia uma notificação por push (com o texto do item inserido) a todos os registros do aplicativo do Windows Phone depois que a inserção é executada com êxito.

3.  Clique na guia **Enviar por Push**, selecione **Habilitar as notificações por push sem autenticação** e, em seguida, clique em **Salvar**.

    > [WACOM.NOTE]Quando você estiver concluindo este tutorial usando uma versão do serviço móvel antiga, você pode ver um link na parte inferior da guia **Push** que diz **Habilitar push aprimorado**. Clique nele agora para atualizar seu serviço móvel para se integrar com os Hubs de Notificação. Esta alteração não pode ser revertida. Para obter detalhes sobre como habilitar as notificações por push aprimoradas em um serviço móvel em produção, consulte [esta orientação][esta orientação].

    ![][2]

    Isso permite que o serviço móvel se conecte ao MPNS no modo não autenticado para enviar notificações por push.

    > [WACOM.NOTE]Este tutorial usa MPNS no modo não autenticado. Nesse modo, o MPNS limita o número de notificações que podem ser enviadas a um canal do dispositivo. Para remover essa restrição, você deve gerar e carregar um certificado clicando em **Carregar** e selecionando o certificado. Para obter mais informações sobre como gerar o certificado, consulte [Configurando um serviço web autenticado para enviar notificações por push ao Windows Phone][Configurando um serviço web autenticado para enviar notificações por push ao Windows Phone].

## <span id="test"></span></a>Testar notificações de push no seu aplicativo

1.  No Visual Studio, pressione F5 para executar o aplicativo.

    > [WACOM.NOTE] Você pode encontrar um RegistrationAuthorizationException 401 Não autorizado ao testar o emulador do Windows Phone. Isto pode ocorrer durante a chamada `RegisterNativeAsync()` devido a forma que o emulador do Windows Phone sincroniza seu relógio com o PC host. Pode resultar em um token de segurança que será rejeitado. Para resolver isto basta definir manualmente o relógio no emulador antes de realizar o teste.

2.  No aplicativo, insira o texto "hello push" na caixa de texto e clique em **Salvar**, depois imediatamente clique no botão iniciar ou botão voltar para sair do aplicativo.

    ![][3]

    Isso enviará uma solicitação de inserção ao serviço móvel para armazenar o item adicionado. Observe que o dispositivo recebe uma notificação do sistema que diz **hello push**.

    ![][4]

    > [WACOM.NOTE]Você não receberá a notificação quando você ainda estiver no aplicativo. Para receber uma notificação do sistema enquanto o aplicativo está ativo, você deverá manipular o evento [ShellToastNotificationReceived][ShellToastNotificationReceived].

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação do aplicativo da Windows Store para usar os Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir um dos seguintes tutoriais:

-   [Enviar notificações em push para usuários autenticados][Enviar notificações em push para usuários autenticados]
    
	Saiba como usar marcas para enviar notificações por push a partir de um Serviço Móvel apenas para um usuário autenticado.

-   [Enviar notificações de transmissão para assinantes][Enviar notificações de transmissão para assinantes]
    
	Saiba como os usuários podem registrar e receber notificações por push para categorias de interesse.

<!---+ [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

	Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

-   [Introdução aos dados][Introdução aos dados]
    
	Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][Introdução à autenticação]
    
	Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

-   [O que são Hubs de Notificação?][O que são Hubs de Notificação?]
    
	Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

-   [Referência conceitual do tutorial do .NET de Serviços Móveis][Referência conceitual do tutorial do .NET de Serviços Móveis]
    
	Saiba mais sobre como usar os Serviços Móveis com o .NET.

-   [Referência de script de servidor dos Serviços Móveis][Referência de script de servidor dos Serviços Móveis]
    
	Saiba mais sobre como implementar a lógica de negócios no seu serviço móvel.

 
 


  [Atualizar o aplicativo para registrar-se para notificações]: #update-app
  [Atualizar scripts de servidor para enviar notificações por push]: #update-scripts
  [Inserir dados para receber notificações por push]: #test
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started
  [Introdução aos dados]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-data
  [aplicativo da Windows Store]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push
  [0]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
  [esta orientação]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png
  [Configurando um serviço web autenticado para enviar notificações por push ao Windows Phone]: http://msdn.microsoft.com/pt-br/library/windowsphone/develop/ff941099(v=vs.105).aspx
  [3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
  [ShellToastNotificationReceived]: http://msdn.microsoft.com/pt-br/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx
  [Enviar notificações em push para usuários autenticados]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users/
  [Enviar notificações de transmissão para assinantes]: /pt-br/documentation/articles/notification-hubs-windows-phone-send-breaking-news/
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-users
  [Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
