<properties 
	pageTitle="Adicionar notificações por push para seu aplicativo de serviços móveis (Windows Store) | Centro de Desenvolvimento Móvel" 
	description="Saiba como usar serviços móveis do Azure e Hubs de notificação para enviar notificações por push para seu aplicativo da Windows Store." 
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
	ms.date="06/11/2015" 
	ms.author="glenga"/>


# Adicionar notificações por push a seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Visão geral

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações de push para um aplicativo do Windows Phone Silverlight. Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure para o projeto quickstart. Ao concluir, seu serviço móvel enviará uma notificação por push usando Hubs de Notificação sempre que um registro for inserido. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

Este tutorial se baseia no aplicativo de exemplo TodoList. Antes de iniciar este tutorial, primeiro você deverá concluir o tópico [Adicionar Serviços Móveis a um aplicativo existente] para conectar seu projeto ao serviço móvel. Quando um serviço móvel não tiver sido conectado, o assistente Adicionar Notificação por Push poderá criar essa conexão para você.

>[AZURE.NOTE]Para enviar notificações por push para um aplicativo do Windows Phone 8.1, siga a versão do [aplicativo da Windows Store](../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) deste tutorial.

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

    Esse código recupera o ChannelURI do aplicativo do Serviço de Notificação por Push da Microsoft usado pelo Windows Phone 8.x "Silverlight” e depois registra o ChannelURI para notificações por push.

	>[AZURE.NOTE]Neste tutorial, o serviço móvel envia uma notificação de toast ao dispositivo. Ao enviar uma notificação de bloco, você deve chamar o método **BindToShellTile** no canal.

4. Na parte superior do manipulador de eventos **Application_Launching** no App.xaml.cs, adicione a seguinte chamada para o novo método **AcquirePushChannel**:

        AcquirePushChannel();

	Isso garante que o registro seja solicitado sempre que a página for carregada. Em seu aplicativo, convém fazer esse registro periodicamente apenas para garantir que o registro seja atual.

5. Pressione a tecla **F5** para executar o aplicativo. Uma caixa de diálogo pop-up com a chave do registro é exibida.
  
6.	No Gerenciador de Soluções, expanda **Propriedades**, abra o arquivo WMAppManifest.xml, clique na guia **Recursos** e verifique se o recurso **ID_CAP_PUSH_NOTIFICATION** está marcado.

   	![Habilitar notificações no VS](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png)

   	Isso garante que seu aplicativo possa gerar notificações de toast.

##<a id="update-scripts"></a> Atualizar scripts de servidor para enviar notificações por push

Finalmente, você deve atualizar o script registrado para a operação de inserção na tabela TodoItem para enviar notificações.

1. Clique em **TodoItem**, clique em **Script** e selecione **Inserir**. 

2. Substitua a função de inserção com o seguinte código e **Salvar**:

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

	Isso permite que o serviço móvel se conecte ao MPNS no modo não autenticado para enviar notificações por push.

	>[AZURE.NOTE]Este tutorial usa MPNS no modo não autenticado. Nesse modo, o MPNS limita o número de notificações que podem ser enviadas a um canal do dispositivo. Para remover essa restrição, você deve gerar e carregar um certificado clicando em **Carregar** e selecionando o certificado. Para obter mais informações sobre como gerar o certificado, consulte [Configurando um serviço web autenticado para enviar notificações por push ao Windows Phone].

##<a id="test"></a> Testar notificações por push no seu aplicativo

1. No Visual Studio, pressione F5 para executar o aplicativo.

    >[AZURE.NOTE]Você pode encontrar um RegistrationAuthorizationException 401 Não autorizado ao testar o emulador do Windows Phone. Isto pode ocorrer durante a chamada de `RegisterNativeAsync()` devido a forma que o emulador do Windows Phone sincroniza seu relógio com o PC anfitrião. Pode resultar em um token de segurança que será rejeitado. Para resolver isto basta definir manualmente o relógio no emulador antes de realizar o teste.

5. No aplicativo, insira o texto "hello push" na caixa de texto e clique em **Salvar**, depois imediatamente clique no botão iniciar ou botão voltar para sair do aplicativo.

   	![Digite o texto no aplicativo](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png)

  	Isso enviará uma solicitação de inserção ao serviço móvel para armazenar o item adicionado. Observe que o dispositivo recebe uma notificação do sistema que diz **hello push**.

	![Notificação recebida](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]Você não receberá a notificação enquanto ainda estiver no aplicativo. Para receber uma notificação do sistema enquanto o aplicativo está ativo, você deverá manipular o evento [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx).

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação do aplicativo da Windows Store para usar os Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir um dos seguintes tutoriais:

+ [Enviar notificações em push para usuários autenticados](mobile-services-javascript-backend-windows-phone-push-notifications-app-users.md) <br/>Saiba como usar marcas para enviar notificações por push a partir de um Serviço Móvel apenas para um usuário autenticado.

+ [Enviar notificações de transmissão para assinantes](../notification-hubs-windows-phone-send-breaking-news.md)  
<br/>Saiba como os usuários podem registrar e receber notificações por push para categorias de interesse.

+ [Enviar notificações independentes de plataformas para assinantes](../notification-hubs-aspnet-cross-platform-notify-users.md) <br/>Saiba usar modelos para enviar notificações por push do seu serviço móvel, sem precisar colocar cargas específicas de plataforma no seu back-end.


Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

* [Hubs de Notificação do Azure - Diretrizes de diagnóstico](../notification-hubs-diagnosing.md) <br/>Aprenda a solucionar seus problemas com notificações por push.

* [Introdução à autenticação] <br/>Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

* [O que são Hubs de Notificação?] <br/>Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

* [Referência conceitual do tutorial dos Serviços Móveis em .NET] <br/>Saiba mais sobre como usar os Serviços Móveis com o .NET.

* [Referência de script de servidor dos Serviços Móveis] <br/>Saiba mais sobre como implementar a lógica de negócios no seu serviço móvel.

<!-- Anchors. -->

<!-- Images. -->


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Adicionar Serviços Móveis a um aplicativo existente]: mobile-services-windows-phone-get-started-data.md
[Introdução à autenticação]: mobile-services-windows-phone-get-started-users.md

[Configurando um serviço web autenticado para enviar notificações por push ao Windows Phone]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx

[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Referência conceitual do tutorial dos Serviços Móveis em .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md

[O que são Hubs de Notificação?]: ../notification-hubs-overview.md

 

<!---HONumber=July15_HO4-->