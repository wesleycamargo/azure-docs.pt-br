---
title: "Gatilhos e associações no Azure Functions"
description: "Saiba como usar gatilhos e associações no Azure Functions para conectar a execução de seu código a eventos online e a serviços baseados em nuvem."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, functions, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: a122271b5fdffd9db33a7dca5908e15f002041d7
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2018
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Conceitos de gatilhos e de associações do Azure Functions

Este artigo é uma visão geral conceitual dos gatilhos e associações no Azure Functions. Recursos que são comuns a todas as associações e todas as linguagens de programação compatíveis são descritos aqui.

## <a name="overview"></a>Visão geral

Um *gatilho* define como uma função é invocada. Uma função deve ter exatamente um gatilho. Gatilhos têm dados associados, que geralmente é o conteúdo que disparou a função.

*Associações* de entrada e saída fornecem uma maneira declarativa de se conectar aos dados de dentro do seu código. Associações são opcionais e uma função pode ter várias associações de entrada e saída. 

Gatilhos e associações permitem evitar que os detalhes dos serviços com os quais você está trabalhando sejam embutidos no código. Sua função recebe dados (por exemplo, o conteúdo de uma mensagem da fila) em parâmetros de função. Você envia dados (por exemplo, para criar uma mensagem da fila) usando o valor retornado da função, um parâmetro `out`, ou um [objeto coletor](functions-reference-csharp.md#writing-multiple-output-values).

Quando você desenvolve funções usando o Portal do Azure, gatilhos e associações são configuradas em um arquivo *function.json*. O portal fornece uma interface do usuário para essa configuração, mas você pode editar o arquivo diretamente, alterando para o **Editor avançado**.

Ao desenvolver funções usando o Visual Studio para criar uma biblioteca de classes, você configura gatilhos e associações decorando métodos e parâmetros com atributos.

## <a name="supported-bindings"></a>Associações com suporte

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Para obter informações sobre quais associações estão na visualização ou são aprovadas para o uso de produção, consulte [Idiomas com suporte](supported-languages.md).

## <a name="example-queue-trigger-and-table-output-binding"></a>Exemplo: gatilho de fila e associação de saída de tabela

Suponha que você deseja gravar uma nova linha no Armazenamento de Tabelas do Azure sempre que uma nova mensagem aparece no Armazenamento de Filas do Azure. Esse cenário pode ser implementado usando um gatilho do Armazenamento de Filas do Azure e uma associação de saída do Armazenamento de Tabelas do Azure. 

Aqui está um arquivo *function.json* para esse cenário. 

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

O primeiro elemento na matriz `bindings` é o gatilho do Armazenamento de Filas. As propriedades `type` e `direction` identificam o gatilho. A propriedade `name` identifica o parâmetro de função que receberá o conteúdo da mensagem de fila. O nome da fila a ser monitorada está em `queueName` e a cadeia de conexão está na configuração de aplicativo identificada por `connection`.

O segundo elemento na matriz `bindings` é a associação de saída do Armazenamento de Tabelas do Azure. As propriedades `type` e `direction` identificam a associação. A propriedade `name` especifica como a função fornecerá a nova linha da tabela, nesse caso, usando o valor retornado da função. O nome da tabela está em `tableName` e a cadeia de conexão está na configuração de aplicativo identificada por `connection`.

Para exibir e editar o conteúdo de *function.json* no Portal do Azure, clique na opção **Editor avançado** na guia **Integrar** da sua função.

> [!NOTE]
> O valor de `connection` é o nome de uma configuração de aplicativo que contém a cadeia de conexão, não a cadeia de conexão propriamente dita. Associações usam cadeias de conexão armazenadas em configurações de aplicativo para impor a melhor prática que dita que *function.json* não contêm segredos do serviço.

Aqui está o código de script C# que funciona com esse gatilho e essa associação. Observe que o nome do parâmetro que fornece o conteúdo da mensagem da fila é `order`; esse nome é necessário porque o valor da propriedade `name` em *function.json* é `order` 

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

O mesmo arquivo function.json pode ser usado com uma função JavaScript:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

Em uma biblioteca de classes, o mesmo gatilho e informações de associação &mdash; nomes de fila e tabela, contas de armazenamento, parâmetros de função para entrada e saída &mdash; é fornecido por atributos:

```csharp
 public static class QueueTriggerTableOutput
 {
     [FunctionName("QueueTriggerTableOutput")]
     [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
     public static Person Run(
         [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order, 
         TraceWriter log)
     {
         return new Person() {
                 PartitionKey = "Orders",
                 RowKey = Guid.NewGuid().ToString(),
                 Name = order["Name"].ToString(),
                 MobileNumber = order["MobileNumber"].ToString() };
     }
 }

 public class Person
 {
     public string PartitionKey { get; set; }
     public string RowKey { get; set; }
     public string Name { get; set; }
     public string MobileNumber { get; set; }
 }
```

## <a name="binding-direction"></a>Direção de associação

Todos os disparadores e associações têm uma propriedade `direction` no arquivo *function.json*:

- Para gatilhos, a direção sempre é `in`
- Associações de entrada e saída usam `in` e `out`
- Algumas associações dão suporte a uma direção especial `inout`. Se você usar `inout`, somente o **Editor avançado** estará disponível na guia **Integrar**.

Quando você usa [atributos em uma biblioteca de classes](functions-dotnet-class-library.md) para configurar associações e gatilhos, a direção é fornecida em um construtor de atributo ou inferida do tipo de parâmetro.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>Usando o tipo de retorno da função para retornar um único resultado

O exemplo anterior mostra como usar o valor retornado da função para fornecer saída para uma associação, que é especificada em *function.json* usando o valor especial `$return` para a propriedade `name`. (Isso é compatível apenas em linguagens que têm um valor retornado como script C#, JavaScript e F#.) Se uma função tiver várias associações de saída, use `$return` apenas para uma delas. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Os exemplos a seguir mostram como tipos de retorno são usados com associações de saída em script C#, JavaScript e F#.

```cs
// C# example: use method return value for output binding
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
// C# example: async method, using return value for output binding
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

```javascript
// JavaScript: return a value in the second parameter to context.done
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

```fsharp
// F# example: use return value for output binding
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="binding-datatype-property"></a>Associação da propriedade dataType

No .NET, use o tipo de parâmetro para definir o tipo de dados para os dados de entrada. Por exemplo, use `string` para associar ao texto de um gatilho de fila, uma matriz de bytes para ler como binário e um tipo personalizado para desserializar para um objeto POCO.

Para idiomas que são digitados dinamicamente como JavaScript, use a propriedade `dataType` no arquivo *function.json*. Por exemplo, para ler o conteúdo de uma solicitação HTTP em formato binário, defina `dataType` para `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Outras opções para `dataType` são `stream` e `string`.

## <a name="resolving-app-settings"></a>Resolvendo configurações de aplicativo

Como prática recomendada, os segredos e cadeias de conexão devem ser gerenciados usando configurações do aplicativo, em vez de arquivos de configuração. Isso limita o acesso a esses segredos e torna seguro armazenar *function.json* em um repositório de controle do código-fonte público.

Configurações do aplicativo também são úteis sempre que você desejar alterar a configuração com base no ambiente. Por exemplo, em um ambiente de teste, pode ser útil monitorar um contêiner de armazenamento de filas ou de blobs diferente.

Configurações do aplicativo são resolvidas sempre que um valor está entre sinais de porcentagem, como `%MyAppSetting%`. Observe que a propriedade `connection` dos gatilhos e associações é um caso especial e resolve automaticamente os valores de configurações do aplicativo. 

O exemplo a seguir é um gatilho do Armazenamento de Filas do Azure que usa uma configuração de aplicativo `%input-queue-name%` para definir a fila em que o gatilho é disparado.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Você pode usar a mesma abordagem em bibliotecas de classes:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-metadata-properties"></a>Propriedades de metadados de gatilho

Além do conteúdo dos dados fornecido por um gatilho (como a mensagem da fila que disparou uma função), vários gatilhos fornecem valores de metadados adicionais. Esses valores podem ser usados como parâmetros de entrada em C# e F# ou propriedades no objeto `context.bindings` em JavaScript. 

Por exemplo, um gatilho do Armazenamento de Filas do Azure é compatível com as seguintes propriedades:

* QueueTrigger – disparar o conteúdo da mensagem em caso de uma cadeia de caracteres válida
* DequeueCount
* ExpirationTime
* ID
* InsertionTime
* NextVisibleTime
* PopReceipt

Esses valores de metadados estão acessíveis nas propriedades do arquivo *function.json*. Por exemplo, suponha que você usa um gatilho de fila e que a mensagem da fila contém o nome de um blob que você deseja ler. No arquivo *function.json*, você pode usar a propriedade de metadados `queueTrigger` na propriedade `path` do blob, conforme mostrado no exemplo a seguir:

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

Detalhes de propriedades de metadados para cada gatilho são descritos no artigo de referência correspondente. Para obter um exemplo, consulte [metadados de gatilho de fila](functions-bindings-storage-queue.md#trigger---message-metadata). A documentação também está disponível na guia **Integrar** do portal, na seção **Documentação** abaixo da área de configuração de associação.  

## <a name="binding-expressions-and-patterns"></a>Padrões e expressões de associação

Um dos recursos mais poderosos de gatilhos e associações são as *expressões de associação*. Na configuração para uma associação, é possível definir expressões padrão que podem ser usadas em outras associações ou no seu código. Metadados de gatilho também podem ser usados em expressões de associação, conforme mostrado na seção anterior.

Por exemplo, suponha que você deseja redimensionar imagens no contêiner de armazenamento de blobs específico, semelhante ao modelo **Redimensionador de Imagem** na página **Nova Função** do Portal do Azure (veja o cenário **Amostras**). 

Essa é a definição *function.json*:

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Observe que o parâmetro `filename` é usado na definição do gatilho de blobs e na associação de saída de blobs. Esse parâmetro também pode ser usado no código de função.

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

A mesma capacidade de usar padrões e expressões de associação se aplica a atributos em bibliotecas de classes. Por exemplo, aqui está uma função de redimensionamento de imagem em uma biblioteca de classes:

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```

### <a name="create-guids"></a>Criar GUIDs

A expressão de associação `{rand-guid}` cria um GUID. O exemplo a seguir usa um GUID para criar um nome de blob exclusivo: 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>Hora atual

A expressão de associação `DateTime` resolve para `DateTime.UtcNow`.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties"></a>Associar a propriedades de entrada personalizadas

Expressões de associação também podem fazer referência a propriedades que são definidas no próprio conteúdo de gatilho. Por exemplo, pode ser útil associar dinamicamente um arquivos de armazenamento de blobs de um nome de arquivo fornecido em um webhook.

Por exemplo, o *function.json* a seguir usa uma propriedade chamada `BlobName` do conteúdo do gatilho:

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Para fazer isso em C# e F#, você deve definir um POCO que define os campos que serão desserializados no conteúdo do gatilho.

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

No JavaScript, a desserialização JSON é executada automaticamente e você pode usar as propriedades diretamente.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

## <a name="configuring-binding-data-at-runtime"></a>Configuração de associação de dados em tempo de execução

No C#, e em outras linguagens .NET, você pode usar um padrão de associação obrigatório, em vez de associações declarativas em *function.json* e atributos. A associação obrigatória é útil quando os parâmetros de associação precisam ser calculado no tempo de execução, em vez do tempo de design. Para obter mais informações, consulte [Associação em tempo de execução por meio de associações obrigatórias](functions-reference-csharp.md#imperative-bindings) na referência do desenvolvedor do C#.

## <a name="functionjson-file-schema"></a>Esquema de arquivo function.json

O esquema de arquivo *function.json* está disponível em [http://json.schemastore.org/function](http://json.schemastore.org/function).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre uma associação específica, consulte os artigos a seguir:

- [HTTP e webhooks](functions-bindings-http-webhook.md)
- [Timer](functions-bindings-timer.md)
- [Armazenamento de filas](functions-bindings-storage-queue.md)
- [Armazenamento de Blobs](functions-bindings-storage-blob.md)
- [Armazenamento de tabelas](functions-bindings-storage-table.md)
- [Hub de Evento](functions-bindings-event-hubs.md)
- [Barramento de Serviço](functions-bindings-service-bus.md)
- [Banco de dados do Azure Cosmos](functions-bindings-cosmosdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Hubs de Notificação](functions-bindings-notification-hubs.md)
- [Aplicativos Móveis](functions-bindings-mobile-apps.md)
- [Arquivo externo](functions-bindings-external-file.md)
