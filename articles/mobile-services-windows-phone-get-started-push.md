<properties pageTitle="Introdução às notificações por push (push herdado) | Centro de desenvolvimento móvel" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Phone app (legacy push)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="glenga" />


# Introdução às notificações por push nos Serviços Móveis (envio herdado)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#" >C# da Windows Store</a>
    <a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">JavaScript da Windows Store</a>
    <a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a>
    <a href="/pt-br/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/pt-br/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>    -->
	<a href="/pt-br/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title=".NET backend">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started-push/"  title="JavaScript backend" class="current">Back-end JavaScript</a>
</div>

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações por push para um aplicativo Windows Phone 8. 
Neste tutorial você adiciona notificações por push utilizando o Serviço de Notificação por push da Microsoft (MPNS) ao projeto de início rápido. Ao concluir, seu serviço móvel enviará uma notificação de push sempre que um registro é inserido.

>[WACOM.NOTE]Este tópico dá suporte aos <em>serviços móveis existentes</em> que ainda não foram <em> atualizados </em> para usar a integração dos Hubs de Notificação. Quando você cria um serviço móvel <em>novo</em>, esta funcionalidade integrada é habilitada automaticamente. Para novos serviços móveis, consulte [Introdução às notificações por push](/pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/).
>
>Os Serviços Móveis integram-se aos Hubs de Notificação do Azure para dar suporte à funcionalidade de notificação por push, como modelos, várias plataformas e escala aprimorada. <em>Você deve atualizar seus serviços móveis existentes para usar os Hubs de Notificação quando possível</em>. Assim que você tiver feito a atualização, consulte esta versão da [Introdução às notificações por push](/pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/).

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Criar a tabela de registros]
2. [Adicionar notificações por push ao aplicativo]
3. [Atualizar scripts para enviar notificações por push]
4. [Inserir dados para receber notificações]

Este tutorial requer o [Visual Studio 2012 Express para Windows Phone], ou uma versão posterior.

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis]. 

   >[WACOM.NOTE]Quando você envia mais de 500 mensagens por usuário a cada dia, você deve usar Hubs de Notificação. Para obter mais informações, consulte <a href="/pt-br/manage/services/notification-hubs/getting-started-windows-dotnet/">Introdução aos Hubs de Notificação</a>.

## <a name="create-table"></a>Criar uma nova tabela

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

<h2><a name="add-push"></a>Adicionar notificações de push para seu aplicativo</h2>
		
1. No Visual Studio, abra o arquivo de projeto MainPage.xaml.cs e adicione o seguinte código que cria uma nova classe de **Registros**:

	    public class Registrations
	    {
	        public string Id { get; set; }
	
	        [JsonProperty(PropertyName = "handle")]
	        public string Handle { get; set; }
	    }
	
	A propriedade Handle é usada para armazenar o canal URI.

2. Abra o arquivo App.xaml.cs e adicione o seguinte usando a instrução:

        using Microsoft.Phone.Notification;

3. Adicione o seguinte ao App.xaml.cs:
	
        public static HttpNotificationChannel CurrentChannel { get; private set; }

		private async void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }
                  
	       IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
	       var registration = new Registrations { Handle = CurrentChannel.ChannelUri.AbsoluteUri };
	       await registrationsTable.InsertAsync(registration);
        }

   	Esse código adquire e armazena um canal para uma assinatura de notificação por push e o associa ao bloco padrão do aplicativo.

	<div class="dev-callout"><b>Observação</b>
		<p>Neste tutorial, o serviço móvel envia uma notificação de bloco invertida para o dispositivo. Quando você envia uma notificação do sistema, é preciso chamar o método <strong>BindToShellToast</strong> no canal. Para oferecer suporte às notificações em bloco e do sistema, chame ambos <strong>BindToShellTile</strong> e  <strong>BindToShellToast</strong> </p>
	</div>
    
4. Na parte superior do manipulador de eventos **Application_Launching** no App.xaml.cs, adicione a seguinte chamada ao novo método **AcquirePushChannel**:

        AcquirePushChannel();

   	Isso garante que a propriedade **CurrentChannel** seja inicializada toda vez que o aplicativo for iniciado.


5.	No Gerenciador de Soluções, expanda **Propriedades**, abra o arquivo WMAppManifest.xml, clique na guia **Recursos** e verifique se a funcionalidade **ID___CAP___PUSH_NOTIFICATION** está marcada.

   	![][1]

   	Isso garante que seu aplicativo receba notificações de push.

<h2><a name="update-scripts"></a>Atualizar os scripts de inserção registrados no Portal de Gerenciamento</h2>

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

4. Clique em **TodoItem**, clique em **Script** e selecione **Inserir**. 

   	![][10]

3. Substitua a função de inserção pelo seguinte código e clique em **Salvar**:

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
                    	    push.mpns.sendFlipTile(registration.handle, {
                        	    title: item.text
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

<h2><a name="test"></a>Testar notificações por push no seu aplicativo</h2>

1. No Visual Studio, selecione **Implantar Solução** no menu **Compilar**.

2. No emulador, passe para a esquerda para exibir a lista de aplicativos instalados e localize o novo aplicativo **TodoList**.

3. Mantenha o ícone do aplicativo pressionado e selecione **fixar para iniciar** no menu de contexto.

  	![][2]

  	Isso fixa um bloco chamado **TodoList** no menu Iniciar.

4. Toque no bloco chamado **TodoList** para iniciar o aplicativo. 

  	![][3]

5. No aplicativo, insira o texto "hello push" na caixa de texto e, em seguida, clique em **Salvar**.

   	![][4]

  	Isso enviará uma solicitação de inserção ao serviço móvel para armazenar o item adicionado.

6. Pressione o botão **Iniciar** para retornar ao menu Iniciar. 

  	![][5]

  	Observe que o aplicativo recebeu uma notificação por push e que o título do bloco é agora **hello push**.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstra o recurso básico de notificação por push fornecido pelos Serviços Móveis. Se seu aplicativo requer recursos mais avançados, como o envio de notificações em várias plataformas, roteamento baseado em assinatura ou volumes muito grandes, convém usar os Hubs de Notificação do Azure com seu serviço móvel. Para obter mais informações, consulte um dos seguintes tópicos de Hubs de Notificação:

+ [Introdução aos Hubs de Notificação]
  <br/>Saiba como aproveitar os Hubs de Notificação no seu aplicativo Windows Store.

+ [O que são Hubs de Notificação?]
	<br/>Aprenda a criar e enviar notificações por push a usuários em várias plataformas.

+ [Enviar notificações para assinantes]
	<br/>Saiba como os usuários podem registrar e receber notificações por push para categorias de interesse.

<!--+ [Send notifications to users]
	<br/>Learn how to send push notifications from a Mobile Service to specific users on any device.

+ [Send cross-platform notifications to users]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários do seu aplicativo com uma conta do Windows.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como registrar e usar scripts de servidor.

* [Referência conceitual do tutorial do .NET de Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com o .NET. 

<!-- Anchors. -->
[Criar a tabela de registros]: #create-table
[Atualizar scripts para enviar notificações por push]: #update-scripts
[Adicionar notificações por push ao aplicativo]: #add-push
[Inserir dados para receber notificações]: #test
[Próximas etapas]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[10]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png



<!-- URLs. -->
[SDK dos Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=268375
[Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-wp8
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-wp8
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-wp8
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-wp8
[Notificações por push para usuários de aplicativos]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-wp8
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[objeto mpns]: http://go.microsoft.com/fwlink/p/?LinkId=271130
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library/
[Introdução aos Hubs de Notificação]: /pt-br/manage/services/notification-hubs/get-started-notification-hubs-wp8/
[O que são Hubs de Notificação?]: /pt-br/develop/net/how-to-guides/service-bus-notification-hubs/
[Enviar notificações para assinantes]: /pt-br/manage/services/notification-hubs/breaking-news-wp8/
[Enviar notificações para usuários]: /pt-br/manage/services/notification-hubs/notify-users/
[Enviar notificações entre plataformas aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services/
