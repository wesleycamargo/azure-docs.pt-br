 <properties
	pageTitle="Adicionar notificações por push ao seu aplicativo de Serviços Móveis (Xamarin.Forms) — Serviços Móveis"
	description="Saiba como usar notificações por push em aplicativos Xamarin.Forms com Serviços Móveis do Azure."
	documentationCenter="xamarin"
	authors="normesta"
	manager="stevenpo"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.workload="mobile"
	ms.date="06/10/2015"
	ms.author="normesta"/>

# Adicionar notificações por push ao aplicativo Xamarin.Forms
[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Visão geral

Este tutorial mostra como usar os Serviços Móveis do Azure para enviar notificações por push ao aplicativo da sua solução Xamarin.Forms para iOS, Android e Windows Phone. Comece criando um serviço móvel. Em seguida, você vai baixar um exemplo inicial, registrar-se nos serviços de notificação por push apropriados e adicionar código à solução para receber notificações desses serviços.

Ao concluir este tutorial, seu serviço móvel enviará uma notificação por push toda vez que um usuário adicionar uma tarefa em um dos aplicativos. Você pode encontrar o exemplo completo aqui: [Exemplo completo da notificação por push do Azure para Xamarin.Forms].

Este tutorial exige o seguinte:

+ Um dispositivo iOS 8 (não é possível testar notificações por push no simulador do iOS)
+ Associação no Programa de Desenvolvedores de iOS
+ [Xamarin.iOS Studio]
+ [Componente dos Serviços Móveis do Azure]
+ Uma conta ativa do Google
+ [Componente do cliente Google Cloud Messaging]. Você adicionará esse componente durante o tutorial.

Neste tópico:

1. [Criar um novo serviço móvel](#create-service)
2. [Baixar e configurar o exemplo inicial](#download-starter-sample)
4. [Adicionar notificações por push ao aplicativo Xamarin.Forms](#iOS)
5. [Adicionar notificações por push ao aplicativo Xamarin.Forms.Android](#Android)
6. [Adicionar notificações por push ao aplicativo Xamarin.Forms.Windows](#Windows)
7. [Atualizar o script de inserção de tabela do Azure para enviar notificações por push a todos os aplicativos](#all-apps)

## <a name="create-service"></a>Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

Para ser capaz de armazenar dados de aplicativo no novo serviço móvel, primeiro você deve criar uma nova tabela.

1. No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2. Clique na guia **Dados** e, em seguida, clique em **+Criar**.

    ![][123]

   	Isso exibe a caixa de diálogo **Criar uma nova tabela**.

3. No **Nome da tabela**, digite _TodoItem_ e, em seguida, clique no botão de verificação.

    ![][124]

  	Isso cria uma nova tabela de armazenamento **TodoItem** com o conjunto de permissões padrão, o que significa que qualquer usuário do aplicativo pode acessar e modificar dados na tabela.

    > [AZURE.NOTE]O mesmo nome de tabela é usado no Guia de início rápido dos Serviços Móveis. No entanto, cada tabela é criada em um esquema específico para um determinado serviço móvel. Isso é para evitar colisões de dados quando vários serviços móveis usam o mesmo banco de dados.

4. Clique na nova tabela **TodoItem** e verifique se não há linhas de dados.

5. Clique na guia **Colunas** e verifique se há uma única coluna **id**, que é criada automaticamente para você.

  	Este é o requisito mínimo para uma tabela nos Serviços Móveis.

    > [AZURE.NOTE]Quando o esquema dinâmico estiver ativado no seu serviço móvel, novas colunas serão criadas automaticamente quando objetos JSON forem enviados para o serviço móvel por uma operação de inserção ou atualização.

Agora você está pronto para usar o novo serviço móvel como armazenamento de dados para o aplicativo.

## <a name="download-starter-sample"></a>Baixar e configurar o exemplo inicial
Adicionaremos notificações por push a um exemplo existente.
  
1. Baixe o exemplo a seguir: [Exemplo inicial de notificação por push do Azure para Xamarin.Forms].

2. No Portal de Gerenciamento, clique em **Serviços Móveis**, em seguida, clique no serviço móvel. Clique na guia **Painel** e anote a **URL do Site**. Em seguida, clique em **Gerenciar Chaves** e anote a **Chave do Aplicativo**. Você precisará desses valores quando acessar o serviço móvel do seu código de aplicativo.

3. No projeto **ToDoAzure(Portable)** da solução, abra o arquivo **Constants.cs**, substitua `ApplicationURL` e `ApplicationKey` pela URL do site e chave de aplicativo que você obteve na etapa anterior.

## <a name="iOS"></a>Adicionar notificações por push ao aplicativo Xamarin.Forms.iOS

Você adicionará notificações por push ao aplicativo iOS usando o APNS (Serviço de Notificação por Push da Apple). Você precisará de uma conta ativa do Google e do [Componente cliente Google Cloud Messaging].

>[AZURE.IMPORTANT]Devido aos requisitos do APNS (Serviço de Notificação por Push da Apple), você deve implantar e testar as notificações por push em um dispositivo compatível com o iOS (iPhone ou iPad) em vez de usar o emulador.

O APNS usa certificados para autenticar seu serviço móvel. Siga estas instruções para criar os certificados necessários e carregá-los no seu Serviço Móvel. Para a documentação oficial do recurso APNS, consulte [Serviço de Notificação por Push da Apple].

### <a name="certificates"></a>Gerar o arquivo de Solicitação de Assinatura de Certificado

Primeiro, gere o arquivo CSR (Solicitação de Assinatura de Certificado), que é usado pela Apple para gerar um certificado assinado.

1. Em utilitários, execute a **ferramenta Acesso do Conjunto de Chaves**.

2. Clique em **Acesso do Conjunto de Chaves**, expanda **Assistente de Certificado** e clique em **Solicitar um Certificado de uma Autoridade de Certificação...**.

    ![][5]

3. Digite seu **Endereço de Email de Usuário**, digite um valor de **Nome Comum**, certifique-se de que a opção **Salvar em disco** esteja selecionada e clique em **Continuar**.

    ![][6]

4. Digite um nome para o arquivo CSR (Solicitação de Assinatura de Certificado) em **Salvar como**, selecione o local em **Onde** e, em seguida, clique em **Salvar**.

    ![][7]

    Lembre-se do local escolhido.

Em seguida, você registrará seu aplicativo na Apple, habilitará as notificações por push e carregará esse CSR exportado para criar um certificado de push.

### <a name="register"></a>Registrar seu aplicativo para notificações por push

Para poder enviar notificações por push para um aplicativo iOS dos serviços móveis, você deve registrar seu aplicativo na Apple e também registrar para notificações por push.

1. Se você ainda não registrou o seu aplicativo, navegue até o <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a> no Centro de Desenvolvedores da Apple. Faça o logon com a sua ID da Apple, clique em **Identificadores** e clique em **IDs de Aplicativo**. Finalmente, clique no sinal **+** para criar uma ID para seu aplicativo.

    ![][102]

2. Digite um nome para seu aplicativo em **Descrição**, insira e lembre-se do **Identificador de pacote** exclusivo, marque a opção "Notificações por Push" na seção "Serviços de Aplicativos" e, em seguida, clique em **Continuar**. Este exemplo usa a ID **MobileServices.Quickstart**, mas você não pode reutilizar essa mesma ID, pois as IDs de aplicativo devem ser exclusivas para todos os usuários. Portanto, recomenda-se acrescentar o nome completo ou as iniciais após o nome do aplicativo.

    ![][103]

    Isso gerará sua ID de aplicativo e solicitará a você para **Enviar** as informações. Clique em **Enviar**.

    ![][104]

    Após clicar em **Enviar**, você verá a tela **Registro concluído**, conforme mostrado abaixo. Clique em **Concluído**.

    ![][105]

3. Localize a ID de aplicativo que acabou de criar e clique na respectiva linha.

    ![][106]

    Clicar na ID do aplicativo faz com que os detalhes sobre o aplicativo e a ID do aplicativo sejam exibidos. Clique no botão **Configurações**.

    ![][107]

4. Role até a parte inferior da tela e clique no botão **Criar Certificado...** na seção **Certificado SSL por Push para Desenvolvimento**.

    ![][108]

    Isso exibirá o assistente "Adicionar Certificado de iOS".

    Nota: Este tutorial usa um certificado de desenvolvimento. O mesmo processo é usado para registrar um certificado de produção. Verifique apenas se você definiu o mesmo tipo de certificado ao carregar o certificado para os Serviços Móveis.

5. Clique em **Escolher Arquivo**, navegue até o local onde você salvou o arquivo CSR anteriormente e clique em **Gerar**.

    ![][110]

6. Depois que o certificado for criado pelo portal, clique no botão **Baixar** e, em seguida, clique em **Concluído**.

    ![][111]

    Isso baixará o certificado de assinatura e salvará esse certificado no seu computador, na pasta Downloads.

    ![][9]

    Nota: por padrão, o arquivo baixado, um certificado de desenvolvimento, é denominado <strong>aps\_development.cer</strong>.

7. Clique duas vezes no certificado de push baixado, **aps\_development.cer**.

    Isso instalará o novo certificado no Conjunto de Chaves, conforme mostrado abaixo:

    ![][10]

    Nota: O nome no seu certificado pode ser diferente, mas ele será prefixado com <strong>Serviços de Notificação por Push do iOS para Desenvolvimento da Apple:</strong>.

Posteriormente, você usará esse certificado para gerar um arquivo. p12 e carregá-lo para os Serviços Móveis para habilitar a autenticação com APNS.

### <a name="profile"></a>Criar um perfil de provisionamento para o aplicativo

1. De volta ao <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a>, selecione **Perfis de Provisionamento**, escolha **Tudo** e clique no botão **+** para criar um novo perfil. Isso iniciará o Assistente **Adicionar Perfil de Provisionamento do iOS**.

    ![][112]

2. Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento e clique em **Continuar**.

3. Em seguida, selecione a ID para o aplicativo Quickstart de Serviços Móveis na lista suspensa **ID do Aplicativo** e clique em **Continuar**.

    ![][113]

4. Na tela **Selecionar certificados**, selecione o certificado criado anteriormente e clique em **Continuar**.

    ![][114]

5. Em seguida, selecione os **Dispositivos** a serem usados no teste e clique em **Continuar**.

    ![][115]

6. Finalmente, selecione um nome para o perfil em **Nome do Perfil**, clique em **Gerar** e, em seguida, clique em **Concluído**.

    ![][116]

    Isso criará um novo perfil de provisionamento.

    ![][117]

7. No Xcode, abra o Organizador, selecione a exibição Dispositivos, selecione **Perfis de Provisionamento** na seção **Biblioteca** no painel esquerdo e clique no botão **Atualizar** localizado na parte inferior do painel central.

### <a name="configure-mobileServices"></a>Configurar Serviços Móveis para enviar solicitações de push

Após registrar seu aplicativo com o APNS e configurar seu projeto, configure seu serviço móvel para integrá-lo ao APNS.

1. Em Acesso do Conjunto de Chaves, clique com o botão direito do mouse no novo certificado, clique em **Exportar**, nomeie o arquivo, selecione o formato **.p12** e clique em **Salvar**.

    ![][28]

    Anote o nome do arquivo e o local do certificado exportado.

2. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu aplicativo.

    ![][18]

3. Clique na guia **Enviar por Push** e em **Carregar** nas **Configurações de notificação por push da Apple**.

    ![][19]

    Isso exibirá a caixa de diálogo Carregar Certificado.

4. Clique em **Arquivo**, selecione o arquivo de certificado exportado .p12, digite a **Senha**, verifique se o **Modo** correto está selecionado, clique no ícone de verificação e clique em **Salvar**.

    ![][20]

Agora, seu serviço móvel está configurado para funcionar com o APNS.

### <a name="configure-app"></a>Configurar seu aplicativo Xamarin.iOS

1. No Xamarin.Studio ou Visual Studio, abra **Info.plist** e atualize o **Identificador de Pacote** com a ID que você criou anteriormente.

    ![][121]

2. Role para baixo até **Modos de Tela de Fundo** e marque a caixa **Habilitar Modos de Plano de Fundo** e a caixa **Notificações remotas**.

    ![][122]

3. Clique duas vezes em seu projeto no Painel de Solução para abrir **Opções de Projeto**.

4.  Escolha **Assinatura do Pacote iOS** em **Compilar** e selecione a **Identidade** e o **Perfil de provisionamento** correspondente que você acabou de configurar para este projeto.

    ![][120]

    Isso garantirá que o projeto Xamarin use o novo perfil para a assinatura de código. Para obter a documentação oficial de provisionamento do dispositivo Xamarin, consulte [Provisionamento do dispositivo Xamarin].

### <a name="add-push"></a>Adicionar notificações de push para seu aplicativo

1. No Xamarin.Studio ou Visual Studio, expanda o projeto **ToDoAzure.iOS**, abra a classe **AppDelegate** e substitua o evento **FinishedLaunching** pelo seguinte código:

        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
             // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            global::Xamarin.Forms.Forms.Init();
            instance = this;
            CurrentPlatform.Init();
            
            todoItemManager = new ToDoItemManager();
            App.SetTodoItemManager(todoItemManager);


            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
            
            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }

6. Em **AppDelegate**, substitua o evento **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            string _deviceToken = deviceToken.Description;
            _deviceToken = _deviceToken.Trim('<', '>').Replace(" ", "");

            // Get Mobile Services client
            MobileServiceClient client = todoItemManager.GetClient;

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };
            
            const string template = "{"aps":{"alert":"$(message)"}}";

            var expiryDate = DateTime.Now.AddDays(90).ToString
                (System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));

            var push = client.GetPush();

            push.RegisterTemplateAsync(_deviceToken, template, expiryDate, "myTemplate", tag)
        }

7. Em **AppDelegate**, substitua o evento **ReceivedRemoteNotification**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

Seu aplicativo foi atualizado para oferecer suporte a notificações de push.

### <a name="update-scripts"></a>Atualizar o script de inserção registrado no Portal de Gerenciamento

1. No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**.

    ![][21]

2. Em **todoitem**, clique na guia **Script** e selecione **Inserir**.

    ![][22]

    Isso exibe a função que é chamada quando ocorre uma inserção na tabela **TodoItem**.

3. Substitua a função de inserção com o seguinte código e **Salvar**:

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {                      
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';            

              // Write the default response and send a notification
              // to all platforms.            
              push.send(null, payload, {               
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },              
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }           
               });                 
              }
           });   
          }

    Isso registra um novo script de inserção, que envia uma notificação por push (o texto inserido) para o dispositivo fornecido na solicitação de inserção.

   >[AZURE.NOTE]Esse script atrasa o envio da notificação para que você tenha tempo de fechar o aplicativo para receber uma notificação do sistema.

### <a name="test"></a>Testar notificações por push no seu aplicativo

1. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo em um dispositivo compatível com iOS; em seguida, clique em **OK** para aceitar as notificações por push

   >[AZURE.NOTE]Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

2. No aplicativo, clique no botão **Adicionar**, adicione um título de tarefa e clique no botão **Salvar**. 

3. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.


Este tutorial foi concluído com êxito.

## <a name="Android"></a>Adicionar notificações por push ao aplicativo Xamarin.Forms.Android

Você adicionará notificações por push ao aplicativo Android usando o serviço GCM (Google Cloud Messaging). Você precisará de uma conta ativa do Google e do [Componente cliente Google Cloud Messaging].

###<a id="register"></a>Habilitar as mensagens em nuvem do Google

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

###<a id="configure"></a>Configurar o serviço móvel para enviar solicitações por push

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

###<a id="update-scripts"></a>Atualizar o script de inserção registrado para enviar notificações

>[AZURE.NOTE]As etapas a seguir mostram como atualizar o script registrado para a operação de inserção na tabela TodoItem no Portal de Gerenciamento do Azure. Você também pode acessar e editar este script de serviço móvel diretamente no Visual Studio, no nó do Azure do Gerenciador de Servidores.

No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**.

   ![][21]

2. Em **todoitem**, clique na guia **Script** e selecione **Inserir**.

   ![][22]

    This displays the function that is invoked when an insert occurs in the **TodoItem** table.

3. Substitua a função de inserção com o seguinte código e **Salvar**:

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {                      
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';            

              // Write the default response and send a notification
              // to all platforms.            
              push.send(null, payload, {               
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },              
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }           
               });                 
              }
           });   
          }


    Isso registra um novo script de inserção, que envia uma notificação por push (o texto inserido) para o dispositivo fornecido na solicitação de inserção.

   >[AZURE.NOTE]Esse script atrasa o envio da notificação para que você tenha tempo de fechar o aplicativo para receber uma notificação do sistema.


###<a id="configure-app"></a>Configurar o projeto existente para notificações por push

1. No modo de exibição Solução, expanda a pasta **Componentes** no aplicativo Xamarin.Android e verifique se o pacote Serviços Móveis do Azure está instalado. 

2. Clique com o botão direito do mouse na pasta **Componentes**, clique em **Obter Mais Componentes...**, procure o componente **Cliente do Google Cloud Messaging** e adicione-o ao projeto.

1. Abra o arquivo de projeto MainActivity.cs e adicione a seguinte instrução using à classe:

		using Gcm.Client;


4.	Na classe **MainActivity**, adicione o seguinte código ao método **OnCreate**, depois da chamada ao método **LoadApplication**:
            
            try
            {
                // Check to ensure everything's setup right
                GcmClient.CheckDevice(this);
                GcmClient.CheckManifest(this);

                // Register for push notifications
                System.Diagnostics.Debug.WriteLine("Registering...");
                GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
            }
            catch (Java.Net.MalformedURLException)
            {
                CreateAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
            }
            catch (Exception e)
            {
                CreateAndShowDialog(e, "Error");
            }

Sua **MainActivity** agora está preparada para adicionar notificações por push.

###<a id="add-push"></a>Adicionar código de notificações por push ao seu aplicativo

5. No projeto ToDoAzure.Droid, crie uma nova classe no projeto chamado `GcmService`.

5. Adicione as seguintes instruções using à classe **GcmService**:

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;

6. Adicionar as solicitações de permissão a seguir entre as instruções **using** e a declaração **namespace**:

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7. No arquivo de projeto **GcmService.cs**, adicione a seguinte classe:
 
        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]

        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
        
            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };

        }

	No código acima, você deve substituir _`<PROJECT_NUMBER>`_ pelo número do projeto atribuído pelo Google ao provisionar o aplicativo no portal do desenvolvedor do Google.

8. No arquivo de projeto GcmService.cs, adicione o seguinte código que define a classe **GcmService**:
 
         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


	Observe que essa classe deriva de **GcmServiceBase** e que o atributo **Service** deve ser aplicado a essa classe.

	>[AZURE.NOTE]A classe **GcmServiceBase** implementa os métodos **OnRegistered()**, **OnUnRegistered()**, **OnMessage()** e **OnError()**. Você deve substituir esses métodos na classe **GcmService**.

5. Adicione o código a seguir à classe **GcmService** que substitui o manipulador de eventos **OnRegistered**.

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");

            MobileServiceClient client =  MainActivity.DefaultService.todoItemManager.GetClient;
            
            var push = client.GetPush();

            MainActivity.DefaultService.RunOnUiThread(() => Register(push, null));

        }
        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string template = "{"data":{"message":"$(message)"}}";

                await push.RegisterTemplateAsync(RegistrationID, template, "mytemplate", tags);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

	Esse método usa a ID de registro retornada do GCM para registrar-se no Azure para notificações por push.

10. Substitua o método **OnMessage** em **GcmService** pelo seguinte código:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(PushHandlerBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
        }

12. Adicione as substituições de método a seguir para **OnUnRegistered()** e **OnError()**, necessárias à compilação do projeto.

        protected override void OnError(Context context, string errorId)
        {
              Log.Error(PushHandlerBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }

###<a id="test"></a>Testar notificações por push no seu aplicativo

Você pode testar o aplicativo anexando um telefone Android com um cabo USB diretamente ou usando um dispositivo virtual no emulador.

Quando você executa o aplicativo no emulador, certifique-se de usar um Android Virtual Device (AVD) que oferece suporte a APIs do Google.

> [AZURE.IMPORTANT]Para receber as notificações por push, você deve configurar uma conta do Google em seu Dispositivo Virtual para Android (no emulador, navegue até **Configurações** e clique em **Adicionar Conta**). Além disso, certifique-se de que o emulador esteja conectado à Internet.

1. A partir de **Ferramentas**, clique em **Abrir gerenciador de emulador do Android**, selecione seu dispositivo e, em seguida, clique em **Editar**.
    
    ![][125]

2. Selecione **Google APIs** em **Destino** e clique em **OK**.
    
    ![][126]

3. Na barra de ferramentas superior, clique em **Executar**e, em seguida, selecione seu aplicativo. Isso iniciará o emulador e executará o aplicativo.

  O aplicativo recupera o *registrationId* do GCM e registrará no Hub de Notificação.

1. No aplicativo, adicione uma nova tarefa.

2. Passe o dedo para baixo da parte superior da tela para abrir a Central de Notificação do dispositivo para ver a notificação.

	![][127]

## <a name="Windows"></a>Adicionar notificações por push ao aplicativo Xamarin.Forms.Windows

Esta seção mostra como usar os Serviços Móveis do Azure para enviar notificações por push ao aplicativo do Windows Phone Silverlight que está incluído na sua solução Xamarin.Forms.

###<a id="update-app"></a> Atualizar o aplicativo para registrar-se para notificações

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
                    const string template =
                    "<?xml version="1.0" encoding="utf-8"?><wp:Notification " +
                    "xmlns:wp="WPNotification"><wp:Toast><wp:Text1>$(message)</wp:Text1></wp:Toast></wp:Notification>";

                    await client.GetPush()
                        .RegisterTemplateAsync(CurrentChannel.ChannelUri.ToString(), template, "mytemplate");
                });
        }

    Esse código recupera o ChannelURI do aplicativo do Serviço de Notificação por Push da Microsoft usado pelo Windows Phone 8.x "Silverlight” e depois registra o ChannelURI para notificações por push.

	>[AZURE.NOTE]Neste tutorial, o serviço móvel envia uma notificação de toast ao dispositivo. Ao enviar uma notificação de bloco, você deve chamar o método **BindToShellTile** no canal.

4. Na parte superior do manipulador de eventos **Application\_Launching** no App.xaml.cs, adicione a seguinte chamada para o novo método **AcquirePushChannel**:

        AcquirePushChannel();

	Isso garante que o registro seja solicitado sempre que a página for carregada. Em seu aplicativo, convém fazer esse registro periodicamente apenas para garantir que o registro seja atual.

5. Pressione a tecla **F5** para executar o aplicativo. Uma caixa de diálogo pop-up com a chave do registro é exibida.
  
6.	No Gerenciador de Soluções, expanda **Propriedades**, abra o arquivo WMAppManifest.xml, clique na guia **Recursos** e verifique se o recurso **ID\_CAP\_PUSH\_NOTIFICATION** está marcado.

   	![Habilitar notificações no VS](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-app-enable-push-wp8.png)

   	Isso garante que seu aplicativo possa gerar notificações de toast.

###<a id="update-scripts"></a> Atualizar scripts de servidor para enviar notificações por push

Finalmente, você deve atualizar o script registrado para a operação de inserção na tabela TodoItem para enviar notificações.

1. No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**.

    ![][21]

2. Em **todoitem**, clique na guia **Script** e selecione **Inserir**.

    ![][22]

    Isso exibe a função que é chamada quando ocorre uma inserção na tabela **TodoItem**.

3. Substitua a função de inserção com o seguinte código e **Salvar**:
          
          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {                      
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';            

              // Write the default response and send a notification
              // to all platforms.            
              push.send(null, payload, {               
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },              
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }           
               });                 
              }
           });   
          }


    Isso registra um novo script de inserção, que envia uma notificação por push (o texto inserido) para o dispositivo fornecido na solicitação de inserção.

3. Clique na guia **Enviar por Push**, selecione **Habilitar as notificações por push sem autenticação** e, em seguida, clique em **Salvar**.

	Isso permite que o serviço móvel se conecte ao MPNS no modo não autenticado para enviar notificações por push.

	>[AZURE.NOTE]Este tutorial usa MPNS no modo não autenticado. Nesse modo, o MPNS limita o número de notificações que podem ser enviadas a um canal do dispositivo. Para remover essa restrição, você deve gerar e carregar um certificado clicando em **Carregar** e selecionando o certificado. Para obter mais informações sobre como gerar o certificado, consulte [Configurando um serviço Web autenticado para enviar notificações por push ao Windows Phone].

###<a id="test"></a> Testar notificações por push no seu aplicativo

1. No Visual Studio, pressione F5 para executar o aplicativo.

    >[AZURE.NOTE]Você pode encontrar um RegistrationAuthorizationException 401 Não autorizado ao testar o emulador do Windows Phone. Isto pode ocorrer durante a chamada de `RegisterNativeAsync()` devido a forma que o emulador do Windows Phone sincroniza seu relógio com o PC anfitrião. Pode resultar em um token de segurança que será rejeitado. Para resolver isto basta definir manualmente o relógio no emulador antes de realizar o teste.

5. No aplicativo, crie uma nova tarefa com o título **Hello push** e clique imediatamente no botão Iniciar ou Voltar para sair do aplicativo.

  	Isso enviará uma solicitação de inserção ao serviço móvel para armazenar o item adicionado. Observe que o dispositivo recebe uma notificação do sistema que diz **hello push**.

	![Notificação recebida](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]Você não receberá a notificação enquanto ainda estiver no aplicativo. Para receber uma notificação do sistema enquanto o aplicativo estiver ativo, você deverá manipular o evento [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx).
   
<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure Mobile Services]: #configure-mobileServices
[Configure the Xamarin.iOS App]: #configure-app
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test

<!-- Images. -->

[5]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-17.png

[120]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-data-tab-empty.png
[124]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-create-todoitem-table.png
[125]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app7.png
[126]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app8.png
[127]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-area-received.png


[Xamarin.iOS Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Serviço de Notificação por Push da Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: mobile-services-ios-get-started.md

[Provisionamento do dispositivo Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Componente dos Serviços Móveis do Azure]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303
[Xamarin.iOS]: http://xamarin.com/download
[Componente cliente Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Componente do cliente Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Exemplo inicial de notificação por push do Azure para Xamarin.Forms]: https://github.com/Azure/mobile-services-samples/tree/master/TodoListXamarinForms
[Exemplo completo da notificação por push do Azure para Xamarin.Forms]: https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithPushXamarinForms
 

<!---HONumber=August15_HO6-->