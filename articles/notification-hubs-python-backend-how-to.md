<properties 
	pageTitle="Como usar Hubs de notificação com Python" 
	description="Aprenda a usar Hubs de Notificação do Azure de um back-end Python." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="yuaxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="php" 
	ms.topic="article" 
	ms.date="12/09/2014" 
	ms.author="yuaxu"/>

# Como usar Hubs de notificação do Python
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-java-backend-how-to/" title="Java">Java</a><a href="/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a><a href="/documentation/articles/notification-hubs-python-backend-how-to/" title="Python" class="current">Python</a><a href="/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/" title="Node.js">Node.js</a>
</div>

Você pode acessar todos os recursos dos Hubs de Notificação por meio de um Java/PHP/Python/Ruby back-end usando a interface REST do Hub de Notificação, conforme descrito no tópico do MSDN [APIs REST dos Hubs de Notificação](http://msdn.microsoft.com/library/dn223264.aspx).

> [AZURE.NOTE] Isso é uma implementação de referência de exemplo para implementar o envia notificação em Python e não é oficialmente suportada notificações Hub Python SDK.

> [AZURE.NOTE] Este exemplo é escrito usando Python 3.4.

Neste tópico, mostramos como:

* Crie um cliente REST para recursos de Hubs de notificação em Python.
* Envie notificações usando a interface do Python para as API do REST do Hub de notificação. 
* Obtenha um despejo da solicitação/resposta HTTP REST para fins educativos/depuração. 

Você pode seguir o [tutorial de Introdução](notification-hubs-windows-store-dotnet-get-started.md) para sua plataforma móvel de escolha, implementando a parte sobre back-end no Python.

> [AZURE.NOTE] O escopo do exemplo é limitado apenas para enviar notificações e não faz nenhum gerenciamento de registro.

## Interface do cliente
A principal interface do cliente pode fornecer os mesmos métodos que estão disponíveis no [SDK dos Hubs de Notificação .NET](http://msdn.microsoft.com/library/jj933431.aspx). Isso permitirá a tradução diretamente de todos os tutoriais e exemplos atualmente disponíveis neste site e que conta com a colaboração da comunidade na Internet.

Você pode encontrar todo o código disponível no [exemplo de wrapper REST Python].

Por exemplo, para criar um cliente:

	hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
	
Para enviar uma notificação do sistema Windows:
	
	wns_payload = """<toast><visual><binding template="ToastText01"><text id="1">Hello world!</text></binding></visual></toast>"""
	hub.send_windows_notification(wns_payload)
	
## Implementation
Se você ainda não fez isso, siga o nosso [Tutorial de introdução] direto na última seção onde você precisa implementar o back-end.

Todos os detalhes para implementar um wrapper completo do REST podem ser encontrados em [MSDN](http://msdn.microsoft.com/library/dn530746.aspx).  Nesta seção, descreveremos a implementação Python das principais etapas necessárias para acessar os pontos de extremidade REST de Hubs de notificação e envio de notificações

1. Analisar a cadeia de conexão
2. Gerar o token de autorização
3. Enviar uma notificação usando a API REST do HTTP

### Analisar a cadeia de conexão

Aqui está a principal classe que implementa o cliente cujos construtor analisa a cadeia de conexão:

	class NotificationHub:
	    API_VERSION = "?api-version=2013-10"
	    DEBUG_SEND = "&test"
	
	    def __init__(self, connection_string=None, hub_name=None, debug=0):
	        self.HubName = hub_name
	        self.Debug = debug
	
	        # Parse connection string
	        parts = connection_string.split(';')
	        if len(parts) != 3:
	            raise Exception("Invalid ConnectionString.")
	
	        for part in parts:
	            if part.startswith('Endpoint'):
	                self.Endpoint = 'https' + part[11:]
	            if part.startswith('SharedAccessKeyName'):
	                self.SasKeyName = part[20:]
	            if part.startswith('SharedAccessKey'):
	                self.SasKeyValue = part[16:]


### Criar token de segurança
Os detalhes da criação de token de segurança estão disponíveis [aqui](http://msdn.microsoft.com/library/dn495627.aspx).
Os métodos a seguir devem ser adicionados à classe **NotificationHub** para criar o token com base no URI da solicitação atual e as credenciais extraídas da cadeia de conexão.

	@staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))

    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)

    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest

    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### Enviar uma notificação usando a API REST do HTTP
Primeiro, vamos definir uma classe que representa uma notificação.

	class Notification:
	    def __init__(self, notification_format=None, payload=None, debug=0):
	        valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
	        if not any(x in notification_format for x in valid_formats):
	            raise Exception(
	                "Invalid Notification format. " +
	                "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")
	
	        self.format = notification_format
	        self.payload = payload
	
	        # array with keynames for headers
	        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
	        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
	        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
	        self.headers = None

Essa classe é um contêiner para um corpo de notificação nativa ou um conjunto de propriedades no caso de uma notificação de modelo, um conjunto de cabeçalhos que contém o formato (plataforma nativa ou modelo) e propriedades específicas da plataforma (como a propriedade de expiração da Apple e cabeçalhos WNS).

Consulte a [documentação de APIs REST dos Hubs de Notificação](http://msdn.microsoft.com/library/dn495827.aspx) e os formatos específicos de notificação das plataformas para conhecer todas as opções disponíveis.

Agora, com essa classe, podemos escrever a enviar os métodos de notificação dentro da classe **NotificationHub**.

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")

        connection.request('POST', url, payload, headers)
        response = connection.getresponse()

        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")

        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

        connection.close()

    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload

        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }

        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression

        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})

        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)

        self.make_http_request(url, payload_to_send, headers)

Os métodos acima enviam uma solicitação de HTTP POST para o ponto de extremidade /messages de seu hub de notificação, com o corpo e os cabeçalhos corretos para o envio da notificação.

### Usando a propriedade de depuração para habilitar o registro em log detalhado
A habilitação da propriedade de depuração ao inicializar o Hub de notificação gravará as informações do registro em log detalhadas sobre a solicitação HTTP e despejo da resposta, bem como o resultado do envio da mensagem de notificação detalhada. 
Recentemente, adicionamos essa propriedade chamada [Propriedade TestSend de Hubs de notificação](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx)
que retorna informações detalhadas sobre o resultado do envio da notificação. 
Para usá-la - inicialize usando o seguinte:

	hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

O Envio do Hub de notificação solicita que a URL HTTP seja anexada com uma querystring "test" como um resultado. 

##<a name="complete-tutorial"></a>Concluir o tutorial
Agora você pode concluir o Tutorial de introdução ao enviar a notificação de um back-end do Python.

Inicialize seu cliente dos Hubs de Notificação (substitua a cadeia de conexão e o nome do hub conforme indicado no [Tutorial de introdução]):

	hub = NotificationHub("myConnectionString", "myNotificationHubName")

Em seguida, adicione o código de envio dependendo da sua plataforma móvel de destino.  Este exemplo também adiciona métodos de nível superiores para habilitar as notificações de envio baseadas na plataforma send_windows_notification por exemplo, para windows e send_apple_notification (para a apple) etc. 

### Windows Store e Windows Phone 8.1 (não Silverlight)

	wns_payload = """<toast><visual><binding template="ToastText01"><text id="1">Test</text></binding></visual></toast>"""
	hub.send_windows_notification(wns_payload)

### Silverlight para Windows Phone 8.0 e 8.1

	hub.send_mpns_notification(toast)

### iOS

	alert_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_apple_notification(alert_payload)

### Android
	gcm_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_gcm_notification(gcm_payload)

### Kindle Fire
	adm_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_adm_notification(adm_payload)

### Baidu
	baidu_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_baidu_notification(baidu_payload)

Executar o código do Python deve produzir uma notificação que aparece em seu dispositivo de destino.

## Exemplos:

### Habilitar propriedade de depuração
Quando você ativa o sinalizador de depuração ao inicializar o NotificationHub então verá detalhada a solicitação HTTP e despejo de resposta, bem como NotificationOutcome semelhante ao seguinte onde você possa entender quais cabeçalhos HTTP são passados na solicitação e qual a resposta HTTP foi recebida do Hub de notificação:
   	![][1]

Você verá o resultado do Hub de notificação detalhado, p. ex. 

- quando a mensagem é enviada com êxito para o serviço de notificação por Push. 
	
		<Outcome>The Notification was successfully sent to the Push Notification System</Outcome>

- Se não houvesse nenhum destino encontrado para qualquer notificação por push, em seguida, você provavelmente veria o seguinte na resposta (que indica que não havia nenhum registro encontrado para entregar a notificação provavelmente porque os registros tinham algumas marcas incompatíveis)

		'<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'

### Transmissão de notificação do sistema para Windows 

Observe os cabeçalhos que são enviados quando você está enviando uma transmissão de notificação do sistema para cliente do Windows. 

	hub.send_windows_notification(wns_payload)

![][2]

### Enviar notificação especificando uma marca (ou expressão de marca)

Observe o cabeçalho HTTP de tags que é adicionado à solicitação HTTP (no exemplo a seguir, estamos enviando a notificação somente para registros com a carga  'esportes')

	hub.send_windows_notification(wns_payload, "sports")

![][3]

### Enviar notificação especificando várias marcas

Observe como as Marcas do cabeçalho HTTP se alteram quando várias marcas são enviadas. 
	
	tags = {'sports', 'politics'}
	hub.send_windows_notification(wns_payload, tags)

![][4]

### Notificação modelada

Observe que o Formato de cabeçalho HTTP se altera e o corpo da carga é enviado como parte do corpo da solicitação HTTP:

**No lado do Cliente - modelo registrado**

		var template =
		                @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
            
**No lado do servidor - envio da carga**
		
		template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
		hub.send_template_notification(template_payload)

![][5]


## Próximas etapas
Neste tópico, mostramos como criar um cliente REST do Python para Hubs de notificação.  A partir daqui, você pode:

* Baixar o [exemplo de wrapper REST Python] completo, que contém todo o código acima.
* Continuar a aprender sobre o recurso de criação de marcas dos Hubs de Notificação no [tutorial Últimas Notícias]
* Continue a aprender sobre o recurso de modelos de Hubs de notificação no [tutorial de localização de notícias]

<!-- URLs -->
[Exemplo de wrapper do REST Python]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Tutorial de introdução]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Tutorial de Últimas notícias]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Tutorial de Localização de notícias]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png

<!--HONumber=49-->