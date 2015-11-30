<properties 
	pageTitle="Enviar notificações entre plataformas para um usuário específico no iOS" 
	description="Saiba como enviar notificações por push para todos os dispositivos de um usuário específico."
	services="app-service\mobile" 
	documentationCenter="ios" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-dotnet" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="11/17/2015"
	ms.author="yuaxu"/>

# Enviar notificações entre plataformas para um usuário específico

[AZURE.INCLUDE [app-service-mobile-selector-push-users](../../includes/app-service-mobile-selector-push-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Este tópico mostra como enviar notificações para todos os dispositivos registrados de um usuário específico por meio do back-end móvel. Ele introduziu o conceito de [modelos], que oferece a aplicativos cliente a liberdade de especificar formatos de carga útil e espaços reservados de variáveis no momento do registro. Então o envio chega a todas as plataformas com esses espaços reservados, habilitando notificações entre plataformas.

> [AZURE.NOTE]Para que push funcione com clientes de várias plataformas, você precisará concluir este tutorial para cada plataforma que desejar habilitar. Você só precisará fazer a [atualização do back-end móvel](#backend) uma vez para clientes que compartilham o mesmo back-end móvel.
 
##Pré-requisitos 

Antes de iniciar este tutorial, você já deve ter concluído estes tutoriais do Serviço de Aplicativo para cada plataforma cliente que você deseja que esteja em funcionamento:

+ [Introdução à autenticação]<br/>Adiciona um requisito de logon ao aplicativo de amostra TodoList.

+ [Introdução às notificações por push]<br/>Configura o aplicativo de exemplo TodoList para notificações por push.

##<a name="client"></a>Atualize seu cliente para se registrar para modelos para lidar com pushes entre plataformas

1. Mova os trechos de código de registro de APNs em **application: didFinishLaunchingWithOptions** do **QSAppDelegate.m** para a chamada para **loginWithProvider** no **QSTodoListViewController.m** de modo que aconteça após a conclusão da autenticação:

        [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
            [self refresh];
            
            // register iOS8 or previous devices for notifications
            if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
                [[UIApplication sharedApplication] registerForRemoteNotifications];
            }
            else {
                // Register for remote notifications
                [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
                 UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            }
        }];

2. Substitua a chamada **registerDeviceToken** em **application:didRegisterForRemoteNotificationsWithDeviceToken** pelo seguinte para trabalhar com modelos:

        NSDictionary *templates = @{
                               @"testNotificationTemplate": @{ @"body" : @{ @"aps" : @{ @"alert": @"$(message)" } } }
                               };
    
        // register with templates
        [client.push registerDeviceToken:deviceToken template:templates completion:^(NSError *error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

##<a name="backend"></a>Atualize o back-end de serviço para enviar notificações para um usuário específico

1. No Visual Studio, atualize a definição de método `PostTodoItem` com o seguinte código:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // get notification hubs credentials associated with this mobile app
            string notificationHubName = this.Services.Settings.NotificationHubName;
            string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

            // connect to notification hub
            NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

            // get the current user id and create tag to identify user
            ServiceUser authenticatedUser = this.User as ServiceUser;
            string userTag = "_UserId:" + authenticatedUser.Id;

            // build dictionary for template
            var notification = new Dictionary<string, string>{{"message", item.Text}};

            try
            {
            	await Hub.Push.SendTemplateNotificationAsync(notification, userTag);
            }
            catch (System.Exception ex)
            {
                throw;
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

##<a name="test"></a>Testar o aplicativo

Publique novamente o projeto do back-end móvel e execute qualquer aplicativo cliente que tenha configurado. No item de inserção, o back-end enviará as notificações a todos os aplicativos cliente aos quais o usuário esteja conectado.

<!-- URLs. -->
[Introdução à autenticação]: app-service-mobile-ios-get-started-users.md
[Introdução às notificações por push]: app-service-mobile-ios-get-started-push.md
[modelos]: https://msdn.microsoft.com/pt-BR/library/dn530748.aspx
 

<!---HONumber=Nov15_HO4-->