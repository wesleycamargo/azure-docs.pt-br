<properties linkid="develop-mobile-tutorials-get-started-with-push-xamarin-android" urlDisplayName="Introdução às Notificações por Push" pageTitle="Introdução às Notificações por Push – Serviços Móveis" metaKeywords="" description="Saiba como usar as notificações por push nos aplicativos do Xamarin.Android com os Serviços Móveis do Azure." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Saiba como usar as notificações por push nos Serviços Móveis" documentationCenter="Mobile" authors="" />
# Introdução às notificações por push nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/develop/mobile/tutorials/get-started-with-push-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações de push para um aplicativo do Xamarin.Android. Neste tutorial você adiciona notificações de push utilizando o serviço de Mensagens de Nuvem do Google (GCM) ao projeto de início rápido. Ao concluir, seu serviço móvel enviará uma notificação de push sempre que um registro for inserido.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Registrar seu aplicativo para notificações por push]
2. [Configurar os Serviços Móveis]
2. [Adicionar notificações por push ao aplicativo]
3. [Atualizar scripts para enviar notificações por push]
4. [Inserir dados para receber notificações]

Este tutorial exige o seguinte:

+ Uma conta ativa do Google

Este tutorial baseia-se no guia de início rápido dos Serviços Móveis. Antes de iniciar o tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis]. 

<h2><a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Registrar seu aplicativo para notificações por push</h2>

<div class="dev-callout"><b>Observação</b>
<p>Para concluir o procedimento neste tópico, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá até <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div> 

1. Vá até o site <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Apis do Google</a>, faça o login com suas credenciais de conta do Google e clique em **Criar projeto...**

	![][1]   

	> [WACOM.NOTE]
	> Quando você já tiver um projeto existente, você será direcionado para a página do **Painel** após o login. Para criar um novo projeto a partir do Painel, expanda **API do projeto<**, click **Create...** under **Other projects**, then enter a project name and click **Create project**.

2. Click the Overview button in the left column, and make a note of the Project Number in the Dashboard section. 

	Later in the tutorial you set this value as the **PROJECT_ID** variable in the client.

3. On the <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Em Apis do Google</a>, clique em **Serviços** e em alternar para ativar as **Mensagens de Nuvem do Google para o Android** e aceite os termos do serviço. 

4. Clique em **Acesso às APIs**e em **Criar nova chave de Servidor...** 

	![][2]

5. Em **Configurar Chave do Servidor para a API do projeto**, Clique em **Criar**.

	![][3]

6. Anote o valor da **chave da API**.

	![][4] 

Em seguida, você usará esse valor de chave de API para ativar os Serviços Móveis para autenticar com o GCM e enviar notificações de push em nome do seu aplicativo.

<a name="configure"></a><h2><span class="short-header">Configurar o serviço</span>Configurar os Serviços Móveis para enviar solicitações de envio por push</h2>

1. Faça login no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique no seu aplicativo.

	![][18]

2. Clique na guia **Enviar por Push**, insira o valor da **Chave da API** obtida do GCM no procedimento anterior e clique em **Salvar**.

	![][19]

	Seu serviço móvel agora está configurado para funcionar com o GCM para enviar notificações de push.

<a name="add-push"></a><h2><span class="short-header">Adicionar notificações por push</span>Adicionar notificações por push a seus aplicativos</h2>

1. Primeiro, será preciso adicionar **PushSharp** como uma referência em nosso projeto. Para fazer isso, devemos compilar a versão mais recente do PushSharp e adicionar o DLL compilado como uma referência ao nosso projeto do Xamarin.Android.

2. Acesse a [página do PushSharp Github]e baixe a versão mais recente. Depois de extrair o conjunto de arquivos, vá até a seguinte pasta de amostra de projeto:

	**/Client.Samples/PushSharp.ClientSample.MonoForAndroid/PushSharp.ClientSample.MonoForAndroid.Gcm/**

	.. e abra o arquivo do projeto:
	
	**  PushSharp.ClientSample.MonoForAndroid.Gcm.csproj **

3. Crie a amostra de cliente do MonoForAndroid PushSharp no modo **Versão**.

4. Crie uma pasta **_externa** na sua pasta de projetos do Xamarin.Android

5. Copie o seguinte arquivo da amostra de cliente do MonoForAndroid PushSharp para a pasta **_externa** recém-criado em sua pasta de projetos do Xamarin.Android:

	 **\bin\Release\PushSharp.Client.MonoForAndroid.dll**

6. Abra seu projeto do Xamarin.Android no Xamarin Studio (ou Visual Studio). 

7. Clique com o botão direito na pasta **Referências** do projeto e selecione **Editar referências...**

8. Acesse a guia **.Net Assembly**, procure a pasta **_externa** do projeto, selecione o **PushSharp.Client.MonoForAndroid.dll** que criamos anteriormente e clique em **Adicionar**. Clique em OK para fechar a caixa de diálogo. 

9. Abra **Constants.cs** e adicione a seguinte linha, substituindo **PROJECT\_ID** pela ID do Projeto do Google_que você anotou anteriormente:

		public const string SenderID = "PROJECT_ID"; // Google API Project Number

10. Copie o arquivo **PushService.cs** da amostra de cliente do MonoForAndroid PushSharp para a pasta de projetos do Xamarin.Android e adicione-o ao seu projeto.

11. Altere o namespace usado em **PushService.cs** para corresponder ao namespace do seu projeto (por ex.: XamarinTodoQuickStart).

12. Altere a matriz **SENDER_IDS** em **PushService.cs** para fazer referência à constante **SenderID** criada acima:

		public static string[] SENDER_IDS = new string[] { Constants.SenderID };
		
13. Adicione uma nova propriedade estática ao **PushHandlerService** em **PushService.cs** para controlar nossa ID do registro do dispositivo:

		public static string RegistrationID { get; private set; }
		
14. Atualize o método **OnRegistered** em **PushService.cs** para armazenar a id do registro recebida em nossa variável estática local:

		protected override void OnRegistered(Context context, string registrationId)
		{
			Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;
		}

15. Atualize o método **OnMessage** em **PushService.cs** para exibir a mensagem de envio por push recebida como parte da notificação (substitua a chamada **createNotification**):

        string message = intent.Extras.GetString("message");
        createNotification("New todo item!", "Todo item: " + message);
       
16. Observe que o método **OnMessage** tem o seguinte código por padrão para armazenar a última mensagem de envio por push recebida:

        //Store the message
        var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
        var edit = prefs.Edit();
        edit.PutString("last_msg", msg.ToString());
        edit.Commit();

17. Atualize o método **createNotification** em **PushService.cs** para fazer referência a **TodoActivity** em vez de **DefaultActivity**.

18. Abra **TodoActivity.cs** e adicione as seguintes instruções "using":

        using PushSharp.Client;

19. Em **TodoActivity.cs**, insira as seguintes linhas acima do local onde **MobileServiceClient** foi criado:
	
        // Check to ensure everything's setup right
        PushClient.CheckDevice(this);
        PushClient.CheckManifest(this);

        // Register for push notifications
        System.Diagnostics.Debug.WriteLine("Registering...");
        PushClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);

20. Abra **TodoItem.cs** e adicione um novo campo para controlar a id do dispositivo registrado para a pessoa que adicionou o TodoItem:

        [DataMember(Name = "channel")]
        public string RegistrationId { get; set; }

21. In **TodoActivity.cs** update the **AddItem** method to set the **RegistrationID** of the newly added **TodoItem** to the device's registration ID receieved during registration:

		// Create a new item
		var item = new TodoItem() {
			Text = textNewTodo.Text,
			Complete = false,
            RegistrationId = PushHandlerService.RegistrationID
		};

Seu aplicativo foi atualizado para oferecer suporte a notificações de push.

<h2><a name="update-scripts"></a><span class="short-header">Atualizar o script de inserção</span>Atualizar o script de inserção registrado no Portal de Gerenciamento</h2>

1. No Portal de Gerenciamento, clique na guia **Dados** e na tabela **TodoItem**. 

	![][21]

2. Em **TodoItem**, clique na guia **Script** e selecione **Inserir**.
   
  	![][22]

	Isso exibirá a função invocada quando ocorrer uma inserção na tabela **TodoItem**.

3. Substitua a função de inserção com o seguinte código e clique em **Salvar**:

		function insert(item, user, request) {
			request.execute({
				success: function() {
					// Write to the response and then send the notification in the background
					request.respond();
					push.gcm.send(item.channel, item.text, {
						success: function(response) {
							console.log('Push notification sent: ', response);
						}, error: function(error) {
							console.log('Error sending push notification: ', error);
						}
					});
				}
			});
		}

   Isso registra um novo script de inserção, que usa o [objeto gcm] para enviar uma notificação de push (o texto inserido) para o dispositivo fornecido na solicitação de inserção. 

<h2><a name="test"></a><span class="short-header">Testar o aplicativo</span>Testar notificações por push em seu aplicativo</h2>

1. Execute o aplicativo e adicione um novo item Todo. Certifique-se de que você recebe uma notificação de envio por push sobre o novo item Todo que está sendo adicionado.

2. Reveja a guia **Logs** do seu aplicativo móvel no portal de gerenciamento do Azure para ver as mensagens de log que adicionamos ao método **Inserir** na tabela **TodoItem** acima.

3. Examine a tabela **TodoItem** no portal de gerenciamento do Azure para ver a nova coluna de **canal** que foi adicionada e que contém identificadores de registro exclusivos do dispositivo.

Este tutorial foi concluído com êxito.

## Obtenha o exemplo concluído
Baixe o [o projeto de exemplo concluído]. Lembre-se de atualizar as variáveis **ApplicationURL**, **ApplicationKey** e **SenderID** com suas próprias configurações do Azure. 

## <a name="next-steps"> </a>Próximas etapas

Neste exemplo simples, um usuário recebe uma notificação por push com os dados que você acabou de inserir. No próximo tutorial, [Notificações por push para os usuários do aplicativo], você criará uma tabela Dispositivos separada para armazenar tokens de dispositivo e enviar uma notificação por push para todos os canais armazenados quando ocorrer uma inserção. 

<!-- Anchors. -->
[Registrar seu aplicativo para notificações por push]: #register
[Configurar os Serviços Móveis]: #configure
[Atualizar scripts para enviar notificações por push]: #update-scripts
[Adicionar notificações por push ao aplicativo]: #add-push
[Inserir dados para receber notificações]: #test
[Próximas etapas]:#next-steps

<!-- Images. -->
[1]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-developers.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server2.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server3.png
[18]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-push-tab-android.png
[21]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-insert-script-push2.png







<!-- URLs. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->
[Apis do Google]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Portal de Provisionamento Android]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[SDK Android de Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Introdução aos  Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-xamarin-android
[Introdução aos  dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-android
[Notificações por push para usuários de aplicativos]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-android
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Conceitual do Xamarin dos Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-xamarin-client-library
[objeto gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645
[projeto de exemplo concluído]: http://go.microsoft.com/fwlink/p/?LinkId=331303
[Página do PushSharp Github]: https://github.com/Redth/PushSharp


