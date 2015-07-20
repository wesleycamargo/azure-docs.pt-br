
Em seguida, você precisa alterar a maneira que as notificações por push são registradas para garantir que o usuário seja autenticado antes da tentativa do registro. As atualizações do aplicativo do cliente dependem da maneira na qual você implementou as notificações por push.

###Usando o Assistente de notificações por push no Visual Studio 2013 atualização 2 ou uma versão posterior

Neste método, o assistente gera arquivos novos push.register.cs no seu projeto.

1. No Gerenciador de Soluções do Visual Studio, abra o arquivo de projeto push.register e comente ou exclua a chamada para **addEventListener**. 

2. No arquivo de projeto default.js substitua a função **Logon** existente pelo seguinte código:
 
		// Request authentication from Mobile Services using a Facebook login.
		var login = function () {
		    return new WinJS.Promise(function (complete) {
		        client.login("facebook").done(function (results) {
		            userId = results.userId;
		            // Request a push notification channel.
		            Windows.Networking.PushNotifications
		                .PushNotificationChannelManager
		                .createPushNotificationChannelForApplicationAsync()
		                .then(function (channel) {
		                    // Register for notifications using the new channel
		                    client.push.registerNative(channel.uri);
		                });
		            refreshTodoItems();
		            var message = "You are now logged in as: " + userId;
		            var dialog = new Windows.UI.Popups.MessageDialog(message);
		            dialog.showAsync().done(complete);
		        }, function (error) {
		            userId = null;
		            var dialog = new Windows.UI.Popups
		                .MessageDialog("An error occurred during login", "Login Required");
		            dialog.showAsync().done(complete);
		        });
		    });
		}  

###Notificações por push habilitadas manualmente		

Neste método, você adicionou o código de registro do tutorial diretamente no arquivo de projeto default.js.

1. No Gerenciador de Soluções do Visual Studio, abra o arquivo de projeto default.js e no manipulador de eventos **OnActivated**, localize a linha de código que chame à função **createPushNotificationChannelForApplicationAsync**, que é semelhante à seguinte:

		// Request a push notification channel.
		Windows.Networking.PushNotifications
		    .PushNotificationChannelManager
		    .createPushNotificationChannelForApplicationAsync()
		    .then(function (channel) {
		        // Register for notifications using the new channel
		        client.push.registerNative(channel.uri);
		    }); 
 
2. Mova essa linha de códigos na função **logon**, antes da chamada ao **refreshTodoItems** de forma que a função **logon** seja semelhante à seguinte:
 
		// Request authentication from Mobile Services using a Facebook login.
		var login = function () {
		    return new WinJS.Promise(function (complete) {
		        client.login("facebook").done(function (results) {
		            userId = results.userId;
		            // Request a push notification channel.
		            Windows.Networking.PushNotifications
		                .PushNotificationChannelManager
		                .createPushNotificationChannelForApplicationAsync()
		                .then(function (channel) {
		                    // Register for notifications using the new channel
		                    client.push.registerNative(channel.uri);
		                });
		            refreshTodoItems();
		            var message = "You are now logged in as: " + userId;
		            var dialog = new Windows.UI.Popups.MessageDialog(message);
		            dialog.showAsync().done(complete);
		        }, function (error) {
		            userId = null;
		            var dialog = new Windows.UI.Popups
		                .MessageDialog("An error occurred during login", "Login Required");
		            dialog.showAsync().done(complete);
		        });
		    });
		}  

<!---HONumber=July15_HO2-->