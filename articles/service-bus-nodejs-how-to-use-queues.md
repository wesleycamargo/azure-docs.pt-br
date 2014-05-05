<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="Filas do Service Bus" pageTitle="Como usar as filas do Service Bus (Node.js) – Azure" metaKeywords="Filas do Service Bus no Azure, filas do Azure, mensagens no Azure, Node.js de filas no Azure" description="Saiba como usar filas do Service Bus no Azure. Amostras de códigos escritos no Node.js." metaCanonical="" services="service-bus" documentationCenter="Node.js" title="Como usar as filas do Service Bus" authors="" solutions="" manager="" editor="" />





# Como usar as filas do Service Bus

Este guia mostrará como usar as filas do Service Bus. As amostras são escritas em JavaScript e usam o módulo Node.js do Azure. Os cenários
abrangidos incluem **criar filas, enviar e receber mensagens** e
**excluir filas**. Para obter mais informações sobre filas, consulte a seção [Próximas etapas].

## Sumário

-   [O que são as filas do Service Bus?][]
-   [Criar um namespace de serviço][]
-   [Obter as credenciais de gerenciamento padrão do namespace][]
-   [Crie um aplicativo do Node.js](#create-app)
-   [Configurar seu aplicativo para usar o Service Bus](#configure-app)
-   [Como criar uma fila](#create-queue)
-   [Como enviar mensagens a uma fila](#send-messages)
-   [Como receber mensagens de uma fila](#receive-messages)
-   [Como tratar falhas do aplicativo e mensagens ilegíveis](#handle-crashes)
-   [Próximas etapas](#next-steps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="create-app"> </a>Criar um aplicativo do Node.js

Crie um aplicativo do Node.js em branco. Para obter instruções sobre como criar um aplicativo Node.js, consulte [Criar e implantar um aplicativo Node.js em um site do Azure], [Serviço de Nuvem do Node.js] (usando o Windows PowerShell) ou [Site com o WebMatrix].

## <a name="configure-app"> </a>Configurar seu aplicativo para usar o Service Bus

Para usar o Service Bus do Azure, você precisa baixar e usar o pacote Azure do Node.js. Isso inclui um conjunto de bibliotecas de conveniência que se comunicam com os serviços REST do Service Bus.

### Usar o Gerenciador de Pacotes de Nós (NPM) para obter o pacote

1.  Use a janela de comando **Windows PowerShell para Node.js** para acessar a pasta **c:\\node\\sbqueues\\WebRole1** onde você criou a amostra do aplicativo.

2.  Digite **npm install azure** na janela de comando, que deve
    resultar em uma saída semelhante à seguinte:

        azure@0.7.5 node_modules\azure
		d ateformat@1.0.2-1.2.3
		 xmlbuilder@0.4.2
		 node-uuid@1.2.0
		 mime@1.2.9
		 underscore@1.4.4
		 validator@1.1.1
		 tunnel@0.0.2
		 wns@0.5.3
		 xml2js@0.2.7 (sax@0.5.2)
		 request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Você pode executar o comando **ls** manualmente para verificar se uma pasta
    **node\_modules** foi criada. Dentro dessa pasta, você
    encontrará o pacote **azure**, que contém as bibliotecas necessárias para
    acessar as filas do Service Bus.

### Importar o módulo

Usando o Bloco de Notas ou outro editor de texto, adicione o seguinte à parte superior do arquivo **server.js** do aplicativo:

    var azure = require('azure');

### Configurar uma Conexão do Service Bus do Azure

O módulo azure realizará a leitura das variáveis de ambiente AZURE\ SERVICEBUS\ NAMESPACE e AZURE\ SERVICEBUS\ ACCESS\ KEY para obter as informações necessárias para se conectar ao Service Bus do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta chamando **createServiceBusService**.

Para obter um exemplo de como definir as variáveis de ambiente em um arquivo de configuração para um Serviço de Nuvem do Azure, consulte [Serviço de Nuvem do Node.js com Armazenamento].

Para obter um exemplo de como definir as variáveis de ambiente no portal de gerenciamento para um Site do Azure, consulte [Aplicativo da Web do Node.js com Armazenamento].

## <a name="create-queue"> </a>Como criar uma fila

O objeto **ServiceBusService** permite que você trabalhe com filas. O código a seguir cria um objeto **Azure::ServiceBusService**. Adicione-o próximo à parte superior do arquivo **server.js**, após a instrução de importação do módulo azure:

    var serviceBusService = azure.createServiceBusService();

Ao chamar **createQueueIfNotExists** no objeto **ServiceBusService**, a fila especificada (se houver) será retornada ou uma nova fila com o nome especificado será criada. O código a seguir usa
**createQueueIfNotExists** para criar ou conectar-se à fila denominada 'myqueue':

    serviceBusService.createQueueIfNotExists('myqueue', function(error){
        if(!error){
            // Queue exists
        }
    });

**createServiceBusService** também oferece suporte para opções adicionais, que permitem a substituição de configurações padrão da fila, como a vida útil da mensagem ou o tamanho máximo da fila. O seguinte exemplo mostra a definição do tamanho máximo da fila para 5 GB e a vida útil para 1 minuto:

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

Nesse retorno de chamada, e após processar o returnObject (a resposta da solicitação ao servidor), o retorno de chamada precisará invocar next, se ele existir, para continuar processando outros filtros ou simplesmente invocar finalCallback para terminar a invocação de serviço.

Dois filtros que implementam a lógica de repetição estão incluídos no SDK do Azure para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte código cria um objeto **ServiceBusService** que usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="send-messages"> </a>Como enviar mensagens a uma fila

Para enviar uma mensagem a uma fila do Service Bus, seu aplicativo chamará o
método **sendQueueMessage** do objeto **ServiceBusService**.
Mensagens enviadas para (e recebidas das) filas do Service Bus são
objetos **BrokeredMessage** e têm um conjunto de propriedades padrão (como **Label** e **TimeToLive**), um dicionário usado para manter propriedades personalizadas específicas ao aplicativo e um corpo de aplicativos arbitrários. Um aplicativo pode definir o corpo da mensagem transmitindo um valor da cadeia como a mensagem e quaisquer propriedades padrão solicitadas serão preenchidas pelos valores padrão.

O exemplo a seguir demonstra como enviar uma mensagem de teste à fila chamada que usa **sendQueueMessage**:

    var message = {
        body: 'Test message',
        customProperties: {
            testproperty: 'TestValue'
        };
    serviceBusService.sendQueueMessage('myqueue', message, function(error){
        if(!error){
            // message sent
        }
    });

As filas do Service Bus suportam um tamanho máximo de mensagem de 256 KB (o cabeçalho,
que inclui as propriedades padrão e personalizadas do aplicativo, podem ter um
tamanho máximo de 64 KB). Não há nenhum limite no número de mensagens
mantidas em uma fila mas há uma capacidade do tamanho total das mensagens
mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB.

## <a name="receive-messages"> </a>Como receber mensagens de uma fila

As mensagens são recebidas de uma fila com o método **receiveQueueMessage**
no objeto **ServiceBusService**. Por padrão, as mensagens são excluídas da fila conforme elas são lidas; no entanto, você pode ler (espiar) e bloquear a mensagem sem excluí-la da fila, definindo o parâmetro opcional **isPeekLock** para **true**.

O comportamento padrão da leitura e da exclusão da mensagem como parte da operação de recebimento é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo possa tolerar o não processamento de uma mensagem em caso de falha. Para entender como isso funciona, considere um cenário no qual o consumidor emite a solicitação de recebimento e ocorre uma falha antes de processá-la. Como o Service Bus terá marcado a mensagem
como consumida, quando o aplicativo for reiniciado e começar a consumir
as mensagens novamente, ele terá perdido a mensagem que
foi consumida antes da falha.

Se o parâmetro **isPeekLock** for definido como **true**, o processo de recebimento se torna uma operação de duas etapas, o que torna possível o suporte a aplicativos que não toleram mensagens ausentes. Quando o Service Bus recebe uma solicitação, ele localiza a próxima mensagem a ser consumida, ele a bloqueia para evitar que outros consumidores a recebam e depois a retorna para o aplicativo.
Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma confiável para um processamento futuro), ele conclui o segundo estágio do processo de recebimento chamando o método **deleteMessage** e fornecendo a mensagem a ser excluída como um parâmetro. O método **deleteMessage** marcará a mensagem como consumida e a removerá da fila.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando **receiveQueueMessage**. O exemplo primeiro recebe e exclui uma mensagem, recebe a mensagem usando **isPeekLock** definido para true e exclui a mensagem usando **deleteMessage**:

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

O Service Bus proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros em seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não for capaz de processar a mensagem por algum motivo, ele chamará o método **unlockMessage** no
objeto **ServiceBusService**. Isso fará com que o Service Bus desbloqueie a
mensagem na fila e disponibilize-a para que ela possa ser recebida novamente,
tanto pelo mesmo aplicativo de consumo quanto por algum outro
aplicativo.

Também existe um tempo limite associado a uma mensagem bloqueada na fila, e se o aplicativo falhar em processar a mensagem antes da expiração do tempo limite do bloqueio (por exemplo, se o aplicativo falhar), o Service Bus desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método **deleteMessage** seja chamado, a mensagem será fornecida novamente ao aplicativo quando ele for reiniciado. Isso normalmente é chamado de
**Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez, mas, em determinadas situações, a mesma mensagem pode ser fornecida novamente. Se o cenário não tolerar processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega da mensagem duplicada. Isso geralmente é feito usando a
A propriedade **MessageId** da mensagem, que permanecerá constante entre as tentativas de entrega.

## <a name="next-steps"> </a>Próximas etapas

Agora que você já sabe os princípios das filas do Service Bus, acesse
estes links para saber mais.

-   Consulte a Referência do MSDN: [Filas, tópicos e assinaturas.][]
-   Acesse o repositório [SDK do Azure para Nós] no GitHub.

  [SDK do Azure para Nós]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Próximas etapas]: #next-steps
  [O que são as filas do Service Bus?]: #what-are-service-bus-queues
  [Criar um namespace de serviço]: #create-a-service-namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain-default-credentials
  [Crie um aplicativo do Node.js]: #create-app
  [Configurar seu aplicativo para usar o Service Bus]: #configure-app
  [Como criar uma fila]: #create-queue
  [Como enviar mensagens a uma fila]: #send-messages
  [Como receber mensagens de uma fila]: #receive-messages
  [Como tratar falhas do aplicativo e mensagens ilegíveis]: #handle-crashes
  [Conceitos de fila]: ../../dotNet/Media/sb-queues-08.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  
  
  
  
  
  [Serviço de nuvem do Node.js]: /pt-br/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Filas, tópicos e assinaturas.]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh367516.aspx
  [Site com o WebMatrix]: /pt-br/develop/nodejs/tutorials/web-site-with-webmatrix/
[Portal de Gerenciamento anterior]: ../../Shared/Media/previous-portal.png
  [Criar e implantar um aplicativo Node.js em um site do Azure]: /pt-br/develop/nodejs/tutorials/create-a-website-(mac)/
  [Serviço de Nuvem do Node.js com armazenamento]: /pt-br/develop/nodejs/tutorials/web-app-with-storage/
  [Aplicativo da Web do Node.js com armazenamento]: /pt-br/develop/nodejs/tutorials/web-site-with-storage/
