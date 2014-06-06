<properties linkid="develop-net-tutorials-push-notifications-to-users-ios" urlDisplayName="Enviar notificações por push aos usuários (iOS)" pageTitle="Enviar notificações por push aos usuários (iOS) | Mobile Dev Center" metaKeywords="" description="Saiba como usar os Serviços Móveis para enviar notificações por push aos usuários de seu aplicativo iOS" metaCanonical="" services="" documentationCenter="Mobile" title="Enviar notificações por push aos usuários usando os Serviços Móveis" authors=""  solutions="" writer="" manager="" editor=""  />



 
# Enviar notificações por push aos usuários usando os Serviços Móveis
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/pt-br/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS" class="current">iOS</a><a href="/pt-br/develop/mobile/tutorials/push-notifications-to-users-android" title="Android">Android</a>
</div>

Este tópico estende o [tutorial anterior sobre notificação por push][Get started with push notifications], adicionando uma nova tabela para armazenar tokens do APNS (Serviços de Notificação por Push da Apple). Em seguida, esses tokens podem ser usados para enviar notificações por push aos usuários do aplicativo iPhone ou iPad.  

Este tutorial explica as etapas para atualizar notificações por push em seu aplicativo:

1. [Criar a tabela de dispositivos]
2. [Atualizar o aplicativo]
3. [Atualizar scripts de servidor]
4. [Verificar o comportamento de notificação por push] 

Esse tutorial se baseia no início rápido dos Serviços Móveis e se baseia no tutorial anterior [Introdução às notificações por push]. Antes de iniciar o tutorial, você deve primeiro concluir a [Introdução às notificações por push].  

## <a name="create-table"></a><h2><span class="short-header">Criar a tabela</span>Criar a nova tabela de dispositivos</h2>

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e, em seguida, clique no seu aplicativo.

   	![][0]

2. Clique na guia **Dados** e  **Criar**.

   	![][1]

   	Isso exibe a caixa de diálogo **Criar uma nova tabela**.

3. Mantendo a configuração padrão **Qualquer pessoa com a chave de aplicativo** para todas as permissões, digite _Dispositivos_ em **Nome da tabela** e, em seguida, clique no botão de seleção.

   	![][2]

  	Isso cria a tabela **Dispositivos**, que armazena os tokens de dispositivo usados para enviar notificações por push separadas de dados de item.

Em seguida, você irá modificar o aplicativo de notificações por push para armazenar dados nessa nova tabela em vez de na tabela **TodoItem**.

## <a name="update-app"></a>Atualizar seu aplicativo

1. No Xcode, abra o arquivo QSTodoService.h e adicione a seguinte declaração de método: 

        // Declare method to register device token for other users
        - (void)registerDeviceToken:(NSString *)deviceToken;

   	Isso permite que o representante do aplicativo registre um deviceToken com o serviço móvel.

2. Em QSTodoService.m, adicione o seguinte método de instância:

        // Instance method to register deviceToken in Devices table.
        // Called in AppDelegate.m when APNS registration succeeds.
        - (void)registerDeviceToken:(NSString *)deviceToken
        {
            MSTable *devicesTable = [self.client tableWithName:@"Devices"]; 
            NSDictionary *device = @{ @"deviceToken" : deviceToken };
    
            // Insert the item into the devices table and add to the items array on completion
            [devicesTable insert:device completion:^(NSDictionary *result, NSError *error) {
                if (error) {
                    NSLog(@"ERROR %@", error);
                }
            }];
        }

   	Isso permite que outros chamadores registrem o token de dispositivo com os Serviços Móveis.

3. No arquivo QSAppDelegate.m, adicione a seguinte instrução de importação:

        #import "QSTodoService.h"

     Esse código faz com que AppDelegate reconheça a implementação de TodoService.

4. Em QSAppDelegate.m, substitua o método **didRegisterForRemoteNotificationsWithDeviceToken** pelo seguinte código:

        // We have registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {

           // Register the APNS deviceToken with the Mobile Service Devices table.
           NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
           NSString *token = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        	
           QSTodoService *instance = [QSTodoService defaultService];
           [instance registerDeviceToken:token];
        }

5. Em QSTodoListViewController.m, localize o método **(IBAction)onAdd** e _remova_ o seguinte código:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];
    
        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };
 
   	Substitua isso por este código:

        // We removed the delegate; this application no longer passes the deviceToken here.
        // Remove the device token from the payload
        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };

Seu aplicativo agora foi atualizado para usar a nova tabela Dispositivos para armazenar tokens de dispositivo usados para enviar notificações por push ao dispositivo.

## <a name="update-scripts"></a>Atualizar scripts de servidor

1. No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **Dispositivos**. 

   	![][3]

2. Em **dispositivos**, clique na guia **Script** e selecione **Inserir**.
   
   	![][4]

	Isso exibe a função que é invocada quando ocorre uma inserção na tabela **Dispositivos**.

3. Substitua a função de inserção com o seguinte código e  **Salvar**:

        function insert(item, user, request) {
           var devicesTable = tables.getTable('Devices');
           devicesTable.where({
               token: item.token
           }).read({
               success: insertTokenIfNotFound
           });

           function insertTokenIfNotFound(existingTokens) {
               if (existingTokens.length > 0) {
                   request.respond(200, existingTokens[0]);
               } else {
                   request.execute();
               }
           }
        }

   	Esse script verifica se existem dispositivos com o mesmo token na tabela **Dispositivos**. A inserção prossegue apenas quando nenhum dispositivo correspondente é encontrado. Isso impede registros de dispositivo duplicados.

4. Clique em **TodoItem**, clique em **Script** e selecione **Inserir**. 

   	![][5]

5. Substitua a função de inserção com o seguinte código e  **Salvar**:

        function insert(item, user, request) {
          request.execute({
              success: function() {
                  request.respond();
                  sendNotifications();
              }
          });

          function sendNotifications() {
              var devicesTable = tables.getTable('Devices');
              devicesTable.read({
                  success: function(devices) {
                      // Set timeout to delay the notifications, 
                      // to provide time for the app to be closed 
                      // on the device to demonstrate toast notifications.
                      setTimeout(function() {
                          devices.forEach(function(device) {

                              push.apns.send(device.deviceToken, {
                                  alert: "Toast: " + item.text,
                                  payload: {
                                      inAppMessage: 
                                      "Hey, a new item arrived: '" + 
                                      item.text + "'"
                                  }
                              });
                          });
                      }, 2500);
                  }
              });
          }
      }

    Esse script de inserção envia uma notificação por push (com o texto do item inserido) a todos os dispositivos armazenados na tabela **Dispositivos**.

<h2><a name="test"></a><span class="short-header">Testar o aplicativo</span>Testar notificações por push em seu aplicativo</h2>

1. Pressione o botão **Executar** para criar o projeto e iniciar o aplicativo em um dispositivo capacitado para iOS, em seguida, no aplicativo, digite um texto significativo, como _Uma nova tarefa de Serviços Móveis_ e, em seguida, clique no ícone de sinal de mais (**+**).

  	![][24]

3. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.

  	![][25]

4. Repita a etapa 2 e feche o aplicativo imediatamente; em seguida, verifique se a notificação do sistema a seguir é mostrada.

  	![][26]

Este tutorial foi concluído com êxito.

## Próximas etapas

Isso conclui os tutoriais que demonstram os conceitos básicos de como trabalhar com notificações por push. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Começar com os dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários do seu aplicativo com uma conta do Windows.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como registrar e usar scripts de servidor.

<!-- Anchors. -->
[Criar a tabela de dispositivos]: #create-table
[Atualizar o aplicativo]: #update-app
[Atualizar scripts de servidor]: #update-scripts
[Verificar o comportamento de notificação por push]: #test-app
[Próximas etapas]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-push-notifications-app-users/mobile-services-selection.png
[1]: ./media/mobile-services-ios-push-notifications-app-users/mobile-create-table.png
[2]: ./media/mobile-services-ios-push-notifications-app-users/mobile-create-devices-table.png
[3]: ./media/mobile-services-ios-push-notifications-app-users/mobile-portal-data-tables-devices.png
[4]: ./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-devices.png
[5]: ./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-push2.png



[24]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push4-ios.png

<!-- URLs. -->
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Começar com os Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-ios
[Começar com os dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-ios
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-ios
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-ios

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

