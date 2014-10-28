<properties linkid="develop-nodejs-how-to-guides-service-bus-notification-hubs" urlDisplayName="Notification Hubs" pageTitle="Notification Hubs - Node.js Dev Center" metaKeywords="" description="Learn how to use Notification Hubs to send push notifications. Code samples are written for Node.js applications." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Notification Hubs" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Como usar Hubs de Notificação

Este guia mostrará como usar os Hubs de Notificação
de aplicativos Node.js. Os cenários abordados incluem **o envio de notificações para aplicativos Android, iOS, Windows Phone e Windows Store**. Para obter mais informações sobre Hubs de Notificação, consulte a seção [Próximas etapas][Próximas etapas].

## Sumário

-   [O que são Hubs de Notificação?][O que são Hubs de Notificação?]
-   [Criar um aplicativo Node.js][Criar um aplicativo Node.js]
-   [Configurar o aplicativo para usar o Hub de notificação][Configurar o aplicativo para usar o Hub de notificação]
-   [Como: Enviar notificações][Como: Enviar notificações]
-   [Próximas etapas][Próximas etapas]

## <span id="hub"></span></a>O que são Hubs de Notificação?

Os Hubs de Notificação do Azure fornecem uma infraestrutura de várias plataformas dimensionável para o envio de notificações a dispositivos móveis. Para obter mais informações, consulte [Hubs de Notificação do Azure][Hubs de Notificação do Azure].

## <span id="create"></span></a>Criar um aplicativo do Node.js

Criar um aplicativo Node.js em branco. Para obter instruções sobre como criar um aplicativo Node.js, consulte [Criar e implantar um aplicativo Node.js para um Site do Azure][Criar e implantar um aplicativo Node.js para um Site do Azure], [Serviço de Nuvem do Node.js][Serviço de Nuvem do Node.js] (Usando o PowerShell do Windows) ou [Site com o WebMatrix][Site com o WebMatrix].

## <span id="config"></span></a>Configurar seu aplicativo para usar o hub de notificação

Para usar o Hub de notificação do Azure, você precisa baixar e usar o
pacote do Azure Node.js. Isso inclui um conjunto de bibliotecas de conveniência que
se comunicam com os serviços REST.

### Usar o NPM (gerenciador de pacotes de nós) para obter o pacote

1.  Use uma interface de linha de comando, como **PowerShell** (Windows,) **Terminal** (Mac) ou **Bash** (Unix), e vá até a pasta onde você criou a amostra do aplicativo.

2.  Digite **npm install azure** na janela de comando, que deve
    resultar na seguinte saída:

        azure@0.7.0 node_modules\azure
        |-- dateformat@1.0.2-1.2.3
        |-- xmlbuilder@0.4.2
        |-- node-uuid@1.2.0
        |-- mime@1.2.9
        |-- underscore@1.4.4
        |-- validator@0.4.28
        |-- tunnel@0.0.2
        |-- wns@0.5.3
        |-- xml2js@0.2.6 (sax@0.4.2)
        |-- request@2.16.6 (forever-agent@0.2.0, aws-sign@0.2.0, tunnel-agent@0.2.0, oauth-sign@0.2.0, json-stringify-safe@3.0.0, cookie-jar@0.2.0, node-uuid@1.4.0, qs@0.5.5, hawk@0.10.2, form-data@0.0.7)

3.  Você pode executar o comando **ls** ou **dir** manualmente para verificar se uma pasta
    **node\_modules** foi criada. Dentro dessa pasta, você encontrará o pacote
    **Azure**, que contém as bibliotecas necessárias para acessar
    o hub de notificação.

### Importar o módulo

Usando um editor de texto, adicione o seguinte à parte superior
do arquivo **server.js** do aplicativo:

    var azure = require('azure');

### Instalar uma conexão do hub de notificação do Azure

O objeto **NotificationHubService** permite trabalhar com Hubs de Notificação. O
código a seguir cria um objeto **NotificationHubService** para o Hub de Notificação chamado **hubname**. Adicione-o próximo à
parte superior do arquivo **server.js**, após a instrução de importação do
módulo azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

O valor da conexão **connectionstring** pode ser obtido no Portal de Gerenciamento do Azure executando as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, selecione **Barramento de Serviço** e, em seguida, selecione o namespace que contém o Hub de Notificação.

2.  Selecione **HUBS DE NOTIFICAÇÃO**e, em seguida, selecione o hub que você deseja usar.

3.  Selecione **Exibir Cadeia de Conexão** na seção **visão rápida** e copie o valor da cadeia de conexão.

<div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Voc&ecirc; tamb&eacute;m pode recuperar a cadeia de conex&atilde;o usando o cmdlet <b>Get-AzureSbNamespace</b> fornecido pelo PowerShell do Azure ou o comando <b>azure sb namespace show</b> com as Ferramentas de Linha de Comando do Azure.</p>

</div>

## <span id="send"></span></a> Como enviar notificações

O objeto **NotificationHubService** expõe as seguintes instâncias de objeto para o envio de notificações a dispositivos e aplicativos específicos:

-   **Android** - use o objeto **GcmService**, que está disponível em **notificationHubService.gcm**
-   **iOS** - use o objeto **ApnsService**, que está disponível em **notificationHubService.apns**
-   **Windows Phone** - use o objeto **MpnsService**, que está disponível em **notificationHubService.mpns**
-   **Aplicativos da Windows Store** - use o objeto **WnsService**, que está disponível em **notificationHubService.wns**

### Como enviar notificações de aplicativos Android

O objeto **GcmService** fornece um método **send** que pode ser usado para enviar notificações aos aplicativos Android. O método **send** aceita os seguintes parâmetros:

-   Tags - o identificador da marca. Se nenhuma marca for fornecida, a notificação será enviada a todos os clientes
-   Payload - o JSON da mensagem ou a carga de cadeia de caracteres
-   Callback - a função de retorno de chamada

Para obter mais informações sobre o formato de payload, consulte a seção Payload de [Implementando o servidor GCM (a página pode estar em inglês)][Implementando o servidor GCM (a página pode estar em inglês)].

O código a seguir usa a instância **GcmService** exposta pelo **NotificationHubService** para enviar uma mensagem a todos os clientes.

    var payload = {
      data: {
        msg: 'Hello!'
      }
    };
    notificationHubService.gcm.send(null, payload, function(error){
      if(!error){
        //notification sent
      }
    });

### Como enviar notificações de aplicativos iOS

O objeto **ApnsService** fornece um método **send** que pode ser usado para enviar notificações aos aplicativos iOS. O método **send** aceita os seguintes parâmetros:

-   Tags - o identificador da marca. Se nenhuma marca for fornecida, a notificação será enviada a todos os clientes
-   Payload - o JSON da mensagem ou a carga de cadeia de caracteres
-   Callback - a função de retorno de chamada

Para obter mais informações sobre o formato de payload, consulte a seção Carga de notificação do [Guia de Programação de Notificações Locais e por Push (a página pode estar em inglês)][Guia de Programação de Notificações Locais e por Push (a página pode estar em inglês)].

O código a seguir usa a instância **ApnsService** exposta por **NotificationHubService** para enviar uma mensagem de alerta a todos os clientes:

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
        // notification sent
      }
    });

### Como enviar notificações do Windows Phone

O objeto **MpnsService** fornece um método **send** que pode ser usado para enviar notificações aos aplicativos do Windows Phone. O método **send** aceita os seguintes parâmetros:

-   Tags - o identificador da marca. Se nenhuma marca for fornecida, a notificação será enviada a todos os clientes
-   Payload - carga XML da mensagem
-   TargetName - 'toast' para notificações do sistema. 'token' para notificações de bloco.
-   NotificationClass - a prioridade da notificação. Consulte a seção Elementos de cabeçalhos HTTP de [Notificações por push de um servidor (a página pode estar em inglês)][Notificações por push de um servidor (a página pode estar em inglês)] para obter os valores válidos.
-   Options - cabeçalhos de solicitação opcional
-   Callback - a função de retorno de chamada

Para obter uma lista de TargetName válido, NotificationClass e opções de cabeçalho, consulte [Notificações por push de um servidor (a página pode estar em inglês)][Notificações por push de um servidor (a página pode estar em inglês)].

O código a seguir usa a instância **MpnsService** exposta pelo **NotificationHubService** para enviar um alerta de notificação do sistema:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### Como enviar notificações de aplicativos da Windows Store

O objeto **WnsService** fornece um método **send** que pode ser usado para enviar notificações aos aplicativos da Windows Store. O método **send** aceita os seguintes parâmetros:

-   Tags - o identificador da marca. Se nenhuma marca for fornecida, a notificação será enviada a todos os clientes
-   Payload - a carga da mensagem XML
-   Type - o tipo de notificação
-   Options - cabeçalhos de solicitação opcional
-   Callback - a função de retorno de chamada

Para obter uma lista de tipos e cabeçalhos de solicitação válidos, consulte [Solicitação de serviço e cabeçalhos de resposta do serviço de notificação por push (a página pode estar em inglês)][Solicitação de serviço e cabeçalhos de resposta do serviço de notificação por push (a página pode estar em inglês)].

O código a seguir usa a instância **WnsService** exposta pelo **NotificationHubService** para enviar um alerta de notificação do sistema:

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
        // notification sent
      }
    });

## <span id="next"></span></a>Próximas etapas

Agora que você já sabe as noções básicas do uso de hubs de notificação, siga estes
links para saber mais.

-   Consulte a referência de MSDN: [Hubs de Notificação do Azure][Hubs de Notificação do Azure].
-   Visite o repositório [SDK do Azure para o nó][SDK do Azure para o nó] no GitHub.

  [Próximas etapas]: #next
  [O que são Hubs de Notificação?]: #hub
  [Criar um aplicativo Node.js]: #create
  [Configurar o aplicativo para usar o Hub de notificação]: #config
  [Como: Enviar notificações]: #send
  [Hubs de Notificação do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj927170.aspx
  [Criar e implantar um aplicativo Node.js para um Site do Azure]: /pt-br/develop/nodejs/tutorials/create-a-website-(mac)/
  [Serviço de Nuvem do Node.js]: /pt-br/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Site com o WebMatrix]: /pt-br/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Implementando o servidor GCM (a página pode estar em inglês)]: http://developer.android.com/google/gcm/server.html#payload
  [Guia de Programação de Notificações Locais e por Push (a página pode estar em inglês)]: http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html
  [Notificações por push de um servidor (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/hh221551.aspx
  [Solicitação de serviço e cabeçalhos de resposta do serviço de notificação por push (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windows/apps/hh465435.aspx
  [SDK do Azure para o nó]: https://github.com/WindowsAzure/azure-sdk-for-node
