<properties 
   pageTitle="Guia de programação para Hubs de Eventos do Azure | Microsoft Azure"
   description="Descreve a programação com Hubs de Eventos do Azure usando o SDK do Azure .NET."
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="01/26/2016"
   ms.author="sethm" />

# Guia de programação dos Hubs de Eventos

Este tópico descreve a programação com Hubs de Eventos do Azure usando o SDK do Azure .NET. Ele supõe uma compreensão preliminar de Hubs de Eventos. Para obter uma visão geral conceitual dos Hubs de Eventos, confira [Visão geral dos Hubs de Eventos](event-hubs-overview.md).

## Editores de eventos

O envio de eventos para um Hub de Eventos é realizado usando HTTP POST ou via conexão AMQP 1.0. A escolha de qual usar e quando depende do cenário específico que está sendo resolvido. Conexões do AMQP 1.0 são medidas como conexões negociadas no Barramento de Serviço e são mais apropriadas em cenários com volumes de mensagem frequentemente mais altos e requisitos de latência mais baixos, já que fornecem um canal de mensagens persistente.

Hubs de Eventos são criados e gerenciados usando a classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Ao usar APIs gerenciadas pelo .NET, as principais construções para publicar dados em Hubs de Eventos são as classes [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) e [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx). [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) fornece o canal de comunicação AMQP pelo qual os eventos são enviados ao Hub de Eventos. A classe [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) representa um evento e é usada para publicar mensagens em um Hub de Eventos. Essa classe inclui o corpo, alguns metadados e informações de cabeçalho sobre o evento. Outras propriedades são adicionadas ao objeto [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) à medida que ele passa por um Hub de Eventos.

## Introdução

As classes do .NET que são compatíveis com Hubs de Eventos são fornecidos no assembly Microsoft.ServiceBus.dll. A maneira mais fácil de consultar a API do Barramento de Serviço e configurar seu aplicativo com todas as dependências do Barramento de Serviço é baixar o [pacote NuGet do Barramento de Serviço](https://www.nuget.org/packages/WindowsAzure.ServiceBus). Como alternativa, você pode usar o [Console do Gerenciador de Pacotes](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) no Visual Studio. Digite o comando a seguir na janela [Console do Gerenciador de Pacotes](http://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```
Install-Package WindowsAzure.ServiceBus
```

## Criar um Hub de Eventos

Você pode usar a classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) para criar Hubs de Eventos. Por exemplo:

```
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

Na maioria dos casos, é recomendável que você use os métodos [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx) para evitar gerar exceções se o serviço for reiniciado. Por exemplo:

```
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

Todas as operações de criação de Hubs de Eventos, inclusive [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx), exigem permissões **Gerenciar** no namespace em questão. Se você quiser limitar as permissões de seus aplicativos de consumidor ou publicador, pode evitar essas chamadas de criação de operação no código de produção, ao usar credenciais com permissões limitadas.

A classe [EventHubDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubdescription.aspx) contém detalhes sobre um Hub de Eventos, incluindo regras de autorização, intervalo de retenção de mensagem, IDs de partição, status e caminho. Você pode usar essa classe para atualizar os metadados em um Hub de Eventos.

## Criar um cliente de Hubs de Eventos

A classe principal para interagir com Hubs de Eventos é [Microsoft.ServiceBus.Messaging.EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx). Essa classe fornece recursos de remetente e destinatário. Você pode criar uma instância dessa classe usando o método [Criar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx), conforme mostrado no exemplo a seguir.

```
var client = EventHubClient.Create(description.Path);
```

Esse método usa as informações de conexão do Barramento de Serviço no arquivo App.config, na seção `appSettings`. Para obter um exemplo do XML `appSettings` usado para armazenar informações de conexão de Barramento de Serviço, consulte a documentação do método [Microsoft.ServiceBus.Messaging.EventHubClient.Create(System.String)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx).

Outra opção é criar o cliente por meio de uma cadeia de conexão. Essa opção funciona bem ao usar funções de trabalho do Azure, porque você pode armazenar a cadeia de caracteres nas propriedades de configuração do trabalho. Por exemplo:

```
EventHubClient.CreateFromConnectionString("your_connection_string");
```

A cadeia de conexão será no mesmo formato em que aparece no arquivo App.config dos métodos anteriores:

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=Manage;SharedAccessKey=[key]
```

Por fim, também é possível criar um objeto [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) de uma instância [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx), conforme mostrado no exemplo a seguir.

```
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

É importante observar que outros objetos [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) criados em uma instância de fábrica de mensagens vão reutilizar a mesma conexão TCP subjacente. Portanto, esses objetos têm um limite, no lado do cliente, na taxa de transferência. O método [Criar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx) reutiliza uma única fábrica de mensagens única. Se você precisa de uma taxa de transferência muito alta de um único remetente, é possível criar diversas fábricas de mensagens e um objeto [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) de cada fábrica de mensagens.

## Enviar eventos a um Hub de Eventos

Você envia eventos a um Hub de Eventos criando uma instância [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) e enviando-a pelo método [Send](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.send.aspx). Esse método usa um único parâmetro [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) da instância e o envia de forma síncrona para um Hub de Eventos.

## Serialização de evento

A classe [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) tem [quatro construtores sobrecarregados](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.eventdata.aspx) que usam uma variedade de parâmetros, como um objeto e serializador, uma matriz de bytes ou um fluxo. Também é possível criar uma instância da classe [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) e definir o fluxo de corpo posteriormente. Ao usar o JSON com [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx), você pode usar **Encoding.UTF8.GetBytes()** para recuperar a matriz de bytes para uma cadeia de caracteres codificada em JSON.

## Chave de partição

A classe [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) tem uma propriedade [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) que permite que o remetente especifique um valor de hash para produzir uma atribuição de partição. Usar uma chave de partição garante que todos os eventos com a mesma chave sejam enviados para a mesma partição no Hub de Eventos. Chaves de partição comuns incluem IDs de sessão de usuário e IDs de remetente exclusivo. A propriedade [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) é opcional e pode ser fornecida ao usar os métodos [Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) ou [Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx). Se você não fornecer um valor para [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx), os eventos enviados serão distribuídos para partições que usam um modelo round-robin.

## Operações de envio de eventos em lote

Envio de eventos em lotes pode aumentar significativamente a taxa de transferência. O método [SendBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) usa um parâmetro **IEnumerable** do tipo [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) e envia o lote inteiro como uma operação atômica para o Hub de Eventos.

```
public void SendBatch(IEnumerable<EventData> eventDataList);
```

Observe que um único lote não deve exceder o limite de 256 KB de um evento. Além disso, cada mensagem no lote usa a mesma identidade do publicador. É responsabilidade do remetente garantir que o lote não exceda o tamanho máximo do evento. Em caso afirmativo, um erro **Enviar** do cliente é gerado.

## Enviar de forma assíncrona e em escala

Você também pode enviar eventos para um Hub de Eventos de forma assíncrona. O envio de forma assíncrona pode aumentar a taxa em que um cliente é capaz de enviar eventos. Os métodos [Enviar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.send.aspx) e [SendBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) estão disponíveis nas versões assíncronas que retornam um objeto [Tarefa](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx). Embora essa técnica possa aumentar a taxa de transferência, ela também pode fazer com que o cliente continue a enviar eventos mesmo enquanto está sendo limitado pelo serviço de Hubs de Eventos e pode resultar em falhas por parte do cliente ou mensagens perdidas, se não for implementada corretamente. Além disso, você pode usar a propriedade [RetryPolicy](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.cliententity.retrypolicy.aspx) no cliente para controlar as opções de novas tentativas do cliente.

## Criar um remetente de partição

Embora seja mais comum enviar eventos a um Hub de Eventos com uma chave de partição, em alguns casos, é conveniente enviar eventos diretamente a uma determinada partição. Por exemplo:

```
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

[CreatePartitionedSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.createpartitionedsender.aspx) retorna um objeto [EventHubSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubsender.aspx) que você pode usar para publicar eventos em uma partição específica do Hub de Eventos.

## Consumidores de evento

O Hubs de Eventos tem dois modelos principais para consumo de eventos: receptores diretos e abstrações de nível superior, como [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx). Receptores diretos são responsáveis por sua própria coordenação de acesso a partições em um grupo de consumidores.

### Consumidor direto

A maneira mais direta de ler por meio de uma partição em um grupo de consumidores é usar a classe [EventHubReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubreceiver.aspx). Para criar uma instância dessa classe, você deve usar uma instância da classe [EventHubConsumerGroup](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubconsumergroup.aspx). No exemplo de código a seguir, a ID da partição deve ser especificada ao criar o destinatário para o grupo de consumidores.

```
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

O método [CreateReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubconsumergroup.createreceiver.aspx) tem várias sobrecargas que facilitam o controle sobre o leitor que está sendo criado. Esses métodos incluem a especificação de um deslocamento como uma cadeia de caracteres ou um carimbo de data/hora e a capacidade de especificar se deseja incluir o deslocamento especificado no fluxo retornado ou iniciar depois dele. Depois de criar o receptor, você pode iniciar o recebimento de eventos no objeto retornado. O método [Receber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubreceiver.receive.aspx) tem quatro sobrecargas que controlam os parâmetros da operação de recebimento, como tamanho de lote e tempo de espera. Você pode usar as versões assíncronas desses métodos para aumentar a taxa de transferência de um consumidor. Por exemplo:

```
bool receive = true;
string myOffset;
while(receive)
{
    var message = receiver.Receive();
    myOffset = message.Offset;
    string body = Encoding.UTF8.GetString(message.GetBytes());
    Console.WriteLine(String.Format("Received message offset: {0} \nbody: {1}", myOffset, body));
}
```

Em relação a uma partição específica, as mensagens são recebidas na ordem em que foram enviadas ao Hub de Eventos. O deslocamento é um token de cadeia de caracteres usado para identificar uma mensagem em uma partição.

Observe que uma única partição em um grupo de consumidores não pode ter mais de 5 leitores simultâneos conectados em algum momento. Conforme os leitores são conectados ou desconectados, suas sessões podem permanecer ativas por vários minutos antes que o serviço reconheça que foram desconectados. Durante esse tempo, a reconexão a uma partição pode falhar. Para obter um exemplo completo de como gravar um destinatário direto para Hubs de Eventos, consulte o exemplo [Receptores diretos de Hubs de Eventos no Barramento de Serviço](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6).

### Host do processador de eventos

A classe [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) processa dados de Hubs de Eventos. Você deve usar essa implementação ao criar os leitores de evento na plataforma .NET. [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) fornece um ambiente de tempo de execução seguro, thread-safe, de vários processos, para implementações de processador de eventos, que também fornece gerenciamento de concessão de ponto de verificação e partição.

Para usar a classe [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx), você pode implementar [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx). Essa interface contém três métodos:

- [OpenAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.openasync.aspx)

- [CloseAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.closeasync.aspx)

- [ProcessEventsAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.processeventsasync.aspx)

Para iniciar o processamento de eventos, crie uma instância de [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) fornecendo os parâmetros adequados para seu Hub de Eventos. Em seguida, chame [RegisterEventProcessorAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.registereventprocessorasync.aspx) para registrar sua implementação de [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx) no tempo de execução. Nesse ponto, o host tentará adquirir uma concessão em todas as partições do Hub de Eventos usando um algoritmo "ávido". Essas concessões vão durar por determinado período e, em seguida, devem ser renovadas. Como novos nós, as instâncias de trabalho, nesse caso, ficam online, colocam reservas de concessão e com o tempo, a carga é trocada entre os nós, à medida que cada uma tenta adquirir mais concessões.

![Host do processador de eventos](./media/event-hubs-programming-guide/IC759863.png)

Ao longo do tempo, é estabelecido um equilíbrio. Essa funcionalidade dinâmica permite que o dimensionamento automático com base em CPU seja aplicado aos consumidores de ampliação e redução de escala. Como os Hubs de Eventos não têm um conceito direto de contagem de mensagens, a utilização média da CPU é, geralmente, o melhor mecanismo para medir back end ou escala do consumidor. Se os publicadores começam a publicar mais eventos do que os consumidores podem processar, o aumento de CPU nos consumidores pode ser usado para provocar escala automática na contagem de instâncias de trabalho.

A classe [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) também implementa um mecanismo de verificação com base em armazenamento do Azure. Esse mecanismo armazena o deslocamento por base de partição, de forma que cada consumidor pode determinar qual foi o último ponto de verificação do consumidor anterior. Conforme as partições fazem transição entre os nós por meio de concessões, esse é o mecanismo de sincronização que facilita a alternância de carga.

## Revogação do publicador

Além dos recursos avançados de tempo de execução do [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx), os Hubs de Eventos permitem a revogação do editor para impedir que editores específicos enviem eventos a um Hub de Eventos. Esses recursos serão especialmente úteis se o token do editor tiver sido comprometido ou uma atualização de software estiver causando um comportamento inadequado. Nessas situações, a identidade do editor, que faz parte do token SAS, pode ser impedida de publicar eventos.

Para obter mais informações sobre revogação de publicador e como enviar para Hubs de Eventos como um publicador, consulte [Publicação segura em larga escala de Hubs de Eventos no Barramento de Serviço](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab).

## Próximas etapas

Para saber mais sobre os cenários de Hubs de Eventos, consulte estes links:

- [Visão geral de API de Hubs de Eventos](event-hubs-api-overview.md)
- [Visão geral de Hubs de Evento](event-hubs-overview.md)
- [Exemplos de código de Hubs de Eventos](http://code.msdn.microsoft.com/site/search?query=event hub&f[0].Value=event hub&f[0].Type=SearchText&ac=5)
- [Referência de API do host de processador de eventos](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx)

<!---HONumber=AcomDC_0128_2016-->