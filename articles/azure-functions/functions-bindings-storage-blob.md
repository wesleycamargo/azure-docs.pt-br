---
title: "Associações de armazenamento do Blob do Azure para o Azure Functions"
description: "Entenda como usar gatilhos e associações do Armazenamento de blob do Azure em Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2017
ms.author: glenga
ms.openlocfilehash: 6985d631bdac7114a72f105716c9483d0c5733ba
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2018
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Associações de armazenamento do Blob do Azure para o Azure Functions

Este artigo explica como trabalhar com associações de armazenamento de blob do Azure no Azure Functions. O Azure Functions dá suporte a associações de saída, gatilho e entrada para blobs. O artigo inclui uma seção para cada associação:

* [Gatilho de blob](#trigger)
* [Associação de entrada de blob](#input)
* [Associação de saída de blob](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Não há suporte para [contas de armazenamento exclusivas de blob](../storage/common/storage-create-storage-account.md#blob-storage-accounts). Gatilhos de armazenamento de blobs e associações exigem uma conta de armazenamento de uso geral. 

## <a name="trigger"></a>Gatilho

Use um gatilho de armazenamento de Blob para iniciar uma função quando é detectado um blob novo ou atualizado. O conteúdo do blob é fornecido como entrada para a função.

> [!NOTE]
> Ao usar um gatilho de blob em um plano de Consumo, pode haver um atraso de até 10 minutos no processamento de novos blobs depois que um aplicativo de funções ficar ocioso. Depois que o aplicativo de funções estiver em execução, os blobs serão processados imediatamente. Para evitar esse atraso inicial, considere uma das seguintes opções:
> - Use um plano do Serviço de Aplicativo com a opçao Sempre ativado habilitada.
> - Use outro mecanismo para disparar o processamento de blob, como uma mensagem de fila que contém o nome do blob. Por exemplo, consulte o [exemplo de associações de entrada de blob mais adiante neste artigo](#input---example).

## <a name="trigger---example"></a>Gatilho - exemplo

Consulte o exemplo específico a um idioma:

* [C#](#trigger---c-example)
* [Script do C# (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Gatilho - exemplo C#

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que grava um log quando um blob é adicionado ou atualizado no contêiner `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Para obter mais informações sobre o atributo `BlobTrigger`, consulte [Gatilho - atributos](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Gatilho - exemplo de script C#

O exemplo a seguir mostra uma associação de gatilho de blob em um arquivo *function.json* e código [script C# (.csx)](functions-reference-csharp.md) que usa a associação. A função grava um log quando um blob é adicionado ou atualizado no `samples-workitems` contêiner.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

Aqui está o código script C# que associa a um `Stream`:

```cs
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Aqui está o código script C# que associa a um `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Gatilho - exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho de blob em um arquivo *function.json* e [código JavaScript] (functions-reference-node.md) que usa a associação. A função grava um log quando um blob é adicionado ou atualizado no `samples-workitems` contêiner.

Aqui está o arquivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

## <a name="trigger---attributes"></a>Gatilho – atributos

Em [bibliotecas de classe C#](functions-dotnet-class-library.md), use os seguintes atributos para configurar um gatilho de blob:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs) é definido no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  O construtor do atributo usa uma cadeia de caracteres de caminho que indica o contêiner para inspecionar e, opcionalmente, um [padrão de nome de blob](#trigger---blob-name-patterns). Aqui está um exemplo:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Para ver um exemplo completo, consulte [Gatilho – exemplo de C#](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) é definido no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Oferece uma maneira de especificar a conta de armazenamento para usar. O construtor toma o nome de uma configuração de aplicativo que contenha uma cadeia de conexão de armazenamento. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo a seguir mostra o nível de classe e método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

A conta de armazenamento a ser usada é determinada na seguinte ordem:

* A propriedade `BlobTrigger` do atributo`Connection`.
* O `StorageAccount` atributo aplicado ao mesmo parâmetro do `BlobTrigger` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A conta de armazenamento padrão para a função de aplicativo (configuração de aplicativo "AzureWebJobsStorage").

## <a name="trigger---configuration"></a>Gatilho – configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `BlobTrigger`.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `blobTrigger`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | n/d | Deve ser definido como `in`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. As exceções são mencionadas na seção [uso](#trigger---usage). |
|**name** | n/d | O nome da variável que representa o blob no código de função. | 
|**path** | **BlobPath** |O contêiner para monitorar.  Pode ser um [padrão de nome de blob](#trigger-blob-name-patterns). | 
|**conexão** | **Conexão** | O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o tempo de execução do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o tempo de execução de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.<br><br>A cadeia de conexão deve ser uma conta de armazenamento de finalidade geral e não uma [conta de armazenamento de blobs](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Gatilho - uso

Em C# e script C#, acesse os dados de blob usando um parâmetro de método, como `T paramName`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. É possível associar a qualquer um dos seguintes tipos:

* `Stream`
* `TextReader`
* `Byte[]`
* `string`
* `ICloudBlob` (exige a direção de associação "inout" em *function.json*)
* `CloudBlockBlob` (exige a direção de associação "inout" em *function.json*)
* `CloudPageBlob` (exige a direção de associação "inout" em *function.json*)
* `CloudAppendBlob` (exige a direção de associação "inout" em *function.json*)

Como observado, alguns desses tipos exigem uma `inout`direção de associação no *function.json*. Não há suporte para essa direção pelo editor padrão no portal do Azure, então você deve usar o editor avançado.

Se blobs de texto forem esperados, você poderá associar a um tipo `string`. Isso será recomendado apenas se o tamanho do blob for pequeno, porque o conteúdo inteiro do blob é carregado na memória. Geralmente, é preferível usar um tipo `Stream` ou `CloudBlockBlob`.

Em JavaScript, acesse os dados do blob de entrada usando `context.bindings.<name>`.

## <a name="trigger---blob-name-patterns"></a>Gatilho - padrões de nome de blob

Você pode especificar um padrão de nome de blob na `path` propriedade em *function.json* ou no `BlobTrigger` construtor de atributo. O nome padrão pode ser uma [expressão de associação ou filtro](functions-triggers-bindings.md#binding-expressions-and-patterns).

### <a name="filter-on-blob-name"></a>Filtre por nome de blob

O exemplo a seguir gatilha apenas em blobs no `input` contêiner que iniciam com a cadeia de caracteres "original-":

```json
"path": "input/original-{name}",
```
 
Se o nome do blob for *original-Blob1.txt*, o valor da `name` variável no código da função é `Blob1`.

### <a name="filter-on-file-type"></a>Filtre por tipo de arquivo

O exemplo a seguir é disparado apenas em arquivos *.png*:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtre em chaves em nomes de arquivos

Para procurar as chaves em nomes de arquivos, escape as chaves usando duas chaves. O exemplo a seguir filtra por blobs que têm chaves no nome:

```json
"path": "images/{{20140101}}-{name}",
```

Se o blob é nomeado *{20140101}-soundfile.mp3*, o `name` valor da variável no código da função é *soundfile.mp3*. 

### <a name="get-file-name-and-extension"></a>Obtenha o nome de arquivo e extensão

O exemplo a seguir mostra como associar ao nome do arquivo de blob e extensão separadamente:

```json
"path": "input/{blobname}.{blobextension}",
```
Se um blob é chamado *original-Blob1.txt* o valor das variáveis `blobname` e `blobextension` no código de função é *original-Blob1* e *txt*.

## <a name="trigger---metadata"></a>Gatilho - metadados

O gatilho de blob fornece várias propriedades de metadados. Essas propriedades podem ser usadas como parte de expressões de associação em outras associações ou como parâmetros em seu código. Esses valores têm a mesma semântica que o tipo [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).


|Propriedade  |type  |DESCRIÇÃO  |
|---------|---------|---------|
|`BlobTrigger`|`string`|O caminho do blob de gatilho.|
|`Uri`|`System.Uri`|A URI do blob para o local principal.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|As propriedades do sistema do blob. |
|`Metadata` |`IDictionary<string,string>`|Os metadados definidos pelo usuário para o blob.|

## <a name="trigger---blob-receipts"></a>Gatilho - recebimentos de blob

O tempo de execução do Azure Functions garante que nenhuma função de gatilho de blob seja chamada mais de uma vez para o mesmo blob novo ou atualizado. Para determinar se uma versão de determinado blob foi processada, ele mantém os *recebimentos de blob*.

O Azure Functions armazena recibos do blob em um contêiner denominado *azure-webjobs-hosts* na conta de armazenamento do Azure do seu aplicativo de funções (definido na configuração do aplicativo `AzureWebJobsStorage`). Um recebimento de blob tem as seguintes informações:

* A função disparada ("*&lt;nome do aplicativo de funções>*.Functions.*&lt;nome da função>*", por exemplo: "MyFunctionApp.Functions.CopyBlob")
* O nome do contêiner
* O tipo de blob ("BlockBlob" ou "PageBlob")
* O nome do blob
* O ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

Para forçar o reprocessamento de um blob, exclua manualmente o recebimento desse blob do contêiner *azure-webjobs-hosts*.

## <a name="trigger---poison-blobs"></a>Gatilho - mensagens suspeitas

Quando uma função de gatilho de blob falhar para um determinado blob, o Azure Functions repete essa função até cinco vezes por padrão. 

Se todas as cinco tentativas falharem, o Azure Functions adiciona uma mensagem para uma fila de armazenamento denominada *webjobs-blobtrigger-poison*. A mensagem da fila para blobs suspeitos é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato *&lt;nome do aplicativo de funções>*.Functions.*&lt;nome da função>*)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

## <a name="trigger---polling-for-large-containers"></a>Gatilho - Controle de blobs para grandes contêineres

Se o contêiner de blob que está sendo monitorado contiver mais de 10.000 blobs, as verificações de tempo de execução do Functions varrerão os arquivos de log em busca de blobs novos ou alterados. Esse processo pode resultar em atrasos. Uma função não poderá ser disparada até que se passem vários minutos ou mais tempo depois da criação do blob. Além disso, [logs de armazenamento são criados da "melhor forma dentro do possível"](/rest/api/storageservices/About-Storage-Analytics-Logging). Não há nenhuma garantia de que todos os eventos são capturados. Sob algumas condições, logs poderão ser perdidos. Se você precisar de um processamento de blob mais rápido ou confiável, crie uma [mensagem de fila](../storage/queues/storage-dotnet-how-to-use-queues.md) ao criar o blob. Em seguida, use um [gatilho de fila](functions-bindings-storage-queue.md) em vez de um gatilho de blob para processar o blob. Outra opção é usar a Grade de Eventos; consulte o tutorial [Automatize redimensionamento de imagens carregadas usando a Grade de Eventos](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="input"></a>Entrada

Use uma associação de entrada de Armazenamento de Blobs para ler blobs.

## <a name="input---example"></a>Entrada - exemplo

Consulte o exemplo específico a um idioma:

* [C#](#input---c-example)
* [Script do C# (.csx)](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example"></a>Entrada – exemplo de C#

O exemplo a seguir é uma [função C#](functions-dotnet-class-library.md) que usa um gatilho de fila e uma associação de blob de entrada. A mensagem de fila contém o nome do blob e a função faz o tamanho do blob.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");

}
```        

### <a name="input---c-script-example"></a>Entrada - exemplo de script C#

<!--Same example for input and output. -->

O exemplo a seguir mostra uma associação de entrada e de saída de blob em um arquivo *function.json* e um código [script C# (.csx)](functions-reference-csharp.md) que usa as associações. A função faz uma cópia de um blob de texto. A função é disparada por uma mensagem da fila que contém o nome do blob para copiar. O novo blob é nomeado *{originalblobname}-Copy*.

No arquivo *function.json*, a `queueTrigger` propriedade de metadados é usada para especificar o nome do blob nas propriedades `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

A seção [configuração](#input---configuration) explica essas propriedades.

Aqui está o código de script do C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input---javascript-example"></a>Entrada - exemplo de JavaScript

<!--Same example for input and output. -->

O exemplo a seguir mostra as associações de entrada e de saída de blob em um arquivo *function.json* e [código JavaScript] (functions-reference-node.md) que usa as associações. A função faz uma cópia de um blob. A função é disparada por uma mensagem da fila que contém o nome do blob para copiar. O novo blob é nomeado *{originalblobname}-Copy*.

No arquivo *function.json*, a `queueTrigger` propriedade de metadados é usada para especificar o nome do blob nas propriedades `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

A seção [configuração](#input---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="input---attributes"></a>Entrada – atributos

Em [bibliotecas de classes C#](functions-dotnet-class-library.md), use o [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), que é definido no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

O construtor do atributo usa o caminho para o blob e um parâmetro `FileAccess` que indica a leitura ou gravação, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Você pode usar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [Gatilho - atributos](#trigger---attributes).

## <a name="input---configuration"></a>Entrada - configuração

A tabela a seguir explica as propriedades de configuração de associação que você definir no arquivo *function.json* e o `Blob` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `blob`. |
|**direction** | n/d | Deve ser definido como `in`. As exceções são mencionadas na seção [uso](#input---usage). |
|**name** | n/d | O nome da variável que representa o blob no código de função.|
|**path** |**BlobPath** | O caminho para o blob. | 
|**conexão** |**Conexão**| O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o tempo de execução do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o tempo de execução de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.<br><br>A cadeia de conexão deve ser uma conta de armazenamento de finalidade geral e não uma [conta de armazenamento de blobs](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|
|n/d | **Access** | Indica se você será leitura ou gravação. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrada - uso

Em bibliotecas de classes C# e script C#, acesse os dados de blob usando um parâmetro de método, como `Stream paramName`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. É possível associar a qualquer um dos seguintes tipos:

* `TextReader`
* `string`
* `Byte[]`
* `Stream`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob` (exige a direção de associação "inout" em *function.json*)
* `CloudBlockBlob` (exige a direção de associação "inout" em *function.json*)
* `CloudPageBlob` (exige a direção de associação "inout" em *function.json*)
* `CloudAppendBlob` (exige a direção de associação "inout" em *function.json*)

Como observado, alguns desses tipos exigem uma `inout`direção de associação no *function.json*. Não há suporte para essa direção pelo editor padrão no portal do Azure, então você deve usar o editor avançado.

Se você estiver lendo blobs de texto, você pode vincular a um tipo `string`. Esse tipo será recomendado apenas se o tamanho do blob for pequeno, porque o conteúdo inteiro do blob é carregado na memória. Geralmente, é preferível usar um tipo `Stream` ou `CloudBlockBlob`.

Em JavaScript, acesse os dados do blob usando `context.bindings.<name>`.

## <a name="output"></a>Saída

Usar as associações de saída do Armazenamento de Blobs para gravar os blobs.

## <a name="output---example"></a>Saída - exemplo

Consulte o exemplo específico a um idioma:

* [C#](#output---c-example)
* [Script do C# (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Saída - exemplo C#

O exemplo a seguir é uma [função C#](functions-dotnet-class-library.md) que usa um gatilho de blob e duas associações de blob de saída. A função é gatilhada pela criação de um blob de imagem no contêiner *imagens de amostra*. Isso cria cópias de pequeno e médio tamanho de blob de imagem. 

```csharp
[FunctionName("ResizeImage")]
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

### <a name="output---c-script-example"></a>Saída - exemplo de script C#

<!--Same example for input and output. -->

O exemplo a seguir mostra uma associação de entrada e de saída de blob em um arquivo *function.json* e um código [script C# (.csx)](functions-reference-csharp.md) que usa as associações. A função faz uma cópia de um blob de texto. A função é disparada por uma mensagem da fila que contém o nome do blob para copiar. O novo blob é nomeado *{originalblobname}-Copy*.

No arquivo *function.json*, a `queueTrigger` propriedade de metadados é usada para especificar o nome do blob nas propriedades `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código de script do C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="output---javascript-example"></a>Saída - exemplo JavaScript

<!--Same example for input and output. -->

O exemplo a seguir mostra as associações de entrada e de saída de blob em um arquivo *function.json* e [código JavaScript] (functions-reference-node.md) que usa as associações. A função faz uma cópia de um blob. A função é disparada por uma mensagem da fila que contém o nome do blob para copiar. O novo blob é nomeado *{originalblobname}-Copy*.

No arquivo *function.json*, a `queueTrigger` propriedade de metadados é usada para especificar o nome do blob nas propriedades `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="output---attributes"></a>Saída - atributos

Em [bibliotecas de classes C#](functions-dotnet-class-library.md), use o [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), que é definido no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

O construtor do atributo usa o caminho para o blob e um parâmetro `FileAccess` que indica a leitura ou gravação, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

Para ver um exemplo completo, consulte [Saída – exemplo de C#](#output---c-example).

Você pode usar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [Gatilho - atributos](#trigger---attributes).

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `Blob` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `blob`. |
|**direction** | n/d | Deve ser definido como `out` para uma associação de saída. As exceções são mencionadas na seção [uso](#output---usage). |
|**name** | n/d | O nome da variável que representa o blob no código de função.  Definido como `$return` para referenciar o valor de retorno da função.|
|**path** |**BlobPath** | O caminho para o blob. | 
|**conexão** |**Conexão**| O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o tempo de execução do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o tempo de execução de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.<br><br>A cadeia de conexão deve ser uma conta de armazenamento de finalidade geral e não uma [conta de armazenamento de blobs](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|
|n/d | **Access** | Indica se você será leitura ou gravação. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - uso

Em bibliotecas de classes C# e script C#, acesse os dados de blob usando um parâmetro de método, como `Stream paramName`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. É possível associar a qualquer um dos seguintes tipos:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob` (exige a direção de associação "inout" em *function.json*)
* `CloudBlockBlob` (exige a direção de associação "inout" em *function.json*)
* `CloudPageBlob` (exige a direção de associação "inout" em *function.json*)
* `CloudAppendBlob` (exige a direção de associação "inout" em *function.json*)

Como observado, alguns desses tipos exigem uma `inout`direção de associação no *function.json*. Não há suporte para essa direção pelo editor padrão no portal do Azure, então você deve usar o editor avançado.

Se você estiver lendo blobs de texto, você pode vincular a um tipo `string`. Esse tipo será recomendado apenas se o tamanho do blob for pequeno, porque o conteúdo inteiro do blob é carregado na memória. Geralmente, é preferível usar um tipo `Stream` ou `CloudBlockBlob`.

Em JavaScript, acesse os dados do blob usando `context.bindings.<name>`.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Vá para um guia de início rápido que usa um gatilho de armazenamento de Blob](functions-create-storage-blob-triggered-function.md)

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
