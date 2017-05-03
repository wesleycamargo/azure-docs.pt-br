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
ms.date: 04/18/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: e38c9187be42946df1e8059ba44f10f76d32d984
ms.lasthandoff: 04/21/2017


---
# <a name="azure-functions-documentdb-bindings"></a>Associações do Banco de Dados de Documentos do Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e codificar associações do Banco de Dados de Documentos do Azure no Azure Functions. O Azure Functions dá suporte a associações de entrada e saída para o DocumentDB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Para saber mais sobre o DocumentDB, veja [Introdução ao DocumentDB](../documentdb/documentdb-introduction.md) e [Criar um aplicativo de console do DocumentDB](../documentdb/documentdb-get-started.md).

<a id="docdbinput"></a>

## <a name="documentdb-input-binding"></a>Associação de entrada do DocumentDB
A associação de entrada de dados do DocumentDB recupera um documento do DocumentDB e o passa para o parâmetro de entrada nome da função. A ID do documento pode ser determinada com base no gatilho que invoca a função. 

A associação de entrada de dados de documentos tem as seguintes propriedades em *function.json*:

- `name` : nome do Identificador usado no código de função para o documento
- `type` : deve ser definido como "documentdb"
- `databaseName` : O banco de dados que contém o documento
- `collectionName` : A coleção que contém o documento
- `id` : a ID do documento a ser recuperado. Essa propriedade oferece suporte a parâmetros de associações; consulte [Associar a propriedades personalizadas de entrada em uma expressão de associação](functions-triggers-bindings.md#bind-to-custom-input-properties-in-a-binding-expression) no artigo [Conceitos de associações e gatilhos de funções do Azure](functions-triggers-bindings.md).
- `sqlQuery`: Uma consulta SQL do DocumentDB usada para recuperar vários documentos. A consulta oferece suporte a associações de tempo de execução. Por exemplo: `SELECT * FROM c where c.departmentId = {departmentId}`
- `connection`: O nome da configuração do aplicativo que contém a cadeia de conexão de banco de dados de documentos
- `direction` : deve ser definido como `"in"`.

As propriedades `id` e `sqlQuery` não podem ser especificadas. Se `id` nem `sqlQuery` estiver definido, toda a coleção é recuperada.

## <a name="using-a-documentdb-input-binding"></a>Utilizando uma Associação de entrada do Banco de Dados de Documentos do Azure

* Nas funções do C# ou do F#, todas as alterações feitas no documento de entrada (parâmetro de entrada) são enviadas novamente de forma automática para a coleção quando a função é fechada com êxito. 
* Funções de JavaScript, as atualizações não são feitas automaticamente após a saída da função. Em vez disso, use `context.bindings.<documentName>In` e `context.bindings.<documentName>Out` para fazer atualizações. Consulte o [exemplo JavaScript](#injavascript).

<a name="inputsample"></a>

## <a name="input-sample-for-single-document"></a>Exemplo de entrada para um único documento
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
* [JavaScript](#injavascript)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>Amostra de entrada no C# #

```cs
// Change input document contents using DocumentDB input binding 
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>Amostra de entrada no F# #

```fsharp
(* Change input document contents using DocumentDB input binding *)
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

Esse exemplo requer um arquivo `project.json` que especifique as dependências `FSharp.Interop.Dynamic` e `Dynamitey` do NuGet:

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

<a name="injavascript"></a>

### <a name="input-sample-in-javascript"></a>Amostra de entrada no JavaScript

```javascript
// Change input document contents using DocumentDB input binding, using context.bindings.inputDocumentOut
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a name="input-sample-with-multiple-documents"></a>Exemplo de entrada com vários documentos

Suponha que você deseja recuperar vários documentos especificados por uma consulta SQL, usando um gatilho de fila para personalizar os parâmetros de consulta. 

Neste exemplo, o gatilho de fila fornece um parâmetro `departmentId`. Uma mensagem da fila de `{ "departmentId" : "Finance" }` retornará todos os registros para o departamento financeiro. Usar o seguinte em *function.json*:

```
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}"
    "connection": "DocumentDBConnection"
}
```

### <a name="input-sample-with-multiple-documents-in-c"></a>Exemplo de entrada com vários documentos em C#

```csharp
public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
{   
    foreach (var doc in documents)
    {
        // operate on each document
    }    
}

public class QueuePayload
{
    public string departmentId { get; set; }
}
```

### <a name="input-sample-with-multiple-documents-in-javascript"></a>Exemplo de entrada com vários documentos em JavaScript

```javascript
module.exports = function (context, input) {    
    var documents = context.bindings.documents;
    for (var i = 0; i < documents.length; i++) {
        var document = documents[i];
        // operate on each document
    }        
    context.done();
};
```

## <a id="docdboutput"></a>Associação de saída do DocumentDB
A associação de saída do DocumentDB permite que você escreva um novo documento em um banco de dados do DocumentDB do Azure. O *function.json* especifica as propriedades a seguir:

- `name` : nome do Identificador usado no código de função para o novo documento
- `type` : deve ser definido como `"documentdb"`
- `databaseName` : o banco de dados que contém a coleção na qual o novo documento será criado.
- `collectionName` : a coleção na qual o novo documento será criado.
- `createIfNotExists` : é um valor booliano para indicar se a coleção será criada se ela não existir. O padrão é *false*. O motivo para isso é que as novas coleções são criadas com a taxa de transferência reservada, o que tem implicações de preço. Para obter mais detalhes, visite a [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection`: O nome da configuração do aplicativo que contém a cadeia de conexão de banco de dados de documentos
- `direction` : deve ser definido como `"out"`

## <a name="using-a-documentdb-output-binding"></a>Utilizando uma associação de saída do banco de dados do documento
Esta seção mostra como usar a associação de saída do DocumentDB no código da função.

Quando você grava para o parâmetro de saída em sua função, por padrão um novo documento é gerado no banco de dados, com um GUID gerado automaticamente como a ID do documento. Você pode especificar a ID do documento de saída, especificando a propriedade `id` JSON no parâmetro de saída. 

>[!Note]  
>Ao especificar a ID de um documento existente, ela é substituída pelo novo documento de saída. 

Para obter vários documentos de saída, você também pode associar a `ICollector<T>` ou `IAsyncCollector<T>`, sendo `T` um dos tipos com suporte.

<a name="outputsample"></a>

## <a name="documentdb-output-binding-sample"></a>Associação de saída do DocumentDB
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
* [JavaScript](#outjavascript)

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

Esse exemplo requer um arquivo `project.json` que especifique as dependências `FSharp.Interop.Dynamic` e `Dynamitey` do NuGet:

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

<a name="outjavascript"></a>

### <a name="output-sample-in-javascript"></a>Amostra de saída no JavaScript

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

