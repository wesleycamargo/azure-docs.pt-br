<properties pageTitle="Introdução às notificações de push (Windows Phone) | Centro de Desenvolvimento Móvel" metaKeywords="" description="Aprenda a usar os Serviços Móveis do Azure para enviar notificações de push para seu aplicativo do Windows Phone." metaCanonical="" services="" documentationCenter="Mobile" title="Introdução às notificações de push nos Serviços Móveis" authors=""  solutions="" writer="glenga" manager="" editor=""  />


# Introdução as notificações de push nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#" >Windows Store C#</a><a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a><a href="/pt-br/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Back-end do .NET">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started-push/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a>
</div>

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações de push para um aplicativo do Windows Phone 8. 
Neste tutorial você adiciona notificações de push utilizando o Serviço de Notificação de push da Microsoft (MPNS) ao projeto de inicialização rápida. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro é inserido.

>[WACOM.NOTE]Os Serviços Móveis agora integram-se aos Hubs de Notificação do Azure para oferecer suporte à funcionalidade de notificação por push, como modelos, várias plataformas e escala. Essa funcionalidade integrada está no modo de visualização. Para obter mais informações, consulte esta versão da [Introdução às notificações de push](/pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/).

Este tutorial orienta você pelas seguintes etapas básicas para ativar as notificações de push:

1. [Criar a tabela de registros]
2. [Adicionar notificações de push para o aplicativo]
3. [Atualizar scripts para enviar notificações de push]
4. [Inserir dados para receber notificações]

Este tutorial requer [Visual Studio 2012 Express para Windows Phone] ou uma versão posterior.

Este tutorial se baseia na inicialização rápida dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir [Introdução aos Serviços Móveis]. 

   >[WACOM.NOTE]Quando você envia mais de 500 mensagens por usuário a cada dia, você deve usar Hubs de Notificação. Para obter mais informações, consulte <a href="/pt-br/manage/services/notification-hubs/getting-started-windows-dotnet/">Introdução aos Hubs de Notificação</a>.

## <a name="create-table"></a>Criar uma nova tabela

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

<h2><a name="add-push"></a><span class="short-header">Adicionar notificações de push</span>Adicionar notificações de push a seus aplicativos</h2>
		
1. No Visual Studio, abra o arquivo de projeto MainPage.xaml.cs e adicione o seguinte código que cria uma nova classe de **registros**:

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

		private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }
                  
	       IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
	       var registration = new Registrations { Handle = CurrentChannel.Uri };
	       await registrationsTable.InsertAsync(registration);
        }

   	Esse código adquire e armazena um canal para uma assinatura de notificação por push e o associa ao bloco padrão do aplicativo.

	<div class="dev-callout"><b>Observação</b>
		<p>Neste tutorial, o serviço móvel envia uma notificação de bloco invertida para o dispositivo. Quando você envia uma notificação do sistema, é preciso chamar o método <strong>BindToShellToast</strong> no canal. Para oferecer suporte às notificações em bloco e do sistema, chame ambos <strong>BindToShellTile</strong> e  <strong>BindToShellToast</strong> </p>
	</div>
    
4. Em cima do manipulador de eventos **Application_Launching** no App.xaml.cs, adicione a seguinte chamada ao novo método **AcquirePushChannel**:

        AcquirePushChannel();

   	Isso garante que a propriedade **CurrentChannel** é inicializada toda vez que o aplicativo é iniciado.


5.	No Solution Explorer, expanda **Propriedades**, abra o arquivo WMAppManifest.xml, clique na guia **Recursos** e veja se o recurso **ID___CAP___PUSH_NOTIFICATION** está marcado.

   	![][1]

   	Isso garante que seu aplicativo receba notificações de push.

<h2><a name="update-scripts"></a><span class="short-header">Atualizar o script de inserção</span>Atualizar os scripts de inserção registrados no Portal de Gerenciamento</h2>

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

4. Clique em **TodoItem**, clique em **Script** e selecione **Inserir**. 

   	![][10]

3. Substitua a função de inserção com o seguinte código e, em seguida, clique em **Salvar**:

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
                    	    push.mpns.sendFlipTile(registration.uri, {
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

<h2><a name="test"></a><span class="short-header">Testar o aplicativo</span>Testar notificações de push em seu aplicativo</h2>

1. No Visual Studio, selecione **Implantar Solução** no menu **Compilação**.

2. No emulador, passe para a esquerda para exibir a lista de aplicativos instalados e localize o novo aplicativo **TodoList**.

3. Mantenha o ícone do aplicativo pressionado e selecione **pin para iniciar** no menu de contexto.

  	![][2]

  	Isso fixa um bloco chamado **TodoList** no menu Iniciar.

4. Toque no bloco chamado **TodoList** para iniciar o aplicativo. 

  	![][3]

5. No aplicativo, insira o texto "hello push" na caixa de texto e, em seguida, clique em **Salvar**.

   	![][4]

  	Isso envia uma solicitação de inserção ao serviço móvel para armazenar o item adicionado.

6. Pressione o botão **Iniciar** para retornar ao menu Iniciar. 

  	![][5]

  	Observe que o aplicativo recebeu uma notificação por push e que o título do bloco é agora **hello push**.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstra recurso básico de notificação por push fornecido pelos Serviços Móveis. Se seu aplicativo requer recursos mais avançados, como o envio de notificações em várias plataformas, roteamento baseado em assinatura ou volumes muito grandes, convém usar os Hubs de Notificação do Azure com seu serviço móvel. Para obter mais informações, consulte um dos seguintes tópicos de Hubs de Notificação:

+ [Introdução aos Hubs de Notificação]
  <br/>Saiba como aproveitar os Hubs de Notificação no seu aplicativo da Windows Store.

+ [O que são os Hubs de Notificação?]
	<br/>Aprenda a criar e enviar notificações de push para os usuários em várias plataformas.

+ [Enviar notificações para assinantes]
	<br/>Saiba como os usuários podem registrar e receber notificações de push para categorias de interesse.

<!--+ [Enviar notificações para usuários]
	<br/>Saiba como enviar as notificações de push de um Serviço Móvel para usuários específicos em qualquer dispositivo.

+ [Enviar notificações de várias plataformas para usuários]
	<br/>Aprenda a usar os modelos para enviar notificações de push de um Serviço Móvel, sem a necessidade de colocar cargas de específicas da plataforma no seu back-end.
-->

Considere a possibilidade de encontrar mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar usuários de seu aplicativo com uma conta do Windows.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre o registro e o uso de scripts de servidor.

* [Referência conceitual de tutorial do .NET de Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com o .NET. 

<!-- Anchors. -->
[Criar a tabela de registros]: #create-table
[Atualizar scripts para enviar notificações de push]: #update-scripts
[Adicionar notificações de push para o aplicativo]: #add-push
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
[Introdução às notificações de push]: /pt-br/develop/mobile/tutorials/get-started-with-push-wp8
[Notificações por push para usuários de aplicativos]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-wp8
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[objeto mpns]: http://go.microsoft.com/fwlink/p/?LinkId=271130
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Referência conceitual de tutorial do .NET de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library/
[Introdução aos Hubs de Notificação]: /pt-br/manage/services/notification-hubs/get-started-notification-hubs-wp8/
[O que são os Hubs de Notificação?]: /pt-br/develop/net/how-to-guides/service-bus-notification-hubs/
[Enviar notificações para assinantes]: /pt-br/manage/services/notification-hubs/breaking-news-wp8/
[Enviar notificações para usuários]: /pt-br/manage/services/notification-hubs/notify-users/
[Enviar notificações de várias plataformas para usuários]: /pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services/

