<properties linkid="develop-mobile-tutorials-get-started-with-push-xamarin-ios" urlDisplayName="Introdução às Notificações por Push" pageTitle="Introdução às notificações por push (Xamarin.iOS) - Serviços Móveis" metaKeywords="" description="Saiba como usar as notificações por push nos aplicativos do Xamarin.iOS com os Serviços Móveis do Azure." metaCanonical="" disqusComments="0" umbracoNaviHide="1" editor="mollybos" documentationCenter="Mobile" title="Introdução às notificações por push nos Serviços Móveis" authors="" />

# Introdução às notificações por push nos Serviços Móveis
<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

<p>Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações de push para um aplicativo do Xamarin.iOS. Neste tutorial, você adicionará as notificações por push usando o APNS (Serviço de Notificação por Push da Apple) ao projeto de início rápido. Ao concluir, seu serviço móvel enviará uma notificação de push sempre que um registro é inserido.</p>

   <div class="dev-callout"><b>Observação</b>
   <p>Este tutorial demonstra uma maneira simplificada de envio de notificações por push, anexando um token do dispositivo de notificação por push ao registro inserido. Lembre-se de seguir o seguinte tutorial para ter uma ideia de como incorporar as notificações por push aos seus aplicativos do mundo real.</p>
   </div>

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Gerar a solicitação de assinatura de certificado] 
2. [Registrar seu aplicativo e habilitar as notificações por push]
3. [Criar um perfil de provisionamento para o aplicativo]
3. [Configurar os Serviços Móveis]
4. [Adicionar notificações por push ao aplicativo]
5. [Atualizar scripts para enviar notificações por push]
6. [Inserir dados para receber notificações]

Este tutorial exige o seguinte:

+ [XCode 5.0][Install Xcode] 
+ Um dispositivo compatível com o iOS 5.0 (ou versão posterior)
+ Associação ao Programa de Desenvolvedores do iOS
+ [Xamarin.iOS]
+ [Componente dos Serviços Móveis do Azure]

   <div class="dev-callout"><b>Observação</b>
   <p>Devido aos requisitos de configuração das notificações por push, você deve implementar e testar as notificações por push em um dispositivo compatível com o iOS (iPhone ou iPad), em vez do emulador.</p>
   </div>

Este tutorial baseia-se no guia de início rápido dos Serviços Móveis. Antes de iniciar o tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis]. 

O APNS (Serviço de Notificação por Push da Apple) usa certificados para autenticar seu serviço móvel. Siga estas instruções para criar os certificados necessários e carregá-los no seu Serviço Móvel. Para a documentação oficial do recurso APNS, consulte [Serviço de Notificação por Push da Apple].

<h2><a name="certificates"></a><span class="short-header">Gerar o arquivo CSR</span>Gerar o arquivo de Solicitação de Assinatura de Certificado</h2>

Primeiro, gere o arquivo CSR (Solicitação de Assinatura de Certificado), que é usado pela Apple para gerar um certificado assinado.

1. Na pasta Utilities, execute a ferramenta Acesso do Conjunto de Chaves.

2. Clique em **Acesso do Conjunto de Chaves**, expanda **Assistente do Certificado** e clique em **Solicitar um certificado de um autoridade do certificado...**.

  	![][5]

3. Selecione seu **Endereço de e-mail do usuário**, digite os valores **Nome comum** e **Endereço de e-mail do CA**, verifique se a opção **Salvo no disco** está selecionada e clique em **Continuar**.

  	![][6]

4. Digite um nome para o arquivo CSR (Solicitação de Assinatura de Certificado) em **Salvar como**, selecione o local em **Onde** e clique em **Salvar**.

  	![][7]
  
  	Isso salvará o arquivo CSR no local selecionado; o local padrão está situado na Área de Trabalho. Lembre-se do local escolhido para esse arquivo.

Em seguida, você registrará seu aplicativo na Apple, habilitará as notificações por push e carregará esse CSR exportado para criar um certificado de push.

<h2><a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Registrar seu aplicativo para notificações por push</h2>

Para poder enviar notificações por push para um aplicativo iOS dos serviços móveis, você deve registrar seu aplicativo na Apple e também registrar para notificações por push.  

1. Se você ainda não tiver registrado o aplicativo, vá até o <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a> no Centro de Desenvolvedores da Apple, faça login com sua ID da Apple, clique em **Identificadores**, clique em **IDs de Aplicativo** e, finalmente, clique no sinal **+**.

   	![][102] 

2. Digite um nome para o seu aplicativo em **Descrição**, insira o valor _MobileServices.Quickstart_ em **Identificador de Pacote**, marque a opção "Notificações por Push" na seção "Serviços de Aplicativo" e depois clique em **Continuar**. Este exemplo usa a ID **MobileServices.Quickstart**, mas você não pode reutilizar essa mesma ID, pois as IDs de aplicativo devem ser exclusivas para todos os usuários. Portanto, recomenda-se acrescentar o nome completo ou as iniciais após o nome do aplicativo. 

   	![][103]
   
   	Isso gerará sua ID de aplicativo e solicitará a você para **Enviar** as informações. Clique em **Enviar**.
   
   	![][104] 
   
   	Após clicar em **Enviar**, você verá a tela **Registro concluído**, conforme mostrado abaixo. Clique em **Concluído**.
   
   	![][105]

	Nota: Se optar por fornecer um valor de **Identificador de Pacote** diferente de *MobileServices.Quickstart*, você também deverá atualizar o valor do identificador de pacote em seu projeto Xcode.
    

3. Localize a ID do aplicativo que acabou de criar e clique na respectiva linha. 

   	![][106]
   
   	Ao clicar na ID de aplicativo, os detalhes sobre o aplicativo e a ID do aplicativo serão exibidos. Clique no botão **Configurações**.
   
   	![][107] 
   
4. Role até a parte inferior da tela e clique no botão **Criar Certificado...** na seção **Certificado SSL por Push para Desenvolvimento**.

   	![][108] 

   	Isso exibirá o assistente "Adicionar Certificado de iOS".
   
   	![][108] 

    Nota: Este tutorial usa um certificado de desenvolvimento. O mesmo processo é usado para registrar um certificado de produção. Verifique apenas se você definiu o mesmo tipo de certificado ao carregar o certificado para os Serviços Móveis.

5. Clique em **Selecionar arquivo**, vá até o local em que salvou o arquivo CSR criado na primeira tarefa e clique em **Gerar**. 

  	![][110]
  
6. Depois que o certificado for criado pelo portal, clique no botão **Download** e clique em **Concluído**.
 
  	![][111]  

   	Isso baixará o certificado de assinatura e salvará esse certificado no seu computador, na pasta Downloads. 

  	![][9] 

    Nota: Por padrão, o arquivo baixado, um certificado de desenvolvimento, é denominado <strong>aps_development.cer</strong>.

7. Clique duas vezes no certificado de push baixado **aps_development.cer**.

   	Isso instalará o novo certificado no Conjunto de Chaves, conforme mostrado abaixo:

   	![][10]

    Nota: O nome no seu certificado pode ser diferente, mas ele será prefixado com <strong>Serviços de Notificação por Push do iOS para Desenvolvimento da Apple:</strong>.

Posteriormente, você usará esse certificado para gerar um arquivo. p12 e carregá-lo para os Serviços Móveis para habilitar a autenticação com APNS.

<h2><a name="profile"></a><span class="short-header">Provisionar o aplicativo</span>Criar um perfil de configuração para o aplicativo</h2>
 
1. No <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a>, selecione **Perfis de Provisionamento**, **Todos** e clique no botão **+** para criar um novo perfil. Isso iniciará o Assistente **Adicionar Perfil de Provisionamento do iOS**

   	![][112]

2. Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento e clique em **Continuar**

   	![][113]

3. Em seguida, selecione a ID para o aplicativo Quickstart de Serviços Móveis na lista suspensa **ID do Aplicativo** e clique em **Continuar**

   	![][114]

4. Na tela **Selecionar certificados** selecione o certificado criado anteriormente e clique em **Continuar**
  
   	![][115]

5. Em seguida, selecione os **Dispositivos** que serão usados no teste e clique em **Continuar**

   	![][116]

6. Por fim, selecione um nome para o perfil em **Nome do perfil**, clique em **Gerar** e depois clique em **Concluído**

   	![][117]
  
  	Isso criará um novo perfil de provisionamento.

7. No Xcode, abra o Organizador, selecione a exibição Dispositivos, selecione **Perfis de Provisionamento** na seção **Biblioteca** no painel esquerdo e clique no botão **Atualizar** localizado na parte inferior do painel central. 

   	![][101]

8. Em **Destinos**, clique em **Quickstart**, expanda **Identidade de Assinatura de Código** e depois selecione o novo perfil em **Depurar**.

   	![][17]

Isso garantirá que o projeto Xcode use o novo perfil para a assinatura do código. Em seguida, carregue o certificado para Serviços Móveis.

<a name="configure"></a><h2><span class="short-header">Configurar o serviço</span>Configurar os Serviços Móveis para enviar solicitações de envio por push</h2>

Após registrar seu aplicativo com o APNS e configurar seu projeto, configure seu serviço móvel para integrá-lo ao APNS.

1. Em Acesso do Conjunto de Chaves, clique com botão direito do mouse no novo certificado, clique em **Exportar**, nomeie o arquivo como QuickstartPusher, selecione o formato **.p12** e clique em **Salvar**.

   	![][28]

  	Anote o nome do arquivo e o local do certificado exportado.

    <div class="dev-callout"><b>Observação</b>
	<p>Este tutorial cria um arquivo QuickstartPusher.p12. O nome do arquivo e o local podem ser diferentes.</p>
    </div>

2. Faça login no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu aplicativo.

   	![][18]

3. Clique na guia **Enviar por Push** e clique **Carregar**.

   	![][19]

   	Isso exibirá a caixa de diálogo Carregar Certificado.

4. Clique em **Arquivo**, selecione o arquivo de certificado exportado do QuickstartPusher.p12, digite a **Senha**, verifique se o **Modo** correto está selecionado, clique no ícone de verificação e clique em **Salvar**.

   	![][20] 

    <div class="dev-callout"><b>Observação</b>
	<p>Este tutorial usa certificados de desenvolvimento.</p>
    </div>

Agora, seu serviço móvel está configurado para trabalhar com o APNS.

<a name="add-push"></a><h2><span class="short-header">Adicionar notificações por push</span>Adicionar notificações por push a seus aplicativos</h2>

1. No Xamarin.Studio, abra o arquivo AppDelegate.cs e adicione a seguinte propriedade:

        public string DeviceToken { get; set; }

2. Abra a classe **TodoItem** e adicione a seguinte propriedade:

        [DataMember(Name = "deviceToken")]
        public string DeviceToken { get; set; }

    <div class="dev-callout"><b>Observação</b>
	<p>Quando o esquema dinâmico estiver habilitado no seu serviço móvel, uma nova coluna 'deviceToken' será adicionada automaticamente à tabela <strong>TodoItem</strong> quando um novo item que contém essa propriedade for inserido.</p>
    </div>

3. Em **AppDelegate**, substitua o evento **FinishedLaunching**: 

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

            return true;
        }

4. Em **AppDelegate**, substitua o evento **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            string trimmedDeviceToken = deviceToken.Description;
            if (!string.IsNullOrWhiteSpace(trimmedDeviceToken))
            {
                trimmedDeviceToken = trimmedDeviceToken.Trim('<');
                trimmedDeviceToken = trimmedDeviceToken.Trim('>');
            }
            DeviceToken = trimmedDeviceToken;
        }

5. Em **AppDelegate**, substitua o evento **ReceivedRemoteNotification**:

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

6. Em **TodoListViewController**, modifique a ação **OnAdd** para obter o token do dispositivo armazenado em **AppDelegeate**e armazene-o no **TodoItem** que estiver sendo adicionado.

      string deviceToken = ((AppDelegate)UIApplication.SharedApplication.Delegate).DeviceToken;

			var newItem = new TodoItem() 
			{
				Text = itemText.Text, 
				Complete = false,
                DeviceToken = deviceToken
			};

Seu aplicativo foi atualizado para oferecer suporte a notificações de push.

<h2><a name="update-scripts"></a><span class="short-header">Atualizar o script de inserção</span>Atualizar o script de inserção registrado no Portal de Gerenciamento</h2>

1. No Portal de Gerenciamento, clique na guia **Dados** e na tabela **TodoItem**. 

   	![][21]

2. Em **todoitem**, clique na guia **Script** e selecione **Inserir**.
   
  	![][22]

   	Isso exibirá a função invocada quando ocorrer uma inserção na tabela **TodoItem**.

3. Substitua a função de inserção com o seguinte código e clique em **Salvar**:

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send(item.deviceToken, {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

   	Isso registra um novo script de inserção, que usa o [objeto apns] para enviar uma notificação por push (o texto inserido) para o dispositivo fornecido na solicitação de inserção. 


   	<div class="dev-callout"><b>Observação</b>
   <p>Esse script atrasa o envio da notificação para que você tenha tempo de fechar o aplicativo para receber uma notificação do sistema.</p>
   </div> 

<h2><a name="test"></a><span class="short-header">Testar o aplicativo</span>Testar notificações por push em seu aplicativo</h2>

1. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo em um dispositivo compatível com iOS; em seguida, clique em **OK** para aceitar as notificações por push

  	![][23]

    <div class="dev-callout"><b>Observação</b>
    <p>Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.</p>
    </div>

2. No aplicativo, digite um texto significativo, como por exemplo _Uma nova tarefa de Serviços Móveis_ e clique no ícone de adição (**+**).

  	![][24]

3. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.

  	![][25]

4. Repita a etapa 2 e feche o aplicativo imediatamente; em seguida, verifique se a notificação do sistema a seguir é mostrada.

  	![][26]

Este tutorial foi concluído com êxito.

## Obtenha o exemplo concluído
Baixe o [o projeto de exemplo concluído]. Lembre-se de atualizar as variáveis **applicationURL** e **applicationKey** com suas próprias configurações do Azure. 

## <a name="next-steps"> </a>Próximas etapas

Neste exemplo simples, um usuário recebe uma notificação por push com os dados que você acabou de inserir. O token de dispositivo usado pelo APNS é fornecido para o serviço móvel pelo cliente na solicitação. No próximo tutorial, [Notificações por push para os usuários do aplicativo], você criará uma tabela Dispositivos separada para armazenar tokens de dispositivo e enviar uma notificação por push para todos os canais armazenados quando ocorrer uma inserção. 

<!-- Anchors. -->
[Gerar a solicitação de assinatura de certificado]: #certificates
[Registrar seu aplicativo e habilitar as notificações por push]: #register
[Criar um perfil de provisionamento para o aplicativo]: #profile
[Configurar os Serviços Móveis]: #configure
[Atualizar scripts para enviar notificações por push]: #update-scripts
[Adicionar notificações por push ao aplicativo]: #add-push
[Inserir dados para receber notificações]: #test
[Próximas etapas]:#next-steps

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

[Instalar o Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portal de Provisionamento do iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[SDK do iOS de Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Serviço de Notificação por Push da Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Introdução aos  Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-xamarin-ios
[Introdução aos  dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Notificações por push para usuários de aplicativos]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-ios
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios


[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[objeto apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Componente dos Serviços Móveis do Azure]: http://components.xamarin.com/view/azure-mobile-services/
[projeto de exemplo concluído]: http://go.microsoft.com/fwlink/p/?LinkId=331303

