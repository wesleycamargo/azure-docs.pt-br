<properties linkid="develop-notificationhubs-tutorials-get-started-ios" urlDisplayName="Introdução" pageTitle="Introdução aos Hubs de Notificação do Azure" metaKeywords="" description="Saiba como usar os Hubs de Notificação do Azure para notificações por push" metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Introdução aos Hubs de Notificação" authors="elioda" solutions="" manager="" editor="" />

# Introdução aos Hubs de Notificação
<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/manage/services/notification-hubs/getting-started-windows-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/notification-hubs-ios-get-started/" title="iOS" class="current">iOS</a><a href="/pt-br/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/pt-br/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>	

Este tópico mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo iOS. 
Neste tutorial, você cria um aplicativo para iOS em branco, que recebe notificações por push, usando o serviço de Notificação por Push da Apple (APNs). Quando concluir, você poderá transmitir notificações por push a todos os dispositivos que executarem seu aplicativo usando o hub de notificação.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Gerar a solicitação de assinatura de certificado] 
2. [Registrar seu aplicativo e habilitar as notificações por push]
3. [Criar um perfil de provisionamento para o aplicativo]
4. [Configurar seu Hub de Notificação]
5. [Conectando seu aplicativo ao Hub de Notificação]
6. [Enviar notificações a partir de seu back-end]

Este tutorial demonstra o simples cenário de transmissão usando hubs de notificação. Siga o próximo tutorial para saber como usar hubs de notificação para atender usuários e grupos de dispositivos específicos. Este tutorial requer os seguintes pré-requisitos:

+ [SDK de Serviços Móveis do iOS]
+ [XCode 4.5][Install Xcode] 
+ Um dispositivo compatível com o iOS 5.0 (ou versão posterior)
+ Associação ao Programa de Desenvolvedores do iOS

   <div class="dev-callout"><b>Observação</b>
   <p>Devido aos requisitos de configuração das notificações por push, você deve implantar e testar as notificações por push em um dispositivo compatível com o iOS (iPhone ou iPad), em vez de usar o emulador.</p>
   </div>

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre hubs de notificação para aplicativos para iOS. 

<div class="dev-callout"><strong>Observação</strong> <p>Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação gratuita do Azure</a>.</p></div>

O Serviço de Notificação por Push da Apple (APNS) usa certificados para autenticar seu serviço móvel. Siga as seguintes instruções para criar os certificados necessários e carregá-los em seu Serviço Móvel. Para obter a documentação oficial do APNS, consulte [Serviço de Notificação por Push da Apple].



<h2><a name="certificates"></a><span class="short-header">Gerar o arquivo CSR</span>Gerar o arquivo de Solicitação de Assinatura de Certificado</h2>

Primeiro, você deve gerar o arquivo CSR (Solicitação de Assinatura de Certificado), que é usado pela Apple para gerar um certificado assinado.

1. Na pasta Utilidades, execute a ferramenta Acesso do Conjunto de Chaves.

2. Clique em **Acesso do Conjunto de Chaves**, expanda o **Assistente de Certificado** e clique em **Solicitar um Certificado a partir de uma Autoridade de Certificação...**.
 
  	![][5]

3. Selecione seu **Endereço de E-mail de Usuário** e **Nome Comum**, certifique-se de que a opção **Salvar em disco** esteja selecionada e, em seguida, clique em **Continuar**. Deixe o campo **Endereço de E-mail do CA** em branco, pois este não é necessário.

  	![][6]

4. Digite um nome para o arquivo CSR (Solicitação de Assinatura de Certificado) em **Salvar como**, selecione o local em **Onde** e, em seguida, clique em **Salvar**.

  	![][7]
  
  Isto salvará o arquivo CSR no local selecionado. O local padrão estará situado na área de trabalho. Lembre-se do local escolhido para este arquivo.

Em seguida, registre seu aplicativo com a Apple, habilite as notificações por push e carregue este CSR exportado para criar um certificado de push.

<h2><a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Registrar seu aplicativo para notificações por push</h2>

Para enviar notificações por push para um aplicativo de serviços móveis para iOS, você deve registrar seu aplicativo na Apple e registrá-lo também para notificações por push.  

1. Se você ainda não registrou seu aplicativo, navegue até o <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a> no Centro de Desenvolvedores da Apple, faça logon com sua ID da Apple, clique em **Identificadores**, em seguida, clique em **IDs de Aplicativo** e, finalmente, clique no sinal **+** para registrar um novo aplicativo.

   	![][B102] 

2. Digite um nome para seu aplicativo em **Descrição**, insira o valor _MobileServices.Quickstart_ em **Identificador de Pacote**, marque a opção "Notificações por Push" na seção "Serviços de Aplicativo" e, em seguida, clique em **Continuar**. Este exemplo usa a ID **MobileServices.Quickstart**, mas você não pode reutilizar esta mesma ID, pois as IDs de aplicativo devem ser exclusivas para todos os usuários. Portanto, recomenda-se acrescentar seu nome completo ou as iniciais após o nome do aplicativo. 

   	![][B103]
   
   	Isto gerará sua ID de aplicativo e solicitará a você que envie as informações usando a opção **Enviar**. Clique em **Enviar**.
   
   	![][B104] 
   
   	Após clicar em **Enviar**, você verá a tela **Registro concluído**, conforme mostrado abaixo. Clique em **Concluído**.
   
   	![][B105]

    > [WACOM.NOTE] Se escolher oferecer um valor de <strong>Identificador de Pacote</strong> diferente de <i>MobileServices.Quickstart</i>, você deverá também atualizar o valor do identificador de pacote em seu projeto Xcode.

3. Localize a ID do aplicativo que você acabou de criar e clique na respectiva linha. 

   	![][B106]
   
   	Ao clicar na ID do aplicativo, os detalhes sobre o aplicativo e a ID do aplicativo serão exibidos. Clique no botão **Configurações**.
   
   	![][B107] 
   
4. Role até a parte inferior da tela e clique no botão **Criar Certificado...** na seção **Certificado SSL por Push para Desenvolvimento**.

   	![][B108] 

   	Isto exibirá o assistente "Adicionar Certificado para iOS".
   
   	![][B108] 


    > [WACOM.NOTE] Este tutorial usa um certificado de desenvolvimento. O mesmo processo é usado para registrar um certificado de produção. Verifique se você definiu o mesmo tipo de certificado ao carregar o certificado para os Serviços Móveis.

5. Clique em **Selecionar Arquivo**, vá até o local em que você salvou o arquivo CSR criado na primeira tarefa e clique em **Gerar**. 

  	![][B110]
  
6. Depois que o certificado for criado pelo portal, clique no botão **Baixar** e, em seguida, clique em **Concluído**.
 
  	![][B111]  

   	Isto baixará o certificado de assinatura e salvará este certificado em seu computador, na pasta Downloads. 

  	![][B9] 

    > [WACOM.NOTE] Por padrão, o arquivo baixado, um certificado de desenvolvimento, é denominado **aps_development.cer**.

7. Clique duas vezes no certificado de push baixado **aps_development.cer**.

   	Isto instalará o novo certificado no Conjunto de Chaves, conforme mostrado abaixo:

   	![][B10]

    > [WACOM.NOTE] O nome em seu certificado pode ser diferente, mas ele será prefixado como **Serviços de Notificação por Push do iOS para Desenvolvimento da Apple:**.

Posteriormente, você usará este certificado para gerar um arquivo .p12 e carregá-lo em Serviços Móveis para habilitar a autenticação com o APNS.


<h2><a name="profile"></a><span class="short-header">Provisionar o aplicativo</span>Criar um perfil de provisionamento para o aplicativo</h2>
 
1. No <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a>, selecione **Perfis de Provisionamento**, **Todos** e, em seguida, clique no botão **+** para criar um novo perfil. Isto exibirá o Assistente **Adicionar Perfil de Provisionamento do iOS**.

   	![][120]

2. Selecione **Desenvolvimento de Aplicativos do iOS**, em **Desenvolvimento**, como o tipo de perfil de provisionamento, e clique em **Continuar**.

   	![][121]

3. Em seguida, selecione a ID para o aplicativo Quickstart de Serviços Móveis na lista suspensa **ID do Aplicativo** e clique em **Continuar**.

   	![][122]

4. Na tela **Selecionar certificados**, selecione o certificado que você criou anteriormente e clique em **Continuar**.
  
   	![][123]

5. Em seguida, selecione os **Dispositivos** que serão usados para teste e clique em **Continuar**.

   	![][124]

6. Por fim, escolha um nome para o perfil em **Nome do Perfil**, clique em **Gerar** e em **Concluído**.

   	![][125]
   
   	![][126]
	
	Isto criará um novo perfil de provisionamento.

7. No Xcode, abra o Organizador, escolha a exibição Dispositivos, selecione **Perfis de Provisionamento** na seção **Biblioteca** no painel esquerdo e importe o perfil de provisionamento que você acabou de criar.

8. À esquerda, selecione seu dispositivo e importe novamente o perfil de provisionamento. 

9. Em Acesso do Conjunto de Chaves, clique com o botão direito do mouse no novo certificado, clique em **Exportar**, digite um nome para seu certificado, selecione o formato **.p12** e, em seguida, clique em **Salvar**.

   	![][18]

	Anote o nome do arquivo e o local do certificado exportado.

<h2><a name="configure-hub"></a><span class="short-header">Configurar seu Hub de Notificação</span>Configurar seu Hub de Notificação</h2>

1. Faça logon no [Portal de Gerenciamento do Azure] e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativo**, em **Service Bus**, em **Hub de Notificação** e, em seguida, em **Criação Rápida**.

   	![][27]

3. Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um novo Hub de Notificação**.

   	![][28]

4. Clique no namespace que você acabou de criar (geralmente ***nome do hub de notificação* -ns**) e clique na guia **Configurar** na parte superior.

   	![][29]

5. Clique na guia **Hubs de Notificação** na parte superior e, em seguida, clique no hub de notificação que você acabou de criar.

   	![][210]

6. Selecione a guia **Configurar** na parte superior e, em seguida, clique em **Carregar** para as configurações de notificação da Apple. Em seguida, selecione o certificado **.p12** que você exportou anteriormente e a senha para o certificado. Certifique-se de escolher se você deseja usar a **Produção** (se desejar enviar notificações por push para os usuários que adquiriram seu aplicativo na loja) ou o serviço de envio por push **Área Segura** (durante o desenvolvimento).

   	![][211]

7. Clique na guia **Painel** na parte superior e, em seguida, clique em **Informações de Conexão**. Tome nota das duas cadeias de conexão.

   	![][212]

Seu hub de notificação agora está configurado para funcionar com o APNs e você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações.

<h2><a name="connecting-app"></a><span class="short-header">Conectando seu aplicativo</span>Conectando seu aplicativo ao Hub de Notificação</h2>

1. No XCode, crie um novo projeto do iOS e selecione o modelo **Aplicativo de Modo de Exibição Único**.

   	![][31]

2. Em **Destinos**, clique no nome do projeto e expanda a opção **Identidade de Assinatura de Código** e, em seguida, em **Depurar**, selecione o perfil de identidade de assinatura de código. Além disto, se estiver usando uma versão mais recente do XCode, altere a opção **Níveis** de **Básico** para **Todos** e defina o item de linha **Perfil de Provisionamento** para o perfil de provisionamento. 

   	![][32]

3. Baixe o SDK de Serviços Móveis do Azure para iOS. Abra o arquivo .zip e arraste a pasta WindowsAzureMessaging.framework para a pasta Estrutura em seu projeto do XCode. Selecione **Copiar itens na pasta do grupo de destino** e, em seguida, clique em **OK**.

   	![][33]

4. Em seu arquivo AppDelegate.h, adicione a seguinte diretiva de importação:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5. Em seu arquivo AppDelegate.m, adicione o seguinte código no método `didFinishLaunchingWithOptions`:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

6. No mesmo arquivo, adicione o seguinte método:

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {    
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
		                              @"<connection string>" notificationHubPath:@"mynh"];
		    
		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
	    	}];
		}

7. *(opcional)* Novamente, no mesmo arquivo, adicione o seguinte método para exibir um **UIAlert** caso a notificação seja recebida enquanto o aplicativo estiver ativo:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		    @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}
	
8. Execute o aplicativo em seu dispositivo.

<h2><a name="send"></a><span class="short-header">Enviar notificação</span>Enviar notificação a partir de seu back-end</h2>

Você pode enviar notificações usando Hubs de Notificação de qualquer back-end usando nossa <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dn223264.aspx">interface REST</a> Neste tutorial, você enviará notificações com um aplicativo de console .NET e com um Serviço Móvel usando um script de nó.

Para enviar notificações usando um aplicativo .NET:

1. Crie um novo aplicativo de console com o Visual C#: 

	![][213]

2. Adicione uma referência ao SDK do Service Bus do Azure com o <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacote NuGet do WindowsAzure.ServiceBus</a>. No menu principal do Visual Studio, clique em **Ferramentas**, em **Gerenciador de Pacotes da Biblioteca** e, em seguida, em **Console do Gerenciador de Pacotes**. Então, na janela do console, digite:

        Install-Package WindowsAzure.ServiceBus

	e pressione Enter.

2. Abra o arquivo Program.cs e adicione a seguinte instrução:

        using Microsoft.ServiceBus.Notifications;

3. Em sua classe `Programa`, adicione o método seguinte. Certifique-se de substituir o espaço reservado "nome do hub" pelo nome do hub de notificação que é exibido no portal na guia **Hubs de Notificação** (como **mynotificationhub2** no exemplo anterior):

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Em seguida, adicione a seguinte linha em seu método `Main`:

         SendNotificationAsync();
		 Console.ReadLine();

5. Pressione a tecla F5 para executar o aplicativo. Você deverá receber um alerta em seu dispositivo. Se você estiver usando Wi-Fi, certifique-se de que a conexão esteja funcionando.

Você encontrará todas as cargas possíveis no [Guia de Programação Local e de Notificação por Push] da Apple.

Para enviar uma notificação usando um Serviço Móvel, consulte a [Introdução aos Serviços Móveis] e, em seguida, faça o seguinte:

1. Faça logon no [Portal de Gerenciamento do Azure] e selecione seu Serviço Móvel.

2. Selecione a guia **Agendador** na parte superior.

   	![][215]

3. Crie um novo trabalho agendado, insira um nome e, em seguida, selecione **Sob demanda**.

   	![][216]

4. Quando o trabalho for criado, clique no nome do trabalho. Em seguida, clique na guia **Script** na barra superior.

5. Insira o script a seguir na função do seu agendador. Certifique-se de substituir os espaços reservados pelo nome de seu hub de notificação e pela cadeia de conexão para a *DefaultFullSharedAccessSignature* que você obteve anteriormente. Clique em **Salvar**.

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


6. Clique em **Executar uma Vez** na barra inferior. Você deverá receber um alerta em seu dispositivo.

## <a name="next-steps"> </a>Próximas etapas

Neste simples exemplo, você envia notificações para todos os seus dispositivos iOS. Para selecionar usuários de destinos específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá consultar [Usar Hubs de Notificação para enviar notícias de última hora]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação] e em [Instruções sobre Hubs de Notificação para iOS].

<!-- Anchors. -->
[Gerar a solicitação de assinatura de certificado]: #certificates
[Registrar seu aplicativo e habilitar as notificações por push]: #register
[Criar um perfil de provisionamento para o aplicativo]: #profile
[Configurar seu Hub de Notificação]: #configure-hub
[Conectando seu aplicativo ao Hub de Notificação]: #connecting-app
[Enviar notificações a partir de seu back-end]: #send
[Próximas etapas]:#next-steps

<!-- Images. -->
[5]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step10.png


[18]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step18.png


[105]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
[33]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png


[B102]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-02.png
[B103]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-03.png
[B104]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-04.png
[B105]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-05.png
[B106]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-06.png
[B107]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-07.png
[B108]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-08.png
[B110]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-10.png
[B111]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-11.png
[B9]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step9.png
[B10]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step10.png


<!-- URLs. -->
[SDK de Serviços Móveis do iOS]: http://go.microsoft.com/fwLink/?LinkID=266533
[Enviar página de aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-ios
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/jj927170.aspx
[Instruções sobre Hubs de Notificação para iOS]: http://msdn.microsoft.com/pt-br/library/jj927168.aspx
[Instalar o Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portal de Provisionamento do iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Usar Hubs de Notificação para enviar notificações por push aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-aspnet
[Usar Hubs de Notificação para enviar notícias de última hora]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet

[Guia de Programação Local e de Notificação por Push]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1

