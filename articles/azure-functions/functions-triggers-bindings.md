---
title: "Gatilhos e associações do Azure Functions | Microsoft Docs"
description: Entenda como usar gatilhos e bindings no Azure Functions.
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, functions, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"
ms.assetid: cbc7460a-4d8a-423f-a63e-1cd33fef7252
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: d809636cae48dd0ccca4c99370f41996430d89e4
ms.openlocfilehash: b5d8d38f03514d89bf6c0b5e36adf0379f1bef1a


---
# <a name="azure-functions-triggers-and-bindings-developer-reference"></a>Referências de gatilhos e de bindings do Azure Functions para desenvolvedores
Este tópico fornece referência geral para associações e gatilhos. Ele inclui alguns dos recursos de associação avançada e sintaxe com suporte por todos os tipos de associação.  

Se você estiver procurando por informações detalhadas sobre configuração e codificação de um tipo específico de associação ou gatilho, talvez queira clicar em um dos gatilhos ou associações listadas abaixo:

[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigos pressupõem que você já tenha lido a [referência do desenvolvedor do Azure Functions](functions-reference.md) e os artigos de referência do [C#](functions-reference-csharp.md), [F#](functions-reference-fsharp.md) ou [Node.js](functions-reference-node.md) para desenvolvedores.

## <a name="overview"></a>Visão geral
Os gatilhos são respostas de eventos usadas para disparar seu código personalizado. Eles permitem que você responda a eventos em toda a plataforma do Azure ou localmente. As associações representam os metadados necessários usados para conectar seu código ao gatilho desejado ou aos dados de entrada ou saída associados. O arquivo *function.json* para cada função contém todas as associações relacionadas. Não há limite para o número de associações de entrada e saídas que uma função pode ter. No entanto, há suporte apenas para uma única associação de gatilho em cada função.  

Para obter uma ideia melhor das diferentes associações que você pode integrar a seu aplicativo Azure Functions, consulte a tabela a seguir.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Para entender melhor gatilhos e associações em geral, suponha que você deseja executar algum código para processar um novo item colocado em uma fila de armazenamento do Azure. O Azure Functions fornece um gatilho de fila do Azure para dar suporte a isso. Você precisaria das informações a seguir para monitorar a fila:

* A conta de armazenamento na qual a fila existe.
* O nome da fila.
* Um nome de variável que seu código usaria para se referir ao novo item que foi colocado na fila.  

Uma associação de gatilho de fila contém essas informações para uma função do Azure. Aqui está um exemplo *function.json* que contém uma associação de gatilho de fila. 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ],
  "disabled": false
}
```

Seu código pode enviar diferentes tipos de saída, dependendo de como o novo item de fila é processado. Por exemplo, talvez você queira gravar um novo registro em uma tabela de armazenamento do Azure.  Para fazer isso, você pode configurar uma associação de saída para uma tabela de armazenamento do Azure. Aqui está um exemplo *function.json* que inclui uma associação de saída de tabela de armazenamento que pode ser usada com um gatilho de fila. 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "name": "myNewUserTableBinding",
      "tableName": "newUserTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A função C# a seguir responde a um novo item que está sendo colocado em fila e grava uma nova entrada de usuário em uma tabela de armazenamento do Azure.

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;

public static async Task Run(string myNewUserQueueItem, IAsyncCollector<Person> myNewUserTableBinding, 
                                TraceWriter log)
{
    // In this example the queue item is a JSON string representing an order that contains the name, 
    // address and mobile number of the new customer.
    dynamic order = JsonConvert.DeserializeObject(myNewUserQueueItem);

    await myNewUserTableBinding.AddAsync(
        new Person() { 
            PartitionKey = "Test", 
            RowKey = Guid.NewGuid().ToString(), 
            Name = order.name,
            Address = order.address,
            MobileNumber = order.mobileNumber }
        );
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string Address { get; set; }
    public string MobileNumber { get; set; }
}
```

Para obter mais exemplos de código e informações mais específicas sobre os tipos de armazenamento do Azure com suporte, consulte [Gatilhos e associações de Azure Functions para armazenamento do Azure](functions-bindings-storage.md).

Para usar os recursos mais avançados de associação no portal do Azure, clique na opção **Editor avançado** na guia **Integrar** da sua função. O editor avançado permite que você edite o *function.json* diretamente no portal.

## <a name="random-guids"></a>GUIDs aleatórios
O Azure Functions fornece uma sintaxe para gerar GUIDs aleatórios com suas associações. A seguinte sintaxe de associação gravará a saída para um novo BLOB com um nome exclusivo em um contêiner de armazenamento do Azure: 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```


## <a name="returning-a-single-output"></a>Retornando um único resultado
Em casos em que o código de função retorna uma única saída, você pode usar uma associação de saída denominada `$return` para manter uma assinatura de função mais natural em seu código. Isso só pode ser usado com linguagens que dão suporte a um valor retornado (C#, Node.js, F#). A associação seria semelhante à associação de saída de blob a seguir usada com um gatilho de fila.

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "name": "input",
      "direction": "in",
      "queueName": "test-input-node"
    },
    {
      "type": "blob",
      "name": "$return",
      "direction": "out",
      "path": "test-output-node/{id}"
    }
  ]
}
```

O código C# a seguir retorna a saída mais naturalmente sem usar um parâmetro `out` na assinatura de função.

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

Exemplo de Async:

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```


Essa mesma abordagem é demonstrada abaixo com Node.js.

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

Exemplo de F# fornecido abaixo.

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

Isso também pode ser usado com vários parâmetros de saída, especificando uma única saída com `$return`.

## <a name="resolving-app-settings"></a>Resolvendo configurações de aplicativo
É uma prática recomendada armazenar informações confidenciais como parte do ambiente de tempo de execução usando configurações de aplicativo. Ao manter informações confidenciais fora dos arquivos de configuração do aplicativo, você limita a exposição quando um repositório público é usado para armazenar arquivos de aplicativo.  

O tempo de execução do Azure Functions resolve as configurações de aplicativo para valores quando o nome de configuração de aplicativo é circunscrito em sinais de porcentagem, `%your app setting%`. A [associação do Twilio](functions-bindings-twilio.md) a seguir usa uma configuração de aplicativo chamada `TWILIO_ACCT_PHONE` para o campo `from` da associação. 

```json
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received Node.js",
  "direction": "out"
},
```



## <a name="parameter-binding"></a>Associação de parâmetro
Em vez de uma definição de configuração estática para as propriedades de associação de saída, você pode definir as configurações para ser associado dinamicamente aos dados que fazem parte da associação de entrada do gatilho. Considere um cenário no qual novos pedidos são processados usando uma fila de armazenamento do Azure. Cada novo item de fila é uma cadeia de caracteres JSON que contém pelo menos as seguintes propriedades:

```json
{
  "name" : "Customer Name",
  "address" : "Customer's Address".
  "mobileNumber" : "Customer's mobile number in the format - +1XXXYYYZZZZ."
}
```

Talvez você queira enviar uma mensagem de texto SMS ao cliente usando sua conta do Twilio como uma atualização de que o pedido foi recebido.  Você pode configurar o campo `body` e `to` da sua associação de saída do Twilio para ser vinculada dinamicamente a `name` e `mobileNumber` que faziam parte da entrada da seguinte maneira.

```json
{
  "name": "myNewOrderItem",
  "type": "queueTrigger",
  "direction": "in",
  "queueName": "queue-newOrders",
  "connection": "orders_STORAGE"
},
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received",
  "direction": "out"
},
```

Agora o código de função só precisa inicializar o parâmetro de saída da seguinte maneira. Durante a execução, as propriedades de saída serão vinculadas aos dados de entrada desejados.

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using System.Threading.Tasks;
using Newtonsoft.Json;
using Twilio;

public static async Task<SMSMessage> Run(string myNewOrderItem, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myNewOrderItem}");

    dynamic order = JsonConvert.DeserializeObject(myNewOrderItem);    

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // The following isn't needed since we use parameter binding for this
    //string msg = "Hello " + order.name + ", thank you for your order.";
    //smsText.Body = msg;
    //smsText.To = order.mobileNumber;

    return smsText;
}
```

Node.js:

```javascript
module.exports = function (context, myNewOrderItem) {    
    context.log('Node.js queue trigger function processed work item', myNewOrderItem);    

    // No need to set the properties of the text, we use parameters in the binding. We do need to 
    // initialize the object.
    var smsText = {};    

    context.done(null, smsText);
}
```

## <a name="advanced-binding-at-runtime-imperative-binding"></a>Associação avançada em tempo de execução (associação obrigatória)

O padrão de associação de entrada e saída com a *function.json* é chamado associação [*declarativa*](https://en.wikipedia.org/wiki/Declarative_programming), em que a associação é definida pela declaração do JSON. No entanto, é possível usar a associação [obrigatória](https://en.wikipedia.org/wiki/Imperative_programming). Com esse padrão, associe a qualquer número de associações de entrada e saída com suporte imediatamente no código da função.
Talvez você precise da associação obrigatória em casos em que o cálculo do caminho de associação ou de outras entradas precise ocorrer em tempo de execução na função, em vez de em tempo de design. 

Para realizar a associação obrigatória, faça o seguinte:

- **Não** inclua uma entrada em *function.json* para as associações obrigatórias desejadas.
- Passe um parâmetro de entrada [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) ou [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs). 
- Use o padrão de C# a seguir para realizar a associação de dados.

        using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
        {
                ...
        }

em que `BindingTypeAttribute` é o atributo do .NET que define a associação e `T` é o tipo de entrada ou saída com suporte nesse tipo de associação. `T` também não pode ser um tipo de parâmetro `out` (como `out JObject`). Por exemplo, a associação de saída de tabela dos Aplicativos Móveis dá suporte a [seis tipos de saída](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), mas você só pode usar [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) para `T`.
    
O código de exemplo a seguir cria uma [associação de saída do Armazenamento de Blobs](functions-bindings-storage-blob.md#storage-blob-output-binding) com o caminho do blob definido em tempo de execução e grava uma cadeia de caracteres no blob.

        using Microsoft.Azure.WebJobs;
        using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;
        
        public static async Task Run(string input, Binder binder)
        {
                using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
                {
                        writer.Write("Hello World!!");
                }
        }

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) define a associação de entrada ou saída do [Armazenamento de Blobs](functions-bindings-storage-blob.md) e [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) é um tipo de associação de saída com suporte.
No estado em que se encontra, o código obtém a configuração de aplicativo padrão para a cadeia de conexão da conta de armazenamento (`AzureWebJobsStorage`). Especifique uma configuração de aplicativo personalizada a ser usada adicionando [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando a matriz de atributo para `BindAsync<T>()`. Por exemplo,

        using Microsoft.Azure.WebJobs;
        using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;
        
        public static async Task Run(string input, Binder binder)
        {
                var attributes = new Attribute[]
                {
                        new BlobAttribute("samples-output/path"),
                        new StorageAccountAttribute("MyStorageAccount")
                };
                using (var writer = await binder.BindAsync<TextWriter>(attributes))
                {
                        writer.Write("Hello World!");
                }
        }

A tabela a seguir mostra o atributo do .NET correspondente a ser usado para cada tipo de associação e a qual pacote será feita a referência.

| Associação | Atributo | Adicionar referência |
|------|------|------|
| DocumentDB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.DocumentDB"` |
| Hubs de Eventos | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Aplicativos Móveis | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Hubs de Notificação | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Barramento de Serviço | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| Fila de armazenamento | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Armazenamento de blobs | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Tabela de armazenamento | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions"` |



## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os recursos a seguir:

* [Testando uma função](functions-test-a-function.md)
* [Dimensionar uma função](functions-scale.md)




<!--HONumber=Nov16_HO3-->


