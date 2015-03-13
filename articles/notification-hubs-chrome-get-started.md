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
	ms.tgt_pltfrm="" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="01/10/2015" 
	ms.author="piyushjo"/>
# Introdução aos Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/pt-br/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/pt-br/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/pt-br/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a><a href="/pt-br/documentation/articles/notification-hubs-chrome-get-started/" title="Chrome" class="current">Chrome</a></div>

Este tópico mostra como usar os Hubs de notificação do Azure para enviar notificações por push para um aplicativo Chrome.

Um dos principais benefícios do uso de notificações do aplicativo de Chrome é que as notificações aparecem dentro do contexto do navegador Google Chrome e você não precisa ter o aplicativo Chrome executando ou aberto no navegador (embora o próprio navegador Chrome deva estar em execução). Você também pode obter uma exibição consolidada de todas as suas notificações na janela de notificações do Chrome. 

>[AZURE.NOTE] Essa não é uma notificação por push de navegador genérica e é específica para aplicativos do Chrome - consulte [Visão geral de aplicativos do Chrome] para obter detalhes. Os aplicativos de Chrome eram anteriormente conhecidos como "Aplicativos empacotados" e são diferentes dos "aplicativos hospedados", mais simples. Consulte [Aplicativos Web instaláveis] para saber a diferença. Os aplicativos do Chrome também podem ser executados em Dispositivos móveis (Android e iOS) usando o Apache Cordova. Consulte [Aplicativos do Chrome em Dispositivos móveis] para obter mais informações. 

Neste tutorial, criaremos um aplicativo do Chrome que recebe notificações por push usando o Google Cloud Messaging (GCM). Ao concluir, você poderá transmitir notificações push para todos os usuários do Chrome que instalaram esse aplicativo Chrome. 

O tutorial explica as seguintes etapas básicas para habilitar as notificações por push:

* [Habilitar o Google Cloud Messaging (GCM)](#register)
* [Configurar seu Hub de notificação](#configure-hub)
* [Conectar seu aplicativo Chrome ao Hub de notificação](#connect-app)
* [Enviar notificação para seu aplicativo Chrome](#send)
* [Próximas etapas](#next-steps)

Este tutorial demonstra o simples cenário de transmissão usando Hubs de Notificação. A configuração do GCM e do Hub de notificação do Azure é idêntica à configuração para o Android desde que o [Google Cloud Messaging para Chrome] foi substituído e o mesmo GCM agora oferece suporte a dispositivos com Android e instâncias do Chrome. 

Certifique-se de seguir os tutoriais em Próximas etapas para ver como usar hubs de notificação para atender usuários e grupos de dispositivos específicos. 

>[AZURE.NOTE] Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http://azure.microsoft.com/ documentation/articles/notification-hubs-chrome-get-started/" target="_blank">Avaliação Gratuita do Azure</a>.

##<a id="register"></a>Habilitar o Google Cloud Messaging (GCM)

1. Navegue até o site [Google Cloud Console], entre com as credenciais de sua conta do Google e clique no botão **Criar Projeto**. Forneça um **nome de projeto** apropriado e clique no botão **Criar**. 

   	![][1]

2. Anote o **número do projeto** na página de projetos para o projeto que você acabou de criar. Você o usará como o **ID de remetente do GCM** no aplicativo Chrome para registrar com o GCM.

   	![][2]

3. No painel esquerdo, clique em **APIs & auth**, role para baixo e clique no botão de alternância para ativar o **Google Cloud Messaging para Android**. Você não precisa habilitar *Google Cloud Messaging for Chrome*. Também é possível que apenas o nome possa ser alterado para *Google Cloud Messaging* no futuro. 

   	![][3]

4. No painel esquerdo, clique em **Credenciais** -> **Criar Nova chave** -> **Chave do servidor** -> **Criar**

   	![][4]

5. Anote **Chave da API** do servidor. Você irá configurar isso em seu Hub de notificação antes de habilitá-lo para poder enviar notificações por push para o GCM. 

   	![][5]

##<a id="configure-hub"></a>Configurar seu Hub de notificação

1. Faça logon no **[Portal de Gerenciamento do Azure]** e clique em **+ NOVO** na parte inferior da tela.

2. Clique em **Serviços de aplicativo** -> **Barramento de Serviço** -> **Hub de notificação** -> **Criação rápida**. Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um novo Hub de Notificação**.

   	![][6]

4. Vá para o Hub de notificação que você acabou de criar. Clique no namespace que hospeda seu hub de notificação (geralmente ***nome do hub de notificação*-ns**).   

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
O exemplo a seguir é baseado no [Exemplo do GCM de aplicativo Chrome] e usa a maneira recomendada de criar um aplicativo Chrome. Nas seções a seguir, vamos destacar as etapas relacionadas ao Hub de notificação do Azure. É recomendável que você baixe a fonte para este aplicativo Chrome do [Exemplo de Hub de notificação de aplicativo Chrome].

O aplicativo Chrome é criado usando JavaScript e você pode usar qualquer de seu editor preferido do word para criar isso. 

1. Abaixo, esta é a aparência desse aplicativo Chrome. 

   	![][15]

2. Crie uma pasta e nomeie-a **ChromePushApp**. Você pode nomeá-la do que você quiser. 

3. Baixe o *cryto-js library* da [biblioteca crypto-js] nessa pasta. Essa pasta de biblioteca contém duas subpastas - *components* e *rollups*. 

4. Crie um arquivo manifest.json. Todos os aplicativos Chrome são apoiados por um arquivo de manifesto que descreve os metadados de aplicativo e, em particular, as permissões disponíveis para o aplicativo.

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
	
	Observe o *permissions* elemento que especifica que esse aplicativo Chrome poderá receber notificações por push do GCM. Ele também deve especificar o URI do Hub de notificação do Azure onde o aplicativo Chrome fará uma chamada REST para registrar.
	Isso usa um arquivo de ícone gcm_128.png que você encontrará na fonte reutilizada a partir do exemplo original do GCM. Você pode usar qualquer imagem que desejar. 
 
5. Crie um arquivo chamado background.js com o código a seguir:

		// Retorna uma nova ID de notificação usada na notificação.
		function getNotificationId() {
		  var id = Math.floor(Math.random() * 9007199254740992) + 1;
		  return id.toString();
		}
		
		function messageReceived(message) {
		  // Uma mensagem é um objeto com uma propriedade de dados que
		  // consiste em pares de valores chave.
		
		  // Concatene todos os pares de valores chave para formar uma cadeia de caracteres de exibição.
		  var messageString = "";
		  for (var key in message.data) {
		    if (messageString != "")
		      messageString += ", "
		    messageString += key + ":" + message.data[key];
		  }
		  console.log("Message received: " + messageString);
		
		  // Exibe uma pop-up de notificação para mostrar a mensagem do GCM.
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
		
		// Configura um ouvinte de evento de mensagem do GCM.
		chrome.gcm.onMessage.addListener(messageReceived);
		
		// Configura os ouvintes para acionar o primeiro registro de tempo.
		chrome.runtime.onInstalled.addListener(firstTimeRegistration);
		chrome.runtime.onStartup.addListener(firstTimeRegistration);

	Este é o arquivo que surge o html da janela do aplicativo Chrome (* register.html*) e também define o manipulador *messageReceived* para tratar a notificação por push recebida. 

6. Crie um arquivo chamado *register.html* que define a interface do usuário do aplicativo Chrome. Observe que este exemplo usa *CryptoJS v3.1.2*. Se você baixou qualquer outra versão, corrija o caminho de origem do script. 

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

7. Crie um arquivo chamado *register.js* com o código a seguir. Esse arquivo especifica o script por trás de *register.html*. Os aplicativos Chrome não permitem a execução embutida de forma que é necessário criar um script de backup separado para sua interface do usuário. 

		var registrationId = "";
		var hubName        = "", connectionString = "";
		var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";
		
		window.onload = function() { 
		   document.getElementById("registerWithGCM").onclick = registerWithGCM;  
		   document.getElementById("registerWithNH").onclick = registerWithNH; 
		   updateLog ("Você não registrou ainda. Faça o ID de remetente do provedor e registre com o GCM e, em seguida, com os Hubs de notificação."); 
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
		
		  // Impede o botão registrar seja clicado novamente antes de concluir o registro
		  document.getElementById("registerWithGCM").disabled = true;
		}
		
		function registerCallback(regId) {
		  registrationId = regId;
		  document.getElementById("registerWithGCM").disabled = false;
		
		  if (chrome.runtime.lastError) {
		    // Quando o registro falhar, manipular o erro e tentar novamente o
		    // registro posterior.
		    updateLog("Registration failed: " + chrome.runtime.lastError.message);
		    return;
		  }
		
		  updateLog("Registration with GCM succeeded.");
		  document.getElementById("registerWithNH").disabled = false;
		
		  // Marca que o registro foi feito pela primeira vez.
		  chrome.storage.local.set({registered: true});
		}
		
		function registerWithNH() {
		  hubName = document.getElementById("hubName").value.trim();
		  connectionString = document.getElementById("connectionString").value.trim();
		  splitConnectionString();
		  generateSaSToken();
		  sendNHRegistrationRequest();
		}
		
		// De http://msdn.microsoft.com/library/dn495627.aspx 
		function splitConnectionString()
		{
		  var parts = connectionString.split(';');
		  if (parts.length != 3)
		  throw "Erro ao analisar cadeia de conexão";
		
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
		  var expiresInMins = 10; // expira em 10 minutos
		
		  // Define a validade em segundos
		  var expireOnDate = new Date();
		  expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
		  var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
		    .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
		    .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
		    .getUTCSeconds()) / 1000;
		  var tosign = targetUri + '\n' + expires;
		
		  // usando CryptoJS
		  var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
		  var base64signature = signature.toString(CryptoJS.enc.Base64);
		  var base64UriEncoded = encodeURIComponent(base64signature);
		
		  // construir a cadeia de caracteres de autorização
		  sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
		                  + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
		}
		
		function sendNHRegistrationRequest()
		{
		  var registrationPayload = 
		  "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
		  "<entry xmlns=\"http://www.w3.org/2005/Atom\">" + 
		      "<content type=\"application/xml\">" + 
		          "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
		              "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
		          "</GcmRegistrationDescription>" +
		      "</content>" +
		  "</entry>";
		
		  // Atualizar a carga com a id de registro obtida anteriormente
		  registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);
		
		  var url = originalUri + "/registrations/?api-version=2014-09";
		  var client = new XMLHttpRequest();
		
		  client.onload = function () {
		    if (client.readyState == 4) {
		      if (client.status == 200) {
		        updateLog ("Registro do Hub de notificação bem-sucedido!");
		        updateLog(client.responseText);
		      } else {
		        updateLog ("Falha no registro do Hub de notificação!");
		        updateLog("HTTP Status: " + client.status + " : " + client.statusText);
		        updateLog ("resposta HTTP: " + "\n" + client.responseText);
		      }
		    }
		  };
		
		  client.onerror = function () {
		        updateLog ("ERRO - Falha no registro do Hub de notificação!");
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
	- *window.onload* define os eventos do clique do botão dos dois botões na interface do usuário - um registra com o GCM e o outro usa a id de registro retornada após o registro com o GCM para registrar com Hubs de notificação do Azure. 
	- A função *updatelog* define uma função de log simples. 
	- *registerWithGCM * é o primeiro clique no botão do manipulador que faz a chamada *chrome.gcm.register* para o GCM para registrar essa instância do aplicativo Chrome. 
	- *registerCallback* é a função de retorno de chamada que é chamada quando a chamada de registro GCM acima retorna. 
	- *registerWithNH* é o segundo clique do botão manipulador que faz o registro com os Hubs de notificação. Obtém o *hubName* e *connectionString* que o usuário especificou e cria a chamada da API REST de Registro de Hubs de notificação. 
	- *splitConnectionString* e *generateSaSToken* são a implementação de Javascript da criação de um token SaS que deve ser enviado em todas as chamadas da API REST. Mais informações sobre isto aqui - http://msdn.microsoft.com/pt-br/library/dn495627.aspx 
	- *sendNHRegistrationRequest* é a função que faz com que uma chamada REST HTTP. 
	- *registrationPayload* define a carga de xml de registro. Mais informações sobre isso aqui - [Criar o registro da API REST NH]. Podemos atualizar a id do registro com o que recebemos do GCM. 
	- *client* é uma instância de *XMLHttpRequest* usada para fazer a solicitação HTTP POST. Observe que podemos atualizar o cabeçalho  *Authorization* com o sasToken. A conclusão bem-sucedida dessa chamada registrará essa instância do aplicativo Chrome com os Hubs de notificação do Azure. 
	

8. Você verá o seguinte modo de exibição para a pasta no final deste:
   	![][21]

###Configurar e testar seu aplicativo Chrome

1. Abra seu navegador Chrome. Abra as **Extensões do Chrome** e habilite o **Modo de desenvolvedor**. 

   	![][16]

2. Clique em **Carregar a extensão descompactada** e navegue até a pasta onde você criou os arquivos. Você também pode usar o **aplicativo Chrome e a Ferramenta de desenvolvimento de extensões** que é um aplicativo Chrome em si (e será necessário instalá-lo da Chrome Web Store) e fornece recursos avançados de depuração para o desenvolvimento de aplicativos Chrome. 

   	![][17]

3. Se o aplicativo Chrome for criado sem erros, em seguida, você verá seu aplicativo Chrome ser exibido. 

   	![][18]

4. Insira o **Número do projeto** obtido anteriormente no **Google Cloud Console**, assim como o ID de remetente e clique em **Registrar com o GCM**. Você deve ver uma mensagem *Registration with GCM succeeded.*

   	![][19]

5. Insira seu **Nome do Hub de notificação** e **DefaultListenSharedAccessSignature** obtido anteriormente no Portal de gerenciamento do Azure e clique em **Registrar com o Hub de notificação do Azure**. Você deve ver uma mensagem *Notification Hub Registration succesful!* e os detalhes da resposta do registro que contém o registro de Hubs de notificação do Azure ID. 

   	![][20]  

##<a name="send"></a>Enviar notificação para seu aplicativo Chrome

Neste tutorial você envia notificações com um aplicativo do console .NET embora você possa enviar notificações usando os Hubs de notificação de qualquer back-end usando a <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>. 

Para um exemplo de como enviar notificações de um back-end do Serviços Móveis do Azure integrado com Hubs de notificação, consulte **Introdução às notificações push em Serviços móveis** ([back-end do .NET](/pt-br/documentation/articles/mobile-services-javascript-backend-android-get-started-push/) | [Back-end do JavaScript](/pt-br/documentation/articles/mobile-services-javascript-backend-android-get-started-push/)).  
Para um exemplo de como enviar notificações usando o REST APIs, consulte **Como usar Hubs de Notificação a partir de Java/PHP/Phyton** ([Java](/pt-br/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/pt-br/documentation/articles/notification-hubs-php-backend-how-to/) | [Python](/pt-br/documentation/articles/notification-hubs-python-backend-how-to/)).

1. No Visual Studio, no menu **Arquivo** selecione **Novo** e depois **Projeto...**, em seguida, em **Visual C#** clique em**Windows** e**Aplicativo do console** e clique em**OK**.  Isso cria um novo projeto de aplicativo de console.

2. No menu **Ferramentas**, clique em **Gerenciador de Pacotes de Biblioteca** e, em seguida, selecione **Console do Gerenciador de Pacotes**. Isso exibe a Console do Gerenciador de Pacotes.

3. Na janela da console, execute o seguinte comando:

        Install-Package WindowsAzure.ServiceBus
    
   	Isso adiciona uma referência ao SDK do Barramento de Serviço do Azure com o <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">pacote NuGet do WindowsAzure.ServiceBus</a>. 

4. Abra o arquivo Program.cs e adicione a seguinte instrução `using`:

        usando o Microsoft.ServiceBus.Notifications;

5. Na classe **Programa**, adicione o seguinte método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

   	Substitua o espaço reservado de "nome do hub" pelo nome do hub de notificação que aparece no portal, na guia **Hubs de Notificação**. Além disso, substitua o espaço reservado de cadeias de conexão pela cadeia de conexão chamada **DefaultFullSharedAccessSignature** que você obteve na seção "Configurar seu hub de notificação". 

	>[AZURE.NOTE] Certifique-se de usar a cadeia de conexão com acesso **Completo**, não com acesso para **Escutar**. A cadeia de acesso Escutar não tem permissões para enviar notificações.

5. Em seguida, adicione as seguintes linhas no método **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

6. Verifique se o seu navegador Chrome está aberto. Seu aplicativo Chrome não precisa ser aberto para isso. Você deve ver a seguinte notificação pop-up na área de trabalho. 

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
[biblioteca de criptografia js]: http://code.google.com/p/crypto-js/
[GCM com aplicativos Chrome]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging para Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Notificação de Usuários nos Hubs de Notificação do Azure]: http://azure.microsoft.com/ documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/
[Últimas notícias de Hubs de notificação do Azure]: http://azure.microsoft.com/ documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/

<!--HONumber=45--> 
