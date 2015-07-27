

Em seu aplicativo de back-end, você precisa alternar para envio de notificações de modelo em vez de cargas nativas. Isso simplificará o código de back-end, pois você não terá que enviar várias cargas para diferentes plataformas.

Ao enviar notificações de modelos, você só precisa fornecer um conjunto de propriedades. No nosso caso, enviaremos o conjunto de propriedades contendo a versão localizada das notícias atuais, por exemplo:

	{
		"News_English": "World News in English!",
    	"News_French": "World News in French!",
    	"News_Mandarin": "World News in Mandarin!"
	}


Esta seção mostra como enviar notificações de duas maneiras diferentes:

- usando um aplicativo de console
- usando um script dos Serviços Móveis

O código incluído transmite a dispositivos Windows Store e iOS, como o back-end pode transmitir para qualquer um dos dispositivos com suporte.



## Para enviar notificações usando um aplicativo de console C# ##

Vamos simplesmente modificar o método *SendNotificationAsync* enviando uma notificação de modelo único.

	var hub = NotificationHubClient.CreateClientFromConnectionString("<connection string>", "<hub name>");
    var notification = new Dictionary<string, string>() {
							{"News_English", "World News in English!"},
                            {"News_French", "World News in French!"},
                            {"News_Mandarin", "World News in Mandarin!"}};
    await hub.SendTemplateNotificationAsync(notification, "World");

Observe que esta simples chamada fornecerá a notícia correta localizada para **todos** os seus dispositivos, independentemente da plataforma, como o Hub de Notificação desenvolve e fornece a carga nativa correta para todos os dispositivos inscritos em uma marca específica.

### Serviços Móveis

No Agendador de Serviço Móvel, substitua seu script por:

	var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
			"News_English": "World News in English!",
			"News_French": "World News in French!",
			"News_Mandarin", "World News in Mandarin!"
	}
	notificationHubService.send('World', notification, function(error) {
		if (!error) {
			console.warn("Notification successful");
		}
	});
	
Observe como nesse caso não é necessário enviar várias notificações para localidades e plataformas diferentes.

<!---HONumber=July15_HO3-->