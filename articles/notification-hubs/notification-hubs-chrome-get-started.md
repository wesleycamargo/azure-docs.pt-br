<properties
	pageTitle="Introdução aos Hubs de Notificação do Azure para aplicativos do Chrome | Microsoft Azure"
	description="Neste tutorial, você aprenderá a usar os Hubs de Notificação do Azure para enviar notificações por push a um Aplicativo Chrome."
	services="notification-hubs"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-chrome"
	ms.devlang="JavaScript"
	ms.topic="hero-article"
	ms.date="09/03/2015"
	ms.author="wesmc"/>

# Introdução aos Hubs de Notificação para aplicativos do Chrome

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Este tópico mostra como usar os Hubs de notificação do Azure para enviar notificações por push para um aplicativo Chrome.

Um dos principais benefícios do uso de notificações do Aplicativo Chrome é que as notificações aparecem dentro do contexto do navegador Google Chrome. Você não precisa executar o Aplicativo Chrome ou abrir o navegador (embora o próprio navegador Chrome precise estar em execução). Você também pode obter uma exibição consolidada de todas as suas notificações na janela de notificações do Chrome.

>[AZURE.NOTE] Esta não é uma notificação por push genérica no navegador, mas específica aos Aplicativos Chrome – veja [Visão geral dos Aplicativos Chrome] para obter mais detalhes. Os aplicativos de Chrome eram anteriormente conhecidos como “Aplicativos empacotados” e são diferentes dos “aplicativos hospedados”, mais simples. Consulte [Aplicativos Web instaláveis] para saber a diferença. Os Aplicativos Chrome também podem ser executados em dispositivos móveis (Android e iOS) por meio do Apache Cordova. Consulte [Aplicativos do Chrome em Dispositivos móveis] para obter mais informações.

Neste tutorial, criaremos um Aplicativo Chrome que recebe notificações por push usando o Google Cloud Messaging (GCM). Ao concluir o tutorial, você poderá difundir notificações por push a todos os usuários do Chrome que instalaram esse Aplicativo Chrome.

O tutorial explica as seguintes etapas básicas para habilitar as notificações por push:

* [Habilitar as mensagens em nuvem do Google](#register)
* [Configurar seu Hub de Notificação](#configure-hub)
* [Conectar seu Aplicativo Chrome ao hub de notificação](#connect-app)
* [Enviar uma notificação para seu Aplicativo Chrome](#send)
* [Próximas etapas](#next-steps)

Este tutorial demonstra o cenário de transmissão simples usando Hubs de Notificação. A configuração do GCM e dos Hubs de Notificação do Azure é idêntica à configuração para o Android, já que o [Google Cloud Messaging para Chrome] foi substituído e o mesmo GCM agora dá suporte a dispositivos Android e a instâncias do Chrome.

Lembre-se de seguir os tutoriais na seção “Próximas etapas” para saber como usar os hubs de notificação para abordar usuários e grupos de dispositivos específicos.

>[AZURE.NOTE] Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).

##<a id="register"></a>Habilitar as mensagens em nuvem do Google

1. Navegue até o site [Console do Google Cloud], conecte-se com as credenciais de sua conta do Google e clique no botão **Criar Projeto**. Forneça um **Nome de Projeto** apropriado e clique no botão **Criar**.

   	![][1]

2. Anote o **Número do Projeto** na página de **projetos** do projeto que você acabou de criar. Você o usará como a **ID de Remetente do GCM** no Aplicativo Chrome para se registrar no GCM.

   	![][2]

3. No painel à esquerda, clique em **APIs e autorização**, role para baixo e clique no botão de alternância para habilitar o **Google Cloud Messaging para Android**. Você não precisa habilitar o **Google Cloud Messaging para Chrome**.

   	![][3]

4. No painel à esquerda, clique em **Credenciais** -> **Criar Nova Chave** -> **Chave do Servidor** -> **Criar**.

   	![][4]

5. Anote a **Chave de API** do servidor. Em seguida, você a configurará em seu hub de notificação para habilitá-lo a enviar notificações por push para o GCM.

   	![][5]

##<a id="configure-hub"></a>Configurar seu hub de notificação

1. Entre no [portal do Azure] e clique em **+NOVO** na parte inferior esquerda da tela.

2. Clique em **Serviços de Aplicativos** -> **Barramento de Serviço** -> **Hub de Notificação** -> **Criação Rápida**. Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um novo Hub de Notificação**.

   	![][6]

4. Vá para o hub de notificação que você acabou de criar. Clique no namespace que hospeda o hub de notificação (geralmente ***nome do hub de notificação*-ns**).

   	![][7]

5. Clique na guia **Hubs de Notificação** na parte superior.

   	![][8]

6. Clique na guia **Configurar** na parte superior.

   	![][9]

7. Na guia **Configurar**, role para baixo até as **configurações do Google Cloud Messaging**, insira o valor da **Chave de API** que você obteve na seção anterior e depois clique em **Salvar**.

   	![][10]

8. Clique na guia **Painel** na parte superior e em **Informações de Conexão** na parte inferior.

   	![][11]

9. Anote a **DefaultListenSharedAccessSignature** (que você precisará no Aplicativo Chrome para se registrar no hub de notificação) e a **DefaultFullSharedAccessSignature** (que você precisará para enviar notificações).

   	![][12]

O hub de notificação agora está configurado para funcionar com o GCM e você tem as cadeias de caracteres de conexão necessárias para configurações adicionais.

##<a id="connect-app"></a>Conectar seu Aplicativo Chrome ao hub de notificação

###Criar um novo Aplicativo Chrome

O exemplo a seguir é baseado no [Exemplo do GCM do Aplicativo Chrome] e usa a maneira recomendada para criar um Aplicativo Chrome. Nas seções abaixo, destacaremos as etapas relacionadas aos Hubs de Notificação do Azure. Recomendamos que você baixe a fonte deste Aplicativo Chrome do [Exemplo de Hub de Notificação do Aplicativo Chrome].

O Aplicativo Chrome é criado por meio do JavaScript, e você pode usar um de seus editores de texto preferidos para criá-lo. Abaixo, esta é a aparência desse aplicativo Chrome.

   	![][15]

2. Crie uma pasta e nomeie-a **ChromePushApp** ou com outro nome que desejar.

3. Baixe **cryto-js library** de [crypto-js library] nesta pasta. Esta pasta de bibliotecas conterá duas subpastas: **components** e **rollups**.

4. Crie um arquivo **manifest.json**. Todos os Aplicativos Chrome tem o suporte de um arquivo de manifesto que descreve os metadados do aplicativo e, especificamente, as permissões disponíveis para o aplicativo.

		{
		  "name": "NH-GCM Notifications",
		  "description": "Chrome platform app.",
		  "manifest_version": 2,
		  "version": "0.1",
		  "app": {
		    "background": {
		      "scripts": ["background.js"]
		    }
		  },
		  "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
		  "icons": { "128": "gcm_128.png" }
		}

	Observe o elemento **permissions**, que especifica que este Aplicativo Chrome poderá receber notificações por push do GCM. Ele também deve especificar o URI dos Hubs de Notificação do Azure nos quais o Aplicativo Chrome fará uma chamada REST para o registro. Ela usa um arquivo de ícone, gcm\_128.png, que você encontrará na fonte reutilizada por meio do exemplo original do GCM. Você pode usar qualquer imagem que desejar.

5. Crie um arquivo chamado **background.js** com o seguinte código:

		// Returns a new notification ID used in the notification.
		function getNotificationId() {
		  var id = Math.floor(Math.random() * 9007199254740992) + 1;
		  return id.toString();
		}

		function messageReceived(message) {
		  // A message is an object with a data property that
		  // consists of key-value pairs.

		  // Concatenate all key-value pairs to form a display string.
		  var messageString = "";
		  for (var key in message.data) {
		    if (messageString != "")
		      messageString += ", "
		    messageString += key + ":" + message.data[key];
		  }
		  console.log("Message received: " + messageString);

		  // Pop up a notification to show the GCM message.
		  chrome.notifications.create(getNotificationId(), {
		    title: 'GCM Message',
		    iconUrl: 'gcm_128.png',
		    type: 'basic',
		    message: messageString
		  }, function() {});
		}

		var registerWindowCreated = false;

		function firstTimeRegistration() {
		  chrome.storage.local.get("registered", function(result) {

		    registerWindowCreated = true;
		    chrome.app.window.create(
		      "register.html",
		      {  width: 520,
		         height: 500,
		         frame: 'chrome'
		      },
		      function(appWin) {}
		    );
		  });
		}

		// Set up a listener for GCM message event.
		chrome.gcm.onMessage.addListener(messageReceived);

		// Set up listeners to trigger the first-time registration.
		chrome.runtime.onInstalled.addListener(firstTimeRegistration);
		chrome.runtime.onStartup.addListener(firstTimeRegistration);

	Este é o arquivo que abre o HTML da janela do Aplicativo Chrome (**register.html**) e também define o manipulador **messageReceived** para manipular a notificação por push recebida.

6. Crie um arquivo chamado **register.html** que define a interface do usuário do Aplicativo Chrome. Vale lembrar que este exemplo usa o *CryptoJS v3.1.2*. Se você baixou qualquer outra versão, corrija o caminho de origem do script.

		<html>

		<head>
		<title>GCM Registration</title>
		<script src="register.js"></script>
		<script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
		<script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
		</head>

		<body>

		Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
		<button id="registerWithGCM">Register with GCM</button>
		<br/>
		<br/>
		<br/>

		Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
		Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

		<br/>

		<button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>

		<br/>
		<br/>

		<textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>

		</body>

		</html>

7. Crie um arquivo chamado **register.js** com o código abaixo. Esse arquivo especifica o script por trás de **register.html**. Os Aplicativos Chrome não permitem a execução embutida, por isso é necessário criar um script de backup separado para a sua interface do usuário.

		var registrationId = "";
		var hubName        = "", connectionString = "";
		var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

		window.onload = function() {
		   document.getElementById("registerWithGCM").onclick = registerWithGCM;  
		   document.getElementById("registerWithNH").onclick = registerWithNH;
		   updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
		}

		function updateLog(status) {
		  currentStatus = document.getElementById("console").innerHTML;
		  if (currentStatus != "") {
		    currentStatus = currentStatus + "\n\n";
		  }

		  document.getElementById("console").innerHTML = currentStatus  + status;
		}

		function registerWithGCM() {
		  var senderId = document.getElementById("senderId").value.trim();
		  chrome.gcm.register([senderId], registerCallback);

		  // Prevent register button from being clicked again before the registration finishes.
		  document.getElementById("registerWithGCM").disabled = true;
		}

		function registerCallback(regId) {
		  registrationId = regId;
		  document.getElementById("registerWithGCM").disabled = false;

		  if (chrome.runtime.lastError) {
		    // When the registration fails, handle the error and retry the
		    // registration later.
		    updateLog("Registration failed: " + chrome.runtime.lastError.message);
		    return;
		  }

		  updateLog("Registration with GCM succeeded.");
		  document.getElementById("registerWithNH").disabled = false;

		  // Mark that the first-time registration is done.
		  chrome.storage.local.set({registered: true});
		}

		function registerWithNH() {
		  hubName = document.getElementById("hubName").value.trim();
		  connectionString = document.getElementById("connectionString").value.trim();
		  splitConnectionString();
		  generateSaSToken();
		  sendNHRegistrationRequest();
		}

		// From http://msdn.microsoft.com/library/dn495627.aspx
		function splitConnectionString()
		{
		  var parts = connectionString.split(';');
		  if (parts.length != 3)
		  throw "Error parsing connection string";

		  parts.forEach(function(part) {
		    if (part.indexOf('Endpoint') == 0) {
		    endpoint = 'https' + part.substring(11);
		    } else if (part.indexOf('SharedAccessKeyName') == 0) {
		    sasKeyName = part.substring(20);
		    } else if (part.indexOf('SharedAccessKey') == 0) {
		    sasKeyValue = part.substring(16);
		    }
		  });

		  originalUri = endpoint + hubName;
		}

		function generateSaSToken()
		{
		  targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
		  var expiresInMins = 10; // 10 minute expiration

		  // Set expiration in seconds.
		  var expireOnDate = new Date();
		  expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
		  var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
		    .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
		    .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
		    .getUTCSeconds()) / 1000;
		  var tosign = targetUri + '\n' + expires;

		  // Using CryptoJS.
		  var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
		  var base64signature = signature.toString(CryptoJS.enc.Base64);
		  var base64UriEncoded = encodeURIComponent(base64signature);

		  // Construct authorization string.
		  sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
		                  + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
		}

		function sendNHRegistrationRequest()
		{
		  var registrationPayload =
		  "<?xml version="1.0" encoding="utf-8"?>" +
		  "<entry xmlns="http://www.w3.org/2005/Atom">" +
		      "<content type="application/xml">" +
		          "<GcmRegistrationDescription xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect">" +
		              "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
		          "</GcmRegistrationDescription>" +
		      "</content>" +
		  "</entry>";

		  // Update the payload with the registration ID obtained earlier.
		  registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

		  var url = originalUri + "/registrations/?api-version=2014-09";
		  var client = new XMLHttpRequest();

		  client.onload = function () {
		    if (client.readyState == 4) {
		      if (client.status == 200) {
		        updateLog("Notification Hub Registration succesful!");
		        updateLog(client.responseText);
		      } else {
		        updateLog("Notification Hub Registration did not succeed!");
		        updateLog("HTTP Status: " + client.status + " : " + client.statusText);
		        updateLog("HTTP Response: " + "\n" + client.responseText);
		      }
		    }
		  };

		  client.onerror = function () {
		        updateLog("ERROR - Notification Hub Registration did not succeed!");
		  }

		  client.open("POST", url, true);
		  client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
		  client.setRequestHeader("Authorization", sasToken);
		  client.setRequestHeader("x-ms-version", "2014-09");

		  try {
		      client.send(registrationPayload);
		  }
		  catch(err) {
		      updateLog(err.message);
		  }
		}

	O script acima tem os seguintes argumentos: 
	- *window.onload* define os eventos de clique de botão dos dois botões da interface do usuário. Um deles faz o registro no GCM e o outro usa a ID de registro retornada após o registro no GCM para fazer o registro nos Hubs de Notificação do Azure. 
	- *updateLog* é a função que define uma função simples de registro em log. 
	- *registerWithGCM* é o primeiro manipulador de clique de botão, que faz a chamada de **chrome.gcm.register** para o GCM para registrar essa instância do Aplicativo Chrome. 
	- *registerCallback* é a função de retorno de chamada que é chamada quando a chamada de registro do GCM acima é retornada. 
	- *registerWithNH* é o segundo manipulador de clique de botão, que faz o registro nos Hubs de Notificação. Ele obtém **hubName** e **connectionString** (que o usuário especificou) e cria a chamada à API REST do Registro nos Hubs de Notificação. 
	- *splitConnectionString* e *generateSaSToken* são a implementação de JavaScript da criação de um token de SaS que deve ser enviado em todas as chamadas à API REST. Para obter mais informações, veja [Conceitos comuns](http://msdn.microsoft.com/library/dn495627.aspx). 
	- *sendNHRegistrationRequest* é a função que faz uma chamada a HTTP REST. 
	- *registrationPayload* define a carga de XML do registro. Para obter mais informações, veja [Criar um registro da API REST NH]. Atualizamos a ID do registro com o que recebemos do GCM. 
	- *client* é uma instância de **XMLHttpRequest** que usamos para fazer a solicitação HTTP POST. Vale lembrar que atualizamos o cabeçalho **Authorization** com **sasToken**. A conclusão bem-sucedida dessa chamada registrará essa instância do aplicativo Chrome com os Hubs de notificação do Azure.


Você deverá ver a seguinte exibição para a sua pasta ao final desta configuração: ![][21]

###Configurar e testar seu Aplicativo Chrome

1. Abra seu navegador Chrome. Abra as **extensões do Chrome** e habilite o **Modo de desenvolvedor**.

   	![][16]

2. Clique em **Carregar extensão descompactada** e navegue até a pasta na qual você criou os arquivos. Você também pode usar a **Ferramenta de desenvolvimento de extensões e Aplicativos Chrome**. Essa ferramenta é um Aplicativo Chrome por si só (instalada por meio do Chrome Web Store) e fornece recursos avançados de depuração para o desenvolvimento do seu Aplicativo Chrome.

   	![][17]

3. Se o Aplicativo Chrome for criado sem erros, você o verá sendo exibido.

   	![][18]

4. Insira o **Número do Projeto** obtido anteriormente do **Console do Google Cloud**, bem como a ID do remetente, e clique em **Registrar no GCM**. Você deve ver a mensagem **Registro no GCM concluído com êxito.**

   	![][19]

5. Insira o **Nome do Hub de Notificação** e a **DefaultListenSharedAccessSignature** obtidos anteriormente do Portal do Azure e clique em **Registrar no Hub de Notificação do Azure**. Você deve ver a mensagem **Registro no Hub de Notificação concluído com êxito!** e os detalhes da resposta do registro, que contém a ID de Registro nos Hubs de Notificação do Azure.

   	![][20]

##<a name="send"></a>Enviar uma notificação para seu Aplicativo Chrome

Neste tutorial, você envia as notificações com um aplicativo de console .NET. No entanto, você pode enviar notificações usando os Hubs de Notificação de qualquer back-end por meio da <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>.

Para ver um exemplo de como enviar notificações de um back-end dos Serviços Móveis do Azure integrado aos Hubs de Notificação, veja “Introdução às notificações por push nos Serviços Móveis” ([back-end do .NET](../mobile-services-javascript-backend-android-get-started-push.md) | [back-end do JavaScript](../mobile-services-javascript-backend-android-get-started-push.md)). Para ver um exemplo de como enviar notificações usando as APIs REST, veja “Como usar os Hubs de Notificação por meio do Java/PHP/Python” ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md) | [Python](notification-hubs-python-backend-how-to.md)).

1. No Visual Studio, no menu **Arquivo**, selecione **Novo** e **Projeto**. Em **Visual C#**, clique em **Windows** e **Aplicativo do Console** e depois clique em **OK**. Isso cria um novo projeto de aplicativo de console.

2. No menu **Ferramentas**, clique em **Gerenciador de Pacotes de Biblioteca** e, em seguida, selecione **Console do Gerenciador de Pacotes**. Isso exibe a Console do Gerenciador de Pacotes.

3. Na janela da console, execute o seguinte comando:

        Install-Package WindowsAzure.ServiceBus

   	Isso adiciona uma referência ao SDK do Barramento de Serviço do Azure com o <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">pacote NuGet do WindowsAzure.ServiceBus</a>.

4. Abra o arquivo **Program.cs** e adicione a seguinte instrução `using`:

        using Microsoft.ServiceBus.Notifications;

5. Na classe **Programa**, adicione o seguinte método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{"data":{"message":"Hello Chrome from Azure Notification Hubs"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

   	Lembre-se de substituir o espaço reservado *nome do hub* pelo nome do hub de notificação que aparece no portal, na guia **Hubs de Notificação**. Além disso, substitua o espaço reservado da cadeia de conexão pela cadeia de conexão chamada **DefaultFullSharedAccessSignature** obtida na seção “Configurar seu hub de notificação”.

	>[AZURE.NOTE] Verifique se você está usando a cadeia de conexão com acesso **Completo**, não com acesso para **Escutar**. A cadeia de acesso **Escutar** não tem permissões para enviar notificações.

5. Adicione as seguintes linhas no método **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

6. Verifique se o navegador Chrome está aberto. Seu aplicativo Chrome não precisa ser aberto para isso. Você deve ver a pop-up de notificação abaixo na área de trabalho.

   	![][13]

7. Você também pode ver todas as notificações usando a janela de Notificações do Chrome na barra de tarefas (no Windows) quando o Chrome estiver em execução.

   	![][14]

## <a name="next-steps"> </a>Próximas etapas

Neste exemplo simples, você transmitiu notificações para o seu Aplicativo Chrome.
Saiba mais sobre os Hubs de Notificação em [Visão geral dos Hubs de Notificação].
Para ter como alvo usuários específicos, consulte o tutorial [Notificação de usuários nos Hubs de Notificação do Azure]. Se desejar segmentar os usuários por grupos de interesse, você poderá ler as [Notícias mais recentes dos Hubs de Notificação do Azure].

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Exemplo de Hub de Notificação do Aplicativo Chrome]: http://google.com
[Console do Google Cloud]: http://cloud.google.com/console
[portal do Azure]: https://manage.windowsazure.com/
[Visão geral dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Visão geral dos Aplicativos Chrome]: https://developer.chrome.com/apps/about_apps
[Exemplo do GCM do Aplicativo Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Aplicativos Web instaláveis]: https://developers.google.com/chrome/apps/docs/
[Aplicativos do Chrome em Dispositivos móveis]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Criar um registro da API REST NH]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[crypto-js library]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging para Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Notificação de usuários nos Hubs de Notificação do Azure]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Notícias mais recentes dos Hubs de Notificação do Azure]: notification-hubs-windows-store-dotnet-send-breaking-news.md

<!----HONumber=Sept15_HO2-->