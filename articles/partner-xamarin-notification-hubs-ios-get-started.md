<properties urlDisplayName="Get Started" pageTitle="Introdução aos Hubs de Notificação para aplicativos Xamarin iOS" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin iOS application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" authors="donnam" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="11/11/2014" ms.author="donnam" />

# Introdução aos Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/pt-br/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/pt-br/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/pt-br/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tópico mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo iOS. 
Neste tutorial, você cria um aplicativo Xamarin.iOS em branco que recebe notificações por push usando o serviço de Notificação por Push da Apple (APNs). Ao concluir, você poderá transmitir notificações por push a todos os dispositivos que executam seu aplicativo usando o hub de notificação. O código concluído está disponível no exemplo do [aplicativo NotificationHubs][GitHub].

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Gerar a solicitação de assinatura de certificado] 
2. [Registrar seu aplicativo e habilitar as notificações por push]
3. [Criar um perfil de provisionamento para o aplicativo]
4. [Configurar seu Hub de Notificação]
5. [Conectando seu aplicativo ao Hub de Notificação]
6. [Enviar notificações de seu back-end]

Este tutorial demonstra o cenário de difusão simples usando hubs de notificação. Este tutorial requer os seguintes pré-requisitos:

+ [XCode 5.0][Install Xcode] 
+ Um dispositivo compatível com o iOS 5.0 (ou versão posterior)
+ Associação no Programa de Desenvolvedores de iOS
+ [Xamarin.iOS]
+ [Componente dos Serviços Móveis do Azure]

   <div class="dev-callout"><b>Observação</b>
   <p>Devido aos requisitos de configuração das notificações por push, você deve implantar e testar as notificações por push em um dispositivo compatível com o iOS (iPhone ou iPad) em vez de usar o emulador.</p>
   </div>

Completar este tutorial é um pré-requisito para todos os outros tutoriais de hub de notificação para aplicativos Xamarin.iOS. 

<div class="dev-callout"><strong>Observação</strong> <p>Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F" target="_blank">Avaliação gratuita do Azure</a>.</p></div>

O APNS (Serviço de Notificação por Push da Apple) usa certificados para autenticar seu serviço móvel. Siga estas instruções para criar os certificados necessários e carregá-los no seu Serviço Móvel. Para a documentação oficial do recurso APNS, consulte [Serviço de Notificação por Push da Apple].



<h2><a name="certificates"></a>Gerar o arquivo de Solicitação de Assinatura de Certificado</h2>

Primeiro, gere o arquivo CSR (Solicitação de Assinatura de Certificado), que é usado pela Apple para gerar um certificado assinado.

1. Na pasta Utilities, execute a ferramenta Acesso do Conjunto de Chaves.

2. Clique em **Acesso do Conjunto de Chaves**, expanda **Assistente do Certificado** e clique em **Solicitar um certificado de um autoridade do certificado...**.

  	![][5]

3. Selecione seu **Endereço de Email do Usuário**, digite os valores **Nome comum** e **Endereço de Email do CA**, verifique se a opção **Salvo no disco** está selecionada e clique em **Continuar**.

  	![][6]

4. Digite um nome para o arquivo CSR (Solicitação de Assinatura de Certificado) em **Salvar como**, selecione o local em **Onde** e clique em **Salvar**.

  	![][7]
  
  	Isso salvará o arquivo CSR no local selecionado. O local padrão estará situado na área de trabalho. Lembre-se do local escolhido para esse arquivo.

Em seguida, registre seu aplicativo na Apple, habilite as notificações por push e carregue este CSR exportado para criar um certificado de push.

<h2><a name="register"></a>Registrar seu aplicativo para notificações por push</h2>

Para poder enviar notificações por push para um aplicativo iOS dos serviços móveis, você deve registrar seu aplicativo na Apple e também registrar para notificações por push.  

1. Se você ainda não registrou seu aplicativo, navegue até o <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a> no Centro de Desenvolvedores da Apple. Faça logon com sua ID da Apple, clique em **Identificadores** e clique em **IDs de Aplicativo**. Finalmente, clique no sinal **+** para registrar um novo aplicativo.

   	![][105] 

2. Type a name for your app in **Description**, and a value for **Bundle Identifier**, check the "Push Notifications" option in the "App Services" section, and then click **Continue**.

   	![][106]

   	![][107] 

   	![][108]
   

Isso gerará sua ID do aplicativo e solicitará a você que envie as informações. Clique em **Enviar**.
   
   	![][109] 
   
	Once you click **Submit**, you will see the **Registration complete** screen, as shown below. Click **Done**.
   
   	![][110]

	<div class="dev-callout"><b>Observação</b>
	<p>Se você escolher fornecer um valor de <strong>Identificador de pacote</strong> diferente de <b>MobileServices.Quickstart</b>, você deverá também atualizar o valor do identificador de pacote em seu projeto Xcode.</p>
	 </div>

3. Localize a ID do aplicativo que você acabou de criar e clique na respectiva linha. 

   	![][111]
   
	Ao clicar na ID do aplicativo, os detalhes sobre o aplicativo e a ID do aplicativo serão exibidos:
   
   	![][112] 
   
   	![][113]

4. Clique em **Editar**, role até a parte inferior da tela e clique em **Criar Certificado...** na seção **Certificado SSL por Push para Desenvolvimento**.

   	![][114] 

	This displays the "Add iOS Certificate" assistant.
   
   	![][115] 

	<div class="dev-callout"><b>Note</b>
	<p>This tutorial uses a development certificate. The same process is used when registering a production certificate. Just make sure that you set the same certificate type when you upload the certificate to Mobile Services.</p>
	</div>

5. Click **Choose File**, browse to the location in which you saved the CSR file that you created in the first task, and then click **Generate**. 

  	![][116]
  
6. Depois que o certificado for criado pelo portal, clique em **Baixar** e clique em **Concluído**.
 
  	![][118]

  	![][119]  
  
   	This downloads the signing certificate and saves it to your computer in your **Downloads** folder. 

  	![][9] 

    <div class="dev-callout"><b>Note</b>
	<p>By default, the downloaded file a development certificate is named <strong>aps_development.cer</strong>.</p>
    </div>

7. Double-click the downloaded push certificate **aps_development.cer**.

	This installs the new certificate in the Keychain, as shown below:

   	![][10]

	> [WACOM.NOTE]
	> O nome em seu certificado pode ser diferente, mas ele será prefixado como <strong>Serviços de Notificação por Push do iOS para Desenvolvimento da Apple:</strong>.

	Posteriormente, você usará este certificado para gerar um arquivo .p12 e carregá-lo em seu hub de notificações para habilitar as notificações por push por APNS.

<h2><a name="profile"></a>Criar um perfil de provisionamento para o aplicativo</h2>
 
1. No <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a>, selecione **Perfis de Provisionamento**, **Todos** e clique no botão **+** para criar um novo perfil. Isto exibirá o Assistente **Adicionar Perfil de Provisionamento do iOS**.

   	![][120]

2. Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento e clique em **Continuar**.

   	![][121]

3. Em seguida, selecione a ID para o aplicativo Guia de início rápido dos Serviços Móveis na lista suspensa **ID do Aplicativo** e clique em **Continuar**.

   	![][122]

4. Na tela **Selecionar certificados**, selecione o certificado que você criou anteriormente e clique em **Continuar**.
  
   	![][123]

5. Em seguida, selecione os **Dispositivos** que serão usados para teste e clique em **Continuar**.

   	![][124]

6. Por fim, escolha um nome para o perfil em **Nome do Perfil**, clique em **Gerar** e em **Concluído**.

   	![][125]
   
   	![][126]
	
  	Isso criará um novo perfil de provisionamento.

7. No Xcode, abra o Organizador, escolha a exibição Dispositivos, selecione **Perfis de Provisionamento** na seção **Biblioteca** no painel esquerdo e importe o perfil de provisionamento que você acabou de criar.

8. À esquerda, selecione seu dispositivo e importe novamente o perfil de provisionamento. 

9. Em Acesso do Conjunto de Chaves, clique com o botão direito do mouse no novo certificado, clique em **Exportar**, digite um nome para seu certificado, selecione o formato **.p12** e clique em **Salvar**.

   	![][18]

  	Anote o nome do arquivo e o local do certificado exportado.

Isso garantirá que o projeto Xcode use o novo perfil para a assinatura de código. Em seguida, carregue o certificado em seu hub de notificação.

<h2><a name="configure-hub"></a>Configurar seu Hub de Notificação</h2>

1. Faça logon no [Portal de Gerenciamento do Azure] e clique em **+NOVO** na parte inferior da tela.

2.  Clique em **Serviços de Aplicativos**, em **Barramento de Serviço**, em **Hub de Notificação** e, em seguida, em **Criação Rápida**.

   	![][27]

3. Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um novo Hub de Notificação**.

   	![][28]

4. Clique no namespace que você acabou de criar (geralmente ***nome do hub de notificação*-ns**) e clique na guia **Configurar** na parte superior.

   	![][29]

5. Clique na guia **Hubs de Notificação** na parte superior e clique no hub de notificação que você acabou de criar.

   	![][210]

6. Selecione a guia **Configurar** na parte superior e clique em **Carregar** para as configurações de notificação da Apple. Em seguida, selecione o certificado **.p12** que você exportou anteriormente e a senha para o certificado. Certifique-se de escolher se você deseja usar a **Produção** (se desejar enviar notificações por push para os usuários que adquiriram seu aplicativo na loja) ou o serviço de envio por push **Área Restrita** (durante o desenvolvimento).

   	![][211]

7. Clique na guia **Painel** na parte superior e clique em **Informações de Conexão**. Anote as duas cadeias de conexão.

   	![][212]

Seu hub de notificação agora está configurado para funcionar com o APNs e você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações.

<h2><a name="connecting-app"></a>Conectando seu aplicativo ao Hub de Notificação</h2>

### Baixa a biblioteca WindowsAzure.Messaging

Este assembly fornece uma maneira fácil para registrar-se ao os Hubs de Notificação do Azure. Pode se baixar usando as instruções abaixo ou pode ser encontrada no [download de exemplos][GitHub].

1. Baixar a fonte para [WindowsAzure.Messaging] do GitHub.

2. Compile o projeto e encontre o assembly de saída **WindowsAzure.Messaging.dll** - isso será necessário ao configurar seu aplicativo Xamarin.iOS abaixo.


### Criar um novo projeto

1. No Xamarin Studio, crie um novo projeto do iOS e selecione o modelo **Aplicativo de Modo de Exibição Único**.

   	![][31]

2. Primeiro adicionar a referência ao componente de serviços móveis do Azure. No modo de exibição Solução, clique com botão direito na pasta **Componentes** de seu projeto e escolha **Obter Mais Componentes**. Pesquise o componente **Serviços móveis do Azure** e adicione o componente ao seu projeto.

3. Agora adicione uma referência à biblioteca WindowsAzure.Messaging que baixamos anteriormente. Clique com o botão direito do mouse na pasta **Referências** e escolha **Editar referências...** Na guia **Assembly .Net** encontrar o **WindowsAzure.Messaging.dll** e 

4. Em **AppDelegate.cs**, adicione as seguintes instruções using:

		using Microsoft.WindowsAzure.MobileServices;
    using WindowsAzure.Messaging;

5. Declarar uma instância de **SBNotificationHub**:

		private SBNotificationHub Hub { get; set; }
		
6. Criar uma classe **Constants.cs** com as seguintes variáveis:

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
	
	
7. Em **AppDelegate.cs** atualize o **FinishedLaunching()** para coincidir com o seguinte:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

            return true;
        }

8. Substituir o método **RegisteredForRemoteNotifications()** em **AppDelegate.cs**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null) 
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                } 

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

9. Substituir o método **ReceivedRemoteNotification()** em **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
        
10. Criar o seguinte método **ProcessNotification()** em **AppDelegate.cs**:

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying 
                // "  aps:{alert:"alert msg here"}  " this will work fine.
                // But if you're using a complex alert with Localization keys, etc., 
                // your "alert" object from the aps dictionary will be another NSDictionary. 
                // Basically the json gets dumped right into a NSDictionary, 
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }           
            }
        }

    <div class="dev-callout"><b>Observação</b>
	<p>Você pode optar por substituir **FailedToRegisterForRemoteNotifications()** para lidar com situações incluindo a falta de conexão de rede etc.</p>
    </div>

	
11. Execute o aplicativo em seu dispositivo.

<h2><a name="send"></a>Enviar notificação de seu back-end</h2>

Você pode enviar notificações usando Hubs de Notificação de qualquer back-end usando <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dn223264.aspx">interface REST</a>. Neste tutorial, você enviará notificações com um aplicativo de console .NET e com um Serviço Móvel usando um script de nó.

Para enviar notificações usando um aplicativo .NET:

1.  Crie um novo aplicativo de console do Visual C#: 

   	![][213]

2. Adicione uma referência ao SDK do Service Bus do Azure com o <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacote NuGet do WindowsAzure.ServiceBus</a>. No menu principal do Visual Studio, clique em **Ferramentas**, em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**. Na janela do console, digite:

        Install-Package WindowsAzure.ServiceBus and press Enter.

2. Abra o arquivo Program.cs e adicione a seguinte instrução:

        using Microsoft.ServiceBus.Notifications;

3. Em sua classe 'Program', adicione o seguinte método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Adicione a seguinte linha em seu método 'Main':

         SendNotificationAsync();
		 Console.ReadLine();

5.  Pressione a tecla F5 para executar o aplicativo. Você deverá receber um alerta em seu dispositivo. Se você estiver usando Wi-Fi, certifique-se de que a conexão esteja funcionando.

Você encontrará todas as cargas possíveis no [Guia de Programação Local e de Notificação por Push] da Apple.

Para enviar uma notificação usando um Serviço Móvel, consulte a [Introdução aos Serviços Móveis] e faça o seguinte:

1. Faça logon no [Portal de Gerenciamento do Azure] e selecione seu Serviço Móvel.

2. Selecione a guia **Agendador** na parte superior.

   	![][215]

3. Crie um novo trabalho agendado, insira um nome e selecione **Sob demanda**.

   	![][216]

4.  Quando o trabalho for criado, clique no nome do trabalho. Em seguida, clique na guia **Script** na barra superior.

5.  Insira o script a seguir em sua função de Agendador. Substitua os espaços reservados pelo nome de seu hub de notificação e pela cadeia de conexão para *DefaultFullSharedAccessSignature* que você obteve anteriormente. Clique em **Salvar**.

		var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
		notificationHubService.apns.send(
	    	null,
    		{"aps":
        		{
          		"alert": "Hello from Mobile Services!"
        		}
    		},
    		function (error)
    		{
	        	if (!error) {
    	        	console.warn("Notification successful");
        		}
    		}
		);


6.  Clique em **Executar uma vez** na barra inferior. Você deverá receber um alerta em seu dispositivo.

## <a name="next-steps"> </a>Próximas etapas

Neste simples exemplo, você difunde notificações para todos os seus dispositivos iOS. Para selecionar usuários de destinos específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá consultar [Usar Hubs de Notificação para enviar notícias de última hora]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação] e em [Instruções sobre Hubs de Notificação para iOS].

<!-- Anchors. -->
[Gerar a solicitação de assinatura de certificado]: #certificates
[Registrar seu aplicativo e habilitar as notificações por push]: #register
[Criar um perfil de provisionamento para o aplicativo]: #profile
[Configurar seu Hub de Notificação]: #configure-hub
[Conectando seu aplicativo ao Hub de Notificação]: #connecting-app
[Enviar notificações de seu back-end]: #send
[Próximas etapas]:#next-steps

<!-- Images. -->
[5]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step10.png
[18]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[105]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[SDK do iOS dos Serviços Móveis]: http://go.microsoft.com/fwLink/?LinkID=266533
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-xamarin-ios
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/jj927170.aspx
[Instruções sobre Hubs de Notificação para iOS]: http://msdn.microsoft.com/pt-br/library/jj927168.aspx
[Instalar o Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portal de Provisionamento do iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Usar Hubs de notificação para enviar notificações por push aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-aspnet
[Usar Hubs de Notificação para enviar notícias de última hora]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet

[Guia de Programação Local e de Notificações por Push]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Serviço de Notificação por Push da Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Componente dos Serviços Móveis do Azure]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.iOS]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
