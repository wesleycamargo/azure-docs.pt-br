<properties pageTitle="Introdução às notificações por push (Windows Store) | Mobile Dev Center" metaKeywords="" description="Saiba como usar os Serviços Móveis e os Hubs de Notificação do Azure para enviar notificações por push ao seu aplicativo da Windows Store." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Introdução às notificações por push nos Serviços Móveis" authors="glenga" solutions="" manager="" editor="" />


# Introdução às notificações por push nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a><a href="/pt-br/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android">Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Back-end do .NET">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações por push a um aplicativo da Windows Store. 
Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure para o projeto quickstart. Ao concluir, seu serviço móvel enviará uma notificação por push usando Hubs de Notificação sempre que um registro for inserido. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

>[WACOM.NOTE]Este tutorial demonstra a integração dos Serviços Móveis com os Hubs de Notificação que, atualmente, estão em modo de visualização. Por padrão, o envio de notificações por push usando os Hubs de Notificação não está habilitado em um back-end do JavaScript.  Depois que o novo Hub de Notificação for criado, o processo de integração não poderá ser revertido. As notificações por push para iOS e Android só estão disponíveis atualmente usando o suporte ao envio por push descrito [nesta versão do tópico](/pt-br/documentation/articles/mobile-services-windows-phone-get-started-push/).

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Atualizar o aplicativo para registrar-se para notificações](#update-app)
2. [Atualizar scripts de servidor para enviar notificações por push](#update-scripts)
3. [Inserir dados para receber notificações por push](#test)

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis] ou a [Introdução aos dados] para conectar seu projeto ao serviço móvel. Quando um serviço móvel não é conectado, o assistente Adicionar Notificação por Push cria essa conexão para você. 

##<a id="update-app"></a> Atualizar o aplicativo para registrar-se para notificações

Para que seu aplicativo possa receber notificações por push, você deve registrar um canal de notificação.

1. No Visual Studio, abra o arquivo App.xaml.cs e adicione a seguinte instrução `using`:

        using Microsoft.Phone.Notification;

3. Adicione o seguinte ao App.xaml.cs:
	
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

    Esse código recupera o ChannelURI do aplicativo nos WNS e registra esse ChannelURI para notificações por push.

	>[WACOM.NOTE]Neste tutorial, o serviço móvel envia uma notificação do sistema ao dispositivo. Ao enviar uma notificação de bloco, você deve chamar o método **BindToShellTile** no canal.

4. Na parte superior do manipulador de eventos **Application_Launching** no App.xaml.cs, adicione a seguinte chamada para o novo método **AcquirePushChannel**:

        AcquirePushChannel();

	Isso garante que o registro seja solicitado sempre que a página for carregada. Em seu aplicativo, convém fazer esse registro periodicamente apenas para garantir que o registro seja atual. 

5. Pressione a tecla **F5** para executar o aplicativo. Uma caixa de diálogo pop-up com a chave do registro é exibida.
  
6.	No Gerenciador de Soluções, expanda **Propriedades**, abra o arquivo WMAppManifest.xml, clique na guia **Recursos** e verifique se o recurso **ID___CAP___PUSH_NOTIFICATION** está marcado.

   	![][1]

   	Isso garante que seu aplicativo possa gerar notificações de toast. 

##<a id="update-scripts"></a> Atualizar scripts de servidor para enviar notificações por push

Finalmente, você deve atualizar o script registrado para a operação de inserção na tabela TodoItem para enviar notificações.

1. Clique em **TodoItem**, clique em **Script** e selecione **Inserir**. 

   	![][10]

2. Substitua a função de inserção pelo seguinte código e clique em **Salvar**:

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

3. Clique na guia **Enviar por Push**, selecione **Habilitar as notificações por push sem autenticação** e, em seguida, clique em **Salvar**.

	![][11]

	Isso permite que o serviço móvel se conecte ao MPNS no modo não autenticado para enviar notificações por push.

	>[WACOM.NOTE]Este tutorial usa MPNS no modo não autenticado. Nesse modo, o MPNS limita o número de notificações que podem ser enviadas a um canal do dispositivo. Para remover essa restrição, você deve gerar e carregar um certificado clicando em **Carregar** e selecionando o certificado. Para obter mais informações sobre como gerar o certificado, consulte [Configurando um serviço web autenticado para enviar notificações por push ao Windows Phone].

##<a id="test"></a>Testar notificações por push no seu aplicativo

1. No Visual Studio, pressione F5 para executar o aplicativo.

5. No aplicativo, insira o texto "hello push" na caixa de texto e, em seguida, clique em **Salvar**.

   	![][4]

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
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários do seu aplicativo com uma conta do Windows.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como registrar e usar scripts de servidor.

* [Referência conceitual do tutorial do .NET dos Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com o .NET.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[10]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
[11]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png

<!-- URLs. -->
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-data
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-users
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-push

[Introdução aos Hubs de Notificação]: /pt-br/manage/services/notification-hubs/getting-started-windows-dotnet/
[O que são Hubs de Notificação?]: /pt-br/develop/net/how-to-guides/service-bus-notification-hubs/
[Enviar notificações para assinantes]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet/
[Enviar notificações para usuários]: /pt-br/manage/services/notification-hubs/notify-users/
[Enviar notificações entre plataformas aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Referência conceitual do tutorial do .NET dos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
[Configurando um serviço web autenticado para enviar notificações por push ao Windows Phone]: http://msdn.microsoft.com/pt-br/library/windowsphone/develop/ff941099(v=vs.105).aspx

