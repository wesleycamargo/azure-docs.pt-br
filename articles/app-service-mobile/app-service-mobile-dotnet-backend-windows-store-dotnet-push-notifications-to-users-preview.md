<properties 
	pageTitle="Enviar notificações de interplataforma para um usuário específico com o cliente da Windows Store"
	description="Saiba como enviar notificações por push para todos os dispositivos de um usuário específico."
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/23/2015"
	ms.author="yuaxu"/>

# Enviar notificações entre plataformas para um usuário específico

[AZURE.INCLUDE [app-service-mobile-selector-push-users-preview](../../includes/app-service-mobile-selector-push-users-preview.md)]

Este tópico mostra como enviar notificações para todos os dispositivos registrados de um usuário específico por meio do back-end móvel. Ele introduziu o conceito de [modelos], que oferece a aplicativos cliente a liberdade de especificar formatos de carga útil e espaços reservados de variáveis no momento do registro. Então o envio chega a todas as plataformas com esses espaços reservados, habilitando notificações entre plataformas.

> [AZURE.NOTE]Para que push funcione com clientes de várias plataformas, você precisará concluir este tutorial para cada plataforma que desejar habilitar. Você só precisará fazer a [atualização do back-end móvel](#backend) uma vez para clientes que compartilham o mesmo back-end móvel.
 
##Pré-requisitos 

Antes de iniciar este tutorial, você já deve ter concluído estes tutoriais do Serviço de Aplicativo para cada plataforma cliente que você deseja que esteja em funcionamento:

+ [Introdução à autenticação]<br/>Adiciona um requisito de logon ao aplicativo de amostra TodoList.

+ [Introdução às notificações por push]<br/>Configura o aplicativo de exemplo TodoList para notificações por push.

##<a name="client"></a>Atualize seu cliente para se registrar para modelos para lidar com pushes entre plataformas

1. Em vez disso, faremos **InitNotificationAsync** em **MainPage.cs** para trabalhar com autenticação do usuário. Exclua a definição de método e chamada **InitNotificationAsync** em **App.xmal.cs** e adicione o seguinte em **MainPage.cs** na classe **MainPage**:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
 
            // building templates for wns
            var toastTemplate = "<toast><visual><binding template="ToastText01"><text id="1">$(message)</text></binding></visual></toast>";
            JObject templateBody = new JObject();
            templateBody["body"] = toastTemplate;
 
            JObject wnsToastHeaders = new JObject();
            wnsToastHeaders["X-WNS-Type"] = "wns/toast";
            templateBody["headers"] = wnsToastHeaders;
 
            JObject templates = new JObject();
            templates["testTemplate"] = templateBody;
 
            await App.MobileService.GetPush().RegisterAsync(channel.Uri, templates);
        }

    Você também deseja transferir alguns usando as instruções para **MainPage.cs**.

2. Use esse método logo após a chamada **AuthenticateAsync** em **ButtonLogin_Click**.

        await AuthenticateAsync();
        InitNotificationAsync();

Seu aplicativo agora está configurado para registrar o dispositivo do usuário com as informações de logon do usuário.

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
[Introdução à autenticação]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md
[Introdução às notificações por push]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview.md
[modelos]: https://msdn.microsoft.com/pt-br/library/dn530748.aspx
 

<!---HONumber=62-->