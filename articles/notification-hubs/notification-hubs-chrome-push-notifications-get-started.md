---
title: Notificações por push para aplicativos Chrome com os Hubs de Notificação do Azure | Microsoft Docs
description: Saiba como usar os Hubs de Notificação do Azure para enviar notificações por push para um Aplicativo Chrome.
services: notification-hubs
keywords: notificações por push móveis,notificações por push,notificações por push,notificações por push do chrome
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 75d4ff59-d04a-455f-bd44-0130a68e641f
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-chrome
ms.devlang: JavaScript
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 7bdc692104194bff4a25e6974ba72971af543cbf
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028988"
---
# <a name="tutorial-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Tutorial: Notificações por push para aplicativos Chrome com os Hubs de Notificação do Azure
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Este tutorial orienta você pela criação de um hub de notificação e pelo envio de notificações por push para um exemplo de aplicativo do Google Chrome usando [Google Cloud Messaging (GCM)](https://developers.google.com/cloud-messaging/). O aplicativo do Chrome é executado no contexto de um navegador Google Chrome e registra com o hub de notificação. 

> [!NOTE]
> As notificações por push do aplicativo Chrome não são notificações genéricas do navegador - elas são específicas do modelo de extensibilidade do navegador (veja [Visão geral de aplicativos Chrome] para obter detalhes). Além do navegador da área de trabalho, os aplicativos Chrome são executados em celulares (Android e iOS) por meio do Apache Cordova. Para saber mais, confira [Aplicativos do Chrome em Dispositivos móveis].

Neste tutorial, você deve executar as seguintes etapas:

> [!div class="checklist"]
> * [Habilitar as mensagens em nuvem do Google](#register)
> * [Configurar seu Hub de Notificação](#configure-hub)
> * [Conectar seu Aplicativo Chrome ao hub de notificação](#connect-app)
> * [Enviar uma notificação por push para seu Aplicativo Chrome](#send)

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a id="register"></a>Habilitar as mensagens em nuvem do Google
1. Navegue até o site [Console do Google Cloud](https://console.cloud.google.com/cloud-resource-manager) e entre com suas credenciais de conta do Google
2. Selecione **Criar projeto** na barra de ferramentas. 

    ![Botão Criar Projeto](media/notification-hubs-chrome-get-started/create-project-button.png)
1. Forneça um **Nome de Projeto** apropriado e clique no botão **Criar**.
2. Selecione o ícone de notificações (Sino) na barra de ferramentas e selecione a mensagem **Criar Projeto**. 

    ![Notificação de criação de projeto](media/notification-hubs-chrome-get-started/project-creation-notification.png)
1. Anote o **Número do Projeto** na página **Projetos** do projeto que você criou. Você usa o número de projeto como a **ID de Remetente do GCM** no Aplicativo Chrome para se registrar no GCM.
   
    ![Console do Google Cloud - número do projeto](media/notification-hubs-chrome-get-started/gcm-project-number.png)
3. No painel, selecione **Ir para a visão geral de APIs**. 

    ![Vá para o botão de visões gerais de API](media/notification-hubs-chrome-get-started/go-to-apis-overview-button.png)
1. Na página de API e Serviços, selecione **Habilitar APIs e Serviços**. 

    ![Habilitar APIs e Serviços](media/notification-hubs-chrome-get-started/enable-apis-and-services.png)
1. Pesquise a lista com a palavra-chave **mensagens em nuvem**. Selecione **Google Cloud Messaging** na lista filtrada. 

    ![API do Google Cloud Messaging](media/notification-hubs-chrome-get-started/google-cloud-messaging-api.png)
1. Na página **Google Cloud Messaging**, selecione **Habilitar**.

    ![Habilitar GCM](media/notification-hubs-chrome-get-started/enable-gcm.png)
1. Na página **API e Serviços**, alterne para a guia **Credenciais**. Selecione **Criar credenciais**e, em seguida, selecione **Chave de API**. 

    ![Criar botão de chave de API](media/notification-hubs-chrome-get-started/create-api-key-button.png)
1. Na caixa de diálogo **Chave de API criada**, selecione o botão de cópia para copiar a chave para a área de transferência. Salve-a em algum lugar. Você usa esse valor para configurar o hub de notificação na próxima seção para habilitá-lo para enviar notificações por push para o GCM.

    ![Página de API](media/notification-hubs-chrome-get-started/api-created-page.png)
12. Selecione sua chave de API na lista **Chaves de API**. Na página de chave de API, selecione **Endereços IP (servidores web, tarefas cron, etc.)** e digite **0.0.0.0/0** para o endereço IP e clique em Salvar. 

    ![Habilitar endereços IP](media/notification-hubs-chrome-get-started/enable-ip-addresses.png)

## <a id="configure-hub"></a>Criar seu hub de notificação
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

6. Selecione **Google (GCM)** na categoria **CONFIGURAÇÕES DE NOTIFICAÇÃO**, insira a **Chave API** para o projeto do GCM e clique em **Salvar**.

      ![Hubs de Notificação do Azure - Google (GCM)](media/notification-hubs-chrome-get-started/configure-gcm-api-key.png)

## <a id="connect-app"></a>Conectar seu Aplicativo Chrome ao hub de notificação
Agora, o hub de notificação está configurado para funcionar com o GCM, e você tem as cadeias de conexão para registrar seu aplicativo para receber e enviar notificações por push.

### <a name="create-a-new-chrome-app"></a>Criar um novo Aplicativo Chrome
O exemplo a seguir é baseado no [Exemplo do GCM do Aplicativo Chrome] e usa a maneira recomendada para criar um Aplicativo Chrome. Esta seção destaca as etapas que são específicas para os Hubs de Notificação do Azure. 

> [!NOTE]
> Recomendamos que você baixe a fonte deste Aplicativo Chrome do [Exemplo de Hub de Notificação do Aplicativo Chrome]. 

O Aplicativo Chrome é criado por meio do JavaScript, e você pode usar um de seus editores de texto preferidos para criá-lo. A imagem a seguir mostra a aparência do Aplicativo Chrome:

![Aplicativo Google Chrome][15]

1. Crie uma pasta e chame-a de `ChromePushApp`. O nome é arbitrário - se você chamá-la de algo diferente, substitua o caminho nos segmentos de código necessários.
2. Baixe a [biblioteca crypto-js] na pasta criada na segunda etapa. Essa pasta de biblioteca contém duas subpastas: `components` e `rollups`.
3. Crie um arquivo do `manifest.json` . Todos os Aplicativos Chrome são apoiados por um arquivo de manifesto que contém os metadados do aplicativo e, mais importante, todas as permissões concedidas ao aplicativo quando o usuário o instala.
   
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
   
    Observe o elemento `permissions`, que especifica que esse Aplicativo Chrome poderá receber notificações por push do GCM. O aplicativo de exemplo também usa um arquivo de ícone, `gcm_128.png`, que você encontra na fonte reutilizada por meio do exemplo original do GCM. Você poderá substituí-lo por qualquer imagem que se ajuste a [critérios de ícone](https://developer.chrome.com/apps/manifest/icons).
4. Crie um arquivo chamado `background.js` com o código a seguir:
   
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
   
    Este arquivo abre o HTML da janela do Aplicativo Chrome (**register.html**) e também define o manipulador **messageReceived** para manipular a notificação por push recebida.
5. Crie um arquivo chamado `register.html`, que define a interface do usuário do Aplicativo Chrome. 
   
   > [!NOTE]
   > Este exemplo usa **CryptoJS v3.1.2**. Se você tiver baixado outra versão da biblioteca, verifique se você substituiu corretamente a versão no caminho do `src` .
   > 
   > 
   
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
6. Crie um arquivo chamado `register.js` com o código nesta etapa. Esse arquivo especifica o script por trás de `register.html`. Os Aplicativos Chrome não permitem a execução embutida, por isso é necessário criar um script de backup separado para a sua interface do usuário.
   
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
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
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
   
    O script tem os seguintes parâmetros principais:
   
   * **window.onload** define os eventos de clique de botão dos dois botões na interface do usuário. O primeiro manipulador de eventos de clique de botão realiza o registro com o GCM e o outro usa a ID de registro retornada após o registro com o GCM para fazer o registro nos Hubs de Notificação do Azure.
   * **updateLog** é a função que permite as informações de log do código. 
   * **registerWithGCM** é o primeiro manipulador de clique de botão, que faz a chamada `chrome.gcm.register` para o GCM para registrar essa instância do Aplicativo Chrome.
   * **registerCallback** é a função de retorno de chamada que é chamada quando a chamada de registro do GCM acima é retornada.
   * **registerWithNH** é o segundo manipulador de clique de botão, que faz o registro nos Hubs de Notificação. Obtém `hubName` e `connectionString` (que o usuário especificou) e cria a chamada da API REST de Registro de Hubs de Notificação.
   * **splitConnectionString** e **generateSaSToken** são os auxiliares que representam a implementação de JavaScript da criação de um token SaS que deve ser enviado em todas as chamadas da API REST. Para saber mais, veja [Conceitos comuns](http://msdn.microsoft.com/library/dn495627.aspx).
   * **sendNHRegistrationRequest** é a função que faz uma chamada HTTP REST para os Hubs de Notificação do Azure.
   * **registrationPayload** define a carga XML do registro. Para obter mais informações, veja [Criar um registro da API REST NH]. Atualizar a ID do registro com o valor recebido do GCM.
   * **client** é uma instância de **XMLHttpRequest** que o aplicativo usa para fazer a solicitação HTTP POST. Atualize o cabeçalho `Authorization` com `sasToken`. A conclusão bem-sucedida dessa chamada registra essa instância do Aplicativo Chrome com os Hubs de Notificação do Azure.

        A estrutura geral da pasta para este projeto deve ser semelhante a: ![Aplicativo do Google Chrome - Estrutura da Pasta][21]

### <a name="set-up-and-test-your-chrome-app"></a>Configurar e testar seu Aplicativo Chrome
1. Abra seu navegador Chrome. Abra as **extensões do Chrome** e habilite o **Modo de desenvolvedor**.
   
    ![Google Chrome - habilitar o modo de desenvolvedor][16]
2. Clique em **Carregar extensão descompactada** e navegue até a pasta na qual você criou os arquivos. Você também pode usar a **Ferramenta de desenvolvimento de extensões e Aplicativos Chrome**. Essa ferramenta é um Aplicativo Chrome por si só (instalada por meio do Chrome Web Store) e fornece recursos avançados de depuração para o desenvolvimento do seu Aplicativo Chrome.
   
    ![Google Chrome - carregar a extensão descompactada][17]
3. Se o Aplicativo Chrome for criado sem erros, você o verá sendo exibido.
   
    ![Google Chrome - exibição de aplicativo Chrome][18]
4. Insira o **Número do Projeto** obtido anteriormente do **Console do Google Cloud**, bem como a ID do remetente, e clique em **Registrar no GCM**. Você deve ver a mensagem **Registro no GCM concluído com êxito.**
   
    ![Google Chrome - personalização de aplicativo Chrome][19]
5. Insira o **Nome do Hub de Notificação** e a **DefaultListenSharedAccessSignature** obtidos anteriormente do portal e clique em **Registrar no Hub de Notificação do Azure**. Você deve ver a mensagem **Registro no Hub de Notificação concluído com êxito!** e os detalhes da resposta do registro, que contém a ID de Registro nos Hubs de Notificação do Azure.
   
    ![Google Chrome - especificar os detalhes do Hub de Notificação][20]  

## <a name="send"></a>Enviar uma notificação por push para seu Aplicativo Chrome
Para fins de teste, envie notificações por push do Chrome usando um aplicativo de console .NET. 

> [!NOTE]
> Você pode enviar notificações por push com os Hubs de Notificação de qualquer back-end por meio da <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a> pública. Confira o [portal de documentação](https://azure.microsoft.com/documentation/services/notification-hubs/) para obter mais exemplos de plataforma cruzada.
> 
> 

1. No Visual Studio, no menu **Arquivo**, selecione **Novo** e **Projeto**. No **Visual C#**, clique em **Windows** e em **Aplicativo do Console** e depois clique em **OK**.  Essa etapa cria um novo projeto de aplicativo de console.
2. No menu **Ferramentas**, clique em **Gerenciador de Pacotes NuGet** e, em seguida, em **Console do Gerenciador de Pacotes**. Você verá o Console do Gerenciador de Pacotes na janela inferior.
3. Na janela da console, execute o seguinte comando:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
   Uma referência ao SDK do Barramento de Serviço do Microsoft Azure com o <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacote NuGet WindowsAzure.ServiceBus é adicionada automaticamente ao projeto.</a>
4. Abra `Program.cs` e adicione a seguinte instrução `using`:
   
        using Microsoft.Azure.NotificationHubs;
5. Na classe `Program` , adicione o seguinte método:
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }
   
    Lembre-se de substituir o espaço reservado `<hub name>` pelo nome do hub de notificação que aparece no [portal](https://portal.azure.com), na página Hub de Notificação. Além disso, substitua o espaço reservado da cadeia de conexão pela cadeia de conexão chamada `DefaultFullSharedAccessSignature` que você obteve na seção de configuração do hub de notificação.
   
    > [!NOTE]
    > Verifique se você está usando a cadeia de conexão com acesso **Completo**, não com acesso para **Escutar**. A cadeia de conexão de acesso **Escutar** não concede permissões para enviar notificações por push.
6. Adicione as seguintes chamadas ao método `Main` :
   
         SendNotificationAsync();
         Console.ReadLine();
7. Verifique se o Chrome está em execução e execute o aplicativo de console.
8. Você deve ver a pop-up de notificação abaixo na área de trabalho.
   
    ![Google Chrome - notificação][13]
9. Você também pode ver todas as notificações usando a janela de Notificações do Chrome na barra de tarefas (no Windows) quando o Chrome estiver em execução.
   
    ![Google Chrome - lista de notificações](./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png)

> [!NOTE]
> Você não precisa executar o Aplicativo Chrome ou abrir o navegador (embora o próprio navegador Chrome precise estar em execução). Você também pode obter uma exibição consolidada de todas as suas notificações na janela de notificações do Chrome.
> 
> 

## <a name="next-steps"> </a>Próximas etapas
Neste tutorial, você envia notificações de transmissão para todos os clientes registrados com o back-end. Para saber como enviar notificações por push a dispositivos específicos, avance ao seguinte tutorial: 

> [!div class="nextstepaction"]
>[Notificações por push para especificar dispositivos](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->
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
[Exemplo de Hub de Notificação do Aplicativo Chrome]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Notification Hubs Overview]: notification-hubs-push-notification-overview.md
[Visão geral de aplicativos Chrome]: https://developer.chrome.com/apps/about_apps
[Exemplo do GCM do Aplicativo Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Aplicativos do Chrome em Dispositivos móveis]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Criar um registro da API REST NH]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[biblioteca crypto-js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure Notification Hubs Notify Users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Azure Notification Hubs breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
