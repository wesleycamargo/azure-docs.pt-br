<properties
    pageTitle="Como usar as filas do Barramento de Serviço (.NET) | Microsoft Azure"
    description="Aprenda a usar as filas do barramento de serviço no Azure. Amostras de código escrito em C# usando a API .NET."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="07/02/2015"
    ms.author="sethm"/>

# Como usar as filas do Barramento de Serviço do Azure

Este artigo descreve como usar as filas do Barramento de Serviço. Os exemplos são escritos em código C# e utilizam a API .NET. Os cenários cobertos incluem criar filas e enviar e receber mensagens. Para obter mais informações sobre filas, consulte a seção [Próximas etapas](#Next-steps).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Configurar o aplicativo para usar o Barramento de Serviço

Quando você cria um aplicativo que usa o Barramento de Serviço, você deve adicionar uma referência ao assembly do Barramento de Serviço e incluir os espaços para nome correspondentes.

## Adicionar o pacote NuGet do Barramento de Serviço

O pacote **NuGet** de Barramento de Serviço é a maneira mais fácil de obter a API do Barramento de Serviço e de configurar seu aplicativo com todas as dependências de Barramento de serviço. A extensão do Visual Studio do NuGet facilita a instalação e a atualização de bibliotecas e ferramentas no Visual Studio e no Visual Studio Express. O pacote NuGet de Barramento de serviço é a maneira mais fácil de obter a API do Barramento de serviço e configurar seu aplicativo com todas as dependências de Barramento de serviço.

Para instalar o pacote do NuGet em seu aplicativo, proceda da seguinte maneira:

1.  No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** e clique em **Gerenciar Pacotes NuGet**.
2.  Pesquise "Barramento de Serviço" e selecione o item **Barramento de Serviço do Microsoft Azure**. Clique em **Instalar** para concluir a instalação e, em seguida, feche essa caixa de diálogo.

    ![][7]

Agora você está pronto para escrever código para o Barramento de Serviço.

## Como configurar uma cadeia de conexão do Barramento de Serviço

O Barramento de Serviço usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais. Você pode colocar a cadeia de conexão em um arquivo de configuração, em vez de embuti-la no código:

- Ao usar os Serviços de Nuvem do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração de serviço do Azure (arquivos .csdef e .cscfg).
- Ao usar sites do Azure ou Máquinas Virtuais do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração do .NET (por exemplo, o arquivo Web.config).

Nos dois casos, é possível recuperar a cadeia de conexão usando o método `CloudConfigurationManager.GetSetting`, conforme mostrado a seguir neste artigo.

### Configurando a cadeia de conexão ao usar Serviços de Nuvem

O mecanismo de configuração de serviço é exclusivo para projetos de Serviços de Nuvem do Azure e permite que você altere dinamicamente os parâmetros de configuração no Portal do Azure sem reimplantar o aplicativo. Por exemplo, adicione um rótulo `Setting` ao seu arquivo de definição de serviço (.csdef), conforme mostrado no próximo exemplo.

    <ServiceDefinition name="Azure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Em seguida, especifique valores no arquivo de configuração de serviço (.cscfg), conforme mostrado no próximo exemplo.

    <ServiceConfiguration serviceName="Azure1">
    ...
        <Role name="MyRole">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString"
                         value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
            </ConfigurationSettings>
        </Role>
    ...
    </ServiceConfiguration>

Use o nome da chave da Assinatura de Acesso Compartilhado (SAS) e os valores de chave recuperados do portal do Azure, conforme descrito na seção anterior.

### Configuração da cadeia de conexão ao usar sites ou Máquinas Virtuais do Azure

Ao usar sites ou Máquinas Virtuais, é recomendável usar o sistema de configuração .NET (por exemplo, **Web.config**). É possível armazenar a cadeia de conexão usando o elemento `<appSettings>`.

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </appSettings>
    </configuration>

Use o nome de SAS e os valores de chave recuperados do Portal do Azure, conforme descrito na seção anterior.

## Como criar uma fila

Você pode executar operações de gerenciamento de filas do Barramento de Serviço usando a [classe `NamespaceManager`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). A classe fornece métodos para criar, enumerar e excluir filas.

Esse exemplo constrói um objeto `NamespaceManager` usando a classe `CloudConfigurationManager` do Azure com uma cadeia de conexão que consiste no endereço básico de um namespace de serviço do Barramento de Serviço e as credenciais apropriadas de SAS com permissões para gerenciá-lo. Essa cadeia de conexão está no formato mostrado no exemplo a seguir.

    Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey

Use o exemplo a seguir, considerando os parâmetros de configuração da seção anterior.

    // Create the queue if it does not exist already.
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue("TestQueue");
    }

Há sobrecargas do método [`CreateQueue`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createqueue.aspx) que permitem ajustar as propriedades da fila (por exemplo, definir o valor padrão de "vida útil" a ser aplicado às mensagens enviadas para a fila). Essas configurações são aplicadas usando a classe [`QueueDescription`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx). O exemplo a seguir mostra como criar uma fila chamada "TestQueue" com um tamanho máximo de 5 GB e uma mensagem padrão de vida útil de 1 minuto.

    // Configure queue settings.
    QueueDescription qd = new QueueDescription("TestQueue");
    qd.MaxSizeInMegabytes = 5120;
    qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new queue with custom settings.
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue(qd);
    }

> [AZURE.NOTE]Você pode usar o método [`QueueExists`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.queueexists.aspx) em objetos [`NamespaceManager`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) para verificar se já existe uma fila com um nome especificado dentro de um namespace do serviço.

## Como enviar mensagens para uma fila

Para enviar uma mensagem a uma fila do Barramento de Serviço, seu aplicativo cria um objeto [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) usando a cadeia de conexão.

O código a seguir demonstra como criar um objeto [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) para a fila "TestQueue" que você acabou de criar usando a chamada à API [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx).

    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client =
        QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    Client.Send(new BrokeredMessage());

Mensagens enviadas a filas do Barramento de Serviço (e recebidas delas) são instâncias da classe [`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Os objetos `BrokeredMessage` têm um conjunto de propriedades padrão (como [`Label`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [`TimeToLive`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), um dicionário que é usado para manter as propriedades personalizadas específicas do aplicativo e um corpo de dados arbitrários do aplicativo. Um aplicativo pode definir o corpo da mensagem passando qualquer objeto serializável para o construtor do objeto [`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx), e o **DataContractSerializer** adequado será então usado para serializar o objeto. Como alternativa, é possível fornecer um **System.IO.Stream**.

O exemplo a seguir demonstra como enviar cinco mensagens de teste ao objeto [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) “TestQueue” obtido no exemplo de código anterior.

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body.
       BrokeredMessage message = new BrokeredMessage("Test message " + i);

       // Set some addtional custom app-specific properties.
       message.Properties["TestProperty"] = "TestValue";
       message.Properties["Message number"] = i;

       // Send message to the queue.
       Client.Send(message);
     }

As filas do Barramento de Serviço são compatíveis com um [tamanho máximo de mensagem de 256 Kb](service-bus-quotas.md) (o cabeçalho, que inclui as propriedades padrão e personalizadas do aplicativo, podem ter um tamanho máximo de 64 KB). Não há nenhum limite no número de mensagens mantidas em uma fila mas há uma capacidade do tamanho total das mensagens mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB. Se o particionamento estiver habilitado, o limite superior será maior. Para obter mais informações, consulte [Particionamento de Entidades de Mensagens](https://msdn.microsoft.com/library/azure/dn520246.aspx).

## Como receber mensagens de uma fila

A maneira recomendada para receber mensagens de uma fila é usar um objeto [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx). Os objetos `QueueClient` podem trabalhar de dois modos diferentes: [`ReceiveAndDelete` e `PeekLock`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Ao usar o modo **ReceiveAndDelete**, o recebimento é uma operação única; isto é, quando o Barramento de Serviço recebe uma solicitação de leitura de uma mensagem em uma fila, ele marca a mensagem como sendo consumida e a retorna ao aplicativo. **ReceiveAndDelete** é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

No modo **PeekLock** (que é o modo padrão), o recebimento se torna uma operação de duas etapas, o que torna possível o suporte a aplicativos que não toleram mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) na mensagem recebida. Quando o Barramento de Serviço vê a chamada `Complete`, ele marca a mensagem como sendo consumida e a remove da fila.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando o modo **PeekLock**. Para especificar um outro valor [`ReceiveMode`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx), você pode usar outra sobrecarga de [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx). Este exemplo usa o retorno de chamada [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) para processar mensagens assim que chegarem no **TestQueue**.

    string connectionString =
      CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
    QueueClient Client =
      QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    // Configure the callback options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    // Callback to handle received messages.
    Client.OnMessage((message) =>
    {
        try
        {
            // Process message from queue.
            Console.WriteLine("Body: " + message.GetBody<string>());
            Console.WriteLine("MessageID: " + message.MessageId);
            Console.WriteLine("Test Property: " +
            message.Properties["TestProperty"]);

            // Remove message from queue.
            message.Complete();
        }
            catch (Exception)
        {
            // Indicates a problem, unlock message in queue.
            message.Abandon();
        }
    }, options);

Este exemplo configura o retorno de chamada [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) usando um objeto [`OnMessageOptions`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx). [`AutoComplete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) é definido como **false** para habilitar o controle manual do momento de chamar [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) na mensagem recebida. [`AutoRenewTimeout`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) é definido como um minuto, o que faz com que o cliente aguarde um minuto por uma mensagem antes que a chamada expire e o cliente faça uma nova chamada para verificar se há mensagens. Esse valor de propriedade reduz o número de vezes que o cliente faz chamadas passíveis de cobrança que não recuperam mensagens.

## Como tratar falhas do aplicativo e mensagens ilegíveis

O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele chamará o método [`Abandon`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) na mensagem recebida (em vez do método [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Isso fará com que o Barramento de Serviço desbloqueie a mensagem na fila e disponibilize-a para que ela possa ser recebida novamente pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se houver falha no processamento da mensagem pelo aplicativo por expiração do tempo limite de bloqueio (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver falha do aplicativo após o processamento da mensagem, mas antes que a solicitação [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) seja gerada, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Isso é geralmente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez, mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é realizado usando a propriedade [`MessageId`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) da mensagem, que permanecerá constante nas tentativas da entrega.

## Próximas etapas

Agora que você já sabe as noções básicas das filas de Barramento de Serviço, siga estes links para saber mais.

-   Confira [Filas, tópicos e assinaturas][].
-   Compile um aplicativo de trabalho que envia e recebe mensagens de e para uma fila do Barramento de Serviço: [Tutorial do .NET do Sistema de Mensagens Agenciado do Barramento de Serviço].
-   Exemplos do Barramento de Serviço: baixe em [Exemplos do Azure][] ou confira [visão geral][].

  [What are Service Bus Queues]: #what-queues
  [Create a Service Namespace]: #create-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-creds
  [Configure Your Application to Use Service Bus]: #configure-app
  [How to: Set Up a Service Bus Connection String]: #set-up-connstring
  [How to: Configure your Connection String]: #config-connstring
  [How to: Create a Queue]: #create-queue
  [How to: Send Messages to a Queue]: #send-messages
  [How to: Receive Messages from a Queue]: #receive-messages
  [How to: Handle Application Crashes and Unreadable Messages]: #handle-crashes
  [Azure portal]: http://manage.windowsazure.com
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Filas, tópicos e assinaturas]: service-bus-queues-topics-subscriptions.md
  [Tutorial do .NET do Sistema de Mensagens Agenciado do Barramento de Serviço]: http://msdn.microsoft.com/library/azure/hh367512.aspx
  [Exemplos do Azure]: https://code.msdn.microsoft.com/windowsazure/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [visão geral]: service-bus-samples.md

<!---HONumber=Sept15_HO2-->