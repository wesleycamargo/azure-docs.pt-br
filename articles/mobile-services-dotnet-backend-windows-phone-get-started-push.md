<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Windows Azure .Net runtime mobile services and Notification Hubs to send push notifications to your Windows phone app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc"  solutions="" writer="wesmc" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Introdução às notificações por push nos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Este tópico mostra como usar os Serviços Móveis do Azure com um back-end do .NET para enviar notificações por push para um aplicativo do Windows Phone Silverlight 8. Neste tutorial, você habilita notificações por push usando os Hubs de Notificação do Windows Azure para o projeto quickstart. Ao concluir, seu serviço móvel enviará uma notificação por push usando Hubs de Notificação sempre que um registro for inserido. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1.  [Atualizar o aplicativo para registrar-se para notificações][Atualizar o aplicativo para registrar-se para notificações]
2.  [Atualizar o servidor para enviar notificações por push][Atualizar o servidor para enviar notificações por push]
3.  [Habilitar notificações por push para teste local][Habilitar notificações por push para teste local]
4.  [Inserir dados para receber notificações por push][Inserir dados para receber notificações por push]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis] ou a [Introdução aos dados][Introdução aos dados] para conectar seu projeto ao serviço móvel.

> [WACOM.NOTE]Este tutorial é destinado para aplicativos do Windows Phone 8.1 "Silverlight”. Se, em vez disso, você estiver desenvolvendo um aplicativo do Windows Phone 8.1 Store, consulte a versão do [aplicativo da Windows Store][aplicativo da Windows Store] deste tutorial. Para obter informações sobre os aplicativos do Windows Phone Silverlight e como eles se comparam aos aplicativos do Windows Phone Store, consulte [aplicativos do Windows Phone Silverlight 8.1][aplicativos do Windows Phone Silverlight 8.1].

## <span id="update-app"></span></a> Atualizar o aplicativo para registrar-se para notificações

Para que seu aplicativo possa receber notificações por push, você deve registrar um canal de notificação.

1.  No Visual Studio, abra o arquivo App.xaml.cs e adicione a seguinte instrução `using`:

        using Microsoft.Phone.Notification;

2.  Adicione o método `AcquirePushChannel` a seguir para a classe `App`:

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
                    System.Exception exception = null;
                    try
                    {
                        await MobileService.GetPush()
                            .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                    }
                    catch (System.Exception ex)
                    {
                        CurrentChannel.Close();
                        exception = ex;
                    }
                    if (exception != null)
                    {
                        Deployment.Current.Dispatcher.BeginInvoke(() =>
                        {
                            MessageBox.Show(exception.Message, 
                                            "Registering for Push Notifications",
                                            MessageBoxButton.OK);
                        });
                    }
            });
            CurrentChannel.ShellToastNotificationReceived += 
                new EventHandler<NotificationEventArgs>((o, args) =>
                {
                    string message = "";
                    foreach (string key in args.Collection.Keys)
                    {
                        message += key + " : " + args.Collection[key] + ", ";
                    }
                    Deployment.Current.Dispatcher.BeginInvoke(() =>
                    {
                        MessageBox.Show(message);
                    });
            });
        }

    Este código recupera o URI do canal para o aplicativo, se ele existir. Caso contrário, ele será criado. O URI do canal será aberto e vinculado para notificações do sistema. Depois que o URI do canal está totalmente aberto, o manipulador do método `ChannelUriUpdated` é chamado e o canal é registrado para notificações por push recebidas. Se o registro falhar, o canal será fechado para que as execuções subsequentes do aplicativo possam tentar o registro novamente. O manipulador `ShellToastNotificationReceived` está configurado para que o aplicativo possa receber e manipular as notificações por push durante a execução.

3.  No manipulador de eventos `Application_Launching` no App.xaml.cs, adicione a seguinte chamada para o novo método `AcquirePushChannel`:

        AcquirePushChannel();

    Isso garante que o registro seja solicitado sempre que o aplicativo for carregado. Em seu aplicativo, convém fazer esse registro periodicamente apenas para garantir que o registro seja atual.

4.  Pressione a tecla **F5** para executar o aplicativo. Uma caixa de diálogo pop-up com a chave do registro é exibida.

5.  No Visual Studio, abra o arquivo Package.appxmanifest e verifique se **Compatível com Toast**, está definido como **Sim** na guia **Interface do Usuário do Aplicativo**.

    ![][0]

    Isso garante que seu aplicativo possa gerar notificações de toast.

## <span id="update-server"></span></a>Atualizar o servidor para enviar notificações por push

1.  No Gerenciador de Soluções do Visual Studio, expanda a pasta **Controladores** no projeto do serviço móvel. Abra TodoItemController.cs e atualize a definição do método `PostTodoItem` pelo seguinte código:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            MpnsPushMessage message = new MpnsPushMessage();
            message.XmlPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>" + item.Text + "</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Esse código irá enviar uma notificação por push (com o texto do item inserido) depois de inserir um item de tarefa pendente. Se ocorrer um erro, o código adicionará uma entrada no log de erros que pode ser visualizada na guia **Logs** do serviço móvel no Portal de Gerenciamento.

2.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu aplicativo.

3.  Clique na guia **Enviar por Push**, selecione **Habilitar as notificações por push sem autenticação** e, em seguida, clique em **Salvar**.

    ![][1]

    > [WACOM.NOTE]Este tutorial usa MPNS no modo não autenticado. Nesse modo, o MPNS limita o número de notificações que podem ser enviadas a um canal do dispositivo. Para remover essa restrição, você deve gerar e carregar um certificado clicando em **Carregar** e selecionando o certificado. Para obter mais informações sobre como gerar o certificado, consulte [Configurando um serviço web autenticado para enviar notificações por push ao Windows Phone][Configurando um serviço web autenticado para enviar notificações por push ao Windows Phone].

Isso permite que o serviço móvel se conecte ao MPNS no modo não autenticado para enviar notificações por push.

## <span id="local-testing"></span></a> Habilitar notificações por push para teste local

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## <span id="test"></span></a>Testar notificações de push no seu aplicativo

1.  No Visual Studio, pressione F5 para executar o aplicativo.

    > [WACOM.NOTE] Você pode encontrar um RegistrationAuthorizationException 401 Não autorizado ao testar o emulador do Windows Phone. Isto pode ocorrer durante a chamada `RegisterNativeAsync()` devido a forma que o emulador do Windows Phone sincroniza seu relógio com o PC host. Pode resultar em um token de segurança que será rejeitado. Para resolver isto basta definir manualmente o relógio no emulador antes de realizar o teste.

2.  No aplicativo, insira o texto "hello push" na caixa de texto e clique em **Salvar**, depois imediatamente clique no botão iniciar ou botão voltar para sair do aplicativo.

    ![][2]

    Isso enviará uma solicitação de inserção ao serviço móvel para armazenar o item adicionado. Observe que o dispositivo recebe uma notificação do sistema que diz **hello push**.

    ![][3]

    > [WACOM.NOTE]Você não receberá a notificação quando você ainda estiver no aplicativo. Para receber uma notificação do sistema enquanto o aplicativo está ativo, você deverá manipular o evento [ShellToastNotificationReceived][ShellToastNotificationReceived].

## <a name="next-steps">Próximas etapas</a>

Este tutorial demonstrou as noções básicas sobre a ativação do aplicativo do Windows Phone para usar os Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir o tutorial seguinte, [Enviar notificações em push para usuários autenticados][Enviar notificações em push para usuários autenticados], que permite mostrar como usar marcas para enviar notificações por push a partir de um Serviço Móvel apenas para um usuário autenticado.

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in. -->

Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis e de Hubs de Notificação:

-   [Introdução aos dados][Introdução aos dados]
    
	Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][Introdução à autenticação]
    
	Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

-   [O que são Hubs de Notificação?][O que são Hubs de Notificação?]
    
	Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

-   [Referência conceitual do tutorial do .NET de Serviços Móveis][Referência conceitual do tutorial do .NET de Serviços Móveis]
    
	Saiba mais sobre como usar os Serviços Móveis com o .NET.

 
 


  [Atualizar o aplicativo para registrar-se para notificações]: #update-app
  [Atualizar o servidor para enviar notificações por push]: #update-server
  [Habilitar notificações por push para teste local]: #local-testing
  [Inserir dados para receber notificações por push]: #test
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
  [Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [aplicativo da Windows Store]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [aplicativos do Windows Phone Silverlight 8.1]: http://msdn.microsoft.com/pt-br/library/windowsphone/develop/dn642082(v=vs.105).aspx
  [0]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png
  [Configurando um serviço web autenticado para enviar notificações por push ao Windows Phone]: http://msdn.microsoft.com/pt-br/library/windowsphone/develop/ff941099(v=vs.105).aspx
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
  [ShellToastNotificationReceived]: http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx
  [Enviar notificações em push para usuários autenticados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users/
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
  [Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/documentation/articles/mobile-services-html-how-to-use-client-library
