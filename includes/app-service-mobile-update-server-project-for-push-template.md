
1. No Visual Studio, clique com o botão direito do mouse no projeto do servidor e clique em **Gerenciar pacotes NuGet**, procure por `Microsoft.Azure.NotificationHubs` e clique em **Instalar**. Isso instala a biblioteca de Hubs de Notificação para enviar notificações do seu back-end.

3. No projeto do servidor, abra **Controladores** > **TodoItemController.cs** e adicione os seguintes elementos usando instruções:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;
	

2. No método **PostTodoItem**, adicione o seguinte código após a chamada para **InsertAsync**:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
			this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Este código manda o hub de notificação enviar uma notificação de um modelo para todos os registros de modelo que contêm "messageParam". A cadeia de caracteres será inserida no lugar de messageParam em cada PNS que tiver um registro usando "messageParam". Isso permite enviar a notificação para APNS, GCM, WNS ou qualquer outro PNS.

<!---HONumber=AcomDC_1125_2015-->