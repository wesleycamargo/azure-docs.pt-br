<properties
	pageTitle="Introdução aos Hubs de Notificação para aplicativos Xamarin iOS"
	description="Aprenda a usar Hubs de notificação do Azure para enviar notificações por push para um aplicativo do iOS Xamarin."
	services="notification-hubs"
	documentationCenter="xamarin"
	authors="ysxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="04/14/2015"
	ms.author="yuaxu"/>

# Introdução aos Hubs de Notificação

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

##Visão geral

Este tópico mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo iOS. Neste tutorial, você cria um aplicativo Xamarin.iOS em branco que recebe notificações por push usando o serviço de Notificação por Push da Apple (APNs). Ao concluir, você poderá transmitir notificações por push a todos os dispositivos que executam seu aplicativo usando o hub de notificação. O código concluído está disponível na amostra do [aplicativo NotificationHubs][GitHub].

Este tutorial demonstra o cenário de transmissão simples usando hubs de notificação.

##Pré-requisitos

Este tutorial requer os seguintes pré-requisitos:

+ [XCode 6.0][Install Xcode]
+ Um dispositivo compatível com o iOS 7.0 (ou versão posterior)
+ Associação no Programa de Desenvolvedores de iOS
+ [Xamarin.iOS]
+ [Componente dos Serviços Móveis do Azure]

   > [AZURE.NOTE] Devido aos requisitos de configuração das notificações por push, você deve implantar e testar as notificações por push em um dispositivo compatível com o iOS (iPhone ou iPad) em vez de usar o emulador.

Completar este tutorial é um pré-requisito para todos os outros tutoriais de hub de notificação para aplicativos Xamarin.iOS.

> [AZURE.IMPORTANT] Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

O APNS (Serviço de Notificação por Push da Apple) usa certificados para autenticar seu serviço móvel. Siga estas instruções para criar os certificados necessários e carregá-los no seu Serviço Móvel. Para a documentação oficial do recurso APNS, consulte [Serviço de Notificação por Push da Apple].


##<a name="certificates"></a>Gerar o arquivo de Solicitação de Assinatura de Certificado

Primeiro, gere o arquivo CSR (Solicitação de Assinatura de Certificado), que é usado pela Apple para gerar um certificado assinado.

1. Na pasta Utilities, execute a ferramenta Acesso do Conjunto de Chaves.

2. Clique em **Acesso do Conjunto de Chaves**, expanda **Assistente de Certificado** e clique em **Solicitar um Certificado de uma Autoridade de Certificação...**.

  	![][5]

3. Selecione seu **Endereço de e-mail do usuário**, digite os valores **Nome comum** e **Endereço de e-mail do CA**, verifique se a opção **Salvo no disco** está selecionada e clique em **Continuar**.

  	![][6]

4. Digite um nome para o arquivo CSR (Solicitação de Assinatura de Certificado) em **Salvar como**, selecione o local em **Onde** e, em seguida, clique em **Salvar**.

  	![][7]

  	Isto salvará o arquivo CSR no local selecionado. O local padrão estará situado na área de trabalho. Lembre-se do local escolhido para esse arquivo.

Em seguida, registre seu aplicativo na Apple, habilite as notificações por push e carregue este CSR exportado para criar um certificado de push.

##<a name="register"></a>Registrar seu aplicativo para notificações por push

Para poder enviar notificações por push para um aplicativo iOS dos serviços móveis, você deve registrar seu aplicativo na Apple e também registrar para notificações por push.

1. Se você ainda não registrou seu aplicativo, navegue até o <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a> no Apple Developer Center, faça logon com a sua Apple ID, clique em **Identificadores**, em seguida, clique em **IDs de Aplicativo** e, finalmente, clique no sinal de **+** para registrar um novo aplicativo.

   	![][105]

2. Digite um nome para o seu aplicativo em **Descrição**, insira um valor para **Identificador de Pacote**, marque a opção "Notificações por Push" na seção "Serviços de Aplicativo" e depois clique em **Continuar**.

   	![][106]

   	![][107]

   	![][108]


	Isto gerará sua ID de aplicativo e solicitará a você que envie as informações. Clique em **Enviar**.

   	![][109]

	Após clicar em **Enviar**, você verá a tela **Registro concluído** conforme mostrado abaixo. Clique em **Concluído**.

   	![][110]

	> [AZURE.NOTE] Se escolher oferecer um valor de **Identificador de Pacote** diferente de **MobileServices.Quickstart**, você deverá também atualizar o valor do identificador de pacote em seu projeto Xcode.

3. Localize a ID de aplicativo que acabou de criar e clique na respectiva linha.

   	![][111]

	Ao clicar na ID do aplicativo, os detalhes sobre o aplicativo e a ID do aplicativo serão exibidos:

   	![][112]

   	![][113]

4. Clique em **Editar**, role até a parte inferior da tela e clique em **Criar Certificado...** na seção **Certificado SSL por Push para Desenvolvimento**.

   	![][114]

	Isso exibirá o assistente "Adicionar Certificado de iOS".

   	![][115]

	> [AZURE.NOTE] Este tutorial usa um certificado de desenvolvimento. O mesmo processo é usado para registrar um certificado de produção. Verifique apenas se você definiu o mesmo tipo de certificado ao carregar o certificado para os Serviços Móveis.

5. Clique em **Selecionar Arquivo**, vá até o local onde você salvou o arquivo CSR criado na primeira tarefa e clique em **Gerar**.

  	![][116]

6. Depois que o certificado for criado pelo portal, clique em **Baixar** e clique em **Concluído**.

  	![][118]

  	![][119]

   	Isso baixará o certificado de assinatura e salvará este certificado em seu computador na pasta **Downloads**.

  	![][9]

    > [AZURE.NOTE] Por padrão, o arquivo baixado, um certificado de desenvolvimento, é denominado **aps_development.cer**.

7. Clique duas vezes no certificado de push baixado, **aps_development.cer**.

	Isso instalará o novo certificado no Conjunto de Chaves, conforme mostrado abaixo:

   	![][10]

	> [AZURE.NOTE]
	> O nome em seu certificado pode ser diferente, mas ele será prefixado como <strong>erviços de Notificação por Push do iOS para Desenvolvimento da Apple:</strong>.

	Posteriormente, você usará este certificado para gerar um arquivo .p12 e carregá-lo em seu hub de notificações para habilitar as notificações por push por APNS.

##<a name="profile"></a>Criar um perfil de provisionamento para o aplicativo

1. De volta ao <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a>, selecione **Perfis de Provisionamento**, escolha **Tudo** e clique no botão **+** para criar um novo perfil. Isto exibirá o Assistente **Adicionar Perfil de Provisionamento do iOS**.

   	![][120]

2. Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento e clique em **Continuar**.

   	![][121]

3. Em seguida, selecione a ID para o aplicativo Quickstart de Serviços Móveis na lista suspensa **ID do Aplicativo** e clique em **Continuar**.

   	![][122]

4. Na tela **Selecionar certificados**, selecione o certificado criado anteriormente e clique em **Continuar**.

   ![][123]

5. Em seguida, selecione os **Dispositivos** a serem usados no teste e clique em **Continuar**.

   	![][124]

6. Finalmente, selecione um nome para o perfil em **Nome do Perfil**, clique em **Gerar** e, em seguida, clique em **Concluído**.

   	![][125]

   	![][126]

  	Isso criará um novo perfil de provisionamento.

7. No Xcode, abra o Organizador, escolha a exibição Dispositivos, selecione **Perfis de Provisionamento** na seção **Biblioteca** no painel esquerdo e importe o perfil de provisionamento que você acabou de criar.

8. À esquerda, selecione seu dispositivo e importe novamente o perfil de provisionamento.

9. Em Acesso do Conjunto de Chaves, clique com o botão direito do mouse no novo certificado, clique em **Exportar**, digite um nome para seu certificado, selecione o formato **.p12** e clique em **Salvar**.

   	![][18]

  	Anote o nome do arquivo e o local do certificado exportado.

Isso garantirá que o projeto Xcode use o novo perfil para a assinatura do código. Em seguida, carregue o certificado em seu hub de notificação.

##<a name="configure-hub"></a>Configurar seu Hub de Notificação

1. Faça logon no [Portal de Gerenciamento do Azure], e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativo**, em **Service Bus**, em **Hub de Notificação** e, em seguida, **Criação Rápida**.

   	![][27]

3. Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um novo Hub de Notificação**.

   	![][28]

4. Clique no namespace que você acabou de criar (geralmente o ***nome do hub de notificação*-ns**), e clique na guia **Configurar** na parte superior.

   	![][29]

5. Clique na guia **Hubs de Notificação** na parte superior e clique no hub de notificação que você acabou de criar.

   	![][210]

6. Selecione a guia **Configurar** na parte superior e clique em **Carregar** para as configurações de notificação da Apple. Em seguida, selecione o certificado **.p12** que você exportou anteriormente e a senha para o certificado. Certifique-se de escolher se você deseja usar a **Produção** (se desejar enviar notificações por push para os usuários que adquiriram seu aplicativo na loja) ou o serviço de envio por push **Área Segura** (durante o desenvolvimento).

   	![][211]

7. Clique na guia **Painel** na parte superior e clique em **Informações de Conexão**. Anote as duas cadeias de conexão.

   	![][212]

Seu hub de notificação agora está configurado para funcionar com o APNs e você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações.

##<a name="connecting-app"></a>Conexão de seu aplicativo ao Hub de Notificação

### Criar um novo projeto

1. No Xamarin Studio, crie um novo projeto do iOS e selecione o modelo **API unificado > Aplicativo de Modo de Exibição Único**.

   	![][31]

2. Primeiro, adicione a referência ao componente de mensagens do Azure. No modo de exibição Solução, clique com botão direito na pasta **Componentes** de seu projeto e escolha **Obter Mais Componentes**. Pesquise o componente **Mensagens do Azure** e adicione o componente ao seu projeto.

3. Em **AppDelegate.cs**, adicione a seguinte instrução using:

    using WindowsAzure.Messaging;

4. Declarar uma instância de **SBNotificationHub**:

		private SBNotificationHub Hub { get; set; }

5. Criar uma classe **Constants.cs** com as seguintes variáveis:

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. Em **AppDelegate.cs** atualize o **FinishedLaunching()** para coincidir com o seguinte:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert |
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);

            return true;
        }

7. Substituir o método **RegisteredForRemoteNotifications()** em **AppDelegate.cs**:

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

8. Substituir o método **ReceivedRemoteNotification()** em **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. Criar o seguinte método **ProcessNotification()** em **AppDelegate.cs**:

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

    > [AZURE.NOTE]Você pode optar por substituir **FailedToRegisterForRemoteNotifications()** para lidar com situações que incluem a falta de conexão de rede, entre outras.


10. Execute o aplicativo em seu dispositivo.

##<a name="send"></a>Enviar notificação de seu back-end

Você pode enviar notificações usando Hubs de Notificação de qualquer back-end usando a <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>. Neste tutorial, você enviará notificações com um aplicativo de console .NET e com um Serviço Móvel usando um script de nó.

Para enviar notificações usando um aplicativo .NET:

1. Crie um novo aplicativo de console do Visual C#:

   	![][213]

2. Adicione uma referência ao SDK do Barramento de Serviço do Azure com o <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacote NuGet do WindowsAzure.ServiceBus</a>. No menu principal do Visual Studio, clique em **Ferramentas**, em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**. Na janela do console, digite:

        Install-Package WindowsAzure.ServiceBus and press Enter.

2. Abra o arquivo Program.cs e adicione a seguinte instrução:

        using Microsoft.ServiceBus.Notifications;

3. Em sua classe `Program`, adicione o método a seguir:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{"aps":{"alert":"Hello from .NET!"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Adicione a seguinte linha em seu método `Main`:

         SendNotificationAsync();
		 Console.ReadLine();

5. Pressione a tecla F5 para executar o aplicativo. Você deverá receber um alerta em seu dispositivo. Se você estiver usando Wi-Fi, certifique-se de que a conexão esteja funcionando.

Você encontrará todas as cargas possíveis no [Guia de Programação Local e de Notificação por Push] da Apple.

Para enviar uma notificação usando um Serviço Móvel, consulte a [Introdução aos Serviços Móveis] e faça o seguinte:

1. Faça logon no [Portal de Gerenciamento do Azure] e selecione seu Serviço Móvel.

2. Selecione a guia **Agendador** na parte superior.

   	![][215]

3. Crie um novo trabalho agendado, insira um nome e selecione **Sob demanda**.

   	![][216]

4. Quando o trabalho for criado, clique no nome do trabalho. Em seguida, clique na guia **Script** na barra superior.

5. Insira o script a seguir em sua função de Agendador. Certifique-se de substituir os espaços reservados pelo nome de seu hub de notificação e pela cadeia de conexão para a *DefaultFullSharedAccessSignature* que você obteve anteriormente. Clique em **Salvar**.

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


6. Clique em **Executar uma vez** na barra inferior. Você deverá receber um alerta em seu dispositivo.

## <a name="next-steps"> </a>Próximas etapas

Neste simples exemplo, você envia notificações para todos os seus dispositivos iOS. Para selecionar usuários de destinos específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá consultar [Usar Hubs de Notificação para enviar notícias de última hora]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação] e em [Instruções sobre Hubs de Notificação para iOS].

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Send notifications from your back-end]: #send
[Next Steps]: #next-steps

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
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Introdução aos Serviços Móveis]: /develop/mobile/tutorials/get-started-xamarin-ios
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Instruções sobre Hubs de Notificação para iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Usar Hubs de Notificação para enviar notificações por push aos usuários]: /manage/services/notification-hubs/notify-users-aspnet
[Usar Hubs de Notificação para enviar notícias de última hora]: /manage/services/notification-hubs/breaking-news-dotnet

[Guia de Programação Local e de Notificação por Push]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Serviço de Notificação por Push da Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Componente dos Serviços Móveis do Azure]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.iOS]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS

<!--HONumber=52-->
