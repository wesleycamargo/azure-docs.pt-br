<properties linkid="develop-notificationhubs-tutorials-get-started-ios" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/24/2014" ms.author="krisragh"></tags>

# Introdução aos Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/notification-hubs-ios-get-started/" title="iOS" class="current">iOS</a><a href="/pt-br/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/pt-br/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tópico mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo iOS.
Neste tutorial, você cria um aplicativo para iOS em branco que recebe notificações por push usando o serviço de Notificação por Push da Apple (APNs). Ao concluir, você poderá transmitir notificações por push a todos os dispositivos que executam seu aplicativo usando o hub de notificação.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1.  [Gerar a solicitação de assinatura de certificado][Gerar a solicitação de assinatura de certificado]
2.  [Registrar seu aplicativo e habilitar as notificações por push][Registrar seu aplicativo e habilitar as notificações por push]
3.  [Criar um perfil de provisionamento para o aplicativo][Criar um perfil de provisionamento para o aplicativo]
4.  [Configurar seu Hub de Notificação][Configurar seu Hub de Notificação]
5.  [Conectando seu aplicativo ao Hub de Notificação][Conectando seu aplicativo ao Hub de Notificação]
6.  [Enviar notificações de seu back-end][Enviar notificações de seu back-end]

Este tutorial demonstra o cenário de transmissão simples usando hubs de notificação. Siga o próximo tutorial para aprender a usar hubs de notificação para atender usuários e grupos de dispositivos específicos. Este tutorial requer os seguintes pré-requisitos:

-   [SDK do iOS dos Serviços Móveis][SDK do iOS dos Serviços Móveis]
-   [XCode 4.5][XCode 4.5]
-   Um dispositivo compatível com o iOS 5.0 (ou versão posterior)
-   Associação no Programa de Desenvolvedores de iOS

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b><br /> <p>Devido aos requisitos de configura&ccedil;&atilde;o das notifica&ccedil;&otilde;es por push, voc&ecirc; deve implantar e testar as notifica&ccedil;&otilde;es por push em um dispositivo compat&iacute;vel com o iOS (iPhone ou iPad) em vez de usar o emulador.</p><br /> </div>

</p>
A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre hubs de notificação para aplicativos para iOS.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa ter uma conta ativa do Azure. Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

[WACOM.INCLUDE [Habilitar Notificação por Push da Apple][Habilitar Notificação por Push da Apple]]

## <a name="configure-hub"></a>Configurar seu Hub de Notificação

1.  Em Acesso do Conjunto de Chaves, clique com botão direito do mouse no novo certificado do aplicativo de inicio rápido **Mis certificados**. Clique em **Exportar**, nomeie o arquivo como **.p12** e clique em **Salvar**.

    ![][]

Anote o nome do arquivo e o local do certificado exportado.

> [WACOM.NOTE] Este tutorial cria um arquivo Quickstart.p12. O nome do arquivo e o local podem ser diferentes.

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], e clique em **+NOVO** na parte inferior da tela.

2.  Clique em **Serviços de Aplicativo**, em **Service Bus**, em **Hub de Notificação** e, em seguida, **Criação Rápida**.

    ![][1]

3.  Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um novo Hub de Notificação**.

    ![][2]

4.  Clique no namespace que você acabou de criar (geralmente o ***nome do hub de notificação*-ns**), e clique na guia **Configurar** na parte superior.

    ![][3]

5.  Clique na guia **Hubs de Notificação** na parte superior e clique no hub de notificação que você acabou de criar.

    ![][4]

6.  Selecione a guia **Configurar** na parte superior e clique em **Carregar** para as configurações de notificação da Apple. Em seguida, selecione o certificado **.p12** que você exportou anteriormente e a senha para o certificado. Certifique-se de escolher se você deseja usar a **Produção** (se desejar enviar notificações por push para os usuários que adquiriram seu aplicativo na loja) ou o serviço de envio por push **Área Segura** (durante o desenvolvimento).

    ![][5]

7.  Clique na guia **Painel** na parte superior e clique em **Informações de Conexão**. Anote as duas cadeias de conexão.

    ![][6]

Seu hub de notificação agora está configurado para funcionar com o APNs e você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações.

## <a name="connecting-app"></a>Conectando seu aplicativo ao Hub de Notificação

1.  No XCode, crie um novo projeto do iOS e selecione o modelo **Aplicativo de Modo de Exibição Único**.

    ![][7]

2.  Em **Destinos**, clique no nome do projeto e expanda a opção **Identidade de Assinatura de Código** e, em seguida, em **Depurar**, selecione o perfil de identidade de assinatura de código. Além disto, se estiver usando uma versão mais recente do XCode, altere a opção **Níveis** de **Básico** para **Todos** e defina o item de linha **Perfil de Provisionamento** para o perfil de provisionamento.

    ![][8]

3.  Baixe o SDK de Serviços Móveis do Windows Azure para iOS. Abra o arquivo .zip e arraste a pasta WindowsAzureMessaging.framework para a pasta Estrutura em seu projeto do XCode. Selecione **Copiar itens na pasta do grupo de destino** e, em seguida, clique em **OK**.

    ![][9]

4.  Em seu arquivo AppDelegate.h, adicione a seguinte diretiva de importação:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5.  Em seu arquivo AppDelegate.m, adicione o seguinte código no método `didFinishLaunchingWithOptions`:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

6.  No mesmo arquivo, adicione o seguinte método:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {    
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
                                      @"<connection string>" notificationHubPath:@"mynh"];

            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

7.  *(opcional)* Novamente, no mesmo arquivo, adicione o seguinte método para exibir um **UIAlert** caso a notificação seja recebida enquanto o aplicativo estiver ativo:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

8.  Execute o aplicativo em seu dispositivo.

## <a name="send"></a>Enviar notificações de seu back-end

Você pode enviar notificações usando Hubs de Notificação de qualquer back-end usando a [interface REST][interface REST] Neste tutorial você envia as notificações com um aplicativo de console do .NET. Para um exemplo de como enviar notificações de um back-end do Serviços Móveis do Azure integrado com Hubs de notificação, consulte **Introdução às notificações push no Mobile\_Services** ([back-end do .NET][back-end do .NET] | [Back-end do JaveScript][back-end do .NET]). Para um exemplo de como enviar notificações usando o REST APIs, consulte **Como usar Hubs de Notificação a partir de Java/PHP** ([Java][Java] | [PHP][PHP])

1.  No Visual Studio, no menu **Arquivo** selecionar **Novo** e, em seguida, **Projeto...**, em seguida, em **Visual C#** clique no **Windows** e **Aplicativo de console** e clique em **OK**.

    [][20]

    Isso cria um novo projeto de aplicativo de console.

2.  No menu **Ferramentas**, clique em **Gerenciador de Pacotes de Biblioteca** e, em seguida, selecione **Console do Gerenciador de Pacotes**.

    Isso exibe a Console do Gerenciador de Pacotes.

3.  Na janela da console, execute o seguinte comando:

        Install-Package WindowsAzure.ServiceBus

    Isto adiciona uma referência ao SDK do Service Bus do Azure com o [pacote NuGet do WindowsAzure.ServiceBus][pacote NuGet do WindowsAzure.ServiceBus].

4.  Abra o arquivo Program.cs e adicione a seguinte instrução `using`:

        using Microsoft.ServiceBus.Notifications;

5.  Na classe **Programa**, adicione o seguinte método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

    Substitua o espaço reservado de "nome do hub" pelo nome do hub de notificação que aparece no portal, na guia **Hubs de Notificação**. Além disso, substituir o espaço reservado de cadeias de conexão com a cadeia de conexão chamada **DefaultFullSharedAccessSignature** que você obteve na seção "Configurar seu Hub de Notificação."

    > [WACOM.NOTE]Observe que essa é a cadeia de conexão com acesso **Completo**, não com acesso **Escutar**. A cadeia de acesso Escutar não tem permissões para enviar notificações.

6.  Em seguida, adicione as seguintes linhas no método **Main**:

         SendNotificationAsync();
         Console.ReadLine();

7.  Pressione a tecla F5 para executar o aplicativo de console.

    Você deverá receber um alerta em seu dispositivo. Se você estiver usando Wi-Fi, certifique-se de que a conexão esteja funcionando.

Você encontrará todas as cargas possíveis no [Guia de Programação Local e de Notificação por Push][Guia de Programação Local e de Notificação por Push] da Apple.

## <a name="next-steps"> </a>Próximas etapas

Neste simples exemplo, você envia notificações para todos os seus dispositivos iOS. Para selecionar usuários de destinos específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários][Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá consultar [Usar Hubs de Notificação para enviar notícias de última hora][Usar Hubs de Notificação para enviar notícias de última hora]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação][Diretrizes dos Hubs de Notificação].

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Universal]: /pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [Windows Phone]: /pt-br/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /pt-br/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /pt-br/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /pt-br/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [Gerar a solicitação de assinatura de certificado]: #certificates
  [Registrar seu aplicativo e habilitar as notificações por push]: #register
  [Criar um perfil de provisionamento para o aplicativo]: #profile
  [Configurar seu Hub de Notificação]: #configure-hub
  [Conectando seu aplicativo ao Hub de Notificação]: #connecting-app
  [Enviar notificações de seu back-end]: #send
  [SDK do iOS dos Serviços Móveis]: http://go.microsoft.com/fwLink/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [Habilitar Notificação por Push da Apple]: ../includes/enable-apple-push-notifications.md
  []: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-27.png
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [1]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
  [2]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
  [3]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
  [4]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
  [5]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
  [6]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png
  [7]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
  [8]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
  [9]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png
  [interface REST]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn223264.aspx
  [back-end do .NET]: /pt-br/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Java]: /pt-br/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /pt-br/documentation/articles/notification-hubs-php-backend-how-to/
  [pacote NuGet do WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [Guia de Programação Local e de Notificação por Push]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
  [Usar Hubs de Notificação para enviar notificações por push aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-aspnet
  [Usar Hubs de Notificação para enviar notícias de última hora]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet
  [Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/jj927170.aspx
