<properties urlDisplayName="How to use Notification Hubs with PHP" pageTitle="Como usar Hubs de Notificação com PHP" metaKeywords="" description="Aprenda a usar Hubs de notificação do Azure de um back-end do PHP." metaCanonical="" services="mobile-services,notification-hubs,push,php" documentationCenter="" title="How to use Notification Hubs with PHP" authors="piyushjo" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="php" ms.topic="article" ms.date="11/14/2014" ms.author="piyushjo" />

# Como usar Hubs de Notificação no PHP
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/pt-br/documentation/articles/notification-hubs-java-backend-how-to/" title="Java">Java</a><a href="/pt-br/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP" class="current">PHP</a><a href="/pt-br/documentation/articles/notification-hubs-python-backend-how-to/" title="Python">Python</a>
</div>

Você pode acessar todos os recursos dos Hubs de Notificação por meio de um back-end do Java/PHP/Ruby usando a interface REST do Hub de Notificação, conforme descrito no tópico do MSDN[APIs REST dos Hubs de Notificação](http://msdn.microsoft.com/pt-br/library/dn223264.aspx).

Neste tópico, mostramos como:

* Criar um cliente REST para recursos dos Hubs de Notificação no PHP;
* Siga o [Tutorial de introdução](http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-ios-get-started/) para sua plataforma móvel de escolha, implementando a parte sobre back-end no PHP.

## Interface do cliente
A principal interface do cliente pode fornecer os mesmos métodos que estão disponíveis no [SDK dos Hubs de Notificação .NET](http://msdn.microsoft.com/pt-br/library/jj933431.aspx), isso permitirá que você traduza diretamente todos os tutoriais e amostras atualmente disponíveis neste site, e contribua para a comunidade na Internet.

Você pode encontrar todos os códigos disponíveis na [amostra de wrapper de REST PHP].

Por exemplo, para criar um cliente:

	$hub = new NotificationHub("connection string", "hubname");	

Para enviar uma notificação nativa do iOS:
	
	$notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
	$hub->sendNotification($notification);

## Implementação
Se você ainda não o fez, siga o nosso [Tutorial de introdução] até a última seção em que você tem que implementar o back-end.
Além disso, se você quiser, pode usar o código da [amostra de wrapper REST PHP] e ir diretamente para [Concluir o tutorial](#complete-tutorial) .

Todos os detalhes para implementar um wrapper completo do REST podem ser encontrados em [MSDN](http://msdn.microsoft.com/pt-br/library/dn530746.aspx). Nesta seção, descreveremos a implementação do PHP das principais etapas necessárias para acessar os pontos de extremidade de REST dos Hubs de Notificação:

1. Analisar a cadeia de conexão
2. Gerar o token de autorização
3. Realizar a chamada do HTTP

### Analisar a cadeia de conexão

Aqui está a classe principal que implementa o cliente, cujo construtor analisa a cadeia de conexão:

	class NotificationHub {
		const API_VERSION = "?api-version=2013-10";
	
		private $endpoint;
		private $hubPath;
		private $sasKeyName;
		private $sasKeyValue;
	
		function __construct($connectionString, $hubPath) {
			$this->hubPath = $hubPath;
	
			$this->parseConnectionString($connectionString);
		}
	
		private function parseConnectionString($connectionString) {
			$parts = explode(";", $connectionString);
			if (sizeof($parts) != 3) {
				throw new Exception("Error parsing connection string: " . $connectionString);
			}
	
			foreach ($parts as $part) {
				if (strpos($part, "Endpoint") === 0) {
					$this->endpoint = "https" . substr($part, 11);
				} else if (strpos($part, "SharedAccessKeyName") === 0) {
					$this->sasKeyName = substr($part, 20);
				} else if (strpos($part, "SharedAccessKey") === 0) {
					$this->sasKeyValue = substr($part, 16);
				}
			}
		}
	}


### Criar token de segurança
Os detalhes da criação do token de segurança estão disponíveis[aqui](http://msdn.microsoft.com/pt-br/library/dn495627.aspx).
O método a seguir deve ser adicionado à classe **NotificationHub**para criar o token com base no URI da solicitação atual e as credenciais extraídas da cadeia de conexão.

	private function generateSasToken($uri) {
		$targetUri = strtolower(rawurlencode(strtolower($uri)));

		$expires = time();
		$expiresInMins = 60;
		$expires = $expires + $expiresInMins * 60;
		$toSign = $targetUri . "\n" . $expires;

		$signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

		$token = "SharedAccessSignature sr=" . $targetUri . "&sig="
					. $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

		return $token;
	}

### Enviar uma notificação
Primeiro, vamos definir uma classe que representa uma notificação.

	class Notification {
		public $format;
		public $payload;
	
		# array with keynames for headers
		# Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
		# Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
		public $headers;
	
		function __construct($format, $payload) {
			if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
				throw new Exception('Invalid format: ' . $format);
			}
	
			$this->format = $format;
			$this->payload = $payload;
		}
	}

Essa classe é um contêiner para um corpo de notificação nativa ou um conjunto de propriedade no caso de uma notificação de modelo, e um conjunto de cabeçalhos que contém propriedades específicas de formato (plataforma nativa ou modelo) e plataforma (como a propriedade de expiração da Apple e cabeçalhos WNS).

Consulte a [Documentação de APIs REST dos Hubs de Notificação](http://msdn.microsoft.com/pt-br/library/dn495827.aspx) e os formatos das plataformas de notificação específicos para todas as opções disponíveis.

Armados com essa classe, agora podemos gravar os métodos de notificação de envio dentro da classe **NotificationHub**.

	public function sendNotification($notification) {
		$this->sendNotification($notification, "");
	}

	public function sendNotification($notification, $tagsOrTagExpression) {
		if (is_array($tagsOrTagExpression)) {
			$tagExpression = implode(" || ", $tagsOrTagExpression);
		} else {
			$tagExpression = $tagsOrTagExpression;
		}

		# build uri
		$uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
		$ch = curl_init($uri);

		if (in_array($notification->format, ["template", "apple", "gcm"])) {
			$contentType = "application/json";
		} else {
			$contentType = "application/xml";
		}

		$token = $this->generateSasToken($uri);

		$headers = [
		    'Authorization: '.$token,
		    'Content-Type: '.$contentType,
		    'ServiceBusNotification-Format: '.$notification->format
		];

		if ("" !== $tagExpression) {
			$headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
		}

		# add headers for other platforms
		if (is_array($notification->headers)) {
			$headers = array_merge($headers, $notification->headers);
		}
		
		curl_setopt_array($ch, array(
		    CURLOPT_POST => TRUE,
		    CURLOPT_RETURNTRANSFER => TRUE,
		    CURLOPT_SSL_VERIFYPEER => FALSE,
		    CURLOPT_HTTPHEADER => $headers,
		    CURLOPT_POSTFIELDS => $notification->payload
		));

		// Send the request
		$response = curl_exec($ch);

		// Check for errors
		if($response === FALSE){
		    throw new Exception(curl_error($ch));
		}

		$info = curl_getinfo($ch);

		if ($info['http_code'] <> 201) {
			throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
		}
	} 

Os métodos acima enviam uma solicitação de HTTP POST para o ponto de extremidade /messages de seu hub de notificação, com o corpo e os cabeçalhos corretos para o envio da notificação.

## <a name="complete-tutorial"></a>Concluir o tutorial
Agora você pode concluir o tutorial de introdução enviando a notificação por meio de um back-end do PHP.

Inicialize seu cliente Hubs de Notificação (substitua a cadeia de conexão e o nome do hub conforme indicado no [Tutorial de introdução]):
	$hub = new NotificationHub("connection string", "hubname");	

Em seguida, adicione o código de envio dependendo da sua plataforma móvel de destino.

### Windows Store e Windows Phone 8.1 (não Silverlight)

	$toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
	$notification = new Notification("windows", $toast);
	$notification->headers[] = 'X-WNS-Type: wns/toast';
	$hub->sendNotification($notification);

### iOS

	$alert = '{"aps":{"alert":"Hello from PHP!"}}';
	$notification = new Notification("apple", $alert);
	$hub->sendNotification($notification);

### Android
	$message = '{"data":{"msg":"Hello from PHP!"}}';
	$notification = new Notification("gcm", $message);
	$hub->sendNotification($notification);

### Silverlight para Windows Phone 8.0 e 8.1

	$toast = '<?xml version="1.0" encoding="utf-8"?>' .
		        '<wp:Notification xmlns:wp="WPNotification">' .
		           '<wp:Toast>' .
		                '<wp:Text1>Hello from PHP!</wp:Text1>' .
		           '</wp:Toast> ' .
		        '</wp:Notification>';
	$notification = new Notification("mpns", $toast);
	$notification->headers[] = 'X-WindowsPhone-Target : toast';
	$notification->headers[] = 'X-NotificationClass : 2';
	$hub->sendNotification($notification);


### Kindle Fire
	$message = '{"data":{"msg":"Hello from PHP!"}}';
	$notification = new Notification("adm", $message);
	$hub->sendNotification($notification);

A execução do código PHP agora deve produzir uma notificação que aparece no dispositivo de destino.


## Próximas etapas
Neste tópico, mostramos como criar um cliente REST simples do Java para Hubs de Notificação. A partir daqui, você pode:

* Baixar a [amostra do wrapper de REST PHP] completa, que contém todos os códigos acima.
* Continuar a aprender sobre o recurso de criação de tags dos Hubs de Notificação no[tutorial de Notícias recentes]
* Saber como enviar notificações por push a usuários individuais no[tutorial Notificar usuários]

[amostra do wrapper de REST PHP]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[amostra de wrapper REST PHP]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Amostra de wrapper de REST PHP]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Tutorial de introdução]: http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-ios-get-started/

<!--HONumber=35.1-->
