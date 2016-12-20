---
title: "Associações de blob de armazenamento do Azure Functions | Microsoft Docs"
description: "Entenda como usar gatilhos e associações do Armazenamento do Azure em Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/02/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: 96f253f14395ffaf647645176b81e7dfc4c08935
ms.openlocfilehash: 880ea646b1e976975f610ce81d0b372e81d2e34a


---
# <a name="azure-functions-storage-blob-bindings"></a>Associações de blob de armazenamento do Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e codificar associações de blob do Armazenamento do Azure em Azure Functions. O Azure Functions dá suporte a associações de entrada, saída e gatilho para blobs do Armazenamento do Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Não há suporte para [conta de armazenamento somente de blob](../storage/storage-create-storage-account.md#blob-storage-accounts). O Azure Functions requer uma conta de armazenamento de uso geral para ser usado com blobs. 
> 
> 

<a name="trigger"></a>

## <a name="storage-blob-trigger"></a>Gatilho de blob de armazenamento
O gatilho de blob do Azure Storage permite monitorar um contêiner de armazenamento de blobs novos e atualizados e reagir a eles. 

O gatilho de blob do Armazenamento de uma função usa os seguintes objetos JSON na matriz `bindings` de function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection":"<Name of app setting - see below>"
}
```

Observe o seguinte:

* Para `path`, consulte [Padrões de nome](#pattern) para descobrir como formatar os padrões de nome de blob.
* `connection` deve conter o nome de uma configuração de aplicativo que contenha uma cadeia de conexão de armazenamento. No portal do Azure, o editor padrão na guia **Integrar** define essa configuração de aplicativo para você ao criar uma conta de armazenamento ou selecionar uma conta existente. Para criar essa configuração de aplicativo manualmente, consulte [Definir esta configuração de aplicativo manualmente](). 

Além disso, consulte um destes subtítulos a seguir para obter mais informações:

* [Padrões de nome](#pattern)
* [Recebimentos de blob](#receipts)
* [Manipulação de blobs suspeitos](#poison)

<a name="pattern"></a>

### <a name="name-patterns"></a>Padrões de nome
É possível especificar um padrão de nome de blob na propriedade `path` . Por exemplo:

```json
"path": "input/original-{name}",
```

Esse caminho encontraria um blob chamado *original-Blob1.txt* no contêiner *input* e o valor da `name` variável no código de função seria `Blob1`.

Outro exemplo:

```json
"path": "input/{blobname}.{blobextension}",
```

Esse caminho também poderia ser um blob chamado *original-Blob1.txt* e o valor das variáveis `blobname` e `blobextension` no código de função seria *original-Blob1* e *txt*.

Você pode restringir o tipo de arquivo de blobs usando um valor fixo para a extensão de arquivo. Por exemplo:

```json
"path": "samples/{name}.png",
```

Nesse caso, apenas blobs *.png* no contêiner *exemplos* disparam a função.

As chaves são caracteres especiais nos padrões de nome. Para especificar nomes de blob com chaves, duplique as chaves. Por exemplo:

```json
"path": "images/{{20140101}}-{name}",
```

Esse caminho encontraria um blob denominado *{20140101}-soundfile.mp3* no contêiner *imagens* e o `name` valor da variável no código da função seria *soundfile.mp3*. 

<um nome"recebimentos"></a>

### <a name="blob-receipts"></a>Recebimentos de blob
O tempo de execução do Azure Functions garante que nenhuma função de gatilho de blob seja chamada mais de uma vez para o mesmo blob novo ou atualizado. Ele faz isso mantendo *recebimentos de blob* para determinar se uma versão de determinado blob foi processada.

Os recebimentos de blob são armazenados em um contêiner denominado *azure-webjobs-hosts* na conta de armazenamento do Azure do seu aplicativo de funções (especificado na configuração do aplicativo `AzureWebJobsStorage`). Um recebimento de blob tem as seguintes informações:

* A função disparada ("*&lt;nome do aplicativo de funções>*.Functions.*&lt;nome da função>*", por exemplo: "functionsf74b96f7.Functions.CopyBlob")
* O nome do contêiner
* O tipo de blob ("BlockBlob" ou "PageBlob")
* O nome do blob
* O ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

Para forçar o reprocessamento de um blob, exclua manualmente o recebimento desse blob do contêiner *azure-webjobs-hosts*.

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>Manipulação de blobs suspeitos
Quando uma função de gatilho de blob falhar, o Azure Functions repete essa função até cinco vezes por padrão (incluindo a primeira tentativa) para um determinado blob. Se todas as cinco tentativas falharem, o Functions adiciona uma mensagem para uma fila de armazenamento denominada *webjobs-blobtrigger-poison*. A mensagem da fila para blobs suspeitos é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato *&lt;nome do aplicativo de funções>*.Functions.*&lt;nome da função>*)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

### <a name="blob-polling-for-large-containers"></a>Sondagem de blobs para grandes contêineres
Se o contêiner de blob observado pela associação contiver mais de 10.000 blobs, as verificações de tempo de execução do Functions varrerão os arquivos de log em busca de blobs novos ou alterados. Esse processo não ocorre em tempo real. Uma função não poderá ser disparada até que se passem vários minutos ou mais tempo depois da criação do blob. Além disso, [logs de armazenamento são criados da "melhor forma dentro do possível"](https://msdn.microsoft.com/library/azure/hh343262.aspx). Não há nenhuma garantia de que todos os eventos são capturados. Sob algumas condições, logs poderão ser perdidos. Se as limitações de velocidade e de confiabilidade de gatilhos de blob para grandes contêineres não forem aceitáveis para o seu aplicativo, o método recomendado será criar uma [mensagem de fila](../storage/storage-dotnet-how-to-use-queues.md) ao criar o blob e usar um [gatilho de fila](functions-bindings-storage-queue.md) em vez de um gatilho de blob para processar o blob.

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Uso de gatilho
Em funções do C#, você associa aos dados de blob de entrada usando um parâmetro nomeado na assinatura da função, como `<T> <name>`.
Em que `T` é o tipo de dados no qual você deseja desserializar os dados e `paramName` é o nome especificado no [gatilho JSON](#trigger). Em funções do Node.js, você acessa os dados de blob de entrada usando `context.bindings.<name>`.

O blob pode ser desserializado em qualquer um destes tipos:

* Qualquer [Objeto](https://msdn.microsoft.com/library/system.object.aspx) – útil para dados de blob serializados para JSON.
  Se você declarar um tipo de entrada personalizado (por exemplo, `FooType`), o Azure Functions tenta desserializar os dados JSON para o tipo especificado.
* Cadeia de caracteres - útil para dados de blob de texto.

Em funções do C#, você também pode associar a qualquer um dos seguintes tipos, e o tempo de execução do Functions tentará desserializar os dados de blob usando esse tipo:

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Outros tipos desserializados por [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) 

## <a name="trigger-sample"></a>Exemplo de gatilho
Suponha que você tem o seguinte function.json, que define um gatilho de blob de armazenamento:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

Veja o exemplo específico por linguagem que registra em log o conteúdo de cada blob adicionado ao contêiner monitorado.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>Uso de gatilho em C# #

```cs
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger usage in F# ##
```fsharp

``` 
-->

<a name="triggernodejs"></a>

### <a name="trigger-usage-in-nodejs"></a>Uso de gatilho em Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

<a name="input"></a>

## <a name="storage-blob-input-binding"></a>Associação de entrada de blob de armazenamento
A associação de entrada de blob de Armazenamento do Azure permite usar um blob de um contêiner de armazenamento na função. 

A entrada de blob de Armazenamento de uma função usa os seguintes objetos JSON na matriz `bindings` de function.json:

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "blob",
  "direction": "in"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

Observe o seguinte:

* `path` deve conter o nome do contêiner e o nome do blob. Por exemplo, se você tiver um [gatilho de fila](functions-bindings-storage-queue.md) em sua função, você pode usar `"path": "samples-workitems/{queueTrigger}"` para apontar para um blob no `samples-workitems` contêiner com um nome que corresponda ao nome de blob especificado na mensagem de gatilho.   
* `connection` deve conter o nome de uma configuração de aplicativo que contenha uma cadeia de conexão de armazenamento. No portal do Azure, o editor padrão da guia **Integrar** define essa configuração de aplicativo para você quando você cria uma conta de Armazenamento ou seleciona uma conta existente. Para criar essa configuração de aplicativo manualmente, consulte [Definir esta configuração de aplicativo manualmente](). 

<a name="inputusage"></a>

## <a name="input-usage"></a>Uso de entrada
Em funções do C#, você associa aos dados de blob de entrada usando um parâmetro nomeado na assinatura da função, como `<T> <name>`.
Em que `T` é o tipo de dados no qual você deseja desserializar os dados e `paramName` é o nome especificado na [associação de entrada](#input). Em funções do Node.js, você acessa os dados de blob de entrada usando `context.bindings.<name>`.

O blob pode ser desserializado em qualquer um destes tipos:

* Qualquer [Objeto](https://msdn.microsoft.com/library/system.object.aspx) – útil para dados de blob serializados para JSON.
  Se você declarar um tipo de entrada personalizado (por exemplo, `FooType`), o Azure Functions tenta desserializar os dados JSON para o tipo especificado.
* Cadeia de caracteres - útil para dados de blob de texto.

Em funções do C#, você também pode associar a qualquer um dos seguintes tipos, e o tempo de execução do Functions tentará desserializar os dados de blob usando esse tipo:

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="inputsample"></a>

## <a name="input-sample"></a>Amostra de entrada
Suponha que você tem o function.json a seguir, que define um [gatilho de fila de armazenamento](functions-bindings-storage-queue.md), uma entrada de blob de armazenamento e uma saída de blob de armazenamento:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

Consulte o exemplo específico por linguagem que copia o blob de entrada para o blob de saída.

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="input-usage-in-c"></a>Uso de entrada em C# #

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

``` 
-->

<a name="innodejs"></a>

### <a name="input-usage-in-nodejs"></a>Uso de entrada em Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

<a name="output"></a>

## <a name="storage-blob-output-binding"></a>Associação de saída de blob de armazenamento
A associação de saída de blob de Armazenamento do Azure permite gravar blobs em um contêiner de armazenamento na função. 

A saída de blob de Armazenamento de uma função usa os seguintes objetos JSON na matriz `bindings` de function.json:

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "blob",
  "direction": "out"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
}
```

Observe o seguinte:

* `path` deve conter o nome do contêiner e o nome do blob de gravação. Por exemplo, se você tiver um [gatilho de fila](functions-bindings-storage-queue.md) em sua função, você pode usar `"path": "samples-workitems/{queueTrigger}"` para apontar para um blob no `samples-workitems` contêiner com um nome que corresponda ao nome de blob especificado na mensagem de gatilho.   
* `connection` deve conter o nome de uma configuração de aplicativo que contenha uma cadeia de conexão de armazenamento. No portal do Azure, o editor padrão na guia **Integrar** define essa configuração de aplicativo para você ao criar uma conta de armazenamento ou selecionar uma conta existente. Para criar essa configuração de aplicativo manualmente, consulte [Definir esta configuração de aplicativo manualmente](). 

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso de saída
Em funções do C#, você associa ao blob de saída usando o parâmetro `out` nomeado na assinatura da função, como `out <T> <name>`, em que `T` é o tipo de dados no qual você deseja serializar os dados, e `paramName` é o nome especificado na [associação de saída](#output). Em funções do Node.js, você acessa o blob de saída usando `context.bindings.<name>`.

Você pode gravar no blob de saída usando qualquer um dos seguintes tipos:

* Qualquer [Objeto](https://msdn.microsoft.com/library/system.object.aspx) - útil para serialização JSON.
  Se você declarar um tipo de saída personalizada (por exemplo, `out FooType paramName`), o Azure Functions tenta serializar o objeto em JSON. Se o parâmetro de saída for nulo quando a função for encerrada, o tempo de execução de funções criará um blob como objeto nulo.
* Cadeia de caracteres - (`out string paramName`) útil para dados de blob de texto. o tempo de execução de funções criará um blob somente se o parâmetro de cadeia de caracteres não for nulo quando a função for encerrada.

Em funções do C#, também é possível executar a saída para qualquer um dos seguintes tipos:

* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="outputsample"></a>

## <a name="output-sample"></a>Amostra de saída
Consulte [amostra de entrada](#inputsample).

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Nov16_HO3-->


