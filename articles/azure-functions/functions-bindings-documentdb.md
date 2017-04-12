---
title: "Associações do DocumentDB do Azure Functions | Microsoft Docs"
description: "Entenda como usar associações do Banco de Dados de Documentos do Azure no Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/10/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 2ac78606f851068fa0fb7dcab3bac1c629b9cdb3
ms.lasthandoff: 04/03/2017


---
# <a name="azure-functions-documentdb-bindings"></a>Associações do Banco de Dados de Documentos do Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e codificar associações do Banco de Dados de Documentos do Azure no Azure Functions. O Azure Functions dá suporte a associações de entrada e saída para o DocumentDB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Para saber mais sobre o DocumentDB, veja [Introdução ao DocumentDB](../documentdb/documentdb-introduction.md) e [Criar um aplicativo de console do DocumentDB](../documentdb/documentdb-get-started.md).

<a id="docdbinput"></a>

## <a name="documentdb-input-binding"></a>Associação de entrada do DocumentDB
A associação de entrada de dados do DocumentDB recupera um documento do DocumentDB e o passa para o parâmetro de entrada nome da função. A ID do documento pode ser determinada com base no gatilho que invoca a função. 

A entrada do DocumentDB de uma função usa o seguinte objeto JSON na matriz `bindings` de function.json:

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "id": "<Id of the DocumentDB document - see below>",
  "connection": "<Name of app setting with connection string - see below>",
  "direction": "in"
},
```

Observe o seguinte:

* `id` oferece suporte a associações semelhante ao `{queueTrigger}`, que usa o valor de cadeia de caracteres da mensagem da fila como o documento de identificação.
* `connection` deve ser o nome de uma configuração de aplicativo que aponta para o ponto de extremidade para sua conta do DocumentDB (com o valor `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>`). Se você criar uma conta do DocumentDB por meio do portal de funções da interface do usuário, o processo de criação de conta cria uma configuração de aplicativo para você. Para usar uma conta existente do DocumentDB, você precisará [definir a configuração deste aplicativo manualmente](functions-how-to-use-azure-function-app-settings.md). 
* Se o documento especificado não for encontrado, o parâmetro de entrada nomeado para a função é definido como `null`. 

## <a name="input-usage"></a>Uso de entrada
Esta seção mostra como usar a associação de entrada do DocumentDB no código da função.

Nas funções do C# ou do F#, todas as alterações feitas no documento de entrada (parâmetro de entrada) são enviadas novamente de forma automática para a coleção quando a função é fechada com êxito. As funções do Node.js, as atualizações no documento na associação de entrada não são enviadas para a coleção. No entanto, você pode usar `context.bindings.<documentName>In` e `context.bindings.<documentName>Out` para fazer atualizações para documentos de entrada. Veja como isso é feito no [exemplo do Node.js](#innodejs).

<a name="inputsample"></a>

## <a name="input-sample"></a>Amostra de entrada
Suponha que você tenha a seguinte associação de entrada do DocumentDB na matriz `bindings` de function.json:

```json
{
  "name": "inputDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id" : "{queueTrigger}",
  "connection": "MyAccount_DOCUMENTDB",     
  "direction": "in"
}
```

Veja o exemplo de idioma específico que usa essa associação de entrada para atualizar o valor de texto do documento.

* [C#](#incsharp)
* [F#](#infsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>Amostra de entrada no C# #

```cs
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>Amostra de entrada no F# #

```fsharp
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

Você precisa adicionar um arquivo do `project.json` que especifique as dependências `FSharp.Interop.Dynamic` e `Dynamitey` do NuGet:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

Para adicionar um arquivo do `project.json`, veja [gerenciamento de pacotes do F#](functions-reference-fsharp.md#package).

<a name="innodejs"></a>

### <a name="input-sample-in-nodejs"></a>Amostra de entrada no Node.js

```javascript
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a id="docdboutput"></a>Associação de saída do DocumentDB
A associação de saída do DocumentDB permite que você escreva um novo documento em um banco de dados do DocumentDB do Azure. 

A associação de saída usa o seguinte objeto JSON na matriz `bindings` de function.json: 

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "createIfNotExists": <true or false - see below>,
  "connection": "<Value of AccountEndpoint in Application Setting - see below>",
  "direction": "out"
}
```

Observe o seguinte:

* Defina `createIfNotExists` como `true` para criar o banco de dados e a coleção se eles não existirem. O valor padrão é `false`. As novas coleções são criadas com a taxa de transferência reservada, o que tem implicações de preço. Para obter mais informações, consulte [Preços do Banco de Dados de Documentos](https://azure.microsoft.com/pricing/details/documentdb/).
* `connection` deve ser o nome de uma configuração de aplicativo que aponta para o ponto de extremidade para sua conta do DocumentDB (com o valor `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>`). Se você criar uma conta do DocumentDB por meio do portal de funções da interface do Usuário, o processo de criação de conta cria uma configuração de um novo aplicativo para você. Para usar uma conta existente do DocumentDB, você precisará [definir a configuração deste aplicativo manualmente](functions-how-to-use-azure-function-app-settings.md). 

## <a name="output-usage"></a>Uso de saída
Esta seção mostra como usar a associação de saída do DocumentDB no código da função.

Quando você grava para o parâmetro de saída em sua função, por padrão um novo documento é gerado no banco de dados, com um GUID gerado automaticamente como a ID do documento. Você pode especificar a ID do documento de saída, especificando a propriedade `id` JSON no parâmetro de saída. 

>[!Note]  
>Ao especificar a ID de um documento existente, ela é substituída pelo novo documento de saída. 

Você pode gravar na saída usando qualquer um dos seguintes tipos:

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

Para obter vários documentos de saída, você também pode associar a `ICollector<T>` ou `IAsyncCollector<T>`, sendo `T` um dos tipos com suporte.


<a name="outputsample"></a>

## <a name="output-sample"></a>Amostra de saída
Suponha que você tenha a seguinte associação de saída do DocumentDB na matriz `bindings` de function.json:

```json
{
  "name": "employeeDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connection": "MyAccount_DOCUMENTDB",     
  "direction": "out"
}
```

E que você tenha uma associação de entrada de fila para uma fila que recebe o JSON no seguinte formato:

```json
{
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

E você deseja criar documentos do DocumentDB no formato a seguir para cada registro:

```json
{
  "id": "John Henry-123456",
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

Veja o exemplo de idioma específico que usa essa associação de saída para adicionar documentos ao seu banco de dados.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Amostra de saída no C# #

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
{
  log.Info($"C# Queue trigger function processed: {myQueueItem}");

  dynamic employee = JObject.Parse(myQueueItem);

  employeeDocument = new {
    id = employee.name + "-" + employee.employeeId,
    name = employee.name,
    employeeId = employee.employeeId,
    address = employee.address
  };
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Amostra de saída no F# #

```fsharp
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Employee = {
  id: string
  name: string
  employeeId: string
  address: string
}

let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
  log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
  let employee = JObject.Parse(myQueueItem)
  employeeDocument <-
    { id = sprintf "%s-%s" employee?name employee?employeeId
      name = employee?name
      employeeId = employee?employeeId
      address = employee?address }
```

Você precisa adicionar um arquivo do `project.json` que especifique as dependências `FSharp.Interop.Dynamic` e `Dynamitey` do NuGet:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

Para adicionar um arquivo do `project.json`, veja [Gerenciamento de pacotes do F#](functions-reference-fsharp.md#package).

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Amostra de saída no Node.js

```javascript
module.exports = function (context) {

  context.bindings.employeeDocument = JSON.stringify({ 
    id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
    name: context.bindings.myQueueItem.name,
    employeeId: context.bindings.myQueueItem.employeeId,
    address: context.bindings.myQueueItem.address
  });

  context.done();
};
```

