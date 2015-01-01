<properties urlDisplayName="How to use Notification Hubs with Java" pageTitle="Como usar Hubs de Notificação com Java" metaKeywords="" description="Learn how to use Azure Notification Hubs from a Java back-end." metaCanonical="" services="mobile-services,notification-hubs,push,java" documentationCenter="" title="How to use Notification Hubs with Java" authors="piyushjo" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="java" ms.topic="article" ms.date="11/14/2014" ms.author="piyushjo" />

# Como usar os Hubs de Notificação do Java/PHP
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/pt-br/documentation/articles/notification-hubs-java-backend-how-to/" title="Java" class="current">Java</a><a href="/pt-br/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a>
</div>

Você pode acessar todos os recursos dos Hubs de Notificação por meio de um back-end do Java/PHP/Ruby usando a interface REST do Hub de Notificação, conforme descrito no tópico do MSDN[APIs REST dos Hubs de Notificação](http://msdn.microsoft.com/pt-br/library/dn223264.aspx).

Neste tópico, mostramos como:

* Criar um cliente REST para recursos dos Hubs de Notificação no Java;
* Siga o [Tutorial de introdução](http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-ios-get-started/) para sua plataforma móvel de escolha, implementando a parte sobre back-end no Java.

##<a name="client-interface"></a>Interface do cliente
A principal interface do cliente pode fornecer os mesmos métodos que estão disponíveis no [SDK dos Hubs de Notificação .NET](http://msdn.microsoft.com/pt-br/library/jj933431.aspx), isso permitirá que você traduza diretamente todos os tutoriais e amostras atualmente disponíveis neste site, e contribua para a comunidade na Internet.

Você pode encontrar todos os códigos disponíveis na [amostra de wrapper do REST do Java].

Por exemplo, para criar um cliente:

	new NotificationHub("connection string", "hubname");	

Para criar um registro no iOS (análogo para Windows, Android, Windows Phone e Kindle Fire):

	String id = hub.createRegistrationId();
	AppleRegistration reg = new AppleRegistration(id, DEVICETOKEN);
	reg.getTags().add("myTag");
	reg.getTags().add("myOtherTag");
	hub.upsertRegistration(reg);

Para enviar uma notificação nativa do iOS:
	
	Notification n = Notification.createAppleNotifiation("APNS body");
	hub.sendNotification(n);

##<a name="implementation"></a>Implementação
Se você ainda não o fez, siga o nosso [Tutorial de introdução] até a última seção em que você tem que implementar o back-end.
Além disso, se você quiser, pode usar o código da [amostra de wrapper REST Java] e ir diretamente para [Concluir o tutorial](#complete-tutorial) .

Todos os detalhes para implementar um wrapper completo do REST podem ser encontrados em [MSDN](http://msdn.microsoft.com/pt-br/library/dn530746.aspx). Nesta seção, descreveremos a implementação do Java das principais etapas necessárias para acessar os pontos de extremidade de REST dos Hubs de Notificação:

1. Analisar a cadeia de conexão
2. Gerar o token de autorização
3. Realizar a chamada do HTTP

Nos trechos a seguir, utilizamos os seguintes componentes:

* [Apache HttpComponents](http://hc.apache.org/httpcomponents-client-ga/)
* [Apache Commons-Codec](http://commons.apache.org/proper/commons-codec/)
* [Apache Commons-Io](http://commons.apache.org/proper/commons-io/)

### Analisar a cadeia de conexão

Aqui está a classe principal que implementa o cliente, cujo construtor analisa a cadeia de conexão:

	public class NotificationHub {

		private static final String APIVERSION = "?api-version=2013-10";
		private static final String CONTENT_LOCATION_HEADER = "Location";
		private String endpoint;
		private String hubPath;
		private String SasKeyName;
		private String SasKeyValue;
	
		private HttpClient httpClient;
	
		public NotificationHub(String connectionString, String hubPath) {
			this.httpClient = HttpClients.createDefault();
			this.hubPath = hubPath;
	
			String[] parts = connectionString.split(";");
			if (parts.length != 3)
				throw new RuntimeException("Error parsing connection string: "
						+ connectionString);
	
			for (int i = 0; i < parts.length; i++) {
				if (parts[i].startsWith("Endpoint")) {
					this.endpoint = "https" + parts[i].substring(11);
				} else if (parts[i].startsWith("SharedAccessKeyName")) {
					this.SasKeyName = parts[i].substring(20);
				} else if (parts[i].startsWith("SharedAccessKey")) {
					this.SasKeyValue = parts[i].substring(16);
				}
			}
		}
	}


### Criar token de segurança
Os detalhes da criação do token de segurança estão disponíveis[aqui](http://msdn.microsoft.com/pt-br/library/dn495627.aspx).
O método a seguir deve ser adicionado à classe **NotificationHub**para criar o token com base no URI da solicitação atual e as credenciais extraídas da cadeia de conexão.

	private String generateSasToken(URI uri) {
		String targetUri;
		try {
			targetUri = URLEncoder
					.encode(uri.toString().toLowerCase(), "UTF-8")
					.toLowerCase();

			long expiresOnDate = System.currentTimeMillis();
			int expiresInMins = 60; // 1 hour
			expiresOnDate += expiresInMins * 60 * 1000;
			long expires = expiresOnDate / 1000;
			String toSign = targetUri + "\n" + expires;

			// Get an hmac_sha1 key from the raw key bytes
			byte[] keyBytes = SasKeyValue.getBytes("UTF-8");
			SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

			// Get an hmac_sha1 Mac instance and initialize with the signing key
			Mac mac = Mac.getInstance("HmacSHA256");
			mac.init(signingKey);

			// Compute the hmac on input data bytes
			byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

			// Convert raw bytes to Hex
			String signature = URLEncoder.encode(
					Base64.encodeBase64String(rawHmac), "UTF-8");

			// construct authorization string
			String token = "SharedAccessSignature sr=" + targetUri + "&sig="
					+ signature + "&se=" + expires + "&skn=" + SasKeyName;
			return token;
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}

### Enviar uma notificação
Primeiro, vamos definir uma classe que representa uma notificação.

	import java.util.HashMap;
	import java.util.Iterator;
	import java.util.Map;
	import org.apache.http.entity.ContentType;

	public class Notification {
		private Map<String, String> headers = new HashMap<String, String>();
		private String body;
		private ContentType contentType;
	
		public static Notification createWindowsNotification(String body) {
			Notification n = new Notification();
			n.body = body;
			n.headers.put("ServiceBusNotification-Format", "windows");
	
			if (body.contains("<toast>"))
				n.headers.put("X-WNS-Type", "wns/toast");
			if (body.contains("<tile>"))
				n.headers.put("X-WNS-Type", "wns/tile");
			if (body.contains("<badge>"))
				n.headers.put("X-WNS-Type", "wns/badge");
			if (body.startsWith("<")) {
				n.contentType = ContentType.APPLICATION_XML;
			}
			return n;
		}
	
		public static Notification createAppleNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "apple");
			return n;
		}
	
		public static Notification createGcmNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "gcm");
			return n;
		}

		public static Notification createAdmNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "adm");
			return n;
		}

		public static Notification createMpnsNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.headers.put("ServiceBusNotification-Format", "windowsphone");
	
			if (body.contains("<wp:Toast>")) {
				n.headers.put("X-WindowsPhone-Target", "toast");
				n.headers.put("X-NotificationClass", "2");
			}
			if (body.contains("<wp:Tile>")) {
				n.headers.put("X-WindowsPhone-Target", "tile");
				n.headers.put("X-NotificationClass", "1");
			}
			if (body.startsWith("<")) {
				n.contentType = ContentType.APPLICATION_XML;
			}
			return n;
		}
	
		public static Notification createTemplateNotification(
				Map<String, String> properties) {
			Notification n = new Notification();
			StringBuffer buf = new StringBuffer();
			buf.append("{");
			for (Iterator<String> iterator = properties.keySet().iterator(); iterator
					.hasNext();) {
				String key = iterator.next();
				buf.append("\"" + key + "\":\"" + properties.get(key) + "\"");
				if (iterator.hasNext())
					buf.append(",");
			}
			buf.append("}");
			n.body = buf.toString();
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "template");
			return n;
		}
	
		public Map<String, String> getHeaders() { return headers; }
	
		public void setHeaders(Map<String, String> headers) { this.headers = headers; }
	
		public String getBody() { return body; }
	
		public void setBody(String body) { this.body = body; }
	
		public ContentType getContentType() { return contentType; }
	
		public void setContentType(ContentType contentType) { this.contentType = contentType; }
	}

Essa classe é um contêiner para um corpo de notificação nativa ou um conjunto de propriedade no caso de uma notificação de modelo, e um conjunto de cabeçalhos que contém propriedades específicas de formato (plataforma nativa ou modelo) e plataforma (como a propriedade de expiração da Apple e cabeçalhos WNS). Também definimos alguns construtores convenientes para gerar os tipos de notificação utilizados comumente.

Consulte a [Documentação de APIs REST dos Hubs de Notificação](http://msdn.microsoft.com/pt-br/library/dn495827.aspx) e os formatos das plataformas de notificação específicos para todas as opções disponíveis.

Armados com essa classe, agora podemos gravar os métodos de notificação de envio dentro da classe **NotificationHub**.

	public void sendNotification(Notification notification) {
		sendNotification(notification, "");
	}

	public void sendNotification(Notification notification, Set<String> tags) {
		if (tags.isEmpty())
			throw new IllegalArgumentException(
					"tags has to contain at least an element");

		StringBuffer exp = new StringBuffer();
		for (Iterator<String> iterator = tags.iterator(); iterator.hasNext();) {
			exp.append(iterator.next());
			if (iterator.hasNext())
				exp.append(" || ");
		}

		sendNotification(notification, exp.toString());
	}

	public void sendNotification(Notification notification, String tagExpression) {
		HttpPost post = null;
		try {
			URI uri = new URI(endpoint + hubPath + "/messages" + APIVERSION);
			post = new HttpPost(uri);
			post.setHeader("Authorization", generateSasToken(uri));

			if (tagExpression != null && !"".equals(tagExpression)) {
				post.setHeader("ServiceBusNotification-Tags", tagExpression);
			}

			for (String header : notification.getHeaders().keySet()) {
				post.setHeader(header, notification.getHeaders().get(header));
			}

			post.setEntity(new StringEntity(notification.getBody()));
			HttpResponse response = httpClient.execute(post);

			if (response.getStatusLine().getStatusCode() != 201) {
				String msg = "";
				if (response.getEntity() != null
						&& response.getEntity().getContent() != null) {
					msg = IOUtils.toString(response.getEntity().getContent());
				}
				throw new RuntimeException("Error: " + response.getStatusLine()
						+ " body: " + msg);
			}

		} catch (Exception e) {
			throw new RuntimeException(e);
		} finally {
			if (post != null)
				post.releaseConnection();
		}
	}

Os métodos acima enviam uma solicitação de HTTP POST para o ponto de extremidade /messages de seu hub de notificação, com o corpo e os cabeçalhos corretos para o envio da notificação.

##<a name="complete-tutorial"></a>Concluir o tutorial
Agora você pode concluir o tutorial de introdução enviando a notificação por meio de um back-end do Java.

Inicialize seu cliente Hubs de Notificação (substitua a cadeia de conexão e o nome do hub conforme indicado no [Tutorial de introdução]):
	NotificationHub hub = new NotificationHub("{connection string}", "{hubname}");

Em seguida, adicione o código de envio dependendo da sua plataforma móvel de destino.

### Windows Store e Windows Phone 8.1 (não Silverlight)


	Notification n = Notification.createWindowsNotification(toast);
	hub.sendNotification(n);

### iOS

	String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
	Notification n = Notification.createAppleNotification(alert);
	hub.sendNotification(n);

### Android
	String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
	Notification n = Notification.createGcmNotification(message);
	hub.sendNotification(n);

### Silverlight para Windows Phone 8.0 e 8.1

	String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
		        "<wp:Notification xmlns:wp=\"WPNotification\">" +
		           "<wp:Toast>" +
		                "<wp:Text1>Hello from Java!</wp:Text1>" +
		           "</wp:Toast> " +
		        "</wp:Notification>";
	Notification n = Notification.createMpnsNotification(toast);
	hub.sendNotification(n);

### Kindle Fire
	String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
	Notification n = Notification.createAdmNotification(message);
	hub.sendNotification(n);

A execução do código Java agora deve produzir uma notificação que aparece no dispositivo de destino.


##<a name="next-steps"></a>Próximas etapas
Neste tópico, mostramos como criar um cliente REST simples do Java para Hubs de Notificação. A partir daqui, você pode:

* Baixar a [amostra do wrapper de REST do Java] completa, que contém todos os códigos acima, além do gerenciamento de registros.
* Continuar a aprender sobre o recurso de criação de tags dos Hubs de Notificação no[tutorial de Notícias recentes]
* Saber como enviar notificações por push a usuários individuais no[tutorial Notificar usuários]




[Amostra de wrapper de REST Java]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-java
[Tutorial de introdução]: http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-ios-get-started/

<!--HONumber=35_1-->
