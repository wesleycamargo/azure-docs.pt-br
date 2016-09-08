<properties
	pageTitle="Gatilhos e associações de Azure Functions para Armazenamento do Azure | Microsoft Azure"
	description="Entenda como usar gatilhos e associações do Armazenamento do Azure em Azure Functions."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande"/>

# Gatilhos e associações de Azure Functions para Armazenamento do Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e codificar gatilhos e associações do Armazenamento do Azure em Azure Functions.

[AZURE.INCLUDE [introdução](../../includes/functions-bindings-intro.md)]

## <a id="storagequeuetrigger"></a> Gatilho de fila do Armazenamento do Azure

#### function.json para um gatilho de fila de armazenamento

O arquivo *function.json* especifica as propriedades a seguir.

- `name`: o nome da variável usada no código de função para a fila ou a mensagem da fila.
- `queueName`: o nome da fila a ser sondada. Para regras de nomenclatura de fila, confira [Naming Queues and Metadata (Nomeando filas e metadados)](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. Se você deixar o `connection` vazio, o gatilho funcionará com a cadeia de conexão de armazenamento padrão para o aplicativo de funções, que é especificado pela configuração de aplicativo AzureWebJobsStorage.
- `type`: deve ser definido como *queueTrigger*.
- `direction`: deve ser definido como *in*.

*function.json* de exemplo para um gatilho de fila de armazenamento:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### Tipos de gatilho de fila com suporte

A mensagem da fila pode ser desserializada para qualquer um destes tipos:

* Objeto (de JSON)
* Cadeia de caracteres
* Matriz de bytes
* `CloudQueueMessage` (C#)

#### Metadados de gatilho de fila

Você pode obter metadados de fila em sua função usando estes nomes de variáveis:

* expirationTime
* insertionTime
* nextVisibleTime
* ID
* popReceipt
* dequeueCount
* queueTrigger (outra maneira de recuperar o texto da mensagem de fila como uma cadeia de caracteres)

Este exemplo de código C# recupera e registra em log os metadados de fila:

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### Tratamento de mensagens suspeitas na fila

As mensagens cujo conteúdo faz com que uma função falhe são chamadas de *mensagens suspeitas*. Quando a função falhar, a mensagem da fila não é excluída e eventualmente é captada novamente, fazendo com que o ciclo seja repetido. O SDK pode interromper automaticamente o ciclo após algumas iterações, ou você fazê-lo manualmente.

O SDK chamará uma função até 5 vezes para processar uma mensagem da fila. Se a quinta tentativa falhar, a mensagem é movida para uma fila de mensagens suspeitas.

A fila de mensagens suspeita é denominada *{originalqueuename}*-suspeita. Você pode gravar uma função para processar as mensagens da fila de mensagens suspeitas registrando-as ou enviando uma notificação de que a atenção manual é necessária.

Se quiser manipular mensagens suspeitas manualmente, você poderá obter o número de vezes que uma mensagem foi selecionada para processamento verificando `dequeueCount`.

## <a id="storagequeueoutput"></a> Associação de saída de fila do Armazenamento do Azure

#### function.json de uma associação de saída de fila de armazenamento

O arquivo *function.json* especifica as propriedades a seguir.

- `name`: o nome da variável usada no código de função para a fila ou a mensagem da fila.
- `queueName`: o nome da fila. Para regras de nomenclatura de fila, confira [Naming Queues and Metadata (Nomeando filas e metadados)](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. Se você deixar o `connection` vazio, o gatilho funcionará com a cadeia de conexão de armazenamento padrão para o aplicativo de funções, que é especificado pela configuração de aplicativo AzureWebJobsStorage.
- `type`: deve ser definido como *queue*.
- `direction`: deve ser definido como *out*.

O *function.json* de exemplo para uma associação de saída de fila de armazenamento que usa um gatilho de fila e grava uma mensagem de fila:

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
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Tipos de associação de saída de fila com suporte

A associação `queue` pode serializar os seguintes tipos para uma mensagem de fila:

* Objeto (`out T` no C#, criará uma mensagem com um objeto nulo se o parâmetro for nulo quando a função terminar)
* Cadeia de caracteres (`out string` no C#, criará a mensagem da fila se o valor do parâmetro não for nulo quando a função terminar)
* Matriz de bytes (`out byte[]` no C#, funciona como uma cadeia de caracteres)
* `out CloudQueueMessage` (C#, funciona como cadeia de caracteres)

No C#, você também pode associar a `ICollector<T>` ou `IAsyncCollector<T>`, sendo `T` um dos tipos com suporte.

#### Exemplos de código de associação de saída de fila

Este exemplo de código C# grava uma mensagem de fila de saída única para cada mensagem de fila de entrada.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

Este exemplo de código C# grava várias mensagens usando `ICollector<T>` (use `IAsyncCollector<T>` em uma função assíncrona):

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

## <a id="storageblobtrigger"></a> Gatilho de blob do Armazenamento do Azure

#### function.json para um gatilho de blob de armazenamento

O arquivo *function.json* especifica as propriedades a seguir.

- `name`: o nome da variável usada no código de função para o blob.
- `path`: um caminho que especifica o contêiner a ser monitorado e, opcionalmente, um padrão de nome de blob.
- `connection`: o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. Se você deixar o `connection` vazio, o gatilho funcionará com a cadeia de conexão de armazenamento padrão para o aplicativo de funções, que é especificado pela configuração de aplicativo AzureWebJobsStorage.
- `type`: deve ser definido como *blobTrigger*.
- `direction`: deve ser definido como *in*.

O *function.json* de exemplo para um gatilho de blob de armazenamento que observa blobs adicionados ao contêiner de itens de trabalho de exemplo:

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

#### Tipos de gatilho de blob com suporte

O blob pode ser desserializado para qualquer um dos seguintes tipos de funções no Node ou C#:

* Objeto (de JSON)
* Cadeia de caracteres

Em funções do C#, também é possível associar a qualquer um dos seguintes tipos:

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

#### Exemplo de código C# de gatilho de blob

Este exemplo de código C# registra em log o conteúdo de cada blob adicionado ao contêiner monitorado.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### Padrões de nome do gatilho de blob

É possível especificar um padrão de nome de blob na propriedade `path`. Por exemplo:

```json
"path": "input/original-{name}",
```

Esse caminho encontraria um blob chamado *original-Blob1.txt* no contêiner *input* e o valor da variável `name` no código de função seria `Blob1`.

Outro exemplo:

```json
"path": "input/{blobname}.{blobextension}",
```

Esse caminho também poderia ser um blob chamado *original-Blob1.txt* e o valor das variáveis `blobname` e `blobextension` no código de função seria *original-Blob1* e *txt*.

Você pode restringir os tipos de blobs que disparam a função especificando um padrão com um valor fixo para a extensão de arquivo. Se você definir `path` como *amostras/{nome}.png*, somente os blobs *.png* no contêiner de *exemplos* vão disparar a função.

Se você precisar especificar um padrão de nome para nomes de blob que têm chaves no nome, duplique as chaves. Por exemplo, para localizar blobs no contêiner *imagens* que têm nomes como este:

		{20140101}-soundfile.mp3

use isto para a propriedade `path`:

		images/{{20140101}}-{name}

No exemplo, o valor da variável `name` seria *soundfile.mp3*.

#### Recebimentos de blob

O tempo de execução do Azure Functions garante que nenhuma função de gatilho de blob seja chamada mais de uma vez para o mesmo blob novo ou atualizado. Ele faz isso mantendo *recebimentos de blob* para determinar se uma versão de determinado blob foi processada.

Os recebimentos de blob são armazenados em um contêiner denominado *azure-webjobs-hosts* na conta de armazenamento do Azure especificada pela cadeia de conexão AzureWebJobsStorage. Um recebimento de blob tem as seguintes informações:

* A função que foi chamada para o blob ("*{nome do aplicativo de funções}*.Functions.*{nome da função}*", por exemplo: "functionsf74b96f7.Functions.CopyBlob")
* O nome do contêiner
* O tipo de blob ("BlockBlob" ou "PageBlob")
* O nome do blob
* O ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

Para forçar o reprocessamento de um blob, você pode excluir manualmente o recebimento desse blob do contêiner *azure-webjobs-hosts*.

#### Manipulação de blobs suspeitos

Quando uma função de gatilho de blob falha, o SDK a chama novamente caso a falha tenha sido causada por um erro transitório. Se a falha for causada pelo conteúdo do blob, a função falhará sempre que tentar processar o blob. Por padrão, o SDK chama uma função até cinco vezes para um blob específico. Se a quinta tentativa falhar, o SDK adicionará uma mensagem a uma fila denominada *webjobs-blobtrigger-poison*.

A mensagem da fila para blobs suspeitos é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato *{nome do aplicativo de funções}*.Functions.*{nome da função}*)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

#### Sondagem de blobs para grandes contêineres

Se o contêiner de blob que o gatilho está monitorando contiver mais de 10.000 blobs, as verificações de tempo de execução do Functions varrerão os arquivos de log em busca de blobs novos ou alterados. Esse processo não ocorre em tempo real; uma função não poderá ser disparada até vários minutos ou mais depois que o blob for criado. Além disso, os [logs de armazenamento são criados com base nos "melhores esforços"](https://msdn.microsoft.com/library/azure/hh343262.aspx); não há qualquer garantia de que todos os eventos serão capturados. Sob algumas condições, logs poderão ser perdidos. Se as limitações de velocidade e de confiabilidade de gatilhos de blob para grandes contêineres não forem aceitáveis para o seu aplicativo, o método recomendado será criar uma mensagem de fila ao criar o blob e usar um gatilho de fila em vez de um gatilho de blob para processar o blob.
 
## <a id="storageblobbindings"></a> Associações de entrada e de saída de blob do Armazenamento do Azure

#### function.json de uma associação de entrada ou de saída de blobs de armazenamento

O arquivo *function.json* especifica as propriedades a seguir.

- `name`: o nome da variável usada no código de função para o blob.
- `path`: um caminho que especifica o contêiner do qual o blob será lido ou onde será gravado, contendo opcionalmente um padrão de nome de blob.
- `connection`: o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. Se você deixar o `connection` vazio, a associação funcionará com a cadeia de conexão de armazenamento padrão para o aplicativo de funções, que é especificado pela configuração de aplicativo AzureWebJobsStorage.
- `type`: deve ser definido como *blob*.
- `direction`: defina como *in* ou *out*.

O *function.json* de exemplo para uma associação de entrada ou de saída de blob de armazenamento, usando um gatilho de fila para copiar um blob:

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

#### Tipos de entrada e de saída de blob com suporte

A associação `blob` pode serializar ou desserializar os seguintes tipos em funções do Node.js ou do C#:

* Objeto (`out T` em C# para blob de saída: criará um blob como um objeto nulo se o valor do parâmetro for nulo quando a função terminar)
* Cadeia de caracteres (`out string` em C# para blob de saída: criará um blob somente se o parâmetro de cadeia de caracteres não for nulo quando a função retornar)

Em funções do C#, também é possível associar aos seguintes tipos:

* `TextReader` (somente entrada)
* `TextWriter` (somente saída)
* `Stream`
* `CloudBlobStream` (somente saída)
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`

#### Exemplo de código C# de saída de blob

Este exemplo de código C# copia um blob cujo nome é recebido em uma mensagem de fila.

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

## <a id="storagetablesbindings"></a> Associações de entrada e de saída de tabelas do Armazenamento do Azure

#### function.json para tabelas de armazenamento

O *function.json* especifica as propriedades a seguir.

- `name`: o nome da variável usada no código de função para a associação de tabela.
- `tableName`: o nome da tabela.
- `partitionKey` e `rowKey`: usadas em conjunto para ler uma única entidade em uma função do C# ou do Node, ou para gravar uma única entidade em uma função do Node.
- `take`: o número máximo de linhas a serem lidas para a entrada de tabela em uma função do Node.
- `filter`: expressão de filtro OData para a entrada de tabela em uma função do Node.
- `connection`: o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. Se você deixar o `connection` vazio, a associação funcionará com a cadeia de conexão de armazenamento padrão para o aplicativo de funções, que é especificado pela configuração de aplicativo AzureWebJobsStorage.
- `type`: deve ser definido como *table*.
- `direction`: defina como *in* ou *out*.

O *function.json* de exemplo a seguir usa um gatilho de fila para ler uma linha da tabela. O JSON fornece um valor de chave de partição embutido em código e especifica que a chave de linha virá da mensagem da fila.

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### Tipos de entrada e de saída de tabelas de armazenamento com suporte

A associação `table` pode serializar ou desserializar objetos em funções do Node.js ou do C#. Os objetos terão as propriedades RowKey e PartitionKey.

Em funções do C#, também é possível associar aos seguintes tipos:

* `T` em que `T` implementa `ITableEntity`
* `IQueryable<T>` (somente entrada)
* `ICollector<T>` (somente saída)
* `IAsyncCollector<T>` (somente saída)

#### Cenários de associação de tabelas de armazenamento

A associação de tabela dá suporte aos seguintes cenários:

* Ler uma única linha em uma função do C# ou do Node.

	Definir `partitionKey` e `rowKey`. As propriedades `filter` e `take` não são usadas neste cenário.

* Ler várias linhas em uma função do C#.

	O tempo de execução de Functions fornece um objeto `IQueryable<T>` associado à tabela. O tipo `T` deve derivar de `TableEntity` ou implementar `ITableEntity`. As propriedades `partitionKey`, `rowKey`, `filter` e `take` não são usadas neste cenário; você pode usar o objeto `IQueryable` para fazer qualquer filtragem necessária.

* Ler várias linhas em uma função do Node.

	Definir as propriedades `filter` e `take`. Não definir `partitionKey` ou `rowKey`.

* Gravar uma ou mais linhas em uma função do C#.

	O tempo de execução de Functions oferece uma associação `ICollector<T>` ou `IAsyncCollector<T>` à tabela, em que `T` especifica o esquema das entidades que você deseja adicionar. Geralmente, o tipo `T` deriva de `TableEntity` ou implementa `ITableEntity`, mas isso não é obrigatório. As propriedades `partitionKey`, `rowKey`, `filter` e `take` não são usadas neste cenário.

#### Exemplo de tabelas de armazenamento: ler uma única entidade de tabela em C# ou em Node

O exemplo de código C# a seguir funciona com o arquivo *function.json* mostrado anteriormente para ler uma entidade de tabela única. A mensagem da fila tem o valor de chave de linha e a entidade de tabela é lida em um tipo definido no arquivo *run.csx*. O tipo inclui as propriedades `PartitionKey` e `RowKey`, e não deriva de `TableEntity`.

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

O exemplo de código Node a seguir também funciona com o arquivo *function.json* anterior para ler uma entidade de tabela única.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### Exemplo de tabelas de armazenamento: ler várias entidades de tabela em C# 

O exemplo de código *function.json* e C# a seguir lê entidades para uma chave de partição especificada na mensagem de fila.

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
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O código C# adiciona uma referência ao SDK de Armazenamento do Azure para que o tipo de entidade possa derivar de `TableEntity`.

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

#### Exemplo de tabelas de armazenamento: criar entidades de tabela em C# 

O exemplo de *function.json* e *run.csx* a seguir mostra como gravar entidades de tabela em C#.

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
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

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

#### Exemplo de tabelas de armazenamento: criar uma entidade de tabela em Node

O exemplo de *function.json* e *run.csx* a seguir mostra como gravar uma entidade de tabela em Node.

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
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## Próximas etapas

[AZURE.INCLUDE [próximas etapas](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0824_2016-->