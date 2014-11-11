<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Node.js) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Node.js" description="Learn how to use Service Bus queues in Azure. Code samples written in Node.js." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Service Bus Queues" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Como usar as filas do Service Bus

Este guia mostrará como usar as filas do Service Bus. As amostras são
escritas em JavaScript e usam o módulo Node.js do Azure. Os cenários
abrangidos incluem **criar filas, enviar e receber mensagens** e
**excluir filas**. Para obter mais informações sobre filas, consulte a seção [Próximas
etapas][Próximas
etapas].

## Sumário

-   [O que são as filas do Service Bus?][O que são as filas do Service Bus?]
-   [Criar um namespace de serviço][Criar um namespace de serviço]
-   [Obter as credenciais de gerenciamento padrão do namespace][Obter as credenciais de gerenciamento padrão do namespace]
-   [Criar um aplicativo Node.js][Criar um aplicativo Node.js]
-   [Configurar seu aplicativo para usar o Service Bus][Configurar seu aplicativo para usar o Service Bus]
-   [Como: Criar uma fila][Como: Criar uma fila]
-   [Como: Enviar mensagens para uma fila][Como: Enviar mensagens para uma fila]
-   [Como: Receber mensagens de uma fila][Como: Receber mensagens de uma fila]
-   [Como: Tratar falhas do aplicativo e mensagens ilegíveis][Como: Tratar falhas do aplicativo e mensagens ilegíveis]
-   [Próximas etapas][Próximas
    etapas]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="create-app"> </a>Criar um aplicativo do Node.js

Criar um aplicativo Node.js em branco. Para obter instruções sobre como criar um aplicativo Node.js, consulte [Criar e implantar um aplicativo Node.js em um site do Azure][Criar e implantar um aplicativo Node.js em um site do Azure], [Serviço de Nuvem do Node.js][Serviço de Nuvem do Node.js] (usando o Windows PowerShell) ou [Site com o WebMatrix][Site com o WebMatrix].

## <a name="configure-app"> </a>Configurar seu aplicativo para usar o Service Bus

Para usar o Service Bus do Azure, você precisa baixar e usar o
pacote do Azure Node.js. Isso inclui um conjunto de bibliotecas de conveniência que
se comunicam com os serviços REST do Service Bus.

### Usar o NPM (gerenciador de pacotes de nós) para obter o pacote

1.  Use a janela de comando **Windows PowerShell para Node.js** para
    acessar a pasta **c:\\\\node\\\\sbqueues\\\\WebRole1** onde você
    criou a amostra do aplicativo.

2.  Digite **npm install azure** na janela de comando, que deve
    resultar em uma saída semelhante à seguinte:

        azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Você pode executar o comando **ls** manualmente para verificar se uma
    pasta **node\_modules** foi criada. Dentro dessa pasta, você encontrará o pacote
    **Azure**, que contém as bibliotecas necessárias para acessar
    as filas do Service Bus.

### Importar o módulo

Usando um bloco de notas, adicione o seguinte à parte superior
do arquivo **server.js** do aplicativo:

    var azure = require('azure');

### Configurar uma conexão do Service Bus do Azure

O módulo azure realizará a leitura das variáveis de ambiente AZURE\_SERVICEBUS\_NAMESPACE and AZURE\_SERVICEBUS\_ACCESS\_KEY para obter as informações necessárias para se conectar ao Service Bus do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta chamando **createServiceBusService**.

Para obter um exemplo de como definir as variáveis de ambiente em um arquivo de configuração para um Serviço de Nuvem do Azure, consulte [Serviço de Nuvem do Node.js com Armazenamento][Serviço de Nuvem do Node.js com Armazenamento].

Para obter um exemplo de como definir as variáveis de ambiente no portal de gerenciamento para um Site do Azure, consulte [Aplicativo da Web do Node.js com Armazenamento][Aplicativo da Web do Node.js com Armazenamento].

## <a name="create-queue"> </a>Como criar uma fila

O objeto **ServiceBusService** permite que você trabalhe com filas. O
código a seguir cria um objeto **ServiceBusService**. Adicione-o próximo à
parte superior do arquivo **server.js**, após a instrução de importação do
módulo azure:

    var serviceBusService = azure.createServiceBusService();

Ao chamar **createQueueIfNotExists** no objeto **ServiceBusService**
, a fila especificada (se houver) será retornada ou uma nova
fila com o nome especificado será criada. O seguinte código usa o
**createQueueIfNotExists** para criar ou conectar-se à fila denominada
'myqueue':

    serviceBusService.createQueueIfNotExists('myqueue', function(error){
        if(!error){
            // Queue exists
        }
    });

**createServiceBusService** também oferece suporte para opções adicionais, que
permitem a substituição de configurações padrão da fila, como a vida útil da mensagem
ou o tamanho máximo da fila. O seguinte exemplo mostra a definição do
tamanho máximo da fila para 5 GB e a vida útil para 1 minuto:

    var queueOptions = {
          MaxSizeInMegabytes: '5120',
          DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
        if(!error){
            // Queue exists
        }
    });

### Filtros

É possível aplicar operações de filtragem opcionais às operações executadas usando **ServiceBusService**. As operações de filtragem podem incluir log, repetição automática etc. Os filtros são objetos que implementam um método com a assinatura:

        function handle (requestOptions, next)

Após fazer seu pré-processamento nas opções de solicitação, o método precisará chamar "next", passando um retorno de chamada com a assinatura a seguir:

        function (returnObject, finalCallback, next)

Nesse retorno de chamada, e após processar o returnObject (a resposta da solicitação ao servidor), o retorno de chamada precisará invocar avançar, se ele existir, para continuar processando outros filtros ou simplesmente invocar finalCallback para terminar a invocação de serviço.

Dois filtros que implementam a lógica de repetição estão incluídos no SDK do Azure para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte código cria um objeto **ServiceBusService** que usa **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="send-messages"> </a>Como enviar mensagens para uma fila

Para enviar uma mensagem para uma fila de Service Bus, seu aplicativo chamará o método
**sendQueueMessage** no objeto **ServiceBusService**.
Mensagens enviadas para (e recebidas das) as filas do Service Bus são objetos
**BrokeredMessage** e têm um conjunto de propriedades padrão (como
**Label** e **TimeToLive**), um dicionário que é usado para manter
propriedades personalizadas específicas ao aplicativo e um corpo de
dados arbitrários do aplicativo. Um aplicativo pode definir o corpo da mensagem
transmitindo um valor da cadeia como a mensagem e quaisquer propriedades padrão
solicitadas serão preenchidas com valores padrão.

O exemplo a seguir demonstra como enviar uma mensagem de teste à
fila chamada que usa **sendQueueMessage**:

    var message = {
        body: 'Test message',
        customProperties: {
            testproperty: 'TestValue'
        }};
    serviceBusService.sendQueueMessage('myqueue', message, function(error){
        if(!error){
            // message sent
        }
    });

As filas de Service Bus oferecem suporte a um tamanho máximo de mensagem de 256 KB (o cabeçalho,
que inclui as propriedades do aplicativo padrão e personalizadas, pode ter
um tamanho máximo de 64 KB). Não há nenhum limite no número de mensagens
mantidas em uma fila, mas há uma capacidade do tamanho total das mensagens
mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um
limite superior de 5 GB.

## <a name="receive-messages"> </a>Como receber mensagens de uma fila

As mensagens são recebidas de uma fila com o método **receiveQueueMessage**
no objeto **ServiceBusService**. Por padrão, as mensagens são
excluídas da fila conforme elas são lidas; no entanto, você pode ler (espiar)
e bloquear a mensagem sem excluí-la da fila, definindo o
parâmetro opcional **isPeekLock** para **true**.

O comportamento padrão da leitura e da exclusão da mensagem como parte da
operação de recebimento é o modelo mais simples e funciona melhor em cenários nos
quais um aplicativo possa tolerar o não processamento de uma mensagem em caso
de falha. Para compreender isso, considere um cenário no qual o
consumidor emite a solicitação de recebimento e então falha antes de
processá-la. Como o Service Bus marcou a mensagem como consumida
e o aplicativo reiniciou e começou a consumir mensagens novamente,
ela perdeu a mensagem que foi consumida antes da falha.

Se o parâmetro **isPeekLock** for definido como **true**, o processo de recebimento se torna
uma operação de duas etapas, o que torna possível o suporte a aplicativos
que não toleram mensagens ausentes. Quando o Service Bus recebe uma
solicitação, ele encontra a próxima mensagem a ser consumida, ela a bloqueia para evitar
que outros consumidores a recebam e a retorna para o aplicativo.
Depois que o aplicativo termina de processar a mensagem (ou a armazena
de forma segura para um processamento futuro), ele conclui o segundo estágio do
processo de recebimento, chamando o método **deleteMessage** e fornecendo a
mensagem a ser excluída como um parâmetro. O método **deleteMessage**
 marcará a mensagem como sendo consumida e a removerá da fila.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e
processadas usando **receiveQueueMessage**. O exemplo primeiro recebe e
exclui uma mensagem, recebe a mensagem usando **isPeekLock** definido
para true e exclui a mensagem usando **deleteMessage**:

    serviceBusService.receiveQueueMessage('taskqueue', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
        }
    });
    serviceBusService.receiveQueueMessage(queueName, { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                }
            }
        }
    });

## <a name="handle-crashes"> </a>Como tratar falhas do aplicativo e mensagens ilegíveis

O Service Bus proporciona funcionalidade para ajudá-lo a se recuperar normalmente
dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um
aplicativo receptor não puder processar a mensagem por algum motivo,
ele chamará o método **unlockMessage** no objeto
**ServiceBusService**. Isso fará com que o Service Bus desbloqueie a
mensagem na fila e disponibilize-a para que ela possa ser recebida novamente
pelo mesmo aplicativo de consumo ou por
outro.

Também há um tempo limite associado a uma mensagem bloqueada na
fila e, se o aplicativo não conseguir processar a mensagem antes da
expiração do tempo limite do bloqueio (por exemplo, em caso de falha do aplicativo), o Service
Bus desbloqueará a mensagem automaticamente e a disponibilizará para ser
recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem,
mas antes que o método **delete\_subscription\_message()** seja chamado, a mensagem
será fornecida novamente ao aplicativo quando reiniciar. Isso é frequentemente chamado de
**Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada
pelo menos uma vez mais, em algumas situações, a mesma mensagem poderá ser
entregue novamente. Se o cenário não tolerar o processamento duplicado, os
desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo
para tratar a entrega de mensagem duplicada. Isso geralmente é obtido com a propriedade
**Message\_id** da mensagem, que permanecerá constante nas
tentativas da entrega.

## <a name="next-steps"> </a>Próximas etapas

Agora que você já sabe as noções básicas das filas de Service Bus, siga estes
links para saber mais.

-   Consulte a referência de MSDN: [Filas, tópicos e assinaturas.][Filas, tópicos e assinaturas.]
-   Visite o repositório [SDK do Azure para o nó][SDK do Azure para o nó] no GitHub.

  [Criar um namespace de serviço]: #create-a-service-namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain-default-credentials
  [Criar um aplicativo Node.js]: #create-app
  [Configurar seu aplicativo para usar o Service Bus]: #configure-app
  [Como: Criar uma fila]: #create-queue
  [Como: Enviar mensagens para uma fila]: #send-messages
  [Como: Receber mensagens de uma fila]: #receive-messages
  [Como: Tratar falhas do aplicativo e mensagens ilegíveis]: #handle-crashes
  [howto-service-bus-queues]: ../includes/howto-service-bus-queues.md
  [Criar e implantar um aplicativo Node.js em um site do Azure]: /pt-br/develop/nodejs/tutorials/create-a-website-(mac)/
  [Serviço de Nuvem do Node.js]: /pt-br/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Site com o WebMatrix]: /pt-br/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Serviço de Nuvem do Node.js com Armazenamento]: /pt-br/develop/nodejs/tutorials/web-app-with-storage/
  [Aplicativo da Web do Node.js com Armazenamento]: /pt-br/develop/nodejs/tutorials/web-site-with-storage/
  [Filas, tópicos e assinaturas.]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh367516.aspx
  [SDK do Azure para o nó]: https://github.com/WindowsAzure/azure-sdk-for-node
