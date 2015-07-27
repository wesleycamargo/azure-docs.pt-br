<properties 
	pageTitle="Use os Serviços Móveis do Azure e os Hubs de Notificação para enviar notificações por push para seu aplicativo do Windows Phone." 
	description="Saiba como usar os Hubs de Notificação com os Serviços Móveis do Azure e para enviar notificações por push para o seu aplicativo Windows Phone." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="wesmc"/>

# Adicionar notificações por push a seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../../includes/mobile-services-selector-get-started-push-legacy.md)]

##Visão geral

Este tópico mostra como usar os Serviços Móveis do Azure com um back-end do .NET para enviar notificações por push a um aplicativo do Windows Phone Silverlight 8 usando o MPNS (Serviço de Notificação por Push da Microsoft). Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure para o projeto quickstart. Ao concluir, seu serviço móvel enviará uma notificação por push usando Hubs de Notificação sempre que um registro for inserido. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

Este tutorial baseia-se no projeto TodoList dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir [Adicionar Serviços Móveis a um aplicativo existente] para conectar seu projeto ao serviço móvel.

>[AZURE.NOTE]Este tutorial é destinado a aplicativos do Windows Phone 8.1 Silverlight. Se, em vez disso, você estiver desenvolvendo um aplicativo do Windows Phone 8.1 Store, consulte a versão do [aplicativo da Windows Store](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push) deste tutorial. Para obter informações sobre os aplicativos do Windows Phone Silverlight e como eles se comparam aos aplicativos do Windows Phone Store, consulte [aplicativos do Windows Phone Silverlight 8.1].

##Atualizar o aplicativo para registrar-se para notificações

Para que seu aplicativo possa receber notificações por push, você deve registrar um canal de notificação.

1. No Visual Studio, abra o arquivo App.xaml.cs e adicione a seguinte instrução `using`:

        using Microsoft.Phone.Notification;

2. Adicione o método `AcquirePushChannel` a seguir à classe `App`:

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

    Este código recupera o URI do canal para o aplicativo, se ele existir. Caso contrário, ele será criado. O URI do canal será aberto e vinculado para notificações de toast. Quando o URI do canal estiver totalmente aberto, o manipulador do método `ChannelUriUpdated` é chamado e o canal é registrado para receber notificações por push. Se o registro falhar, o canal será fechado para que as execuções subsequentes do aplicativo possam tentar o registro novamente. O manipulador `ShellToastNotificationReceived` está configurado para que o aplicativo possa receber e manipular as notificações por push durante a execução.
    
4. No manipulador de eventos `Application_Launching` no App.xaml.cs, adicione a seguinte chamada para o novo método `AcquirePushChannel`:

        AcquirePushChannel();

	Isso garante que o registro seja solicitado sempre que o aplicativo for carregado. Em seu aplicativo, convém fazer esse registro periodicamente apenas para garantir que o registro seja atual.

5. Pressione a tecla **F5** para executar o aplicativo. Uma caixa de diálogo pop-up com a chave do registro é exibida.
  
6. No Visual Studio, abra o arquivo Package.appxmanifest e verifique se **Compatível com Toast**, está definido como **Sim** na guia **Interface do Usuário do Aplicativo**.

   	![][1]

   	Isso garante que seu aplicativo possa gerar notificações de toast.

##Atualizar o servidor para enviar notificações por push

1. No Gerenciador de Soluções do Visual Studio, expanda a pasta **Controladores** no projeto do serviço móvel. Abra TodoItemController.cs e atualize a definição do método `PostTodoItem` com o seguinte código:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            MpnsPushMessage message = new MpnsPushMessage();
            message.XmlPayload = "<?xml version="1.0" encoding="utf-8"?>" +
                "<wp:Notification xmlns:wp="WPNotification">" +
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

2. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu aplicativo.

3. Clique na guia **Enviar por Push**, selecione **Habilitar as notificações por push sem autenticação** e, em seguida, clique em **Salvar**.

   	![Guia push dos Serviços Móveis:](./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png)

	>[AZURE.NOTE]Este tutorial usa MPNS no modo não autenticado. Nesse modo, o MPNS limita o número de notificações que podem ser enviadas a um canal do dispositivo. Para remover essa restrição, você deve gerar e carregar um certificado clicando em <strong>Carregar</strong> e selecionando o certificado. Para saber mais sobre como gerar o certificado, consulte <a href="http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx">Configurar um serviço Web autenticado para enviar notificações por push ao Windows Phone</a>.

Isso permite que o serviço móvel se conecte ao MPNS no modo não autenticado para enviar notificações por push.

##Habilitar notificações por push para teste local

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]


##Testar notificações por push no seu aplicativo

1. No Visual Studio, pressione F5 para executar o aplicativo.

    >[AZURE.NOTE]Você pode encontrar um RegistrationAuthorizationException 401 Não autorizado ao testar o emulador do Windows Phone. Isto pode ocorrer durante a chamada de `RegisterNativeAsync()` devido a forma que o emulador do Windows Phone sincroniza seu relógio com o PC anfitrião. Pode resultar em um token de segurança que será rejeitado. Para resolver isto basta definir manualmente o relógio no emulador antes de realizar o teste.

5. No aplicativo, insira o texto "hello push" na caixa de texto e clique em **Salvar**, depois imediatamente clique no botão iniciar ou botão voltar para sair do aplicativo.

   	![Inserir texto][2]

  	Isso enviará uma solicitação de inserção ao serviço móvel para armazenar o item adicionado. Observe que o dispositivo recebe uma notificação do sistema que diz **hello push**.

	![Notificação por push recebida](./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]Você não receberá a notificação enquanto ainda estiver no aplicativo. Para receber uma notificação do sistema enquanto o aplicativo está ativo, você deverá manipular o evento [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx).

##Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação do aplicativo do Windows Phone para usar os Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir o tutorial seguinte, [Enviar notificações em push para usuários autenticados], que permite mostrar como usar marcas para enviar notificações por push a partir de um Serviço Móvel apenas para um usuário autenticado.

Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis e de Hubs de Notificação:

* [Introdução à autenticação do seu aplicativo] <br/>Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

* [O que são Hubs de Notificação?] <br/>Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

* [Depuração de aplicativos de Hubs de Notificação](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Obtenha diretrizes de solução de problemas e depuração de soluções de Hubs de Notificação.

* [Referência conceitual do tutorial do .NET de Serviços Móveis] <br/>Saiba mais sobre como usar os Serviços Móveis com o .NET.



<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png

<!-- URLs. -->
[Adicionar Serviços Móveis a um aplicativo existente]: mobile-services-dotnet-backend-windows-phone-get-started-data.md
[Introdução à autenticação do seu aplicativo]: mobile-services-dotnet-backend-windows-phone-get-started-users.md

[Enviar notificações em push para usuários autenticados]: mobile-services-dotnet-backend-windows-phone-push-notifications-app-users.md

[O que são Hubs de Notificação?]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-windows-phone-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-windows-phone-send-localized-breaking-news.md


[Referência conceitual do tutorial do .NET de Serviços Móveis]: mobile-services-html-how-to-use-client-library.md
[aplicativos do Windows Phone Silverlight 8.1]: http://msdn.microsoft.com/library/windowsphone/develop/dn642082(v=vs.105).aspx
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO3-->