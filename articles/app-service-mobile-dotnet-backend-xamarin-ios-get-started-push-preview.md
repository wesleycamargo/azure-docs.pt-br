<properties 
	pageTitle="Adicionar notificações por push a seu aplicativo Xamarin iOS com o Serviço de Aplicativo do Azure" 
	description="Aprenda a usar o Serviço de Aplicativo do Azure para enviar notificações por push para o aplicativo iOS" 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="yuaxu"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="02/22/2015" 
	ms.author="yuaxu"/>

# Adicionar notificações por push a seu aplicativo Xamarin iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../includes/app-service-mobile-selector-get-started-push-preview.md)]

Este tópico mostra como usar o Serviço de Aplicativo do Azure para enviar notificações por push para um aplicativo Xamarin.iOS 8.  Neste tutorial você adiciona notificações por push usando o serviço de Notificação por Push da Apple (APNs) ao projeto de [Introdução a aplicativos móveis do Serviço de Aplicativo] projeto.  Ao concluir, o back-end móvel enviará uma notificação por push sempre que um registro for inserido.

Este tutorial exige o seguinte:

+ Um dispositivo iOS 8
+ Associação no Programa de Desenvolvedores de iOS
+ [Xamarin.iOS Studio]
+ [Componente dos Serviços Móveis do Azure]

   > [AZURE.NOTE] Devido aos requisitos de configuração das notificações por push, você deve implantar e testar as notificações por push em um dispositivo compatível com o iOS (iPhone ou iPad) em vez de usar o emulador.

O APNS (Serviço de Notificação por Push da Apple) usa certificados para autenticar seu aplicativo móvel.  Siga estas instruções para criar os certificados necessários e carregá-los no seu aplicativo móvel.  Para a documentação oficial do recurso APNS, consulte [Serviço de Notificação por Push da Apple].

## <a name="certificates"></a>Gerar o arquivo de Solicitação de Assinatura de Certificado

Primeiro, gere o arquivo CSR (Solicitação de Assinatura de Certificado), que é usado pela Apple para gerar um certificado assinado.

1. De utilitários, execute a **ferramenta Acesso do Conjunto de Chaves**.

2. Clique em **Acesso do Conjunto de Chaves**, expanda **Assistente do Certificado** e clique em **Solicitar um certificado de um autoridade do certificado...**.

    ![][5]

3. Digite seu **Endereço de Email de Usuário**, digite um valor de **Nome Comum**, certifique-se de que a opção **Salvar em disco** esteja selecionada e clique em **Continuar**.

    ![][6]

4. Digite um nome para o arquivo CSR (Solicitação de Assinatura de Certificado) em **Salvar como**, selecione o local em **Onde** e então clique em **Salvar**.

    ![][7]
  
    Lembre-se do local escolhido.

Em seguida, você registrará seu aplicativo na Apple, habilitará as notificações por push e carregará esse CSR exportado para criar um certificado de push.

## <a name="register"></a>Registrar seu aplicativo para notificações por push

Para poder enviar notificações por push para um dispositivo iOS do seu aplicativo móvel, você deve registrar seu aplicativo na Apple e se registrar para notificações por push. 

1. Se você ainda não registrou seu aplicativo, navegue até o Portal de Provisionamento do iOS</a> no Centro de Desenvolvedores da Apple, faça logon com sua ID da Apple, clique em **Identificadores** e, em seguida, clique em **IDs do Aplicativo** e, por fim, clique no sinal de **+** para criar um ID do aplicativo para seu aplicativo.
    
    ![][102]

2. Digite um nome para o seu aplicativo em **Descrição**, insira e lembre-se do valor exclusivo para **Identificador de Pacote**, marque a opção "Notificações por Push" na seção "Serviços de Aplicativos" e depois clique em **Continuar**.  Este exemplo usa a ID **MobileServices.Quickstart**, mas você não pode reutilizar essa mesma ID, pois as IDs de aplicativo devem ser exclusivas para todos os usuários.  Portanto, recomenda-se acrescentar o nome completo ou as iniciais após o nome do aplicativo. 

    ![][103]
   
    Isso gerará sua ID do aplicativo e solicitará a você para **Enviar** as informações.  Clique em **Enviar**.
   
    ![][104] 
   
    Após clicar em **Enviar**, você verá a tela **Registro concluído**, conforme mostrado abaixo.  Clique em **Concluído**.
   
    ![][105]    

3. Localize a ID de aplicativo que acabou de criar e clique na respectiva linha. 

    ![][106]
   
    Clicar na ID do aplicativo faz com que os detalhes sobre o aplicativo e a ID do aplicativo sejam exibidos.  Clique no botão **Configurações**.
   
    ![][107] 
   
4. Role até a parte inferior da tela e clique no botão **Criar Certificado...** na seção **Certificado SSL por Push para Desenvolvimento**.

    ![][108] 

    Isso exibirá o assistente "Adicionar Certificado de iOS".
   
    Observação:  Este tutorial usa um certificado de desenvolvimento.  O mesmo processo é usado para registrar um certificado de produção.  Apenas certifique-se de que você definiu o mesmo tipo de certificado ao carregar o certificado para o seu aplicativo móvel.

5. Clique em **Escolher Arquivo**, navegue até o local onde você salvou o arquivo CSR anteriormente e clique **Gerar**. 

    ![][110]
  
6. Depois que o certificado for criado pelo portal, clique no botão **Download** e clique em **Concluído**.
 
    ![][111]  

    Isso baixará o certificado de assinatura e salvará esse certificado no seu computador, na pasta Downloads. 

    ![][9] 

    Observação:  Por padrão, o arquivo baixado, um certificado de desenvolvimento, é denominado <strong>aps_development.cer</strong>.

7. Clique duas vezes no certificado de push baixado, **aps_development.cer**.

    Isso instalará o novo certificado no Conjunto de Chaves, conforme mostrado abaixo:

    ![][10]

    Observação:  O nome em seu certificado pode ser diferente, mas ele será prefixado como <strong>Serviços de Notificação por Push do iOS para Desenvolvimento da Apple:</strong>.

Posteriormente, você usará esse certificado para gerar um arquivo .p12 e vai carregá-lo para o seu aplicativo móvel para habilitar a autenticação com APNS.

## <a name="profile"></a>Criar um perfil de provisionamento para o aplicativo
 
1. De volta ao <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de provisionamento iOS</a>, selecione **Perfis de provisionamento**, selecione **Tudo** e, em seguida, clique no botão **+** para criar um novo perfil.  Isso iniciará o Assistente **Adicionar Perfil de Provisionamento do iOS**.

    ![][112]

2. Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento e clique em **Continuar**.

3. Em seguida, selecione a ID do aplicativo de Guia de Início Rápido do Aplicativo Móvel na lista suspensa **ID do Aplicativo** e clique em **Continuar**.

    ![][113]

4. Na tela **Selecionar certificados** selecione o certificado criado anteriormente e clique em **Continuar**.

    ![][114]

5. Em seguida, selecione os **Dispositivos** a serem usados no teste e clique em **Continuar**.
  
    ![][115]

6. Por fim, selecione um nome para o perfil em **Nome do perfil**, clique em **Gerar** e depois clique em **Concluído**.

    ![][116]

    Isso criará um novo perfil de provisionamento.

    ![][117]

## <a name="configure-appServiceMobile"></a>Configurar o back-end móvel do Serviço de Aplicativo para enviar solicitações por push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../includes/app-service-mobile-apns-configure-push-preview.md)]

##<a id="update-server"></a>Atualizar o servidor para enviar notificações por push

1. No Visual Studio, clique com o botão direito do mouse, em seguida, clique em **Gerenciar Pacotes NuGet**.

2. Pesquise por **Microsoft.Azure.NotificationHubs** e clique em **instalar** para todos os projetos na solução.

3. No Gerenciador de Soluções do Visual Studio, expanda a pasta **Controladores** no projeto do back-end móvel.  Abra o TodoItemController.cs.  Na parte superior do arquivo, adicione as seguintes instruções `using`:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. Adicione o seguinte fragmento ao `PostTodoItem` método após a chamada **InsertAsync**:  

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

        // iOS payload
        var appleNotificationPayload = "{\"aps\":{\"alert\":\"" + item.Text + "\"}}";

        try
        {
            await Hub.Push.SendAppleNativeNotificationAsync(appleNotificationPayload);
        }
        catch (System.Exception ex)
        {
            throw;
        }

    Esse código informa o hub de notificação associado a esse aplicativo móvel para enviar uma notificação de envio depois de uma inserção de item todo.


<h2><a name="publish-the-service"></a>Publicar o back-end móvel no Azure</h2>

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="configure-app"></a>Configurar seu aplicativo Xamarin.iOS

1. No Xamarin.Studio, abra **Info.plist** e atualize o **Identificador de Pacote** com a ID que você criou anteriormente.

    ![][121]

2. Role para baixo até **Modos de Tela de Fundo** e marque a caixa **Habilitar Modos de Tela de Fundo** e a caixa **Notificações remotas**. 

    ![][122]

3. Clique duas vezes em seu projeto no Painel de Solução para abrir **Opções de Projeto**.

4.  Escolha **Assinatura do Pacote iOS** em **Compilar** e selecione a **Identidade** e o **Perfil de provisionamento** que você acabou de configurar para este projeto. 

    ![][120]

    Isso garantirá que o projeto Xamarin use o novo perfil para a assinatura de código.  Para a documentação oficial de provisionamento do dispositivo Xamarin, consulte [Provisionamento do dispositivo Xamarin].

## <a name="add-push"></a>Adicionar notificações por push ao seu aplicativo

1. Em **QSTodoService**, substitua a declaração do cliente existente para que **AppDelegate** possa adquirir o cliente móvel:
        
        public MobileServiceClient client { get; private set; }

2. Em **AppDelegate**, substitua o evento **FinishedLaunching**: 

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert 
                | UIUserNotificationType.Badge 
                | UIUserNotificationType.Sound, 
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. No mesmo arquivo, substitua o evento **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken);
        }

4. Em seguida, substitua o evento **DidReceivedRemoteNotification**:

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

Seu aplicativo foi atualizado para dar suporte a notificações por push.


<h2><a name="publish-the-service"></a>Publicar o back-end móvel no Azure</h2>

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="test"></a>Testar notificações por push em seu aplicativo

1. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo em um dispositivo compatível com iOS. Em seguida, clique em **OK** para aceitar as notificações por push.
	
	> [AZURE.NOTE] Você deve aceitar explicitamente as notificações por push do seu aplicativo.  Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

2. No aplicativo, digite uma tarefa e, em seguida, clique no ícone do sinal de adição (**+**).

3. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.

4. Repita a etapa 2 e imediatamente feche o aplicativo e verifique se uma notificação é exibida.

Este tutorial foi concluído com êxito.

<!-- Images. -->

[24]: ./media/mobile-services-ios-get-started-push/mobile-services-quickstart-push2-ios.png
[Introdução aos aplicativos móveis do Serviço de Aplicativo]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

[5]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[25]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

[120]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-20.png
[121]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-21.png
[122]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-22.png

[Xamarin.iOS Studio]: http://xamarin.com/platform
[Instalar o Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portal de Provisionamento do iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[SDK do iOS dos Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Serviço de Notificação por Push da Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-xamarin-ios
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Notificações por push para usuários do aplicativo]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-ios
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Provisionamento do dispositivo Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[objeto APNs]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Componente dos Serviços Móveis do Azure]: http://components.xamarin.com/view/azure-mobile-services/
[projeto de exemplo concluído]: http://go.microsoft.com/fwlink/p/?LinkId=331303


<!--HONumber=49-->