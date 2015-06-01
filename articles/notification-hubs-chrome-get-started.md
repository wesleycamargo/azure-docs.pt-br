<properties 
	pageTitle="Introdução aos Hubs de notificação do Azure" 
	description="Saiba como usar os Hubs de notificação do Azure para notificações por push aos usuários" 
	services="notification-hubs" 
	documentationCenter="" 
	authors="piyushjo" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-chrome" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="03/15/2015" 
	ms.author="piyushjo"/>
	
# Introdução aos Hubs de Notificação

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

Este tópico mostra como usar os Hubs de notificação do Azure para enviar notificações por push para um aplicativo Chrome.

Um dos principais benefícios do uso de notificações do aplicativo de Chrome é que as notificações aparecem dentro do contexto do navegador Google Chrome e você não precisa ter o aplicativo Chrome executando ou aberto no navegador (embora o próprio navegador Chrome deva estar em execução).  Você também pode obter uma exibição consolidada de todas as suas notificações na janela de notificações do Chrome. 

>[AZURE.NOTE] Essa não é uma notificação por push de navegador genérica e é específica para aplicativos do Chrome - consulte [Visão geral de aplicativos do Chrome] para obter detalhes.  Os aplicativos de Chrome eram anteriormente conhecidos como "Aplicativos empacotados" e são diferentes dos "aplicativos hospedados", mais simples.  Consulte [Aplicativos Web instaláveis] para saber a diferença.  Os aplicativos do Chrome também podem ser executados em Dispositivos móveis (Android e iOS) usando o Apache Cordova.  Consulte [Aplicativos do Chrome em Dispositivos móveis] para obter mais informações. 

Neste tutorial, criaremos um aplicativo do Chrome que recebe notificações por push usando o Google Cloud Messaging (GCM).  Ao concluir, você poderá transmitir notificações push para todos os usuários do Chrome que instalaram esse aplicativo Chrome. 

O tutorial explica as seguintes etapas básicas para habilitar as notificações por push:

* [Habilitar o Google Cloud Messaging (GCM)](#register)
* [Configurar seu Hub de Notificação](#configure-hub)
* [Conectar seu aplicativo Chrome ao Hub de notificação](#connect-app)
* [Enviar notificação para seu aplicativo Chrome](#send)
* [Próximas etapas](#next-steps)

Este tutorial demonstra o simples cenário de transmissão usando Hubs de Notificação.  A configuração do GCM e do Hub de notificação do Azure é idêntica à configuração para o Android desde que o [Google Cloud Messaging para Chrome] foi substituído e o mesmo GCM agora oferece suporte a dispositivos com Android e instâncias do Chrome. 

Certifique-se de seguir os tutoriais em Próximas etapas para ver como usar hubs de notificação para atender usuários e grupos de dispositivos específicos. 

>[AZURE.NOTE] Para concluir este tutorial, você precisa ter uma conta ativa do Azure.  Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos.  Para obter detalhes, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).

##<a id="register"></a>Habilitar o Google Cloud Messaging (GCM)

1. Navegue até o site [Google Cloud Console], entre com as credenciais de sua conta do Google e clique no botão **Criar Projeto**.  Forneça um **nome de projeto** apropriado e clique no botão **Criar**. 

   	![][1]

2. Anote o **número do projeto** na página de projetos para o projeto que você acabou de criar.  Você o usará como o **ID de remetente do GCM** no aplicativo Chrome para registrar com o GCM.

   	![][2]

3. No painel esquerdo, clique em **APIs & auth**, role para baixo e clique no botão de alternância para ativar o **Google Cloud Messaging para Android**.  Você não precisa habilitar o *Google Cloud Messaging para o Chrome*.   Também é possível que o nome pode ser alterar somente para o *Google Cloud Messaging* no futuro. 

   	![][3]

4. No painel esquerdo, clique em **Credenciais** -> **Criar Nova chave** -> **Chave do servidor** -> **Criar**

   	![][4]

5. Anote **Chave da API** do servidor.  Você irá configurar isso em seu Hub de notificação antes de habilitá-lo para poder enviar notificações por push para o GCM. 

   	![][5]

##<a id="configure-hub"></a>Configurar seu Hub de Notificação

1. Faça logon no **[Portal de Gerenciamento do Azure]** e clique em **+ NOVO** na parte inferior da tela.

2. Clique em **Serviços de aplicativo** -> **Barramento de Serviço** -> **Hub de notificação** -> **Criação rápida**.  Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um novo Hub de Notificação**.

   	![][6]

4. Vá para o Hub de notificação que você acabou de criar.  Clique no namespace que hospeda seu hub de notificação (geralmente ***nome do hub de notificação*-ns**).   

   	![][7]

5. Clique na guia **Hubs de notificação** na parte superior.

   	![][8]

6. Agora clique na guia **Configurar** na parte superior. 

   	![][9]

7. Na guia **Configurar**, role para baixo até as **configurações do sistema de mensagens de nuvem do Google**, insira o valor da **chave API** que você obteve na seção anterior e, em seguida, clique em **Salvar**.

   	![][10]

8. Clique na guia **Painel** na parte superior e clique em **Informações de Conexão** na parte inferior. 

   	![][11]
 
9. Anote o **DefaultListenSharedAccessSignature** (que você precisará no aplicativo Chrome para registrar com o Hub de notificação) e o **DefaultFullSharedAccessSignature** (que você precisará enviar notificações) 

   	![][12]

O hub de notificação agora está configurado para funcionar com o GCM e você tem as cadeias de caracteres de conexão necessárias para configurações adicionais.

##<a id="connect-app"></a>Conectar seu aplicativo Chrome ao Hub de notificação

###Criar novo aplicativo Chrome
O exemplo a seguir é baseado no [Exemplo do GCM de aplicativo Chrome] e usa a maneira recomendada de criar um aplicativo Chrome.  Nas seções a seguir, vamos destacar as etapas relacionadas ao Hub de notificação do Azure.  É recomendável que você baixe a fonte para este aplicativo Chrome do [Exemplo de Hub de notificação de aplicativo Chrome].

O aplicativo Chrome é criado usando JavaScript e você pode usar qualquer de seu editor preferido do word para criar isso. 

1. Abaixo, esta é a aparência desse aplicativo Chrome. 

   	![][15]

2. Crie uma pasta e nomeie-a **ChromePushApp**.  Você pode nomeá-la do que você quiser. 

3. Baixe*a biblioteca cryto js* da [biblioteca crypto-js] nessa pasta.   Essa pasta de biblioteca conterá duas subpastas -*componentes* e*rollups*. 

4. Crie um arquivo manifest.json.  Todos os aplicativos Chrome são apoiados por um arquivo de manifesto que descreve os metadados de aplicativo e, em particular, as permissões disponíveis para o aplicativo.

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
	
	Observe o elemento *permissões* que especifica que esse aplicativo Chrome poderá receber notificações por push do GCM.  Ele também deve especificar o URI do Hub de notificação do Azure onde o aplicativo Chrome fará uma chamada REST para registrar.
	Isso usa um arquivo de ícone gcm_128.png que você encontrará na fonte reutilizada a partir do exemplo original do GCM.  Você pode usar qualquer imagem que desejar. 
 
5. Crie um arquivo chamado background.js com o código a seguir:

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
		
		// Set up listeners to trigger the first time registration.
		chrome.runtime.onInstalled.addListener(firstTimeRegistration);
		chrome.runtime.onStartup.addListener(firstTimeRegistration);

	Este é o arquivo que faz surgir o html de janela do aplicativo Chrome (*register.html*) e também define o manipulador*messageReceived* para tratar a notificação por push recebida. 

6. Crie um arquivo chamado*register.html* que define a interface do usuário do aplicativo Chrome.   Observe que este exemplo usa*CryptoJS v3.1.2*.  Se você baixou qualquer outra versão, corrija o caminho de origem do script. 

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

7. Crie um arquivo chamado*register.js* com o código a seguir.  Esse arquivo especifica o script por trás de *register.html*.  Os aplicativos Chrome não permitem a execução embutida de forma que é necessário criar um script de backup separado para sua interface do usuário. 

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
		
		  // Prevent register button from being clicked again before the registration finishes
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
		
		  // Set expiration in seconds
		  var expireOnDate = new Date();
		  expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
		  var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
		    .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
		    .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
		    .getUTCSeconds()) / 1000;
		  var tosign = targetUri + '\n' + expires;
		
		  // using CryptoJS
		  var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
		  var base64signature = signature.toString(CryptoJS.enc.Base64);
		  var base64UriEncoded = encodeURIComponent(base64signature);
		
		  // construct authorization string
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
		
		  // Update the payload with the registration id obtained earlier
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
	- *window.onload* define os eventos de clique do botão dos dois botões na interface do usuário - um registra com o GCM e o outro usa a ID de registro retornada após o registro com o GCM para registrar com Hubs de notificação do Azure. 
	- A função *updateLog* define uma função de log simples. 
	- *registerWithGCM* é o primeiro clique no botão do manipulador que faz a chamada *chrome.gcm.register* para o GCM para registrar essa instância do aplicativo Chrome. 
	- *registerCallback* é a função de retorno de chamada que é chamada quando a chamada de registro GCM acima é retornada.
	- *registerWithNH* é o segundo clique do botão manipulador que faz o registro com os Hubs de notificação.   Obtém o *hubName* e o *connectionString* que o usuário especificou e cria a chamada à API REST de Registro de Hubs de Notificação. 
	- *splitConnectionString* e *generateSaSToken* são a implementação de Javascript da criação de um token SaS que deve ser enviado em todas as chamadas da API REST.  Mais informações sobre isso aqui - http://msdn.microsoft.com/library/dn495627.aspx 
	- *sendNHRegistrationRequest* é a função que faz uma chamada REST HTTP.
	- *registrationPayload* define a carga de xml de registro.  Mais informações sobre isso aqui - [Criar o registro da API REST NH].  Podemos atualizar a id do registro com o que recebemos do GCM. 
	- *client* é uma instância de *XMLHttpRequest* usada para fazer a solicitação HTTP POST.   Observe que podemos atualizar o cabeçalho *Autorização* com o sasToken.  A conclusão bem-sucedida dessa chamada registrará essa instância do aplicativo Chrome com os Hubs de notificação do Azure. 
	

8. Você verá o seguinte modo de exibição para a pasta no final deste:
   	![][21]

###Configurar e testar seu aplicativo Chrome

1. Abra seu navegador Chrome.  Abra as **Extensões do Chrome** e habilite o **Modo de desenvolvedor**. 

   	![][16]

2. Clique em **Carregar a extensão descompactada** e navegue até a pasta onde você criou os arquivos.  Você também pode usar o **aplicativo Chrome e a Ferramenta de desenvolvimento de extensões** que é um aplicativo Chrome em si (e será necessário instalá-lo da Chrome Web Store) e fornece recursos avançados de depuração para o desenvolvimento de aplicativos Chrome. 

   	![][17]

3. Se o aplicativo Chrome for criado sem erros, em seguida, você verá seu aplicativo Chrome ser exibido. 

   	![][18]

4. Insira o **Número do projeto** obtido anteriormente no **Google Cloud Console**, assim como o ID de remetente e clique em **Registrar com o GCM**.  Você deve ver uma mensagem *Registro bem-sucedido com o GCM.*

   	![][19]

5. Insira seu **Nome do Hub de notificação** e **DefaultListenSharedAccessSignature** obtido anteriormente no Portal de gerenciamento do Azure e clique em **Registrar com o Hub de notificação do Azure**.  Você deve ver uma mensagem *Registro bem-sucedido do Hub de notificação!* e os detalhes da resposta do Registro que contém a ID do Registro de Hubs de notificação do Azure. 

   	![][20]  

##<a name="send"></a>Enviar notificação para seu aplicativo Chrome

Neste tutorial, você envia notificações com um aplicativo do console .NET embora você possa enviar notificações usando os Hubs de notificação de qualquer back-end usando a <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>. 

Para um exemplo de como enviar notificações por push de um back-end do Serviços Móveis do Azure integrado com hubs de notificação, consulte **Introdução às notificações por push nos Serviços Móveis** ([back-end do .NET](mobile-services-javascript-backend-android-get-started-push.md) | [Back-end do JavaScript](mobile-services-javascript-backend-android-get-started-push.md)).  
Para obter um exemplo de como enviar notificações usando as APIs REST, consulte **Como usar os Hubs de notificação no Java/PHP/Python** ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md) | [Python](notification-hubs-python-backend-how-to.md)).

1. No Visual Studio, no menu **Arquivo** selecione **Novo** e depois **Projeto...**, em seguida, em **Visual C#** clique em**Windows** e**Aplicativo do console** e clique em**OK**.    Isso cria um novo projeto de aplicativo de console.

2. No menu **Ferramentas**, clique em **Gerenciador de Pacotes de Biblioteca** e, em seguida, selecione **Console do Gerenciador de Pacotes**.  Isso exibe a Console do Gerenciador de Pacotes.

3. Na janela da console, execute o seguinte comando:

        Install-Package WindowsAzure.ServiceBus
    
   	Isso adiciona uma referência ao SDK do Barramento de Serviço do Azure com o <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">pacote NuGet do WindowsAzure.ServiceBus</a>. 

4. Abra o arquivo Program.cs e adicione a seguinte instrução `using`:

        using Microsoft.ServiceBus.Notifications;

5. Na classe **Programa**, adicione o seguinte método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{"data":{"message":"Hello Chrome from Azure Notification Hubs"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

   	Substitua o espaço reservado de "nome do hub" pelo nome do hub de notificação que aparece no portal, na guia **Hubs de Notificação**.  Além disso, substitua o espaço reservado de cadeias de conexão pela cadeia de conexão chamada **DefaultFullSharedAccessSignature** que você obteve na seção "Configurar seu hub de notificação". 

	>[AZURE.NOTE] Certifique-se de usar a cadeia de conexão com acesso **Completo**, não com acesso para **Escutar**.  A cadeia de acesso Escutar não tem permissões para enviar notificações.

5. Em seguida, adicione as seguintes linhas ao método **Principal**:

         SendNotificationAsync();
		 Console.ReadLine();

6. Verifique se o seu navegador Chrome está aberto.  Seu aplicativo Chrome não precisa ser aberto para isso.  Você deve ver a seguinte notificação pop-up na área de trabalho. 

   	![][13]

7. Você também pode ver todas as notificações usando a janela de notificações do Chrome acessível na barra de tarefas (no Windows) quando o Chrome estiver em execução. 

   	![][14]

## <a name="next-steps"> </a>Próximas etapas

Neste exemplo simples você transmite notificações para seu aplicativo Chrome. 
Saiba mais sobre Hubs de notificação na [Visão geral de Hubs de notificação].
Para direcionar usuários específicos, consulte o tutorial [Notificar usuários de Hubs de notificação do Azure], mas se deseja segmentar os usuários por grupos de interesse é possível ler [Ultimas notícias de Hubs de notificação do Azure]. 

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
[Exemplo de Hub de notificação de aplicativo Chrome]: http://google.com
[Console do Google Cloud]: http://cloud.google.com/console
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Visão geral de Hubs de Notificação do Azure]: http://msdn.microsoft.com/library/jj927170.aspx
[Visão geral de aplicativos Chrome]: https://developer.chrome.com/apps/about_apps
[Exemplo do GCM de aplicativo Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Aplicativos Web instaláveis]: https://developers.google.com/chrome/apps/docs/
[Aplicativos Chrome em Dispositivos móveis]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Criar registro da API REST NH]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[biblioteca de crypto-js]: http://code.google.com/p/crypto-js/
[GCM com aplicativos Chrome]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging para Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Notificação de Usuários nos Hubs de Notificação do Azure]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Últimas notícias de Hubs de notificação do Azure]: notification-hubs-windows-store-dotnet-send-breaking-news.md

<!--HONumber=49-->