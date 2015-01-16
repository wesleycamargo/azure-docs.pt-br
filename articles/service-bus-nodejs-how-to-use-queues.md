<properties urlDisplayName="Service Bus Queues" pageTitle="Como usar filas do barramento de serviço (Node.js) - Azure" metaKeywords="filas do barramento de serviço do Azure, filas do Azure, sistema de mensagens do Azure, filas do Azure em Node.js" description="Aprenda a usar as filas do barramento de serviço no Azure. Exemplos de códigos escritos em Node. js." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Service Bus Queues" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />






# Como usar as filas do Service Bus

Este guia mostra como usar as filas do Barramento de Serviço. Os exemplos são
escritos em JavaScript e usam o módulo Node.js do Azure. Os cenários
Os cenários abrangidos incluem **criar filas, enviar e receber mensagens** e
**excluir filas**. Para obter mais informações sobre filas, consulte a seção [Próximas
Etapas].

## Sumário

-   [O que são as filas do Service Bus?][]
-   [Criar um namespace de serviço][]
-   [Obter as credenciais de gerenciamento padrão do namespace][]
-   [Criar um aplicativo Node.js](#create-app)
-   [Configurar seu aplicativo para usar o Service Bus](#configure-app)
-   [Como: Criar uma fila](#create-queue)
-   [Como: Enviar mensagens para uma fila](#send-messages)
-   [Como: Receber mensagens de uma fila](#receive-messages)
-   [Como: Tratar falhas do aplicativo e mensagens ilegíveis](#handle-crashes)
-   [Próximas etapas](#next-steps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="create-app"> </a>Criar um aplicativo Node.js

Criar um aplicativo Node.js em branco. Para obter instruções sobre como criar um aplicativo Node.js, consulte [Criar e implantar um aplicativo Node.js em um site do Azure], [Serviço em Nuvem Node.js][Serviço de Nuvem Node.js] (usando o Windows PowerShell) ou [site com WebMatrix].

## <a name="configure-app"> </a>Configurar seu aplicativo para usar o Service Bus

Para usar o barramento de serviço do Azure, você precisa baixar e usar o
pacote do Node.js do Azure. Isso inclui um conjunto de bibliotecas convenientes que
se comunicam com os serviços REST do barramento de serviço.

### Usar o NPM (gerenciador de pacotes de nós) para obter o pacote

1.  Use a janela de comando **Windows PowerShell para Node.js** para
    navegue para a pasta **c:\\node\\sbqueues\\WebRole1** em que você
    criou o aplicativo de exemplo.

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

3.  Você pode executar manualmente o comando **ls** para verificar se uma pasta
    **node\_modules** foi criada. Dentro dessa pasta, localize o pacote
    **azure** que contém as bibliotecas necessárias para acessar
    filas do barramento de serviço.

### Importar o módulo

Usando o bloco de notas ou outro editor de texto, adicione o seguinte à parte superior do
arquivo **server.js** do aplicativo:

    var azure = require('azure');

### Configurar uma conexão do Service Bus do Azure

O módulo azure lerá as variáveis de ambiente AZURE\_SERVICEBUS\_NAMESPACE e AZURE\_SERVICEBUS\_ACCESS\_KEY para obter as informações necessárias para se conectar ao Barramento de Serviço do Azure. Se essas variáveis de ambiente não estiverem definidas, você deve especificar as informações da conta ao chamar **createServiceBusService**.

Para obter um exemplo de como definir as variáveis de ambiente em um arquivo de configuração para um Serviço de Nuvem do Azure, consulte [Serviço de Nuvem do Node.js com Armazenamento].

Para obter um exemplo de como definir as variáveis de ambiente no portal de gerenciamento para um Site do Azure, consulte [Aplicativo Web do Node.js com Armazenamento]

## <a name="create-queue"> </a>Como criar uma fila

O objeto **ServiceBusService** permite que você trabalhe com filas. O
código a seguir cria um objeto **ServiceBusService**. Adicione-o próximo à
parte superior do arquivo **server.js**, após a instrução de importação do módulo
Azure:

    var serviceBusService = azure.createServiceBusService();

Chamando **createQueueIfNotExists** no objeto **ServiceBusService**
, a fila especificada será retornada (se existir) ou uma nova
fila com o nome especificado será criada. O código a seguir usa
**createQueueIfNotExists** para criar ou conectar-se à fila denominada
'myqueue':

    serviceBusService.createQueueIfNotExists('myqueue', function(error){
        if(!error){
            // Queue exists
        }
    });

**createServiceBusService** também tem suporte para opções adicionais, que
permitem substituir as configurações de fila padrão, como tempo de vida da mensagem
ou tamanho máximo da fila. O exemplo a seguir mostra a configuração do
tamanho máximo da fila para 5 GB e uma vida útil de um minuto:

    var queueOptions = {
          MaxSizeInMegabytes: '5120',
          DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
        if(!error){
            // Queue exists
        }
    });

###Filtros

É possível aplicar operações de filtragem opcionais às operações executadas usando **ServiceBusService**. As operações de filtragem podem incluir log, repetição automática etc. Os filtros são objetos que implementam um método com a assinatura:

		function handle (requestOptions, next)

Após fazer seu pré-processamento nas opções de solicitação, o método precisará chamar "next", passando um retorno de chamada com a assinatura a seguir:

		function (returnObject, finalCallback, next)

Nesse retorno de chamada, e após processar o returnObject (a resposta da solicitação ao servidor), o retorno de chamada precisará invocar avançar, se ele existir, para continuar processando outros filtros ou simplesmente invocar finalCallback para terminar a invocação de serviço.

Dois filtros que implementam a lógica de repetição estão incluídos no SDK do Azure para Node.js,**ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte código cria um objeto **ServiceBusService** que usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="send-messages"> </a>Como enviar mensagens para uma fila

Para enviar uma mensagem a uma fila do Service Bus, seu aplicativo chamará o
**método sendQueueMessage** no objeto **ServiceBusService**.
Mensagens enviadas para (e recebidas das) filas do Service Bus são
**objetos BrokeredMessage**, e possuem um conjunto de propriedades padrão (como
como **Label** e **TimeToLive**), um dicionário usado para conter
propriedades específicas de aplicativo personalizado e um corpo de
dados de aplicativo arbitrário. Um aplicativo pode definir o corpo da mensagem
passando um valor de cadeia de caracteres como a mensagem e qualquer propriedade
padrão requerida será preenchida por valores padrão.

O exemplo a seguir demonstra como enviar uma mensagem de teste para
fila chamada 'myqueue' usando **sendQueueMessage**:

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

Filas de barramento de serviço têm suporte para um tamanho máximo da mensagem de 256 KB (o cabeçalho,
que inclui as propriedades padrão e personalizadas do aplicativo, pode ter
um tamanho máximo de 64 KB). Não há nenhum limite ao número de mensagens
mantidas em uma fila, mas há um limite ao tamanho total das mensagens
mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um
limite superior de 5 GB.

## <a name="receive-messages"> </a>Como receber mensagens de uma fila

As mensagens recebidas de uma fila usando o método **receiveQueueMessage**
no objeto **ServiceBusService**. Por padrão, as mensagens são
excluída da fila conforme elas são lidas; no entanto, você pode ler (espiar)
e bloquear a mensagem sem excluí-la da fila definindo oa
parâmetro opcional **isPeekLock** para **true**.

O comportamento padrão de leitura e exclusão da mensagem como parte da
operação de receber é o modelo mais simples e funciona melhor em cenários em
que o aplicativo pode tolerar o não processamento de uma mensagem no caso
de uma falha. Para entender isso, considere um cenário no qual o
consumidor emite a solicitação de recebimento e então falha antes de processá-la
. Uma vez que o barramento de serviço terá marcado a mensagem como tendo sido consumida,
então, quando o aplicativo reiniciar e começar a consumir mensagens novamente,
ele terá perdido a mensagem que foi consumida antes da falha.

Se o parâmetro **isPeekLock** for definido para **true**, o recebimento se torna
uma operação de dois estágios, que possibilita o suporte a aplicativos
que não podem tolerar mensagens ausentes. Quando o barramento de serviço recebe uma
solicitação, ele encontra a próxima mensagem a ser consumida, bloqueia-a para evitar
que outros consumidores a recebam e retorna para o aplicativo.
Depois que o aplicativo termina de processar a mensagem (ou a armazena
de modo confiável para processamento futuro), ele conclui a segunda etapa do
processo de recebimento chamando o método **deleteMessage** e fornecendo a
mensagem a ser excluída como um parâmetro. O método **deleteMessage**
marcará a mensagem tendo sido consumida e remove-a da fila.

O exemplo a seguir demonstra como as mensagens podem ser recebidas
e processadas usando **receiveQueueMessage**. O exemplo primeiro recebe e
exclui uma mensagem, e então recebe uma mensagem usando **isPeekLock** definido
para true, então exclui a mensagem usando **deleteMessage**:

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

O barramento de serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos
erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um
aplicativo de receptor não puder processar a mensagem por algum motivo,
ele pode chamar o método **unlockMessage** no
objeto **ServiceBusService**. Isso fará com que o barramento de serviço desbloqueie a
mensagem na fila e disponibilize-a para ser recebida novamente,
seja pelo mesmo aplicativo de consumo ou por outro aplicativo de consumo
aplicativo.

Também há um tempo limite associado a uma mensagem bloqueada na
fila e, se o aplicativo falhar em processar a mensagem antes de
o tempo limite de bloqueio expirar (por exemplo, se o aplicativo falhar), o barramento de serviço
desbloqueará a mensagem automaticamente e a disponibilizará para ser
recebida novamente.

No caso de o aplicativo falhar após processar a mensagem
mas antes de o método **deleteMessage** ser chamado, a mensagem será
entregue novamente ao aplicativo quando ele for reiniciado. Isso normalmente é chamado de
**Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada
pelo menos uma vez, mas, em determinadas situações, a mesma mensagem pode ser
entregue novamente. Se o cenário não tolerar processamento duplicado,
os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo
para tratar a entrega de mensagens duplicadas. Isso geralmente é feito usando a propriedade
**MessageId** da mensagem, que permanecerá constante em todas as
tentativas de entrega.

## <a name="next-steps"> </a>Próximas etapas

Agora que você aprendeu os conceitos básicos das filas do barramento de serviço, siga estes
links para saber mais.

-   Consultar a referência de MSDN: [Filas, tópicos e assinaturas.][]
-   Visite o repositório [SDK do Azure para o nó] no GitHub.

  [SDK do Azure para o nó]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Próximas etapas]: #next-steps
  [O que são as filas do Service Bus?]: #what-are-service-bus-queues
  [Criar um namespace de serviço]: #create-a-service-namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain-default-credentials
  [Criar um aplicativo Node.js]: #create-app
  [Configurar seu aplicativo para usar o Service Bus]: #configure-app
  [Como: Criar uma fila]: #create-queue
  [Como: Enviar mensagens para uma fila]: #send-messages
  [Como: Receber mensagens de uma fila]: #receive-messages
  [Como: Tratar falhas do aplicativo e mensagens ilegíveis]: #handle-crashes
  [Conceitos de fila]: ../../dotNet/Media/sb-queues-08.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  
  
  
  
  
  [Serviço de Nuvem do Node.js]: /pt-br/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Filas, tópicos e assinaturas.]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh367516.aspx
  [Site com WebMatrix]: /pt-br/develop/nodejs/tutorials/web-site-with-webmatrix/
[Portal de gerenciamento anterior]: ../../Shared/Media/previous-portal.png
  [Criar e implantar um aplicativo Node.js em um site do Azure]: /pt-br/develop/nodejs/tutorials/create-a-website-(mac)/
  [Serviço de Nuvem do Node.js com Armazenamento]: /pt-br/develop/nodejs/tutorials/web-app-with-storage/
  [Aplicativo Web Node.js com Armazenamento]: /pt-br/develop/nodejs/tutorials/web-site-with-storage/
