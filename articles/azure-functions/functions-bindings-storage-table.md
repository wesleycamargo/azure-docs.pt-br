---
title: "Associações de armazenamento da Tabela do Azure Functions"
description: "Entenda como usar as associações de armazenamento da Tabela do Azure no Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: chrande
ms.openlocfilehash: 2f54df931d03318a50e9397211e3c50d0898556d
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2017
---
# <a name="azure-functions-table-storage-bindings"></a>Associações de armazenamento da Tabela do Azure Functions

Este artigo explica como trabalhar com associações de armazenamento de Tabela do Azure no Azure Functions. O Azure Functions dá suporte a associações de entrada e saída para armazenamento de Tabelas do Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="table-storage-input-binding"></a>Associação de entrada de armazenamento de tabela

Use a associação de entrada de armazenamento de Tabela do Azure para ler uma tabela em uma conta de Armazenamento do Azure.

## <a name="input---example"></a>Entrada - exemplo

Consulte o exemplo específico a um idioma:

* [Uma entidade de leitura pré-compilada C#](#input---c-example-1)
* [Múltiplas entidades de leitura pré-compilada C#](#input---c-example-2)
* [Uma entidade de leitura - script C#](#input---c-script-example-1)
* [Múltiplas entidades de leitura - script C#](#input---c-script-example-2)
* [F#](#input---f-example-2)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example-1"></a>Entrada - exemplo 1 de C#

O exemplo a seguir mostra um código [pré-compilado C#](functions-dotnet-class-library.md) que lê uma linha da tabela. 

O valor de chave de linha “{queueTrigger}” indica que a chave de linha foi obtida da cadeia de caracteres da mensagem da fila.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        TraceWriter log)
    {
        log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}";
    }
}
```

### <a name="input---c-example-2"></a>Exemplo 2 de C# - entrada

O exemplo a seguir mostra um código [pré-compilado C#](functions-dotnet-class-library.md) que lê múltiplas linhas da tabela. Observe que a `MyPoco` classe deriva de `TableEntity`.

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        TraceWriter log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}";
        }
    }
}
```

### <a name="input---c-script-example-1"></a>Entrada - exemplo 1 de script C#

O exemplo a seguir mostra uma associação de entrada de tabela em um arquivo *function.json* e código [script C#](functions-reference-csharp.md) que usa a associação. A função usa um gatilho de fila para ler uma linha da tabela. 

O arquivo *function.json* especifica um `partitionKey` e um `rowKey`. O `rowKey` valor “{queueTrigger}” indica que a chave de linha foi obtida da cadeia de caracteres da mensagem da fila.

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#input---configuration) explica essas propriedades.

Aqui está o código de script do C#:

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="input---c-script-example-2"></a>Entrada - exemplo 2 de script C#

O exemplo a seguir mostra uma associação de entrada de tabela em um arquivo *function.json* e código [script C#](functions-reference-csharp.md) que usa a associação. A função lê entidades para uma chave de partição especificada em uma mensagem da fila.

Aqui está o arquivo *function.json*:

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
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#input---configuration) explica essas propriedades.

O código script C# adiciona uma referência ao SDK de Armazenamento do Azure para que o tipo de entidade possa derivar de `TableEntity`:

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="input---f-example"></a>Entrada - exemplo #F

O exemplo a seguir mostra uma associação de entrada de tabela em um arquivo *function.json* e código [script F#](functions-reference-fsharp.md) que usa a associação. A função usa um gatilho de fila para ler uma linha da tabela. 

O arquivo *function.json* especifica um `partitionKey` e um `rowKey`. O `rowKey` valor “{queueTrigger}” indica que a chave de linha foi obtida da cadeia de caracteres da mensagem da fila.

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#input---configuration) explica essas propriedades.

O código F# é o seguinte:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>Entrada - exemplo de JavaScript

O exemplo a seguir mostra uma associação de entrada de tabela em um arquivo *function.json* e [código JavaScript] (functions-reference-node.md) que usa a associação. A função usa um gatilho de fila para ler uma linha da tabela. 

O arquivo *function.json* especifica um `partitionKey` e um `rowKey`. O `rowKey` valor “{queueTrigger}” indica que a chave de linha foi obtida da cadeia de caracteres da mensagem da fila.

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#input---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

## <a name="input---attributes-for-precompiled-c"></a>Entrada - Atributos para pré-compilado C#
 
Para funções [pré-compilado C#](functions-dotnet-class-library.md), use os seguintes atributos para configurar uma associação de entrada de tabela:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs) é definido no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

  O construtor do atributo usa o nome da tabela, a chave de partição e a chave de linha. Ele pode ser usado em um parâmetro de saída ou no valor de retorno da função, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      TraceWriter log)
  ```

  Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      TraceWriter log)
  ```

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) é definido no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Oferece uma maneira de especificar a conta de armazenamento para usar. O construtor toma o nome de uma configuração de aplicativo que contenha uma cadeia de conexão de armazenamento. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo a seguir mostra o nível de classe e método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  ```

A conta de armazenamento a ser usada é determinada na seguinte ordem:

* A propriedade `Table` do atributo`Connection`.
* O `StorageAccount` atributo aplicado ao mesmo parâmetro do `Table` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A conta de armazenamento padrão para a função de aplicativo (configuração de aplicativo "AzureWebJobsStorage").

## <a name="input---configuration"></a>Entrada - configuração

A tabela a seguir explica as propriedades de configuração de associação que você definir no arquivo *function.json* e o `Table` atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `table`. Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure.|
|**direction** | n/d | Deve ser definido como `in`. Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure. |
|**name** | n/d | O nome da variável que representa a tabela ou entidade no código de função. | 
|**tableName** | **TableName** | O nome da tabela.| 
|**partitionKey** | **PartitionKey** |Opcional. Chave de partição da entidade de tabela para leitura. Consulte a seção de [uso](#input---usage) para obter orientação sobre como usar essa propriedade.| 
|**rowKey** |**RowKey** | Opcional. Chave de linha da entidade de tabela para leitura. Consulte a seção de [uso](#input---usage) para obter orientação sobre como usar essa propriedade.| 
|**take** |**Take** | Opcional. O número máximo de entidades para ler em JavaScript. Consulte a seção de [uso](#input---usage) para obter orientação sobre como usar essa propriedade.| 
|**filter** |**Filter** | Opcional. Uma expressão de filtro OData para a entrada de tabela em JavaScript. Consulte a seção de [uso](#input---usage) para obter orientação sobre como usar essa propriedade.| 
|**conexão** |**Conexão** | O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o tempo de execução do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o tempo de execução de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.<br/>Quando você estiver desenvolvendo localmente, as configurações de aplicativo entram em valores do [arquivo local.settings.json](functions-run-local.md#local-settings-file).|

## <a name="input---usage"></a>Entrada - uso

A associação de entrada da Tabela de Armazenamento dá suporte aos seguintes cenários:

* **Ler uma linha em C# ou o script do C#**

  Definir `partitionKey` e `rowKey`. Acessar os dados da tabela usando um parâmetro de método `T <paramName>`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. `T` geralmente é um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. As propriedades `filter` e `take` não são usadas neste cenário. 

* **Ler uma ou mais linhas em C# ou o script do C#**

  Acessar os dados da tabela usando um parâmetro de método `IQueryable<T> <paramName>`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. `T` geralmente é um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. Você pode usar `IQueryable` métodos para fazer a filtragem necessária. As propriedades `partitionKey`, `rowKey`, `filter` e `take` não são usadas neste cenário.  

> [!NOTE]
> `IQueryable`não funciona em .NET Core, para que ele não funcione no [Functions v2 runtime](functions-versions.md).

  Uma alternativa é usar um `CloudTable paramName` parâmetro do método para ler a tabela usando o SDK de Armazenamento do Azure.

* **Ler uma ou mais linhas em JavaScript**

  Definir as propriedades `filter` e `take`. Não definir `partitionKey` ou `rowKey`. Acesse a entidade (ou entidades) de tabela de entrada usando `context.bindings.<name>`. Os objetos desserializados têm propriedades `RowKey` e `PartitionKey`.

## <a name="table-storage-output-binding"></a>Associação de saída de armazenamento de tabela

Use uma associação de saída de armazenamento de Tabela do Azure para gravar entidades para uma tabela em uma conta de Armazenamento do Azure.

## <a name="output---example"></a>Saída - exemplo

Consulte o exemplo específico a um idioma:

* [Pré-compilado C#](#output---c-example)
* [Script C#](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Saída - exemplo C#

O exemplo a seguir mostra código [pré-compilado C#](functions-dotnet-class-library.md) que usa um gatilho HTTP para gravar uma única linha de tabela. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>Saída - exemplo de script C#

O exemplo a seguir mostra uma associação de saída de tabela em um arquivo *function.json* e código [script C#](functions-reference-csharp.md) que usa a associação. A função escreve múltiplas entidades de tabela.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código de script do C#:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

### <a name="output---f-example"></a>Saída - Exemplo #F

O exemplo a seguir mostra uma associação de saída de tabela em um arquivo *function.json* e código [script C#](functions-reference-fsharp.md) que usa a associação. A função escreve múltiplas entidades de tabela.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#output---configuration) explica essas propriedades.

O código F# é o seguinte:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>Saída - exemplo JavaScript

O exemplo a seguir mostra uma associação de saída de tabela em um arquivo *function.json* e código [script C#](functions-reference-node.md) que usa a associação. A função escreve múltiplas entidades de tabela.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

## <a name="output---attributes-for-precompiled-c"></a>Saída - Atributos para pré-compilado C#

 Para funções [pré-compiladas C#](functions-dotnet-class-library.md), use o [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), que é definido no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

O construtor do atributo usa o nome da tabela. Ele pode ser usado em um `out` parâmetro ou no valor de retorno da função, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
```

Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
```

Você pode usar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [Entrada - Atributos para pré-compilado C#](#input---attributes-for-precompiled-c).

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de associação que você definir no arquivo *function.json* e o `Table` atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `table`. Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure.|
|**direction** | n/d | Deve ser definido como `out`. Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure. |
|**name** | n/d | O nome da variável usada no código da função que representa a tabela ou entidade. Definido como `$return` para referenciar o valor de retorno da função.| 
|**tableName** |**TableName** | O nome da tabela.| 
|**partitionKey** |**PartitionKey** | Chave de partição da entidade de tabela para gravar. Consulte a [seção de uso](#output---usage) para obter orientação sobre como usar essa propriedade.| 
|**rowKey** |**RowKey** | A chave de linha da entidade de tabela para gravar. Consulte a [seção de uso](#output---usage) para obter orientação sobre como usar essa propriedade.| 
|**conexão** |**Conexão** | O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o tempo de execução do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o tempo de execução de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.<br/>Quando você estiver desenvolvendo localmente, as configurações de aplicativo entram em valores do [arquivo local.settings.json](functions-run-local.md#local-settings-file).|

## <a name="output---usage"></a>Saída - uso

A associação de entrada de Armazenamento da Tabela dá suporte aos seguintes cenários:

* **Gravar uma linha em qualquer idioma**

  Em C# e o script C#, acesse a entidade de tabela de saída usando um parâmetro de método como `out T paramName` ou valor de retorno da função. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. `T`pode ser qualquer tipo serializável, se a chave de partição e a chave de linha forem fornecidos pelo arquivo *function.json* ou o `Table` atributo. Caso contrário, `T` deve ser um tipo que inclua `PartitionKey` e `RowKey` propriedades. Nesse cenário, `T` normalmente implementa `ITableEntity` ou deriva de `TableEntity`, mas ele não precisa.

* **Gravar uma ou mais linhas em C# ou o script do C#**

  Em C# e o script C#, acesse a entidade de tabela de saída usando um parâmetro de método como `ICollector<T> paramName` ou `ICollectorAsync<T> paramName`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. `T` especifica o esquema das entidades que você deseja adicionar. Geralmente, `T` deriva de `TableEntity` ou implementa `ITableEntity`, mas isso não é obrigatório. A chave de partição e a linha valores de chave em *function.json* ou o `Table` construtor de atributo não são usados neste cenário.

  Uma alternativa é usar um `CloudTable paramName` parâmetro do método para gravar a tabela usando o SDK de Armazenamento do Azure.

* **Gravar uma ou mais linhas em JavaScript**

  Em funções do JavaScript, acesse a tabela de saída usando `context.bindings.<name>`.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
