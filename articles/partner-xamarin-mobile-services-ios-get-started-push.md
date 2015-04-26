<properties 
	pageTitle="Introdução às notificações por push (Xamarin.iOS) - Serviços Móveis" 
	description="Saiba como usar notificações por push em aplicativos iOS Xamarin com serviços móveis do Azure." 
	documentationCenter="xamarin" 
	authors="ysxu" 
	manager="dwrede" 
	services="mobile-services" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="10/20/2014" 
	ms.author="yuaxu"/>

# Adicionar notificações por push ao seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-push-xamarin](../includes/mobile-services-selector-get-started-push-xamarin.md)]

<p>Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações de push para um aplicativo do Xamarin.iOS 8. Neste tutorial, você adicionará as notificações por push usando o APNS (Serviço de Notificação por Push da Apple) ao projeto [Introdução aos Serviços Móveis]. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.</p>

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Gerar a solicitação de assinatura de certificado]
2. [Registrar o aplicativo e habilitar notificações por push]
3. [Criar um perfil de provisionamento para o aplicativo]
4. [Configurar os Serviços Móveis]
5. [Configurar o aplicativo Xamarin.iOS]
6. [Adicionar notificações por push ao aplicativo]
7. [Atualizar scripts para enviar notificações por push]
8. [Inserir dados para receber notificações]

Este tutorial exige o seguinte:

+ Um dispositivo iOS 8
+ Associação no Programa de Desenvolvedores de iOS
+ [Xamarin.iOS Studio]
+ [Componente dos Serviços Móveis do Azure]

   > [AZURE.NOTE] Devido aos requisitos de configuração das notificações por push, você deve implantar e testar as notificações por push em um dispositivo compatível com o iOS (iPhone ou iPad) em vez de usar o emulador.

O APNS (Serviço de Notificação por Push da Apple) usa certificados para autenticar seu serviço móvel. Siga estas instruções para criar os certificados necessários e carregá-los no seu Serviço Móvel. Para a documentação oficial do recurso APNS, consulte [Serviço de Notificação por Push da Apple].

## <a name="certificates"></a>Gerar o arquivo de Solicitação de Assinatura de Certificado

Primeiro, gere o arquivo CSR (Solicitação de Assinatura de Certificado), que é usado pela Apple para gerar um certificado assinado.

1. De utilitários, execute a **ferramenta Acesso do Conjunto de Chaves**.

2. Clique em **Acesso do Conjunto de Chaves**, expanda **Assistente do Certificado** e clique em **Solicitar um certificado de um autoridade do certificado...**.

    ![][5]

3. Digite seu **Endereço de Email de Usuário**, digite um valor de **Nome Comum**, certifique-se de que a opção **Salvar em disco** esteja selecionada e clique em **Continuar**.

    ![][6]

4. Digite um nome para o arquivo CSR (Solicitação de Assinatura de Certificado) em **Salvar como**, selecione o local em **Onde** e clique em **Salvar**.

    ![][7]
  
    Lembre-se do local escolhido.

Em seguida, você registrará seu aplicativo na Apple, habilitará as notificações por push e carregará esse CSR exportado para criar um certificado de push.

## <a name="register"></a>Registrar seu aplicativo para notificações por push

Para poder enviar notificações por push para um aplicativo iOS dos serviços móveis, você deve registrar seu aplicativo na Apple e também registrar para notificações por push. 

1. Se você ainda não tiver registrado o aplicativo, vá até o <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a> no Centro de Desenvolvedores da Apple, faça logon com sua ID da Apple, clique em **Identificadores**, clique em **IDs de Aplicativo** e, finalmente, clique no sinal **+** para criar uma ID de aplicativo para seu aplicativo.
    
    ![][102]

2. Digite um nome para o seu aplicativo em **Descrição**, insira e lembre-se do valor exclusivo para **Identificador de Pacote**, marque a opção "Notificações por Push" na seção "Serviços de Aplicativos" e depois clique em **Continuar**. Este exemplo usa a ID **MobileServices.Quickstart**, mas você não pode reutilizar essa mesma ID, pois as IDs de aplicativo devem ser exclusivas para todos os usuários. Portanto, recomenda-se acrescentar o nome completo ou as iniciais após o nome do aplicativo. 

    ![][103]
   
    Isso gerará sua ID do aplicativo e solicitará a você para **Enviar** as informações. Clique em **Enviar**.
   
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
   
    Observação: Este tutorial usa um certificado de desenvolvimento. O mesmo processo é usado para registrar um certificado de produção. Verifique apenas se você definiu o mesmo tipo de certificado ao carregar o certificado para os Serviços Móveis.

5. Clique em **Escolher Arquivo**, navegue até o local onde você salvou o arquivo CSR anteriormente e clique **Gerar**. 

    ![][110]
  
6. Depois que o certificado for criado pelo portal, clique no botão **Download** e clique em **Concluído**.
 
    ![][111]  

    Isso baixará o certificado de assinatura e salvará esse certificado no seu computador, na pasta Downloads. 

    ![][9] 

    Observação: Por padrão, o arquivo baixado, um certificado de desenvolvimento, é denominado <strong>aps_development.cer</strong>.

7. Clique duas vezes no certificado de push baixado **aps_development.cer**.

    Isso instalará o novo certificado no Conjunto de Chaves, conforme mostrado abaixo:

    ![][10]

    Observação: O nome em seu certificado pode ser diferente, mas ele será prefixado como <strong>Serviços de Notificação por Push do iOS para Desenvolvimento da Apple:</strong>.

Posteriormente, você usará esse certificado para gerar um arquivo. p12 e carregá-lo para os Serviços Móveis para habilitar a autenticação com APNS.

## <a name="profile"></a>Criar um perfil de provisionamento para o aplicativo
 
1. De volta ao <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a>, selecione **Perfis de Provisionamento**, escolha **Tudo** e clique no botão **+** para criar um novo perfil. Isso iniciará o Assistente **Adicionar Perfil de Provisionamento do iOS**.

    ![][112]

2. Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento e clique em **Continuar**.

3. Em seguida, selecione a ID para o aplicativo Guia de início rápido dos Serviços Móveis na lista suspensa **ID do Aplicativo** e clique em **Continuar**.

    ![][113]

4. Na tela **Selecionar certificados** selecione o certificado criado anteriormente e clique em **Continuar**.

    ![][114]

5. Em seguida, selecione os **Dispositivos** que serão usados para teste e clique em **Continuar**.
  
    ![][115]

6. Por fim, selecione um nome para o perfil em **Nome do perfil**, clique em **Gerar** e depois clique em **Concluído**.

    ![][116]

    Isso criará um novo perfil de provisionamento.

    ![][117]

## <a name="configure-mobileServices"></a>Configurar Serviços Móveis para enviar solicitações de push

Após registrar seu aplicativo com o APNS e configurar seu projeto, configure seu serviço móvel para integrá-lo ao APNS.

1. Em Acesso do Conjunto de Chaves, clique com botão direito do mouse no novo certificado, clique em **Exportar**, nomeie o arquivo, selecione o formato **.p12** e clique em **Salvar**.

    ![][28]

    Anote o nome do arquivo e o local do certificado exportado.

2. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu aplicativo.

    ![][18]

3. Clique na guia **Enviar por Push** e em **Carregar** nas **configurações de notificação por push da Apple**.

    ![][19]

    Isso exibirá a caixa de diálogo Carregar Certificado.

4. Clique em **Arquivo**, selecione o arquivo de certificado exportado .p12, digite a **Senha**, verifique se o **Modo** correto está selecionado, clique no ícone de verificação e clique em **Salvar**.

    ![][20] 

Agora, seu serviço móvel está configurado para funcionar com o APNS.

## <a name="configure-app"></a>Configurar seu aplicativo Xamarin.iOS

1. No Xamarin.Studio, abra **Info.plist** e atualize o **Identificador de Pacote** com a ID que você criou anteriormente.

    ![][121]

2. Role para baixo até **Modos de Plano de Fundo** e marque a caixa **Habilitar Modos de Plano de Fundo** e a caixa **Notificações remotas**. 

    ![][122]

3. Clique duas vezes em seu projeto no Painel de Solução para abrir **Opções de Projeto**.

4.  Escolha **Assinatura do Pacote iOS** em **Compilar** e selecione a **Identidade** e o **Perfil de provisionamento** que você acabou de configurar para este projeto. 

    ![][120]

    Isso garantirá que o projeto Xamarin use o novo perfil para a assinatura de código. Para a documentação oficial de provisionamento do dispositivo Xamarin, consulte [Provisionamento do dispositivo Xamarin].

## <a name="add-push"></a>Adicionar notificações de push para seu aplicativo

1. No Xamarin.Studio, abra o arquivo AppDelegate.cs e adicione a seguinte propriedade:

        public string DeviceToken { get; set; }

2. Abra a classe **TodoItem** e adicione a seguinte propriedade:

        [JsonProperty(PropertyName = "deviceToken")]
        public string DeviceToken { get; set; }

3. Em **QSTodoService**, substitua a declaração de cliente existente para ser:
        
        public MobileServiceClient client { get; private set; }

4. Em seguida, adicione o seguinte método para que **AppDelegate** possa adquirir o cliente posteriormente para registrar notificações por push:

        public MobileServiceClient GetClient {
            get{ 
                return client;
            }
        }

5. Em **AppDelegate**, substitua o evento **FinishedLaunching**: 

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

6. Em **AppDelegate**, substitua o evento **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            DeviceToken = deviceToken.Description;
            DeviceToken = DeviceToken.Trim ('<', '>').Replace (" ", "");

            // Get Mobile Services client
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };
            var push = client.GetPush ();
            push.RegisterNativeAsync (DeviceToken, tag);
        }

7. Em **AppDelegate**, substitua o evento **ReceivedRemoteNotification**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            Debug.WriteLine(userInfo.ToString());
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

8. Em **TodoListViewController**, modifique a ação **OnAdd** para obter o token do dispositivo armazenado em **AppDelegeate**e armazene-o no **TodoItem** que estiver sendo adicionado.

        string deviceToken = ((AppDelegate)UIApplication.SharedApplication.Delegate).DeviceToken;

        var newItem = new TodoItem() 
        {
            Text = itemText.Text, 
            Complete = false,
            DeviceToken = deviceToken
        };

Seu aplicativo foi atualizado para dar suporte a notificações de push.

## <a name="update-scripts"></a>Atualizar o script de inserção registrados no Portal de Gerenciamento

1. No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**. 

    ![][21]

2. Em **todoitem**, clique na guia **Script** e selecione **Inserir**.
   
    ![][22]

    Isso exibirá a função invocada quando ocorrer uma inserção na tabela **TodoItem**.

3. Substitua a função de inserção pelo seguinte código e clique em **Salvar**:

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send("uniqueTag", {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    Isso registra um novo script de inserção, que usa o [objeto apns] para enviar uma notificação por push (o texto inserido) para o dispositivo fornecido na solicitação de inserção. 

   > [AZURE.NOTE] Esse script atrasa o envio da notificação para que você tenha tempo de fechar o aplicativo para receber uma notificação do sistema.

## <a name="test"></a>Testar notificações por push no seu aplicativo

1. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo em um dispositivo compatível com iOS; em seguida, clique em **OK** para aceitar as notificações por push

    ![][23]

   > [AZURE.NOTE] Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

2. No aplicativo, digite um texto significativo, por exemplo, _Uma nova tarefa de Serviços Móveis_, e clique no ícone de adição (**+**).

    ![][24]

3. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.

    ![][25]

4. Repita a etapa 2 e feche o aplicativo imediatamente; em seguida, verifique se a notificação do sistema a seguir é mostrada.

    ![][26]

Este tutorial foi concluído com êxito.

<!-- Anchors. -->
[Gerar a solicitação de assinatura de certificado]: #certificates
[Registrar o aplicativo e habilitar notificações por push]: #register
[Criar um perfil de provisionamento para o aplicativo]: #profile
[Configurar os Serviços Móveis]: #configure-mobileServices
[Configurar o aplicativo Xamarin.iOS]: #configure-app
[Atualizar scripts para enviar notificações por push]: #update-scripts
[Adicionar notificações por push ao aplicativo]: #add-push
[Inserir dados para receber notificações]: #test

<!-- Images. -->

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
[24]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
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
[Notificações por push para usuários de aplicativos]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-ios
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Provisionamento do dispositivo Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[objeto apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Componente dos Serviços Móveis do Azure]: http://components.xamarin.com/view/azure-mobile-services/
[projeto de exemplo concluído]: http://go.microsoft.com/fwlink/p/?LinkId=331303



<!--HONumber=42-->
