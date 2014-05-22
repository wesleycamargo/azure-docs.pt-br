<properties pageTitle="Introdução aos hubs de notificação por push usando os serviços móveis do tempo de execução do .NET" metaKeywords="" description="Saiba como usar os serviços móveis do tempo de execução do .NET do Azure para enviar notificações por push a seu aplicativo do Windows Phone." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Introdução às notificações por push nos Serviços Móveis" authors="wesmc"  solutions="" writer="wesmc" manager="" editor=""  />


# Introdução às notificações por push nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push" title="C# da Windows Store">C# da Windows Store</a>
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push" title="JavaScript da Windows Store">JavaScript da Windows Store</a>
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push" title="Back-end do .NET" class="current">Back-end do .NET</a> | 
	<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/"  title="Back-end do JavaScript">Back-end do JavaScript</a>
</div>

Este tópico mostra como usar os Serviços Móveis do tempo de execução do .NET do Azure para enviar notificações por push para um aplicativo do Windows Phone 8. 
Neste tutorial, você habilita notificações por push usando os Hubs de Notificação do Azure para o projeto quickstart. Ao concluir, seu serviço móvel do tempo de execução do .NET enviará uma notificação por push usando Hubs de Notificação sempre que um registro for inserido. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Atualizar o aplicativo para registrar-se para notificações](#update-app)
3. [Atualizar o servidor para enviar notificações por push](#update-server)
3. [Inserir dados para receber notificações por push](#test)

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis] ou a [Introdução aos dados] para conectar seu projeto ao serviço móvel. 

##<a id="update-app"></a> Atualizar o aplicativo para registrar-se para notificações

Para que seu aplicativo possa receber notificações por push, você deve registrar um canal de notificação.

1. No Visual Studio, abra o arquivo App.xaml.cs e adicione a seguinte instrução `using`:

        using Microsoft.Phone.Notification;

2. Adicione o seguinte método `AcquirePushChannel` à classe `App`: 
	
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

    Este código recupera o URI do canal para o aplicativo, se ele existir. Caso contrário, ele será criado. O URI do canal será aberto e vinculado para notificações do sistema. Depois que o URI do canal está totalmente aberto, o manipulador do método `ChannelUriUpdated` é chamado e o canal é registrado para notificações por push recebidas. Se o registro falhar, o canal será fechado para que as execuções subsequentes do aplicativo possam tentar o registro novamente. O manipulador de `ShellToastNotificationReceived` está configurado para que o aplicativo possa receber e manipular as notificações por push durante a execução.
    
4. No manipulador de eventos `Application_Launching` no App.xaml.cs, adicione a seguinte chamada ao novo método `AcquirePushChannel`:

        AcquirePushChannel();

	Isso garante que o registro seja solicitado sempre que o aplicativo for carregado. Em seu aplicativo, convém fazer esse registro periodicamente apenas para garantir que o registro seja atual. 

5. Pressione a tecla **F5** para executar o aplicativo. Uma caixa de diálogo pop-up com a chave do registro é exibida.
  
6. No Visual Studio, abra o arquivo Package.appxmanifest e verifique se **Compatível com Toast**, está definido como **Sim** na guia **Interface do Usuário do Aplicativo**.

   	![][1]

   	Isso garante que seu aplicativo possa gerar notificações de toast. 

##<a id="update-server"></a> Atualizar o servidor para enviar notificações por push

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

<ol start="2">
<li><p>Faça logon no <a href=" https://manage.windowsazure.com/" target="_blank">Portal de Gerenciamento do Azure</a>, clique em <strong>Serviços Móveis</strong> e, em seguida, clique em seu aplicativo.</p></li>

<li><p>Clique na guia <strong>Enviar por Push</strong>, selecione <strong>Habilitar as notificações por push sem autenticação</strong> e, em seguida, clique em <strong>Salvar</strong>.</p>

</li>
</ol>

   ![][4]

>[WACOM.NOTE]Este tutorial usa MPNS no modo não autenticado. Nesse modo, o MPNS limita o número de notificações que podem ser enviadas a um canal do dispositivo. Para remover essa restrição, você deve gerar e carregar um certificado clicando em <strong>Carregar</strong> e selecionando o certificado. Para obter mais informações sobre como gerar o certificado, consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsphone/develop/ff941099(v=vs.105).aspx">Configurando um serviço web autenticado para enviar notificações por push ao Windows Phone</a>.

Isso permite que o serviço móvel se conecte ao MPNS no modo não autenticado para enviar notificações por push.

##<a id="test"></a>Testar notificações por push no seu aplicativo

1. No Visual Studio, pressione F5 para executar o aplicativo.

5. No aplicativo, insira o texto "hello push" na caixa de texto e, em seguida, clique em **Salvar**.

   	![][2]

  	Isso enviará uma solicitação de inserção ao serviço móvel para armazenar o item adicionado. Observe que o aplicativo recebe uma notificação por push que diz **hello push**.

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


[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png

<!-- URLs. -->
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-store-get-started
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
[Get started with push notifications]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-push

[Introdução aos Hubs de Notificação]: /pt-br/manage/services/notification-hubs/getting-started-windows-dotnet/
[O que são Hubs de Notificação?]: /pt-br/develop/net/how-to-guides/service-bus-notification-hubs/
[Enviar notificações para assinantes]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet/
[Enviar notificações para usuários]: /pt-br/manage/services/notification-hubs/notify-users/
[Enviar notificações entre plataformas aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Referência conceitual do tutorial do .NET dos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library

