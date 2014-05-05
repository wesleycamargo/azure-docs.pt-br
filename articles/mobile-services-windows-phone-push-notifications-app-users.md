<properties linkid="develop-net-tutorials-push-notifications-to-users-wp8" urlDisplayName="Enviar notificações por push aos usuários (WP8)" pageTitle="Enviar notificações por push aos usuários (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Saiba como usar os Serviços Móveis para enviar notificações por push aos usuários do seu aplicativo do Windows Phone." metaCanonical="" services="" documentationCenter="" title="Enviar notificações por push aos usuários usando os Serviços Móveis" authors="glenga" solutions="" manager="" editor="" />




# Enviar notificações por push aos usuários usando os Serviços Móveis
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/pt-br/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/push-notifications-to-users-android" title="Android">Android</a>
</div>


Este tópico estende o [tutorial anterior de notificação por push][Get started with push notifications], adicionando uma nova tabela para armazenar URIs de canal do MPNS (Serviço de Notificação por Push da Microsoft). Em seguida, esses tokens poderão ser usados para enviar notificações por push aos usuários do aplicativo do Windows Phone 8.  

Este tutorial o orientará pelas etapas de atualização das notificações por push no seu aplicativo:

1. [Criar a tabela Channel]
2. [Atualizar o aplicativo]
3. [Atualizar scripts de servidor]
4. [Verificar o comportamento de notificação por push] 

Esse tutorial baseia-se no início rápido dos Serviços Móveis e no tutorial anterior [Introdução às notificações por push]. Antes de iniciar o tutorial, você deve primeiro concluir o tutorial [Introdução às notificações por push].  

## <a name="create-table"></a>Criar uma nova tabela

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e, em seguida, clique no seu aplicativo.

   	![][0]

2. Clique na guia **Dados** e, em seguida, clique em **Criar**.

   	![][1]

   	Isso exibe a caixa de diálogo **Criar uma nova tabela**.

3. Mantendo a configuração padrão **Qualquer pessoa com a chave de aplicativo** para todas as permissões, digite _Channel_ em **Nome da tabela** e, em seguida, clique no botão de seleção.

   	![][2]

  	Isso criará a tabela **Channel**, que armazena os URIs de canal usados para enviar notificações por push separadas dos dados do item.

Em seguida, você modificará o aplicativo de notificações por push para armazenar dados nessa nova tabela, e não na tabela **TodoItem**.

## <a name="update-app"></a>Atualizar seu aplicativo

1. No Visual Studio 2012 Express para Windows Phone, abra o projeto do tutorial [Introdução às notificações por push], abra o arquivo MainPage.xaml.cs e remova a propriedade **Channel** da classe **TodoItem**. Agora sua aparência deve ser esta:

        public class TodoItem
        {
        	public int Id { get; set; }

	       	[JsonProperty(PropertyName = "text")]
	        public string Text { get; set; }
	
	        [JsonProperty(PropertyName = "complete")]
	        public bool Complete { get; set; }
        }

2. Substitua o método do manipulador de eventos **ButtonSave_Click** pela versão original desse método, da seguinte maneira:

        private void ButtonSave_Click(object sender, RoutedEventArgs e)
        {
            var todoItem = new TodoItem { Text = TextInput.Text };
            InsertTodoItem(todoItem);
        }

3. Adicione o código a seguir que cria uma nova classe **Channel**:

	    public class Channel
	    {
	        public int Id { get; set; }
	
	        [JsonProperty(PropertyName = "uri")]
	        public string Uri { get; set; }
	    }

4. Abra o arquivo App.xaml.cs e substitua o método **AcquirePushChannel** pelo código a seguir:

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }
                  
           IMobileServiceTable<Channel> channelTable = App.MobileService.GetTable<Channel>();
           var channel = new Channel { Uri = CurrentChannel.ChannelUri.ToString() };
           channelTable.InsertAsync(channel);
        }

     Esse código insere um canal na tabela Channel.

## <a name="update-scripts"></a>Atualizar scripts de servidor

1. No Portal de Gerenciamento, clique na guia **Dados** e, em seguida, clique na tabela **Channel**. 

   	![][3]

2. Em **channel**, clique na guia **Script** e selecione **Inserir**.
   
   	![][4]

   	Isso exibirá a função que é invocada quando ocorre uma inserção na tabela **Channel**.

3. Substitua a função de inserção com o seguinte código e clique em **Salvar**:

			function insert(item, user, request) {
				var channelTable = tables.getTable('Channel');
				channelTable
					.where({ uri: item.uri })
					.read({ success: insertChannelIfNotFound });
		        function insertChannelIfNotFound(existingChannels) {
	        	    if (existingChannels.length > 0) {
	            	    request.respond(200, existingChannels[0]);
	        	    } else {
	            	    request.execute();
	        	    }
	    	    }
		    }

   	Esse script verifica se há um canal com o mesmo URI na tabela **Channel**. A inserção somente continuará se nenhum canal correspondente tiver sido encontrado. Isso impedirá registros de canal duplicados.

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
        	    var channelTable = tables.getTable('Channel');
        	    channelTable.read({
            	    success: function(channels) {
                	    channels.forEach(function(channel) {
                    	    push.mpns.sendFlipTile(channel.uri, {
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

    Esse script de inserção envia uma notificação por push (com o texto do item inserido) para todos os canais armazenados na tabela **Channel**.

## <a name="test-app"></a>Testar o aplicativo

1. No Visual Studio, selecione **Implantar Solução** no menu **Compilar**.

3. Toque no bloco denominado **TodoList** ou **hello push** para iniciar o aplicativo. 

  	![][23]

5. No aplicativo, insira o texto "hello push again" na caixa de texto e, em seguida, clique em **Salvar**.

   	![][24]

  	Isso enviará uma solicitação de inserção ao serviço móvel para armazenar o item adicionado.

6. Pressione o botão **Iniciar** para retornar ao menu Iniciar. 

  	![][25]

  	Observe que o aplicativo recebeu uma notificação por push e que o título do bloco é agora **hello push**.

9. (Opcional) Execute o aplicativo em dois dispositivos ao mesmo tempo e repita a etapa anterior. 

    A notificação é enviada para todas as instâncias do aplicativo em execução.  

## Próximas etapas

Isso conclui os tutoriais que demonstram as noções básicas de como trabalhar com notificações por push. Considere a possibilidade de obter mais informações sobre os seguintes tópicos dos Serviços Móveis:

* [Começar com os dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários do seu aplicativo com uma conta do Windows.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como registrar e usar scripts de servidor.

<!-- anchors -->
[Criar a tabela Channel]: #create-table
[Atualizar o aplicativo]: #update-app
[Atualizar scripts de servidor]: #update-scripts
[Verificar o comportamento de notificação por push]: #test-app
[Próximas etapas]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-services-selection.png
[1]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-table.png
[2]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-channel-table.png
[3]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-portal-data-tables-channel.png
[4]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-channel.png
[5]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-push2.png


[23]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push4-wp8.png
[24]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push5-wp8.png
[25]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push6-wp8.png

<!-- URLs. -->
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Começar com os Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-wp8
[Começar com os dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-wp8
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-wp8
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-wp8

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

