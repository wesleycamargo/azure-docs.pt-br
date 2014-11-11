<properties linkid="dev-nodejs-how-to-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (Node.js) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic Node.js" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Node.js applications." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Service Bus Topics/Subscriptions" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Como usar os tópicos e as assinaturas do Service Bus

Este guia mostrará como usar os tópicos e as assinaturas do Service Bus
dos aplicativos do Ruby. Os cenários abordados incluem a **criação
de tópicos e assinaturas, a criação de filtros de assinatura, o envio
de mensagens** para um tópico, o **recebimento de mensagens de uma assinatura** e a
**exclusão de tópicos e assinaturas**. Para obter mais informações sobre tópicos
e assinaturas, consulte a seção [Próximas etapas][Próximas etapas].

## Sumário

-   [O que são os tópicos e as assinaturas do Service Bus?][O que são os tópicos e as assinaturas do Service Bus?]
-   [Criar um namespace de serviço][Criar um namespace de serviço]
-   [Obter as credenciais de gerenciamento padrão do namespace][Obter as credenciais de gerenciamento padrão do namespace]
-   [Criar um aplicativo Node.js][Criar um aplicativo Node.js]
-   [Configurar seu aplicativo para usar o Service Bus][Configurar seu aplicativo para usar o Service Bus]
-   [Como: Criar um tópico][Como: Criar um tópico]
-   [Como: criar assinaturas][Como: criar assinaturas]
-   [Como: enviar mensagens para um tópico][Como: enviar mensagens para um tópico]
-   [Como: Receber mensagens de uma assinatura][Como: Receber mensagens de uma assinatura]
-   [Como: Tratar falhas do aplicativo e mensagens ilegíveis][Como: Tratar falhas do aplicativo e mensagens ilegíveis]
-   [Como: excluir tópicos e assinaturas][Como: excluir tópicos e assinaturas]
-   [Próximas etapas][1]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <a name="create-app"></a>Criar um aplicativo do Node.js

Criar um aplicativo Node.js em branco. Para obter instruções sobre como criar um aplicativo Node.js, consulte [Criar e implantar um aplicativo Node.js em um site do Azure][Criar e implantar um aplicativo Node.js em um site do Azure], [Serviço de Nuvem do Node.js][Serviço de Nuvem do Node.js] (usando o Windows PowerShell) ou [Site com o WebMatrix][Site com o WebMatrix].

## <a name="configure-app"></a>Configurar seu aplicativo para usar o Service Bus

Para usar o Service Bus do Azure, você precisa baixar e usar o
pacote do Azure Node.js. Isso inclui um conjunto de bibliotecas de conveniência que
se comunicam com os serviços REST do Service Bus.

### Usar o NPM (gerenciador de pacotes de nós) para obter o pacote

1.  Use uma interface de linha de comando, como **PowerShell** (Windows,) **Terminal** (Mac) ou **Bash** (Unix), e vá até a pasta onde você criou a amostra do aplicativo.

2.  Digite **npm install azure** na janela de comando, que deve
    resultar na seguinte saída:

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
    **azure**, que contém as bibliotecas necessárias para acessar
    os tópicos do Service Bus.

### Importar o módulo

Usando um bloco de notas, adicione o seguinte à parte superior
do arquivo **server.js** do aplicativo:

    var azure = require('azure');

### Configurar uma conexão do Service Bus do Azure

O módulo azure realizará a leitura das variáveis de ambiente AZURE\_SERVICEBUS\_NAMESPACE and AZURE\_SERVICEBUS\_ACCESS\_KEY para obter as informações necessárias para se conectar ao Service Bus do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta chamando **createServiceBusService**.

Para obter um exemplo de como definir as variáveis de ambiente em um arquivo de configuração para um Serviço de Nuvem do Azure, consulte [Serviço de Nuvem do Node.js com Armazenamento][Serviço de Nuvem do Node.js com Armazenamento].

Para obter um exemplo de como definir as variáveis de ambiente no portal de gerenciamento para um Site do Azure, consulte [Aplicativo da Web do Node.js com Armazenamento][Aplicativo da Web do Node.js com Armazenamento].

## <a name="create-topic"></a>Como criar um tópico

O objeto **ServiceBusService** permite que você trabalhe com tópicos. O
código a seguir cria um objeto **ServiceBusService**. Adicione-o próximo à
parte superior do arquivo **server.js**, após a instrução de importação do
módulo azure:

    var serviceBusService = azure.createServiceBusService();

Ao chamar **createTopicIfNotExists** no objeto **ServiceBusService**
, o tópico especificado (se houver) será retornado ou um novo
tópico com o nome especificado será criado. O seguinte código usa o
**createTopicIfNotExists** para criar ou conectar-se ao tópico denominado
'MyTopic':

    serviceBusService.createTopicIfNotExists('MyTopic',function(error){
        if(!error){
            // Topic was created or exists
            console.log('topic created or exists.');
        }
    });

**createServiceBusService** também oferece suporte para opções adicionais, que
permitem a substituição de configurações padrão do tópico, como a vida útil da mensagem
ou o tamanho máximo do tópico. O exemplo a seguir mostra a definição
do tamanho máximo do tópico para 5 GB e da vida útil para 1 minuto:

    var topicOptions = {
            MaxSizeInMegabytes: '5120',
            DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
        if(!error){
            // topic was created or exists
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

## <a name="create-subscription"></a>Como criar assinaturas

As assinaturas do tópico também são criadas com o objeto **ServiceBusService**
. As assinaturas são nomeadas e podem ter um filtro opcional que
restringe o conjunto de mensagens entregues à
fila virtual da assinatura.

<div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>As assinaturas s&atilde;o persistentes e continuar&atilde;o existindo at&eacute; que elas ou o t&oacute;pico ao qual est&atilde;o associadas sejam exclu&iacute;dos.
 Se seu aplicativo contiver l&oacute;gica para criar uma assinatura, ele deveria, primeiro,

revisar se a assinatura j&aacute; existe, usando o m&eacute;todo <strong>getSubscription</strong>.
</p>
</div>

### Criar uma assinatura com o filtro padrão (MatchAll)

**MatchAll** será o filtro padrão usado se nenhum filtro
for especificado quando uma nova assinatura for criada. Quando o filtro **MatchAll**
 é usado, todas as mensagens publicadas no tópico são colocadas na
fila virtual da assinatura. O seguinte exemplo cria uma
assinatura denominada 'AllMessages' e usa o filtro padrão **MatchAll**
.

    serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
        if(!error){
            // subscription created
        }
    });

### Criar assinaturas com os filtros

Você também pode configurar filtros que permitem atribuir um escopo a quais mensagens enviadas
a um tópico devem aparecer dentro de uma assinatura específica do tópico.

O tipo de filtro mais flexível compatível com as assinaturas é o
**SqlFilter**, que implementa um subconjunto do SQL92. Os filtros SQL operam
nas propriedades das mensagens que são publicadas no tópico. Para
obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL,
examine a sintaxe [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Os filtros podem ser adicionados a uma assinatura usando o método **createRule**
do objeto **ServiceBusService**. Este método permite que você
adicione novos filtros a uma assinatura existente.

> [WACOM.NOTE]

> Como o filtro padrão é aplicado automaticamente em todas as
> assinaturas novas, você deve primeiro remover o filtro padrão ou o filtro
> **MatchAll** substituirá todos os outros filtros que você possa especificar. É possível
> remover a regra padrão usando o método **deleteRule** do objeto
> **ServiceBusService**.

O exemplo abaixo cria uma assinatura denominada 'HighMessages' com um
**SqlFilter** que seleciona apenas as mensagens que tenham uma propriedade
**messagenumber** personalizada maior que 3:

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

Da mesma forma, o seguinte exemplo cria uma assinatura denominada
'LowMessages' com um **SqlFilter** que seleciona apenas as mensagens que tenham
uma propriedade **messagenumber** menor ou igual a 3:

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

Quando uma mensagem é agora enviada para 'MyTopic', ela sempre será fornecida aos
destinatários inscritos na assinatura do tópico 'AllMessages' e
será fornecida de forma seletiva para os destinatários inscritos nas assinaturas dos tópicos 'HighMessages' e
'LowMessages' (dependendo do conteúdo de mensagem).

## <a name="send-messages"></a>Como enviar mensagens a um tópico

Para enviar uma mensagem a um tópico do Service Bus, seu aplicativo deve usar o método
**sendTopicMessage** do objeto **ServiceBusService**.
As mensagens enviadas para os tópicos de Service Bus são objetos **BrokeredMessage**. Os objetos
**BrokeredMessage** têm um conjunto de propriedades padrão (como
**Label** e **TimeToLive**), um dicionário usado para armazenar
propriedades personalizadas específicas do aplicativo e um corpo de dados da cadeia de caracteres. Um
aplicativo pode definir o corpo da mensagem transmitindo um valor da cadeia ao
**sendTopicMessage** e todas as propriedades padrão exigidas serão
preenchidas por valores padrão.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para
'MyTopic'. Observe que o valor da propriedade **messagenumber** de cada
mensagem varia de acordo com a iteração do loop (isso determinará qual
assinatura o receberá):

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

Os tópicos de Service Bus suportam um tamanho máximo de mensagem de 256 MB (o cabeçalho,
incluindo as propriedades padrão e personalizadas do aplicativo podem ter
um tamanho máximo de 64 MB). Não há nenhum limite no número de mensagens
mantidas em um tópico, mas há uma capacidade do tamanho total das mensagens
mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um
limite máximo de 5 GB.

## <a name="receive-messages"></a>Como receber mensagens de uma assinatura

As mensagens são recebidas de uma fila com o método
**receiveSubscriptionMessage**no objeto **ServiceBusService**
. Por padrão, as mensagens são excluídas da assinatura conforme elas
são lidas; no entanto, você pode ler (espiar) e bloquear a mensagem sem
excluí-la da assinatura, definindo o parâmetro opcional
**isPeekLock** para **true**.

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
 marcará a mensagem como sendo consumida e a removerá da assinatura.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e
processadas usando **receiveSubscriptionMessage**. O exemplo primeiro
recebe e exclui uma mensagem da assinatura 'LowMessages' e,
em seguida, recebe uma mensagem da assinatura 'HighMessages' usando
**isPeekLock** definido para true. Em seguida, ele exclui a mensagem usando
**deleteMessage**:

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

## <a name="handle-crashes"></a>Como tratar falhas do aplicativo e mensagens ilegíveis

O Service Bus proporciona funcionalidade para ajudá-lo a se recuperar normalmente
dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um
aplicativo receptor não puder processar a mensagem por algum motivo,
ele chamará o método **unlockMessage** no objeto
**ServiceBusService**. Isso fará com que o Service Bus desbloqueie a
mensagem na assinatura e disponibilize-a para que ela possa ser recebida novamente
pelo mesmo aplicativo de consumo ou por
outro.

Também há um tempo limite associado a uma mensagem bloqueada na
assinatura e, se o aplicativo não conseguir processar a mensagem antes da
expiração do tempo limite do bloqueio (por exemplo, em caso de falha do aplicativo), o
Service Bus desbloqueará a mensagem automaticamente e a disponibilizará para
ser recebida novamente.

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

## <a name="delete"></a>Como excluir tópicos e assinaturas

Os tópicos e as assinaturas são persistentes e devem ser explicitamente excluídos
por meio do Portal de Gerenciamento do Azure ou de forma programática.
O exemplo a seguir demonstra como excluir o tópico chamado 'MyTopic':

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

A exclusão de um tópico também excluirá todas as assinaturas registradas
com o tópico. As assinaturas também podem ser excluídas de forma independente. O
código a seguir demonstra como excluir uma assinatura denominada
'HighMessages' do tópico 'MyTopic':

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## <a name="next-steps"></a>Próximas etapas

Agora que você já sabe os princípios dos tópicos do Service Bus, acesse estes
links para saber mais.

-   Consulte a referência de MSDN: [Filas, tópicos e assinaturas][Filas, tópicos e assinaturas].
-   Referência da API para [SqlFilter][SqlFilter].
-   Visite o repositório [SDK do Azure para o nó][SDK do Azure para o nó] no GitHub.

  [Próximas etapas]: #nextsteps
  [Criar um namespace de serviço]: #create-a-service-namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain-default-credentials
  [Criar um aplicativo Node.js]: #create-app
  [Configurar seu aplicativo para usar o Service Bus]: #configure-app
  [Como: Criar um tópico]: #create-topic
  [Como: criar assinaturas]: #create-subscription
  [Como: enviar mensagens para um tópico]: #send-messages
  [Como: Receber mensagens de uma assinatura]: #receive-messages
  [Como: Tratar falhas do aplicativo e mensagens ilegíveis]: #handle-crashes
  [Como: excluir tópicos e assinaturas]: #delete
  [1]: #next-steps
  [howto-service-bus-topics]: ../includes/howto-service-bus-topics.md
  [Criar e implantar um aplicativo Node.js em um site do Azure]: /pt-br/develop/nodejs/tutorials/create-a-website-(mac)/
  [Serviço de Nuvem do Node.js]: /pt-br/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Site com o WebMatrix]: /pt-br/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Serviço de Nuvem do Node.js com Armazenamento]: /pt-br/develop/nodejs/tutorials/web-app-with-storage/
  [Aplicativo da Web do Node.js com Armazenamento]: /pt-br/develop/nodejs/tutorials/web-site-with-storage/
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Filas, tópicos e assinaturas]: http://msdn.microsoft.com/pt-br/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SDK do Azure para o nó]: https://github.com/WindowsAzure/azure-sdk-for-node
