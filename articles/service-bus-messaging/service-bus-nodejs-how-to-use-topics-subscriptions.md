---
title: Como usar os tópicos de Barramento de Serviço com Node.js | Microsoft Docs
description: Aprenda a usar assinaturas e tópicos do Barramento de Serviço no Azure por meio de um aplicativo Node.js.
services: service-bus
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 10/04/2016
ms.author: sethm

---
# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Como usar tópicos do Barramento de Serviço e assinaturas
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este guia descreve como usar tópicos do Barramento de Serviço e assinaturas de aplicativos Node.js. Os cenários abordados incluem a **criação de tópicos e assinaturas**, a **criação de filtros de assinatura**, o **envio de mensagens para um tópico**, o **recebimento de mensagens de uma assinatura** e a **exclusão de tópicos e assinaturas**. Para saber mais sobre tópicos e assinaturas, confira a seção [Próximas etapas](#next-steps).

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-node.js-application"></a>Criar um aplicativo do Node.js
Criar um aplicativo Node.js em branco. Para obter instruções sobre como criar um aplicativo Node.js, consulte [Criar e implantar um aplicativo Node.js em um site do Azure], [Serviço de Nuvem do Node.js][Serviço de Nuvem do Node.js] usando o Windows PowerShell ou Site com o WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar o Barramento de serviço
Para usar o Barramento de Serviço, baixe o pacote do Node.js do Azure. Este pacote inclui um conjunto de bibliotecas que se comunicam com os serviços REST do barramento de serviço.

### <a name="use-node-package-manager-(npm)-to-obtain-the-package"></a>Usar o NPM (gerenciador de pacotes de nós) para obter o pacote
1. Use uma interface de linha de comando, como **PowerShell** (Windows), **Terminal** (Mac,) ou **Bash** (Unix), e navegue até a pasta onde você criou o aplicativo de exemplo.
2. Digite **npm install azure** na janela de comando, que deve resultar na seguinte saída:
   
   ```
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
   ```
3. Você pode executar manualmente o comando **ls** para verificar se uma pasta **node\_modules** foi criada. Dentro dessa pasta, você encontrará o pacote **azure**, que contém as bibliotecas necessárias para acessar os tópicos do Barramento de Serviço.

### <a name="import-the-module"></a>Importar o módulo
Usando o Bloco de Notas ou outro editor de texto, adicione o seguinte ao início do arquivo **server.js** do aplicativo:

```
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Configurar uma conexão do Barramento de Serviço
O módulo Azure lê as variáveis de ambiente AZURE\_SERVICEBUS\_NAMESPACE e AZURE\_SERVICEBUS\_ACCESS\_KEY para obter as informações necessárias para se conectar ao Barramento de Serviço. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta chamando **createServiceBusService**.

Para obter um exemplo de como definir as variáveis de ambiente em um arquivo de configuração para um Serviço de Nuvem do Azure, consulte [Serviço de Nuvem do Node.js com Armazenamento][Serviço de Nuvem do Node.js com Armazenamento].

Para ver um exemplo de como definir variáveis de ambiente no [portal clássico do Azure][portal clássico do Azure] para um Site do Azure, veja [Aplicativo Web do Node.js com Armazenamento][].

## <a name="create-a-topic"></a>Criar um tópico
O objeto **ServiceBusService** permite que você trabalhe com tópicos. O código a seguir cria um objeto **ServiceBusService**. Adicione-o próximo ao início do arquivo **server.js** , após a instrução de importação do módulo Azure:

```
var serviceBusService = azure.createServiceBusService();
```

Ao chamar **createTopicIfNotExists** no objeto **ServiceBusService**, o tópico especificado (se houver) será retornado ou um novo tópico com o nome especificado será criado. O código a seguir usa o **createTopicIfNotExists** para criar ou conectar-se ao tópico denominado 'MyTopic':

```
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

**createServiceBusService** também dá suporte para opções adicionais, que permitem a substituição de configurações padrão do tópico, como a vida útil da mensagem ou o tamanho máximo do tópico. O exemplo a seguir define o tamanho máximo do tópico como 5 GB com uma vida útil de 1 minuto:

```
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filtros
É possível aplicar operações de filtragem opcionais às operações executadas usando **ServiceBusService**. As operações de filtragem podem incluir registro em log, repetição automática, etc. Os filtros são objetos que implementam um método com a assinatura:

```
function handle (requestOptions, next)
```

Após fazer o pré-processamento nas opções de solicitação, o método precisará chamar `next`, passando um retorno de chamada com a assinatura a seguir:

```
function (returnObject, finalCallback, next)
```

Nesse retorno de chamada, e após processar o **returnObject** (a resposta da solicitação ao servidor), o retorno de chamada precisará invocar avançar, se ele existir, para continuar processando outros filtros ou simplesmente invocar **finalCallback** para terminar a invocação de serviço.

Dois filtros que implementam a lógica de repetição estão incluídos no SDK do Azure para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte código cria um objeto **ServiceBusService** que usa **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="create-subscriptions"></a>Criar assinaturas
As assinaturas do tópico também são criadas com o objeto **ServiceBusService**. As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues à fila virtual da assinatura.

> [!NOTE]
> As assinaturas são persistentes e continuarão existindo até que elas ou o tópico ao qual estão associadas sejam excluídos. Se seu aplicativo contiver a lógica para criar uma assinatura, ele deve primeiro verificar se a assinatura já existe usando o método **getSubscription**.
> 
> 

### <a name="create-a-subscription-with-the-default-(matchall)-filter"></a>Criar uma assinatura com o filtro padrão (MatchAll)
O filtro **MatchAll** será o padrão usado se nenhum filtro for especificado quando uma nova assinatura for criada. Quando o filtro **MatchAll** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada “AllMessages” e usa o filtro padrão **MatchAll**.

```
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Criar assinaturas com os filtros
Você também pode configurar filtros que permitem atribuir um escopo a quais mensagens enviadas a um tópico devem aparecer dentro de uma assinatura específica do tópico.

O tipo de filtro mais flexível compatível com as assinaturas é o **SqlFilter**, que implementa um subconjunto do SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, examine a sintaxe [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Os filtros podem ser adicionados a uma assinatura usando o método **createRule** do objeto **ServiceBusService**. Este método permite que você adicione novos filtros a uma assinatura existente.

> [!NOTE]
> Como o filtro padrão é aplicado automaticamente em todas as assinaturas novas, você deve primeiro remover o filtro padrão, senão o filtro **MatchAll** substituirá todos os outros filtros que você possa especificar. Você pode remover a regra padrão usando o método **deleteRule** do objeto **ServiceBusService**.
> 
> 

O exemplo a seguir cria uma assinatura denominada `HighMessages` com um **SqlFilter** que seleciona apenas as mensagens que tenham uma propriedade **messagenumber** personalizada maior do que 3:

```
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'HighMessages', 
            'HighMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

De maneira semelhante, o exemplo a seguir cria uma assinatura denominada `LowMessages` com um **SqlFilter** que seleciona apenas as mensagens que tenham uma propriedade **messagenumber** menor ou igual a 3:

```
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'LowMessages', 
            'LowMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Quando uma mensagem é agora enviada para `MyTopic`, ela sempre será entregue aos destinatários inscritos na assinatura do tópico `AllMessages` e entregue de forma seletiva aos destinatários inscritos nas assinaturas do tópico `HighMessages` e `LowMessages` (dependendo do conteúdo da mensagem).

## <a name="how-to-send-messages-to-a-topic"></a>Como enviar mensagens a um tópico
Para enviar uma mensagem a um tópico do Barramento de Serviço, seu aplicativo deve usar o método **sendTopicMessage** do objeto **ServiceBusService**.
As mensagens enviadas aos tópicos do Barramento de Serviço são objetos **BrokeredMessage**.
Os objetos **BrokeredMessage** têm um conjunto de propriedades padrão (como **Label** e **TimeToLive**), um dicionário usado para manter as propriedades personalizadas específicas do aplicativo e um corpo dos dados da cadeia. Um aplicativo pode definir o corpo da mensagem transmitindo um valor da cadeia ao **sendTopicMessage** e todas as propriedades padrão exigidas serão preenchidas por valores padrão.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para o 'MyTopic'. Observe que o valor da propriedade **messagenumber** de cada mensagem varia de acordo com a iteração do loop (isso determinará qual assinatura o receberá):

```
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Os tópicos do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há nenhum limite no número de mensagens mantidas em um tópico, mas há uma capacidade do tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um limite máximo de 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura
As mensagens são recebidas de uma assinatura usando o método **receiveSubscriptionMessage** no objeto **ServiceBusService**. Por padrão, as mensagens são excluídas da assinatura à medida que são lidas. No entanto, você pode ler (espiar) e bloquear a mensagem sem excluí-la da assinatura, definindo o parâmetro opcional **isPeekLock** como **true**.

O comportamento padrão da leitura e da exclusão da mensagem como parte da operação de recebimento é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo possa tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

Se o parâmetro **isPeekLock** estiver definido como **true**, o processo de recebimento se torna uma operação de duas etapas, o que torna possível o suporte a aplicativos que não toleram mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo.
Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma segura para um processamento futuro), ele conclui o segundo estágio do processo de recebimento, chamando o método **deleteMessage** e fornecendo a mensagem a ser excluída como um parâmetro. O método **deleteMessage** marcará a mensagem como tendo sido consumida e a removerá da assinatura.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando **receiveSubscriptionMessage**. O primeiro exemplo recebe e exclui uma mensagem da assinatura “LowMessages” e recebe uma mensagem da assinatura “HighMessages” usando **isPeekLock** definido como true. Em seguida, ele exclui a mensagem usando **deleteMessage**:

```
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        }
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como tratar falhas do aplicativo e mensagens ilegíveis
O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele chamará o método **unlockMessage** no objeto **ServiceBusService**. Isso fará com que o Barramento de Serviço desbloqueie a mensagem na assinatura e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na assinatura e, se houver falha no processamento da mensagem pelo aplicativo antes da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método **deleteMessage** seja chamado, a mensagem será fornecida novamente ao aplicativo quando reiniciar. Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é obtido com a propriedade **MessageId** da mensagem, que permanecerá constante nas tentativas da entrega.

## <a name="delete-topics-and-subscriptions"></a>Excluir tópicos e assinaturas
Os tópicos e as assinaturas são persistentes e devem ser explicitamente excluídos por meio do [portal clássico do Azure][portal clássico do Azure] ou de forma programática.
O exemplo a seguir demonstra como excluir o tópico denominado `MyTopic`:

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

A exclusão de um tópico também excluirá todas as assinaturas registradas com o tópico. As assinaturas também podem ser excluídas de forma independente. O código a seguir demonstra como excluir uma assinatura chamada `HighMessages` do tópico `MyTopic`:

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## <a name="next-steps"></a>Próximas etapas
Agora que você já sabe os princípios dos tópicos do Barramento de Serviço, acesse estes links para saber mais.

* Consulte [Filas, tópicos e assinaturas][Filas, tópicos e assinaturas].
* Referência da API para [SqlFilter][SqlFilter].
* Visite o repositório [SDK do Azure para o nó][SDK do Azure para o nó] no GitHub.

[SDK do Azure para o nó]: https://github.com/Azure/azure-sdk-for-node
[Portal clássico do Azure]: https://manage.windowsazure.com
[SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Filas, tópicos e assinaturas]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
[Serviço de Nuvem do Node.js]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Criar e implantar um aplicativo Node.js em um site do Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Serviço de Nuvem do Node.js com Armazenamento]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Aplicativo Web Node.js com Armazenamento]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md




<!--HONumber=Oct16_HO2-->


