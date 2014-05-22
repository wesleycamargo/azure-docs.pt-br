<properties linkid="develop-mobile-tutorials-get-started-with-push-js" urlDisplayName="Introdução ao envio por push (JS)" pageTitle="Introdução às notificações por push (JavaScript)" metaKeywords="" description="Saiba como usar notificações por push em seu aplicativo da Windows Store com os Serviços Móveis do Azure." metaCanonical="http://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Introdução às notificações por push nos Serviços Móveis usando o Visual Studio 2012" documentationCenter="Mobile" authors="" />

# Introdução às notificações por push nos Serviços Móveis usando o Visual Studio 2012

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/pt-br/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-js-vs2012" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a> 
</div>	

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações por push a um aplicativo da Windows Store. 
Neste tutorial, você adicionará as notificações por push usando o serviço WNS (Serviço de Notificação por Push do Windows) ao projeto de início rápido. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

<div class="dev-callout"><b>Observação</b>
	<p>Este tutorial adiciona notificações por push a um aplicativo da Windows Store criado no Visual Studio 2012. O Visual Studio 2013 inclui novos recursos que facilitam a configuração de notificações por push em seu aplicativo da Windows Store usando Serviços Móveis. Para obter a versão do Visual Studio 2013, consulte <a href="/pt-br/develop/mobile/tutorials/get-started-with-push-js">Introdução às notificações por push</a>.</p>
</div>

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Registrar seu aplicativo para notificações por push e configurar os Serviços Móveis]
2. [Criar a tabela de registros]
3. [Adicionar notificações por push ao aplicativo]
4. [Atualizar scripts para enviar notificações por push]
5. [Inserir dados para receber notificações]

Este tutorial exige o seguinte:

+ Microsoft Visual Studio 2012 Express para Windows 8
+ Ativar a conta da Windows Store

Este tutorial baseia-se no tutorial [Introdução aos dados]. Antes de iniciar este tutorial, você deve primeiro concluir [o tutorial][Introdução aos dados].

<h2><a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Registrar seu aplicativo para a Windows Store</h2>

Para poder enviar notificações por push para aplicativos da Windows Store dos Serviços Móveis, você deve enviar seu aplicativo para a Windows Store. Em seguida, você deve configurar seu serviço móvel para se integrar com o WNS.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

O serviço móvel e seu aplicativo agora estão configurados para trabalhar com o WNS. Em seguida, você criará uma nova tabela para armazenar registros.

## <a name="create-table"></a>Criar uma nova tabela

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

<h2><a name="add-push"></a><span class="short-header">Adicionar notificações por push</span>Adicionar notificações por push a seus aplicativos</h2>

1. Abra o arquivo default.js e insira o seguinte fragmento de código na sobrecarga do método **app.OnActivated**, imediatamente após o método **args.setPromise**:

        // Get the channel for the application.
        var channel;
        var channelOperation = Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (newChannel) {
                channel = newChannel;
            });

   	Esse código adquire e armazena um canal de notificação por push sempre que o aplicativo é iniciado.
		
4. Insira o código a seguir após o código que cria a instância **MobileServiceClient**:
	
        // Insert the new channel URI into the Registrations table.
        var registrationsTable = client.getTable('Registrations');
        registrationsTable.insert({ handle: channel.uri });         

     Esse código insere o canal atual na tabela Registrations.

6. Abra o arquivo Package.appxmanifest e verifique se, na guia **Interface do Usuário do aplicativo**, **Compatível com toast** está definido como **Sim**.

   	![][15]

   	Isso garante que seu aplicativo possa gerar notificações de toast. 

<h2><a name="update-scripts"></a><span class="short-header">Atualizar o script de inserção</span>Atualizar o script de inserção registrado no Portal de Gerenciamento</h2>

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

4. Clique em **TodoItem**, clique em **Script** e selecione **Inserir**. 

   	![][5]

5. Substitua a função de inserção pelo código a seguir e, em seguida, clique em **Salvar**:

	    function insert(item, user, request) {
    	    request.execute({
        	    success: function() {
            	    request.respond();
            	    sendNotifications();
        	    }
    	    });

	        function sendNotifications() {
        	    var registrationsTable = tables.getTable('Registrations');
        	    registrationsTable.read({
            	    success: function(registrations) {
                	    registrations.forEach(function(registration) {
                    	    push.wns.sendToastText04(registration.handle, {
                        	    text1: item.text
                    	    }, {
                        	    success: function(pushResponse) {
                            	    console.log("Sent push:", pushResponse);
                        	    }
                    	    });
                	    });
            	    }
        	    });
    	    }
	    }

    Esse script de inserção envia uma notificação por push (com o texto do item inserido) para todos os canais armazenados na tabela **Registros**.

<h2><a name="test"></a><span class="short-header">Testar o aplicativo</span>Testar notificações por push em seu aplicativo</h2>

1. No Visual Studio, pressione F5 para executar o aplicativo.

2. No aplicativo, digite um texto em **Inserir um TodoItem** e clique em **Salvar**.

   	![][13]

   	Observe que, após a inserção, o aplicativo receberá uma notificação por push do WNS.

   	![][14]

## <a name="next-steps"> </a>Próximas etapas


Este tutorial demonstra recurso básico de notificação por push fornecido pelos Serviços Móveis. Se seu aplicativo requer recursos mais avançados, como o envio de notificações em várias plataformas, roteamento baseado em assinatura ou volumes muito grandes, convém usar os Hubs de Notificação do Azure com seu serviço móvel. Para obter mais informações, consulte um dos seguintes tópicos de Hubs de Notificação:

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

* [Referência conceitual de tutorial do HTML/JavaScript dos Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com HTML e JavaScript.  

<!-- Anchors. -->
[Registrar seu aplicativo para notificações por push e configurar os Serviços Móveis]: #register
[Criar a tabela de registros]: #create-table
[Atualizar scripts para enviar notificações por push]: #update-scripts
[Adicionar notificações por push ao aplicativo]: #add-push
[Inserir dados para receber notificações]: #test
[Próximas etapas]:#next-steps

<!-- Images. -->





[5]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-insert-script-push2.png







[13]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-quickstart-push2.png
[15]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-app-enable-toast-win8.png



<!-- URLs. -->
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-js
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-js
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Notificações por push para usuários de aplicativos]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-js
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-push-js-vs2012

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Introdução aos Hubs de Notificação]: /pt-br/manage/services/notification-hubs/getting-started-windows-dotnet/
[O que são Hubs de Notificação?]: /pt-br/develop/net/how-to-guides/service-bus-notification-hubs/
[Enviar notificações para assinantes]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet/
[Enviar notificações para usuários]: /pt-br/manage/services/notification-hubs/notify-users/
[Enviar notificações entre plataformas aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Referência conceitual de tutorial do HTML/JavaScript dos Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-html-js-client/
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293

