<properties 
	pageTitle="Introdução às notificações por push (Windows Store) | Centro de Desenvolvimento de Serviços Móveis" 
	description="Saiba como usar serviços móveis do Azure e Hubs de notificação para enviar notificações por push para seu aplicativo da Windows Store." 
	services="mobile-services, notification-hubs" 
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
	ms.date="09/24/2014" 
	ms.author="glenga"/>


# Adicionar notificações por push ao seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações de push para um aplicativo do Windows Phone Silverlight. Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure para o projeto de início rápido. Ao concluir, seu serviço móvel enviará uma notificação por push usando Hubs de Notificação sempre que um registro for inserido. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Atualizar o aplicativo para se registrar para notificações](#update-app)
2. [Atualizar scripts de servidor para enviar notificações por push](#update-scripts)
3. [Inserir dados para receber notificações por push](#test)

Este tutorial baseia-se no Guia de início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis] ou a [Introdução aos dados] para conectar seu projeto ao serviço móvel. Quando um serviço móvel não é conectado, o assistente Adicionar Notificação por Push cria essa conexão para você. 

>[AZURE.NOTE]Para enviar notificações por push para um aplicativo da Windows Phone 8.1 Store, siga a versão [aplicativo da Windows Store](/ pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push) deste tutorial.

##<a id="update-app"></a> Atualizar o aplicativo para se registrar para notificações

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

    Esse código recupera o ChannelURI do aplicativo do Serviço de Notificação por Push da Microsoft usado pelo Windows Phone 8.x "Silverlight" e depois registra o ChannelURI para notificações por push.

	>[AZURE.NOTE]Neste tutorial, o serviço móvel envia uma notificação de toast ao dispositivo. Ao enviar uma notificação de bloco, você deve chamar o método **BindToShellTile** no canal.

4. Na parte superior do manipulador de eventos **Application_Launching** no App.xaml.cs, adicione a seguinte chamada ao novo método **AcquirePushChannel**:

        AcquirePushChannel();

	Isso garante que o registro seja solicitado sempre que a página for carregada. Em seu aplicativo, convém fazer esse registro periodicamente apenas para garantir que o registro seja atual. 

5. Pressione a tecla **F5** para executar o aplicativo. Uma caixa de diálogo pop-up com a chave do registro é exibida.
  
6.	No Gerenciador de Soluções, expanda **Propriedades**, abra o arquivo WMAppManifest.xml, clique na guia **Recursos** e verifique se a funcionalidade **ID___CAP___PUSH_NOTIFICATION** está marcada.

   	![][1]

   	Isso garante que seu aplicativo possa gerar notificações de toast. 

##<a id="update-scripts"></a> Atualizar scripts de servidor para enviar notificações por push

Finalmente, você deve atualizar o script registrado para a operação de inserção na tabela TodoItem para enviar notificações.

1. Clique em **TodoItem**, clique em **Script** e selecione **Inserir**. 

   	![][10]

2. Substitua a função de inserção pelo código a seguir, e clique em **Salvar**:

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

3. Clique na guia **Push**, marque **Habilitar notificações por push sem autenticação** e, em seguida, clique em **Salvar**.

	>[AZURE.NOTE]Quando você estiver concluindo este tutorial usando um serviço móvel mais antigo, é possível que você veja um link na parte inferior da guia **Push** que diz **Habilitar Push Aprimorado**. Clique aqui agora para atualizar seu serviço móvel para se integrar com Hubs de Notificação. Essa alteração não pode ser revertida. Para obter detalhes sobre como habilitar as notificações por push aprimoradas em um serviço móvel em produção, consulte <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">esta orientação</a>.

	![][11]

	Isso permite que o serviço móvel se conecte ao MPNS no modo não autenticado para enviar notificações por push.

	>[AZURE.NOTE]Este tutorial usa MPNS no modo não autenticado. Nesse modo, o MPNS limita o número de notificações que podem ser enviadas a um canal do dispositivo. Para remover essa restrição, você deve gerar e carregar um certificado clicando em **Carregar** e selecionando o certificado. Para obter mais informações sobre como gerar o certificado, consulte [Configurando um serviço Web autenticado para enviar notificações por push ao Windows Phone].

##<a id="test"></a> Testar notificações por push no seu aplicativo

1. No Visual Studio, pressione F5 para executar o aplicativo.

    >[AZURE.NOTE] Você pode encontrar um RegistrationAuthorizationException 401 Não autorizado ao testar o emulador do Windows Phone. Isto pode ocorrer durante a chamada `RegisterNativeAsync()` devido ao modo pelo qual o emulador do Windows Phone sincroniza seu relógio com o PC host. Pode resultar em um token de segurança que será rejeitado. Para resolver isto basta definir manualmente o relógio no emulador antes de realizar o teste.

5. No aplicativo, insira o texto hello push na caixa de texto e clique em **Salvar**, depois clique imediatamente no botão iniciar ou botão voltar para sair do aplicativo.

   	![][4]

  	Isso enviará uma solicitação de inserção ao serviço móvel para armazenar o item adicionado. Observe que o dispositivo recebe uma notificação de toast que diz **hello push**.

	![][5]

	>[AZURE.NOTE]Você não receberá a notificação enquanto ainda estiver no aplicativo. Para receber uma notificação de toast enquanto o aplicativo está ativo, você deverá manipular o evento [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx).



## <a name="next-steps"></a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação do aplicativo da Windows Store para usar os Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir um dos seguintes tutoriais:

+ [Enviar notificações por push para usuários autenticados]
	<br/>Saiba como usar marcas para enviar notificações por push de um Serviço Móvel para apenas um usuário autenticado.

+ [Enviar notificações de transmissão para assinantes]
	<br/>Saiba como os usuários podem registrar e receber notificações por push para categorias de interesse.

<!---+ [Enviar notificações baseadas em modelo para assinantes]
	<br/>Saiba usar modelos para enviar notificações por push de um Serviço Móvel, sem precisar colocar cargas específicas de plataforma no seu back-end.
-->
Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os serviços móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários de seu aplicativo com diferentes tipos de conta usando serviços móveis.

* [O que são Hubs de Notificação?]
  <br/>Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

* [Referência conceitual do tutorial do .NET de Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com o .NET.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como implementar a lógica de negócios no seu serviço móvel.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
[10]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
[11]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png

<!-- URLs. -->
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-data
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-users

[Configurando um serviço Web autenticado para enviar notificações por push ao Windows Phone]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx

[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


[Enviar notificações por push para usuários autenticados]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users/

[O que são Hubs de Notificação?]: /pt-br/documentation/articles/notification-hubs-overview/
[Enviar notificações de transmissão para assinantes]: /pt-br/documentation/articles/notification-hubs-windows-phone-send-breaking-news/
[Enviar notificações baseadas em modelo para assinantes]: /pt-br/documentation/articles/notification-hubs-windows-phone-send-localized-breaking-news/


<!--HONumber=42-->
