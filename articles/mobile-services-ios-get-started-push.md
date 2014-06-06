<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Introdução ao Push (iOS)" pageTitle="Introdução às notificações por push (iOS) | Centro de Desenvolvimento Móvel" metaKeywords="" description="Saiba como usar os Serviços Móveis do Azure para enviar notificações por push para o seu aplicativo iOS." metaCanonical="http://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Introdução às notificações por push nos Serviços Móveis" authors=""  solutions="" writer="" manager="" editor=""  />

 
# Introdução às notificações por push nos Serviços Móveis
<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-ios" title="iOS" class="current">iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações por push para um aplicativo iOS. Neste tutorial, você adicionará as notificações por push usando o APNS (Serviço de Notificação por Push da Apple) ao projeto de início rápido. Ao concluir, seu serviço móvel enviará uma notificação de push sempre que um registro é inserido.</p>
<p>Você pode assistir a uma versão do vídeo deste tutorial clicando no clipe à direita.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/posts/iOS-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-push-ios-180x120.png') !important;" href="http://channel9.msdn.com/posts/iOS-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Executar o vídeo</span></a> <span class="time">10:37</span></div>
</div>

>[WACOM.NOTE] Este tópico mostra como habilitar as notificações por push usando o suporte herdado fornecido pelos Serviços Móveis. Os Hubs de Notificação do Azure são integrados aos Serviços Móveis para que você possa enviar notificações por push baseadas em modelos e entre plataformas para milhões de dispositivos. Por padrão, as notificações por push usando Hubs de Notificação não estão habilitadas e, atualmente, não há suporte para Hubs de Notificação para iOS nas bibliotecas de Serviços Móveis. No entanto, você pode enviar notificações por push do seu serviço móvel usando as bibliotecas do Hub de Notificação. Para obter mais informações, consulte [Introdução aos Hubs de Notificação](/pt-br/documentation/articles/notification-hubs-ios-get-started/).

Este tutorial o orientará pelas etapas básicas a seguir para habilitar as notificações por push:

1. [Gerar a solicitação de assinatura de certificado] 
2. [Registrar seu aplicativo e habilitar as notificações por push]
3. [Criar um perfil de provisionamento para o aplicativo]
3. [Configurar os Serviços Móveis]
4. [Adicionar notificações por push ao aplicativo]
5. [Atualizar scripts para enviar notificações por push]
6. [Inserir dados para receber notificações]

Este tutorial exige o seguinte:

+ [SDK do iOS de Serviços Móveis]
+ [XCode 4.5][Install Xcode] 
+ Um dispositivo compatível com o iOS 5.0 (ou versão posterior)
+ Associação no Programa de Desenvolvedores de iOS

   > [WACOM.NOTE] Devido aos requisitos de configuração das notificações por push, você deve implantar e testar as notificações por push em um dispositivo compatível com o iOS (iPhone ou iPad), em vez de no emulador.

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar o tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis]. 

O APNS (Serviço de Notificação por Push da Apple) usa certificados para autenticar seu serviço móvel. Siga estas instruções para criar os certificados necessários e carregá-los no seu Serviço Móvel. Para a documentação oficial do recurso APNS, consulte [Serviço de Notificação por Push da Apple].

## Gerar o arquivo de Solicitação de Assinatura de Certificado

Primeiro, gere o arquivo CSR (Solicitação de Assinatura de Certificado), que é usado pela Apple para gerar um certificado assinado.

1. Na pasta Utilities, execute a ferramenta Acesso do Conjunto de Chaves.

2. Clique em **Acesso do Conjunto de Chaves**, expanda **Assistente de Certificado** e clique em **Solicitar um Certificado de uma Autoridade de Certificação...**.

  	![][5]

3. Selecione seu **Endereço de Email de Usuário** e seu **Nome Comum**, verifique se **Salvo em disco** está selecionado e, em seguida, clique em **Continuar**. Deixe o campo **Endereço de Email de CA** em branco, pois ele não é necessário.

  	![][6]

4. Digite um nome para o arquivo CSR (Solicitação de Assinatura de Certificado) em **Salvar como**, selecione o local em **Onde** e, em seguida, clique em **Salvar**.

  	![][7]
  
  	Isso salvará o arquivo CSR no local selecionado; o local padrão está situado na Área de Trabalho. Lembre-se do local escolhido para esse arquivo.

Em seguida, você registrará seu aplicativo na Apple, habilitará as notificações por push e carregará esse CSR exportado para criar um certificado de push.

## Registrar seu aplicativo para notificações por push

Para poder enviar notificações por push para um aplicativo iOS dos serviços móveis, você deve registrar seu aplicativo na Apple e também registrar para notificações por push.  

1. Se você ainda não tiver registrado o aplicativo, navegue até o <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a> no Centro de Desenvolvedores da Apple, faça logon com sua ID da Apple, clique em **Identificadores**, clique em **IDs de Aplicativo** e, finalmente, clique no sinal **+** para registrar um novo aplicativo.

   	![][102] 

2. Digite um nome para o seu aplicativo em **Descrição**, insira o valor _MobileServices.Quickstart_ em **Identificador de Pacote**, marque a opção "Notificações por Push" na seção "Serviços de Aplicativo" e, em seguida, clique em **Continuar**. Este exemplo usa a ID **MobileServices.Quickstart**, mas você não pode reutilizar essa mesma ID, pois as IDs de aplicativo devem ser exclusivas para todos os usuários. Portanto, recomenda-se acrescentar o nome completo ou as iniciais após o nome do aplicativo. 

   	![][103]
   
   	Isso gerará sua ID de aplicativo e solicitará a você para **Enviar** as informações. Clique em **Enviar**.
   
   	![][104] 
   
   	Após clicar em **Enviar**, você verá a tela **Registro concluído**, conforme mostrado abaixo. Clique em **Concluído**.
   
   	![][105]

    > [WACOM.NOTE] Se optar por fornecer um valor de <strong>Identificador de Pacote</strong> diferente de <i>MobileServices.Quickstart</i>, você também deverá atualizar o valor do identificador de pacote em seu projeto Xcode.

3. Localize a ID de aplicativo que acabou de criar e clique na respectiva linha. 

   	![][106]
   
   	Quando você clicar na ID de aplicativo, os detalhes sobre o aplicativo e a ID do aplicativo serão exibidos. Clique no botão **Configurações**.
   
   	![][107] 
   
4. Role até a parte inferior da tela e clique no botão **Criar Certificado...** na seção **Certificado SSL por Push para Desenvolvimento**.

   	![][108] 

   	Isso exibirá o assistente "Adicionar Certificado de iOS".
   
   	![][108] 


    > [WACOM.NOTE] Este tutorial usa um certificado de desenvolvimento. O mesmo processo é usado para registrar um certificado de produção. Verifique apenas se você definiu o mesmo tipo de certificado ao carregar o certificado para os Serviços Móveis.

5. Clique em **Escolher Arquivo**, navegue até o local em que salvou o arquivo CSR criado na primeira tarefa e clique em **Gerar**. 

  	![][110]
  
6. Depois que o certificado for criado pelo portal, clique no botão **Baixar** e, em seguida, clique em **Concluído**.
 
  	![][111]  

   	Isso baixará o certificado de assinatura e salvará esse certificado no seu computador, na pasta Downloads. 

  	![][9] 

    > [WACOM.NOTE] Por padrão, o arquivo baixado, um certificado de desenvolvimento, é denominado **aps_development.cer**.

7. Clique duas vezes no certificado de push baixado **aps_development.cer**.

   	Isso instalará o novo certificado no Conjunto de Chaves, conforme mostrado abaixo:

   	![][10]

    > [WACOM.NOTE] O nome no seu certificado pode ser diferente, mas ele será prefixado com **Serviços de Notificação por Push do iOS para Desenvolvimento da Apple:**.

Posteriormente, você usará esse certificado para gerar um arquivo. p12 e carregá-lo para os Serviços Móveis para habilitar a autenticação com APNS.

## Criar um perfil de provisionamento para o aplicativo
 
1. De volta ao <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a>, selecione **Perfis de Provisionamento**, escolha **Tudo** e clique no botão **+** para criar um novo perfil. Isso iniciará o Assistente **Adicionar Perfil de Provisionamento do iOS**

   	![][112]

2. Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento e clique em **Continuar**

   	![][113]

3. Em seguida, selecione a ID para o aplicativo Quickstart de Serviços Móveis na lista suspensa **ID do Aplicativo** e clique em **Continuar**

   	![][114]

4. Na tela **Selecionar certificados**, selecione o certificado criado anteriormente e clique em **Continuar**
  
   	![][115]

5. Em seguida, selecione os **Dispositivos** a serem usados no teste e clique em **Continuar**

   	![][116]

6. Finalmente, selecione um nome para o perfil em **Nome do Perfil**, clique em **Gerar** e, em seguida, clique em **Concluído**

   	![][117]
	
  	Isso criará um novo perfil de provisionamento.

7. No Xcode, abra o Organizador, selecione a exibição Dispositivos, selecione **Perfis de Provisionamento** na seção **Biblioteca** no painel esquerdo e clique no botão **Atualizar** localizado na parte inferior do painel central. 

   	![][101]

8. Em **Destinos**, clique em **Quickstart**, expanda **Identidade de Assinatura de Código** e, em seguida, selecione o novo perfil em **Depurar**.

   	![][17]

Isso garantirá que o projeto Xcode use o novo perfil para a assinatura do código. Em seguida, carregue o certificado para Serviços Móveis.

## Configurar os Serviços Móveis para enviar solicitações de envio por push

Após registrar seu aplicativo com o APNS e configurar seu projeto, configure seu serviço móvel para integrá-lo ao APNS.

1. Em Acesso do Conjunto de Chaves, clique com botão direito do mouse no novo certificado, clique em **Exportar**, nomeie o arquivo como QuickstartPusher, selecione o formato **. p12** e clique em **Salvar**.

   	![][28]

  Anote o nome do arquivo e o local do certificado exportado.

	> [WACOM.NOTE] Este tutorial cria um arquivo QuickstartPusher.p12. O nome do arquivo e o local podem ser diferentes.

2. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e, em seguida, clique no seu aplicativo.

   	![][18]

3. Clique na guia **Push** e clique em **Carregar**.

   	![][19]

	Isso exibirá a caixa de diálogo Carregar Certificado.

4. Clique em **Arquivo**, selecione o arquivo de certificado exportado QuickstartPusher.p12, digite a **Senha**, verifique se o **Modo** correto está selecionado, clique no ícone de verificação e clique em **Salvar**.

   	![][20] 

    > [WACOM.NOTE] Este tutorial usa certificados de desenvolvimento.

Agora, seu serviço móvel está configurado para trabalhar com o APNS.

## Adicionar notificações por push ao seu aplicativo

1. Em Xcode, abra o arquivo QSAppDelegate.h e adicione a propriedade a seguir sob a propriedade ***window**:

        @property (strong, nonatomic) NSString *deviceToken;

    > [WACOM.NOTE] Quando o esquema dinâmico estiver habilitado no seu serviço móvel, uma nova coluna 'deviceToken' será adicionada automaticamente à tabela **TodoItem** quando um novo item que contém essa propriedade for inserido.

2. Em QSAppDelegate.m, substitua o método de manipulador a seguir dentro da implementação: 

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. Em QSAppDelegate.m, adicione o método de manipulador a seguir dentro da implementação: 

        // Estamos registrados, portanto, agora armazene o token de dispositivo (como uma cadeia de caracteres) na instância AppDelegate
        // tomando cuidado para remover os colchetes angulares primeiro.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        }

4. Em QSAppDelegate.m, adicione o método de manipulador a seguir dentro da implementação: 

        // Manipular qualquer falha ao registrar. Nesse caso, definimos o deviceToken como uma cadeia de caracteres
        // vazia para impedir a inserção de falha.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
            self.deviceToken = @"";
        }

5. Em QSAppDelegate.m, adicione o método de manipulador a seguir dentro da implementação:  

        // Porque a notificação de alertas do sistema não funciona quando o aplicativo está em execução, o aplicativo lida com eles.
        // Isso usa o userInfo na carga para exibir um UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

5. Em QSTodoListViewController.m, importe o arquivo QSAppDelegate.h para poder usar o representante para obter o token do dispositivo: 

        #import "QSAppDelegate.h"

6. Em QSTodoListViewController.m, modifique a ação **(IBAction)onAdd**, localizando a linha a seguir: 

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 
 
   Substitua isso por este código:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];
    
        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

   	Isso adicionará uma referência a **QSAppDelegate** para obter o token do dispositivo e, em seguida, modificará a carga de solicitação para incluir esse token de dispositivo.

   	> [WACOM.NOTE] Adicione esse código antes da chamada do método <strong>addItem</strong>.

Seu aplicativo foi atualizado para oferecer suporte a notificações de push.

## Atualizar o script de inserção registrado no Portal de Gerenciamento

1. No Portal de Gerenciamento, clique na guia **Dados** e, em seguida, clique na tabela **TodoItem**. 

   	![][21]

2. Em **todoitem**, clique na guia **Script** e selecione **Inserir**.
   
  	![][22]

   	Isso exibirá a função invocada quando ocorrer uma inserção na tabela **TodoItem**.

3. Substitua a função de inserção com o seguinte código e, em seguida, clique em **Salvar**:

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


   	> [WACOM.NOTE] Esse script atrasa o envio da notificação para que você tenha tempo de fechar o aplicativo para receber uma notificação do sistema.

## Testar as notificações por push no seu aplicativo

1. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo em um dispositivo compatível com iOS; em seguida, clique em **OK** para aceitar as notificações por push

  	![][23]

    > [WACOM.NOTE] Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

2. No aplicativo, digite um texto significativo, por exemplo, _Uma nova tarefa de Serviços Móveis_, e clique no ícone de adição (**+**).

  	![][24]

3. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.

  	![][25]

4. Repita a etapa 2 e feche o aplicativo imediatamente; em seguida, verifique se a notificação do sistema a seguir é mostrada.

  	![][26]

Este tutorial foi concluído com êxito.

## Próximas etapas

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
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portal de Provisionamento do iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[SDK do iOS de Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Serviço de Notificação por Push da Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-ios
[Começar com os dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-ios
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-ios
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-ios
[Notificações por push para os usuários do aplicativo]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-ios
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[objeto apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333


