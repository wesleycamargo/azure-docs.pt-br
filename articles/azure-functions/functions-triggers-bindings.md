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
ms.date: 02/07/2018
ms.author: glenga
ms.openlocfilehash: f43132beb0abae3d4bdf0f538de1b437e6099822
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
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

## <a name="register-binding-extensions"></a>Registrar as extensões de associação

Na versão 2.x do tempo de execução do Azure Functions, você deve registrar explicitamente a [extensões de associação](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/README.md) usadas em seu aplicativo de funções. 

As extensões são entregues como pacotes do NuGet, onde o nome do pacote normalmente começa com [microsoft.azure.webjobs.extensions](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions).  Como instalar e registrar as extensões de associação depende de como você desenvolve suas funções: 

+ [Localmente em C# usando o Visual Studio ou o VS Code](#precompiled-functions-c)
+ [Localmente usando as ferramentas básicas do Azure Functions](#local-development-azure-functions-core-tools)
+ No [portal do Azure](#azure-portal-development) 

Há um conjunto principal de associações na versão 2. x que não são fornecidos como extensões. Não é necessário registrar extensões para os gatilhos e as associações a seguir: HTTP, o timer e o Armazenamento do Azure. 

Para obter informações sobre como configurar um aplicativo de funções para usar a versão 2.x do tempo de execução do Functions, confira [Como direcionar versões do tempo de execução do Azure Functions](set-runtime-version.md). Versão 2. x do tempo de execução do Functions está atualmente em versão prévia. 

As versões do pacote mostradas nesta seção são fornecidas somente como exemplos. Verifique o [site NuGet.org](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions) para determinar qual versão de uma determinada extensão é exigida por outras dependências em seu aplicativo de função.    

###  <a name="local-c-development-using-visual-studio-or-vs-code"></a>Desenvolvimento do C# local usando o Visual Studio ou VS Code 

Quando você usa o Visual Studio ou o Visual Studio Code para desenvolver localmente funções em C#, basta adicionar o pacote do NuGet para a extensão. 

+ **Visual Studio**: Use as ferramentas do Gerenciador de Pacotes do NuGet. O seguinte comendo [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) instala a extensão do Azure Cosmos DB do Console do Gerenciador de Pacotes:

    ```
    Install-Package Microsoft.Azure.WebJobs.Extensions.CosmosDB -Version 3.0.0-beta6 
    ```
+ **Visual Studio Code**: você pode instalar os pacotes do prompt de comando usando o comando [dotnet Adicionar pacote](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) na CLI .NET, da seguinte maneira:

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version 3.0.0-beta6 
    ```

### <a name="local-development-azure-functions-core-tools"></a>Ferramentas básicas do Azure Functions para desenvolvimento local

[!INCLUDE [Full bindings table](../../includes/functions-core-tools-install-extension.md)]

### <a name="azure-portal-development"></a>Desenvolvimento do portal do Azure

Quando você cria uma função ou adiciona uma associação a uma função existente, você será solicitado quando a extensão para o gatilho ou a associação que estiver sendo adicionada exigir o registro.   

Depois que um aviso é exibido para a extensão específica que está sendo instalada, clique em **Instalar** para registrar a extensão. Você só precisa instalar cada extensão uma vez para um aplicativo de função determinada. 

>[!Note] 
>O processo de instalação no portal pode levar até 10 minutos em um plano de consumo.

## <a name="example-trigger-and-binding"></a>Associação e gatilho de exemplo

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

O primeiro elemento na matriz `bindings` é o gatilho do Armazenamento de Filas. As propriedades `type` e `direction` identificam o gatilho. A propriedade `name` identifica o parâmetro de função que recebe o conteúdo da mensagem de fila. O nome da fila a ser monitorada está em `queueName` e a cadeia de conexão está na configuração de aplicativo identificada por `connection`.

O segundo elemento na matriz `bindings` é a associação de saída do Armazenamento de Tabelas do Azure. As propriedades `type` e `direction` identificam a associação. A propriedade `name` especifica como a função fornece a nova linha da tabela, nesse caso, usando o valor retornado da função. O nome da tabela está em `tableName` e a cadeia de conexão está na configuração de aplicativo identificada por `connection`.

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

Em uma biblioteca de classes, o mesmo gatilho e informações de associação &mdash; nomes de fila e tabela, contas de armazenamento, parâmetros de função para entrada e saída &mdash; é fornecido por atributos em vez de um arquivo function.json. Aqui está um exemplo:

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

## <a name="using-the-function-return-value"></a>Usar o valor de retorno de função

Em linguagens que têm um valor de retorno, você pode associar uma associação de saída para o valor de retorno:

* Em uma biblioteca de classe C#, aplique o atributo de associação de saída para o valor de retorno do método.
* Em outras linguagens, defina a propriedade `name` em *function.json* para `$return`.

Se você precisar gravar mais de um item, use um [objeto coletor](functions-reference-csharp.md#writing-multiple-output-values) em vez do valor de retorno. Se houver várias associações de saída, use o valor de retorno de apenas um deles.

Consulte o exemplo específico a um idioma:

* [C#](#c-example)
* [Script do C# (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Exemplo de C#

Aqui está o código C# que usa o valor de retorno para uma associação de saída, seguido por um exemplo de assíncrono:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="c-script-example"></a>Exemplo 2 de C# script

Aqui está a associação de saída no arquivo *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Aqui está o código de script C#, seguido por um exemplo de assíncrono:

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="f-example"></a>Exemplo de F#

Aqui está a associação de saída no arquivo *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

O código F# é o seguinte:

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

### <a name="javascript-example"></a>Exemplo de JavaScript

Aqui está a associação de saída no arquivo *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

No JavaScript, o valor de retorno fica no segundo parâmetro para `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
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

## <a name="binding-expressions-and-patterns"></a>Padrões e expressões de associação

Um dos recursos mais poderosos de gatilhos e associações são as *expressões de associação*. No arquivo *function.json* e em parâmetros de função e de código, você pode usar expressões que são resolvidas para valores de várias fontes.

A maioria das expressões são identificadas, encapsulando-as entre chaves. Por exemplo, em uma função de gatilho de fila, `{queueTrigger}` resolve para o texto de mensagem da fila. Se a propriedade `path` para uma associação de saída de blob é `container/{queueTrigger}` e a função é disparada por uma mensagem da fila `HelloWorld`, um blob denominado `HelloWorld` é criado.

Tipos de expressões de associação

* [Configurações do aplicativo](#binding-expressions---app-settings)
* [Nome do arquivo de gatilho](#binding-expressions---trigger-file-name)
* [Gatilho metadados](#binding-expressions---trigger-metadata)
* [Cargas JSON](#binding-expressions---json-payloads)
* [Novo GUID](#binding-expressions---create-guids)
* [Data e hora atuais](#binding-expressions---current-time)

### <a name="binding-expressions---app-settings"></a>Expressões de associação - configurações do aplicativo

Como prática recomendada, os segredos e cadeias de conexão devem ser gerenciados usando configurações do aplicativo, em vez de arquivos de configuração. Isso limita o acesso a esses segredos e torna seguro armazenar arquivos como *function.json* em repositórios de controle do código-fonte público.

Configurações do aplicativo também são úteis sempre que você desejar alterar a configuração com base no ambiente. Por exemplo, em um ambiente de teste, pode ser útil monitorar um contêiner de armazenamento de filas ou de blobs diferente.

Expressões de associação de configuração do aplicativo são identificadas diferentemente de outras expressões de associação: elas são dispostas em sinais de porcentagem em vez de chaves. Por exemplo, se o caminho de associação de saída de blob é `%Environment%/newblob.txt` e o `Environment` valor de configuração do aplicativo é `Development`, um blob será criado no contêiner `Development`.

Quando uma função é executada localmente, os valores de configuração do aplicativo são provenientes do arquivo *local.settings.json*.

Observe que a propriedade `connection` dos gatilhos e associações é um caso especial e resolve automaticamente os valores de configurações do aplicativo, sem os sinais de porcentagem. 

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

### <a name="binding-expressions---trigger-file-name"></a>Expressões de associação - nome do arquivo de gatilho

O `path` para um gatilho de Blob pode ser um padrão que permite que você se refera ao nome do blob que dispara em outras associações e código de função. O padrão também pode incluir critérios de filtragem que especifique os blobs que podem disparar uma invocação de função.

Por exemplo, na seguinte associação de gatilho de Blob, o `path` padrão é `sample-images/{filename}`, que cria uma expressão de associação denominada `filename`:

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
    ...
```

A expressão `filename` pode ser usada em uma associação de saída para especificar o nome do blob que está sendo criado:

```json
    ...
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

Código de função tem acesso a esse mesmo valor usando `filename` como um nome de parâmetro:

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

A mesma capacidade de usar padrões e expressões de associação se aplica a atributos em bibliotecas de classes. No exemplo a seguir, os parâmetros do construtor de atributo são os mesmos `path` valores dos exemplos de *function.json* anteriores: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    TraceWriter log)
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
}

```

Você também pode criar expressões para partes do nome do arquivo, como a extensão. Para obter mais informações sobre como usar padrões e expressões na cadeia de caracteres de caminho de Blob, consulte a [referência de associação de blob de Armazenamento](functions-bindings-storage-blob.md).
 
### <a name="binding-expressions---trigger-metadata"></a>Expressões de associação - metadados de gatilho

Além do conteúdo dos dados fornecido por um gatilho (como o conteúdo da mensagem da fila que disparou uma função), vários gatilhos fornecem valores de metadados adicionais. Esses valores podem ser usados como parâmetros de entrada em C# e F# ou propriedades no objeto `context.bindings` em JavaScript. 

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

### <a name="binding-expressions---json-payloads"></a>Expressões de associação - cargas JSON

Quando uma carga de gatilho for JSON, você pode consultar as propriedades na configuração de outras associações na mesma função e no código de função.

A exemplo a seguir mostra o arquivo *function.json* arquivo para uma função de webhook que recebe um nome de blob em JSON: `{"BlobName":"HelloWorld.txt"}`. Uma associação de entrada do Blob lê o blob e associação de saída HTTP retorna o conteúdo de blob na resposta HTTP. Observe que a associação de entrada do Blob obtém o nome do blob referindo-se diretamente à `BlobName` propriedade (`"path": "strings/{BlobName}"`)

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
      "path": "strings/{BlobName.FileName}.{BlobName.Extension}",
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

Para este trabalho em C# e F#, você precisa de uma classe que define os campos a serem desserializados, como no exemplo a seguir:

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

No JavaScript, a desserialização JSON é executada automaticamente.

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

#### <a name="dot-notation"></a>Notação de ponto

Se algumas das propriedades na sua carga JSON são objetos com propriedades, você pode consultar esses diretamente usando a notação de ponto. Por exemplo, suponha que o JSON tem esta aparência:

```json
{"BlobName": {
  "FileName":"HelloWorld",
  "Extension":"txt"
  }
}
```

Você pode se referir diretamente a `FileName` como `BlobName.FileName`. Com esse formato JSON, aqui está como se parece a propriedade `path` no exemplo anterior:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

Em C#, você precisaria de duas classes:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

### <a name="binding-expressions---create-guids"></a>Expressões de associação - criar GUIDs

A expressão de associação `{rand-guid}` cria um GUID. O seguinte caminho de blob em um `function.json` arquivo cria um blob com um nome como *50710cb5-84b9-4d87-9d 83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="binding-expressions---current-time"></a>Expressões de associação - hora atual

A expressão de associação `DateTime` resolve para `DateTime.UtcNow`. O seguinte caminho de blob em um `function.json` arquivo cria um blob com um nome como *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="binding-at-runtime"></a>Associando no tempo de execução

No C#, e em outras linguagens .NET, você pode usar um padrão de associação obrigatório, em vez de associações declarativas em *function.json* e atributos. A associação obrigatória é útil quando os parâmetros de associação precisam ser calculado no tempo de execução, em vez do tempo de design. Para obter mais informações, consulte a [referência do desenvolvedor C#](functions-dotnet-class-library.md#binding-at-runtime) ou [referência do desenvolvedor de script C#](functions-reference-csharp.md#binding-at-runtime).

## <a name="functionjson-file-schema"></a>Esquema de arquivo function.json

O esquema de arquivo *function.json* está disponível em [http://json.schemastore.org/function](http://json.schemastore.org/function).

## <a name="handling-binding-errors"></a>Tratamento de erros de associação

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

Para obter links de todos os tópicos de erro relevantes para os vários serviços compatíveis com as Funções, consulte a seção [Códigos de erro de associação](functions-bindings-error-pages.md#binding-error-codes) do tópico de visão geral [Tratamento de erros das Funções do Azure](functions-bindings-error-pages.md).  

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre uma associação específica, consulte os artigos a seguir:

- [HTTP e webhooks](functions-bindings-http-webhook.md)
- [Timer](functions-bindings-timer.md)
- [Armazenamento de filas](functions-bindings-storage-queue.md)
- [Armazenamento de Blobs](functions-bindings-storage-blob.md)
- [Armazenamento de tabelas](functions-bindings-storage-table.md)
- [Hub de Evento](functions-bindings-event-hubs.md)
- [Barramento de Serviço](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-cosmosdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Hubs de Notificação](functions-bindings-notification-hubs.md)
- [Aplicativos Móveis](functions-bindings-mobile-apps.md)
- [Arquivo externo](functions-bindings-external-file.md)
