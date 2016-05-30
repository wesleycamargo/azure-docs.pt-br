<properties
	pageTitle="Gatilhos e associações do Azure Functions | Microsoft Azure"
	description="Entenda como usar gatilhos e associações no Azure Functions."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="04/07/2016"
	ms.author="chrande"/>

# Referências de gatilhos e de associações do Azure Functions para desenvolvedores

Este artigo explica como configurar e codificar gatilhos e associações no Azure Functions. A maioria dessas associações é facilmente gerenciada por meio da interface do usuário **Integrar** do portal do Azure , mas o portal não explica todas as funcionalidades e as opções para cada associação.

Este artigo pressupõe que você já tenha lido a [referência do desenvolvedor do Azure Functions](functions-reference.md) e os artigos de referência do [C#](functions-reference-csharp.md) ou do [Node](functions-reference-node.md) para desenvolvedores.

## Gatilhos e associações HTTP e WebHook

Você pode usar um gatilho HTTP ou WebHook para chamar uma função em resposta a uma solicitação HTTP. A solicitação deve incluir uma chave API, que atualmente está disponível apenas na interface do usuário do portal do Azure.

A URL da função é uma combinação da URL do aplicativo de função e o nome da função:

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

O arquivo *function.json* fornece as propriedades pertinentes para a solicitação e a resposta HTTP.

Propriedades da solicitação HTTP:

- `name`: nome da variável usada no código de função para o objeto da solicitação (ou o corpo da solicitação no caso de funções do Node.js).
- `type`: deve ser definido como *httpTrigger*.
- `direction`: deve ser definido como *in*. 
- `webHookType`: para gatilhos WebHook, os valores válidos são *github*, *slack* e *genericJson*. Para um gatilho HTTP que não seja um WebHook, defina essa propriedade como uma cadeia de caracteres vazia. Para saber mais sobre WebHooks, consulte a seção [Gatilhos WebHook](#webhook-triggers) a seguir.
- `authLevel`: não se aplica a gatilhos WebHook. Defina como "função" para solicitar a chave de API, como "anônimo" para remover o requisito de chave de API ou como "admin" para exigir a chave mestra de API. Consulte [Chaves de API](#apikeys) abaixo para obter mais informações.

Propriedades da resposta HTTP:

- `name`: nome da variável usada no código de função para o objeto de resposta.
- `type`: deve ser definido como *http*.
- `direction`: deve ser definido como *out*. 
 
*function.json* de exemplo:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

### Gatilhos WebHook

Um gatilho WebHook é um gatilho HTTP que tem os seguintes recursos projetados para WebHooks:

* Para provedores específicos do WebHook (no momento, o GitHub e o Slack têm suporte), o tempo de execução do Functions valida a assinatura do provedor.
* Para as funções do Node.js, o tempo de execução do Functions fornece o corpo da solicitação em vez de o objeto da solicitação. Não há nenhum tratamento especial para as funções do C#, já que você controla o que é fornecido ao especificar o tipo de parâmetro. Se você especificar `HttpRequestMessage`, obterá o objeto da solicitação. Se você especificar um tipo POCO, o tempo de execução do Functions tentará analisar um objeto JSON no corpo da solicitação para preencher as propriedades do objeto.
* Para disparar uma função WebHook, a solicitação HTTP deverá incluir uma chave de API. Para os gatilhos HTTP não WebHook, esse requisito é opcional.

Para saber mais sobre como configurar um WebHook do GitHub, consulte [GitHub Developer - Creating WebHooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409).

### Chaves de API

Por padrão, uma chave de API deve ser incluída com uma solicitação HTTP para disparar uma função HTTP ou WebHook. A chave pode ser incluída em uma variável de cadeia de caracteres de consulta chamada `code`, ou pode ser incluída em um cabeçalho HTTP `x-functions-key`. Para as funções não WebHook, você pode indicar que uma chave de API não é necessária, definindo a propriedade `authLevel` como "anonymous" no arquivo *function.json*.

Você pode encontrar os valores de chave de API na pasta *D:\\home\\data\\Functions\\secrets* no sistema de arquivos do aplicativo de função. A chave mestra e a chave de função são definidas no arquivo *host.json*, como mostra este exemplo.

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

A chave de função do *host.json* pode ser usada para disparar qualquer função, mas não irá disparar uma função desabilitada. A chave mestra pode ser usada para disparar qualquer função e irá disparar uma função mesmo se ela estiver desabilitada. Você pode configurar uma função para exigir a chave mestra ao definir a propriedade `authLevel` como "admin".

Se a pasta *secrets* contiver um arquivo JSON com o mesmo nome da função, a propriedade `key` nesse arquivo também poderá ser usada para disparar a função e essa chave só funcionará com a função a que se refere. Por exemplo, a chave de API para uma função chamada `HttpTrigger` é especificada em *HttpTrigger.json* na pasta *secrets*. Aqui está um exemplo:

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] Quando você estiver configurando um gatilho WebHook, não compartilhe a chave mestra com o provedor WebHook. Use uma chave que só funcione com a função que processa o WebHook. A chave mestra pode ser usada para disparar qualquer função, até mesmo as funções desabilitadas.

### Código C# de exemplo para uma função de gatilho HTTP 

O código de exemplo procura um parâmetro `name` na cadeia de consulta ou no corpo da solicitação HTTP.

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

### Código Node.js de exemplo para uma função de gatilho HTTP 

Este código de exemplo procura um parâmetro `name` na cadeia de consulta ou no corpo da solicitação HTTP.

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

### Código C# de exemplo para uma função WebHook do GitHub 

Este código de exemplo registra em log comentários sobre problemas do GitHub.

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

### Código Node.js de exemplo para uma função WebHook do GitHub 

Este código de exemplo registra em log comentários sobre problemas do GitHub.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## Gatilho de temporizador

O arquivo *function.json* fornece uma expressão e uma opção de agenda que indica se a função deve ser disparada imediatamente.

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "runOnStartup": true,
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O gatilho de temporizador lida com a expansão de várias instâncias automaticamente: apenas uma única instância de uma função específica de temporizador será executada em todas as instâncias.

### Formato de expressão de agendamento

A expressão de agendamento pode ser uma [expressão CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) que inclui seis campos: {segundo} {minuto} {hora} {dia} {mês} {dia da semana}. Muitos documentos de expressão cron encontrados online omitem o campo {segundo} e, portanto, se você copiar de um deles, terá de ajustar o campo extra.

A expressão de agendamento também pode estar no formato *hh:mm:ss* para especificar o atraso entre cada disparo da função.

Veja alguns exemplos de expressão de agendamento.

Para disparar uma vez a cada cinco minutos:

```json
"schedule": "0 */5 * * * *",
"runOnStartup": false,
```

Para disparar imediatamente e então a cada duas horas depois disso:

```json
"schedule": "0 0 */2 * * *",
"runOnStartup": true,
```

Para disparar a cada 15 segundos:

```json
"schedule": "00:00:15",
"runOnStartup": false,
```

### Exemplo de código C# de gatilho de temporizador

Este exemplo de código G# grava um único log sempre que a função é disparada.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## Gatilhos e associações do Armazenamento do Azure (filas, blobs, tabelas)

Essa seção contém os seguintes procedimentos:

* [Gatilho de fila do Armazenamento do Azure](#storagequeuetrigger)
* [Associação de saída de fila do Armazenamento do Azure](#storagequeueoutput)
* [Gatilho de blob do Armazenamento do Azure](#storageblobtrigger)
* [Associações de entrada e de saída de blob do Armazenamento do Azure](#storageblobbindings)
* [Associações de entrada e de saída de tabelas do Armazenamento do Azure](#storagetablesbindings)

### <a id="storagequeuetrigger"></a> Gatilho de fila do Armazenamento do Azure

O arquivo *function.json* para um gatilho de fila de armazenamento especifica as propriedades a seguir.

- `name`: o nome da variável usada no código de função para a fila ou a mensagem da fila. 
- `queueName`: o nome da fila a ser sondada. Para regras de nomenclatura de fila, consulte [Nomeando Filas e Metadados](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. Se você deixar `connection` vazio, o gatilho funcionará com a cadeia de conexão de armazenamento padrão para o aplicativo de funções, que é especificado pela configuração do aplicativo AzureWebJobsStorage.
- `type`: deve ser definido como *queueTrigger*.
- `direction`: deve ser definido como *in*. 

#### Exemplo de *Function.json* de um gatilho de fila de armazenamento

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

Se você quiser manipular mensagens suspeitas manualmente, poderá obter o número de vezes que uma mensagem foi selecionada para processamento ao verificar `dequeueCount`.

### <a id="storagequeueoutput"></a> Associação de saída de fila do Armazenamento do Azure

O arquivo *function.json* para uma associação de saída da fila de armazenamento especifica as propriedades a seguir.

- `name`: o nome da variável usada no código de função para a fila ou a mensagem da fila. 
- `queueName`: o nome da fila. Para regras de nomenclatura de fila, consulte [Nomeando Filas e Metadados](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. Se você deixar `connection` vazio, o gatilho funcionará com a cadeia de conexão de armazenamento padrão para o aplicativo de funções, que é especificado pela configuração do aplicativo AzureWebJobsStorage.
- `type`: deve ser definido como *queue*.
- `direction`: deve ser definido como *out*. 

#### Exemplo de *Function.json* de uma associação de saída de fila de armazenamento

Este exemplo usa um gatilho de fila e grava uma mensagem de fila.

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

* Objeto (`out T` em C#, criará uma mensagem com um objeto nulo se o parâmetro for nulo quando a função terminar)
* Cadeia de caracteres (`out string` no C#, criará a mensagem da fila se o valor do parâmetro não for nulo quando a função terminar)
* Matriz de bytes (`out byte[]` em C#, funciona como uma cadeia de caracteres) 
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

### <a id="storageblobtrigger"></a> Gatilho de blob do Armazenamento do Azure

O arquivo *function.json* para um gatilho de blob de armazenamento especifica as propriedades a seguir.

- `name`: o nome da variável usada no código de função para o blob. 
- `path`: um caminho que especifica o contêiner a ser monitorado e, opcionalmente, um padrão de nome de blob.
- `connection`: o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. Se você deixar `connection` vazio, o gatilho funcionará com a cadeia de conexão de armazenamento padrão para o aplicativo de funções, que é especificado pela configuração do aplicativo AzureWebJobsStorage.
- `type`: deve ser definido como *blobTrigger*.
- `direction`: deve ser definido como *in*.

#### Exemplo de *Function.json* de um gatilho de blob de armazenamento

Este exemplo dispara em qualquer blob adicionado ao contêiner samples-workitems.

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

Esse caminho também poderia ser um blob chamado *original-blob1.txt* e o valor das variáveis `blobname` e `blobextension` no código de função seria *original-Blob1* e *txt*.

Você pode restringir os tipos de blobs que disparam a função especificando um padrão com um valor fixo para a extensão de arquivo. Se você definir o `path` como *samples/{nome}.png*, somente os blobs *.png* do contêiner *samples* vão disparar a função.

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

Se o contêiner de blob que o gatilho está monitorando contiver mais de 10.000 blobs, as verificações de tempo de execução do Functions varrerão os arquivos de log em busca de blobs novos ou alterados. Esse processo não ocorre em tempo real; uma função não poderá ser disparada até vários minutos ou mais depois que o blob for criado. Além disso, os [logs de armazenamento são criados com base nos "melhores esforços"](https://msdn.microsoft.com/library/azure/hh343262.aspx); não há nenhuma garantia de que todos os eventos serão capturados. Sob algumas condições, logs poderão ser perdidos. Se as limitações de velocidade e de confiabilidade de gatilhos de blob para grandes contêineres não forem aceitáveis para o seu aplicativo, o método recomendado será criar uma mensagem de fila ao criar o blob e usar um gatilho de fila em vez de um gatilho de blob para processar o blob.
 
### <a id="storageblobbindings"></a> Associações de entrada e de saída de blobs do Armazenamento do Azure

O arquivo *function.json* para uma associação de entrada ou de saída de blob de armazenamento especifica as propriedades a seguir.

- `name`: o nome da variável usada no código de função para o blob. 
- `path`: um caminho que especifica o contêiner do qual o blob será lido ou aonde será gavado, contendo opcionalmente um padrão de nome de blob.
- `connection`: o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. Se você deixar `connection` vazio, a associação funcionará com a cadeia de conexão de armazenamento padrão para o aplicativo de funções, que é especificado pela configuração do aplicativo AzureWebJobsStorage.
- `type`: deve ser definido como *blob*.
- `direction`: defina como *in* ou *out*. 

#### Exemplo de *Function.json* de uma associação de entrada ou de saída de blobs de armazenamento

Este exemplo usa um gatilho de fila para copiar um blob:

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

A associação `blob` pode serializar ou desserializar os seguintes tipos em funções Node.js ou C#:

* Objeto (`out T` no C# para blob de saída: criará um blob como um objeto nulo se o valor do parâmetro for nulo quando a função terminar)
* Cadeia de caracteres (`out string` no C# para blob de saída: criará um blob somente se o parâmetro de cadeia de caracteres não for nulo quando a função retornar)

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

### <a id="storagetablesbindings"></a> Associações de entrada e de saída de tabelas do Armazenamento do Azure

O *function.json* para tabelas de armazenamento especifica as propriedades a seguir.

- `name`: o nome da variável usada no código de função para a associação de tabela. 
- `tableName`: o nome da tabela.
- `partitionKey` e `rowKey`: usados em conjunto para ler uma única entidade em uma função do C# ou do Node, ou para gravar uma única entidade em uma função do Node.
- `take`: o número máximo de linhas a serem lidas para a tabela de entrada em uma função Node.
- `filter`: expressão de filtro OData para a tabela de entrada em uma função de Node.
- `connection`: o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. Se você deixar `connection` vazio, a associação funcionará com a cadeia de conexão de armazenamento padrão para o aplicativo de funções, que é especificado pela configuração do aplicativo AzureWebJobsStorage.
- `type`: deve ser definido como *table*.
- `direction`: defina como *in* ou *out*. 

#### Tipos de entrada e de saída de tabelas de armazenamento com suporte

A associação `table` pode serializar ou desserializar objetos em funções do Node.js ou C#: Os objetos terão as propriedades RowKey e PartitionKey.

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

	Defina as propriedades `filter` e `take`. Não defina `partitionKey` ou `rowKey`.

* Gravar uma ou mais linhas em uma função do C#.

	O tempo de execução do Functions oferece uma associação `ICollector<T>` ou `IAsyncCollector<T>` à tabela, em que `T` especifica o esquema das entidades que você deseja adicionar. Geralmente, o tipo `T` deriva de `TableEntity` ou implementa `ITableEntity`, mas isso não é obrigatório. As propriedades `partitionKey`, `rowKey`, `filter` e `take` não são usadas neste cenário.

#### Exemplo de tabelas de armazenamento: ler uma única entidade de tabela em C# ou em Node

Este exemplo de *function.json* usa um gatilho de fila para ler uma única linha da tabela, com um valor de chave de partição embutido em código e a chave de linha fornecida na mensagem de fila.

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

O exemplo de código C# a seguir funciona com o arquivo *function.json* anterior para ler uma entidade de tabela única. A mensagem da fila tem o valor de chave de linha e a entidade de tabela é lida em um tipo definido no arquivo *run.csx*. O tipo inclui as propriedades `PartitionKey` e `RowKey`, e não deriva de `TableEntity`.

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

O exemplo de *function.json* e de código C# a seguir lê entidades para uma chave de partição especificada na mensagem de fila.

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

O código C# adiciona uma referência ao SDK do Armazenamento do Azure para que o tipo de entidade possa derivar de `TableEntity`.

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

## Gatilhos e associações do Barramento de Serviço do Azure

Essa seção contém os seguintes procedimentos:

* [Barramento de Serviço do Azure: comportamento de PeekLock](#sbpeeklock)
* [Barramento de Serviço do Azure: manipulação de mensagens suspeitas](#sbpoison)
* [Barramento de Serviço do Azure: threading simples](#sbsinglethread)
* [Fila do Barramento de Serviço do Azure ou gatilho de tópico](#sbtrigger)
* [Associação de saída de fila ou tópico do Barramento de Armazenamento do Azure](#sboutput)

### <a id="sbpeeklock"></a> Barramento de Serviço do Azure: comportamento de PeekLock

O tempo de execução Functions receberá uma mensagem no modo `PeekLock` e chamadas `Complete` na mensagem se a função for concluída com êxito, ou chamadas `Abandon` se a função falhar. Se a função for executada por mais tempo que o limite `PeekLock`, o bloqueio é renovado automaticamente.

### <a id="sbpoison"></a> Barramento de Serviço do Azure: manipulação de mensagens suspeitas

O Barramento de Serviço faz seu próprio tratamento de mensagens suspeitas que não pode ser controlado ou definido na configuração ou código do Azure Functions.

### <a id="sbsinglethread"></a> Barramento de Serviço do Azure: threading simples

Por padrão, o tempo de execução do Functions processa várias mensagens da fila simultaneamente. Para direcionar o tempo de execução para processar uma única fila ou mensagem de tópico de cada vez, defina `serviceBus.maxConcurrrentCalls` como 1 no arquivo *host.json*. Para obter informações sobre o arquivo *host.json*, consulte [Estrutura da Pastas](functions-reference.md#folder-structure) no artigo de referência do Desenvolvedor e [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) no wiki do repositório WebJobs.Script.

### <a id="sbtrigger"></a> Fila do Barramento de Serviço do Azure ou gatilho de tópico

O arquivo *function.json* para um gatilho do Barramento de Serviço especifica as propriedades a seguir.

- `name`: o nome da variável usada no código de função para a fila ou tópico, ou a mensagens de tópico ou fila. 
- `queueName`: para gatilho de fila somente, o nome da fila a ser sondada.
- `topicName`: para gatilho de tópico somente, o nome do tópico a ser sondado.
- `subscriptionName`: para gatilho de tópico somente, o nome da assinatura.
- `connection`: o nome de uma configuração de aplicativo que contém uma cadeia de conexão do Barramento de Serviço. A cadeia de conexão deve ser voltada para um namespace do Barramento de Serviço, não limitada a uma fila ou tópico específico. Se a cadeia de conexão não gerenciar direitos, defina a propriedade `accessRights`. Se você deixar `connection` vazio, o gatilho ou a associação funcionará com a cadeia de conexão do Barramento de Serviço padrão para o aplicativo de funções, que é especificado pela configuração do aplicativo AzureWebJobsServiceBus.
- `accessRights`: especifica os direitos de acesso disponíveis para a cadeia de conexão. O valor padrão é `manage`. Defina como `listen` se você estiver usando uma cadeia de conexão que não fornece permissões de gerenciamento. Caso contrário, o tempo de execução do Functions pode tentar e falha ao executar operações que exigem gerenciamento de direitos.
- `type`: deve ser definido como *serviceBusTrigger*.
- `direction`: deve ser definido como *in*. 

A mensagem da fila do Barramento de Serviço pode ser desserializada para qualquer um destes tipos:

* Objeto (de JSON)
* string
* matriz de bytes 
* `BrokeredMessage` (C#) 

#### Exemplo do *Function.json* para usar um gatilho de fila do Barramento de Serviço

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### Exemplo de código C# que processa uma mensagem de fila do Barramento de Serviço

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### Exemplo de código Node.js que processa uma mensagem de fila do Barramento de Serviço

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

### <a id="sboutput"></a> Fila do Barramento de Serviço do Azure ou saída de tópico

O arquivo *function.json* para uma associação de saída do Barramento de Serviço especifica as propriedades a seguir.

- `name`: o nome da variável usada no código de função para a fila ou mensagem da fila. 
- `queueName`: para gatilho de fila somente, o nome da fila a ser sondada.
- `topicName`: para gatilho de tópico somente, o nome do tópico a ser sondado.
- `subscriptionName`: para gatilho de tópico somente, o nome da assinatura.
- `connection`: mesmo para o gatilho do Barramento de Serviço.
- `accessRights`: especifica os direitos de acesso disponíveis para a cadeia de conexão. O valor padrão é `manage`. Defina como `send` se você estiver usando uma cadeia de conexão que não fornece permissões de gerenciamento. Caso contrário, o tempo de execução do Functions pode tentar e falha ao executar operações que exigem gerenciamento de direitos, tais como da criação de filas.
- `type`: deve ser definido como *serviceBus*.
- `direction`: deve ser definido como *out*. 

O Azure Functions pode criar uma mensagem de fila do Barramento de Serviço de qualquer um dos tipos a seguir.

* Objeto (sempre cria uma mensagem JSON, criará a mensagem com um objeto nulo se o valor for nulo quando a função terminar)
* cadeia de caracteres (criará uma mensagem se o valor não for nulo quando a função terminar)
* matriz de bytes (funciona como uma cadeia de caracteres) 
* `BrokeredMessage` (C#, funciona como cadeia de caracteres)

Para criar várias mensagens em uma função C#, você pode usar `ICollector<T>` ou `IAsyncCollector<T>`. Uma mensagem é criada quando você chama o método `Add`.

#### Exemplo de *function.json* para usar um gatilho de temporizador para gravar mensagens de fila do Barramento de Serviço

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Exemplos de código C# que cria mensagens de fila do Barramento de Serviço

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### Exemplo de código Node.js que cria uma mensagem de fila do Barramento de Serviço

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## Associações do Banco de Dados de Documentos do Azure

Essa seção contém os seguintes procedimentos:

* [Associação de entrada do Banco de Dados de Documentos do Azure](#docdbinput)
* [Associação de saída do Banco de Dados de Documentos do Azure](#docdboutput)

### <a id="docdbinput"></a> Associação de entrada do Banco de Dados de Documentos do Azure

As associações de entrada podem carregar um documento de uma coleção do Banco de Dados de Documentos e passá-lo diretamente para sua associação. A identificação do documento pode ser determinada com base no gatilho que invocou a função. Em uma função do C#, quaisquer alterações feitas no registro serão enviadas automaticamente para a coleção quando a função sair com êxito.

O arquivo function.json fornece as seguintes propriedades para uso com a associação de entrada do Banco de Dados de Documentos:

- `name`: nome da variável usada no código de função para o documento.
- `type`: deve ser definido como "documentdb".
- `databaseName`: o banco de dados que contém o documento.
- `collectionName`: a coleção que contém o documento.
- `id`: a ID do documento a ser recuperado. Essa propriedade oferece suporte a associações semelhantes a "{queueTrigger}", que usará o valor da cadeia de caracteres da mensagem de fila como a identificação do documento.
- `connection`: essa cadeia de caracteres deve ser uma Configuração de Aplicativo definida como o ponto de extremidade para sua conta do Banco de Dados de Documentos. Se você escolher a conta na guia Integrar, a configuração do novo Aplicativo será criada para você com um nome que terá este formato: suaConta\_DOCUMENTDB. Se você precisar criar manualmente a configuração do Aplicativo, a cadeia de conexão real deverá ter este formato: AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;.
- `direction: deve ser definido como *"in"*.

function.json de exemplo:
 
	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "id" : "{queueTrigger}",
	      "connection": "MyAccount_DOCUMENTDB",     
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Exemplo de código de entrada do Banco de Dados de Documentos do Azure para um gatilho de fila do C#
 
Usando o function.json de exemplo acima, a associação de entrada do Banco de Dados de Documentos irá recuperar o documento com a identificação que corresponda à cadeia de mensagem de fila e a passará para o parâmetro 'document'. Se esse documento não for encontrado, o parâmetro 'document' será nulo. O documento será então atualizado com o novo valor de texto quando a função sair.
 
	public static void Run(string myQueueItem, dynamic document)
	{   
	    document.text = "This has changed.";
	}

#### Exemplo de código de entrada do Banco de Dados de Documentos do Azure para um gatilho de fila do Node.js
 
Usando o function.json de exemplo acima, a associação de entrada do Banco de Dados de Documentos vai recuperar o documento com a identificação que corresponda à cadeia de mensagem de fila e a passará para a propriedade de associação `documentIn`. Em funções do Node.js, os documentos atualizados não são enviados de volta à coleção. No entanto, você pode passar a associação de entrada diretamente para uma associação de saída do Banco de Dados de Documentos chamada `documentOut` para dar suporte a atualizações. Este exemplo de código atualiza a propriedade de texto do documento de entrada e o define como o documento de saída.
 
	module.exports = function (context, input) {   
	    context.bindings.documentOut = context.bindings.documentIn;
	    context.bindings.documentOut.text = "This was updated!";
	    context.done();
	};

### <a id="docdboutput"></a> Associações de saída do Banco de Dados de Documentos do Azure

Suas funções podem gravar documentos JSON em um Banco de Dados de Documentos do Azure usando a associação de saída **Documento do Banco de Dados de Documentos do Azure**. Para saber mais sobre o Banco de Dados de Documentos do Azure, examine a [Introdução ao Banco de Dados de Documentos](../documentdb/documentdb-introduction.md) e o [Tutorial de Introdução](../documentdb/documentdb-get-started.md).

O arquivo function.json fornece as seguintes propriedades para uso com a associação de saída do Banco de Dados de Documentos:

- `name`: nome da variável usada no código de função para o novo documento.
- `type`: deve ser definido como *"documentdb"*.
- `databaseName`: o banco de dados que contém a coleção na qual o novo documento será criado.
- `collectionName`: a coleção na qual o novo documento será criado.
- `createIfNotExists`: é um valor booliano para indicar se a coleção será criada se ela não existir. O padrão é *false*. O motivo para isso é que as novas coleções são criadas com a taxa de transferência reservada, o que tem implicações de preço. Para obter mais detalhes, visite a [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection`: essa cadeia de caracteres deve ser uma **Configuração de Aplicativo** definida como o ponto de extremidade para sua conta do Banco de Dados de Documentos. Se você escolher sua conta na guia **Integrar**, a configuração do novo Aplicativo será criada para você com um nome que terá este formato: `yourAccount_DOCUMENTDB`. Se você precisar criar manualmente a configuração do Aplicativo, a cadeia de conexão real deverá ter este formato: `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`. 
- `direction`: deve ser definido como *"out"*. 
 
function.json de exemplo:

	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "createIfNotExists": false,
	      "connection": "MyAccount_DOCUMENTDB",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}


#### Exemplo de código de saída do Banco de Dados de Documentos do Azure para um gatilho de fila do Node.js

	module.exports = function (context, input) {
	   
	    context.bindings.document = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	 
	    context.done();
	};

O documento de saída:

	{
	  "text": "I'm running in a Node function! Data: 'example queue data'",
	  "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
	}
 

#### Exemplo de código de saída do Banco de Dados de Documentos do Azure para um gatilho de fila do C#


	using System;

	public static void Run(string myQueueItem, out object document, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   
	    document = new {
	        text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}


#### Exemplo de código de saída do Banco de Dados de Documentos que configura um nome do arquivo

Se você deseja definir o nome do documento na função, basta definir o valor `id`. Por exemplo, se o conteúdo JSON de um funcionário tiver sido removido para a fila semelhante à seguinte:

	{
	  "name" : "John Henry",
      "employeeId" : "123456",
	  "address" : "A town nearby"
	}

Você poderia usar o seguinte código C# em uma função de gatilho de fila:
	
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

Saída de exemplo:

	{
	  "id": "John Henry-123456",
	  "name": "John Henry",
	  "employeeId": "123456",
	  "address": "A town nearby"
	}

## Associações de Aplicativos Móveis do Azure

Os Aplicativos Móveis do Serviço de Aplicativo do Azure permitem que você exponha os dados de ponto de extremidade de tabela para clientes móveis. Esses mesmos dados tabulares podem ser usados em associações de entrada e de saída com o Azure Functions. Como ele dá suporte ao esquema dinâmico, um aplicativo móvel de back-end Node.js é ideal para expor dados tabulares para serem usados com suas funções. O esquema dinâmico está habilitado por padrão e deve ser desabilitado em um aplicativo móvel de produção. Para obter mais informações sobre pontos de extremidade de tabela em um back-end Node.js, consulte [Visão geral: operações de tabela](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations). Nos Aplicativos Móveis, o back-end do Node.js dá suporte à navegação e edição de tabelas no portal. Para obter mais informações, consulte [edição no portal](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#in-portal-editing) no tópico SDK do Node.js. Ao usar um aplicativo móvel de back-end do .NET com o Azure Functions, você deverá atualizar manualmente o modelo de dados conforme exigido pela sua função. Para obter mais informações sobre os pontos de extremidade de tabela em um aplicativo móvel de back-end do .NET, consulte [Como definir um controlador de tabela](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller) no tópico do SDK de back-end do .NET.

Essa seção contém os seguintes procedimentos:

* [Chave de API de tabelas dos Aplicativos Móveis do Azure](#mobiletablesapikey)
* [Associação de entrada de tabelas dos Aplicativos Móveis do Azure](#mobiletablesinput)
* [Associação de saída de tabelas dos Aplicativos Móveis do Azure](#mobiletablesoutput)

### <a id="mobiletablesapikey"></a> Use uma chave de API para proteger o acesso aos seus pontos de extremidade de tabelas dos Aplicativos Móveis.

No Azure Functions, as associações de tabelas móveis permitem especificar uma chave de API, que é um segredo compartilhado que pode ser usado para impedir o acesso indesejado de aplicativos que não fazem parte das suas funções. Os Aplicativos Móveis não possuem suporte interno para a autenticação de chave de API. No entanto, você pode implementar uma chave de API em seu aplicativo móvel de back-end do Node.js ao seguir os exemplos em [Implementação de back-end de Aplicativos Móveis do Serviço de Aplicativo do Azure de uma chave de API](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key). Da mesma forma, você pode implementar uma chave de API em um [aplicativo móvel de back-end .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key).

>[AZURE.IMPORTANT] Essa chave de API não deve ser distribuída com seus clientes de aplicativo móvel, só deve ser distribuída com segurança aos clientes no lado do serviço, como o Azure Functions.

### <a id="mobiletablesinput"></a> Associação de entrada dos Aplicativos Móveis do Azure

As associações de entrada podem carregar um registro de um ponto de extremidade de tabela móvel e passá-lo diretamente para sua associação. A ID do registro é determinada com base no gatilho que invocou a função. Em uma função do C#, quaisquer alterações feitas no registro são enviadas automaticamente para a tabela quando a função sai com êxito.

O arquivo function.json dá suporte às seguintes propriedades para uso com associações de entrada dos Aplicativos Móveis:

- `name`: nome da variável usada no código de função para o novo registro.
- `type`: o tipo de associação deve ser definido como *mobileTable*.
- `tableName`: a tabela na qual o novo registro será criado.
- `id`: a ID do registro a ser recuperado. Essa propriedade dá suporte a associações semelhantes a `{queueTrigger}`, que usará o valor da cadeia de caracteres da mensagem de fila como a identificação do registro.
- `apiKey`: cadeia de caracteres que é a configuração de aplicativo que especifica a chave de API opcional para seu aplicativo móvel. Isso será necessário quando seu aplicativo móvel usar uma chave de API para restringir o acesso de cliente.
- `connection`: cadeia de caracteres que é a configuração de aplicativo que especifica o URI do seu aplicativo móvel.
- `direction`: direção da associação, que deve ser definida como *in*.

function.json de exemplo:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "id" : "{queueTrigger}",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Exemplo de código dos Aplicativos Móveis do Azure para um gatilho de fila do C#

Com base no function.json de exemplo acima, a associação de entrada recupera o registro do ponto de extremidade de tabela dos Aplicativos Móveis com a ID que corresponde à cadeia de mensagem de fila e a passa para o parâmetro *record*. Quando o registro não for encontrado, o parâmetro será nulo. O documento será então atualizado com o novo valor de *Text* quando a função sair.

	#r "Newtonsoft.Json"	
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, JObject record)
	{
	    if (record != null)
	    {
	        record["Text"] = "This has changed.";
	    }    
	}

#### Exemplo de código dos Aplicativos Móveis do Azure para um gatilho de fila do Node.js

Com base no function.json de exemplo acima, a associação de entrada recupera o registro do ponto de extremidade de tabela dos Aplicativos Móveis com a ID que corresponde à cadeia de mensagem de fila e a passa para o parâmetro *record*. Em funções do Node.js, os registros atualizados não são enviados de volta à tabela. Este exemplo de código grava o registro recuperado no log.

	module.exports = function (context, input) {    
	    context.log(context.bindings.record);
	    context.done();
	};


### <a id="mobiletablesoutput"></a>Associação de saída dos Aplicativos Móveis do Azure

Sua função pode gravar um registro para o ponto de extremidade de tabela dos Aplicativos Móveis usando uma associação de saída.

O arquivo function.json dá suporte às seguintes propriedades para uso com uma associação de saída de tabela móvel:

- `name`: nome da variável usada no código de função para o novo registro.
- `type`: o tipo de associação que deve ser definido como *mobileTable*.
- `tableName`: a tabela na qual o novo registro é criado.
- `apiKey`: cadeia de caracteres que é a configuração de aplicativo que especifica a chave de API opcional para seu aplicativo móvel. Isso será necessário quando seu aplicativo móvel usar uma chave de API para restringir o acesso de cliente.
- `connection`: cadeia de caracteres que é a configuração de aplicativo que especifica o URI do seu aplicativo móvel.
- `direction`: direção da associação, que deve ser definida como *out*.

function.json de exemplo:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

#### Exemplo de código dos Aplicativos Móveis do Azure para um gatilho de fila do C#

Este exemplo de código C# insere um novo registro em um ponto de extremidade de tabela dos Aplicativos Móveis com uma propriedade *Text* na tabela especificada na associação acima.

	public static void Run(string myQueueItem, out object record)
	{
	    record = new {
	        Text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}

#### Exemplo de código dos Aplicativos Móveis do Azure para um gatilho de fila do Node.js

Este exemplo de código Node.js insere um novo registro em um ponto de extremidade de tabela dos Aplicativos Móveis com uma propriedade *Text* na tabela especificada na associação acima.

	module.exports = function (context, input) {
	
	    context.bindings.record = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	
	    context.done();
	};

## Associação de saída do Hub de Notificação do Azure

Suas funções podem enviar notificações por push usando um Hub de Notificação do Azure configurado com muito poucas linhas de código. No entanto, o hub de notificação deve estar configurado para os PNS (Serviços de Notificações de Plataforma) que você deseja usar. Para saber mais sobre a configuração de um Hub de Notificação do Azure e sobre o desenvolvimento de aplicativos cliente que se registram para obter notificações, veja [Introdução aos Hubs de Notificação](../notification-hubs/notification-hubs-windows-store-dotnet-get-started.md) e clique em sua plataforma de cliente de destino na parte superior.

O arquivo function.json fornece as seguintes propriedades para uso com a associação de saída de um hub de notificação:

- `name`: nome da variável usada no código de função para a mensagem do hub de notificação.
- `type`: deve ser definido como *"notificationHub"*.
- `tagExpression`: as expressões de marca permitem que você especifique que as notificações sejam entregues a um conjunto de dispositivos que se registraram para receber notificações que correspondem à expressão de marca. Para saber mais, veja [Expressões de marca e de roteamento](../notification-hubs/notification-hubs-routing-tag-expressions.md).
- `hubName`: nome do recurso de hub de notificação no portal do Azure.
- `connection`: essa cadeia de conexão deve ser uma cadeia de conexão de **Configuração de Aplicativo** definida com o valor *DefaultFullSharedAccessSignature* para seu hub de notificação.
- `direction`: deve ser definido como *"out"*. 
 
function.json de exemplo:

	{
	  "bindings": [
	    {
	      "name": "notification",
	      "type": "notificationHub",
	      "tagExpression": "",
	      "hubName": "my-notification-hub",
	      "connection": "MyHubConnectionString",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

### Configuração da cadeia de conexão do Hub de Notificação do Azure

Para usar uma associação de saída de um hub de notificação, você deve configurar a cadeia de conexão para o hub. Você pode fazer isso na guia *Integrar* simplesmente selecionando seu hub de notificação ou criando um novo.

Você também pode adicionar manualmente uma cadeia de conexão a um hub existente adicionando uma cadeia de conexão à *DefaultFullSharedAccessSignature* para seu hub de notificação. Essa cadeia de conexão fornece sua permissão de acesso à função para enviar mensagens de notificação. O valor da cadeia de conexão *DefaultFullSharedAccessSignature* pode ser acessado do botão **chaves** na folha principal do seu recurso de hub de notificação no portal do Azure. Para adicionar manualmente uma cadeia de conexão ao hub, use estas etapas:

1. Na folha **Aplicativo de funções** do portal do Azure, clique em **Configurações do Aplicativo de funções > Ir para configurações do Serviço de Aplicativo**.

2. Na folha **Configurações**, clique em **Configurações do Aplicativo**.

3. Role para baixo até a seção **Cadeias de conexão** e adicione uma entrada nomeada para o valor *DefaultFullSharedAccessSignature* para o hub de notificação. Altere o tipo para **Personalizado**.
4. Faça referência ao nome da sua cadeia de conexão nas associações de saída. Semelhante à **MyHubConnectionString** usada no exemplo acima.

### Exemplo de código de Hub de Notificação do Azure para um gatilho de temporizador do Node.js 

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates.md) que contém `location` e `message`.

	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	   
	    if(myTimer.isPastDue)
	    {
	        context.log('Node.js is running late!');
	    }
	    context.log('Node.js timer trigger function ran!', timeStamp);  
	    context.bindings.notification = {
	        location: "Redmond",
	        message: "Hello from Node!"
	    };
	    context.done();
	};

### Exemplo de código de Hub de Notificação do Azure para um gatilho de fila do C#

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates.md) que contém `message`.


	using System;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	 
	public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
	}
	 
	private static IDictionary<string, string> GetTemplateProperties(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return templateProperties;
	}

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates.md) que contém `message` usando uma cadeia de caracteres JSON válida.

	using System;
	 
	public static void Run(string myQueueItem,  out string notification, TraceWriter log)
	{
		log.Info($"C# Queue trigger function processed: {myQueueItem}");
		notification = "{"message":"Hello from C#. Processed a queue item!"}";
	}

### Exemplo de código C# de gatilho de fila do Hub de Notificação do Azure usando o tipo Notificação

Este exemplo mostra como usar o tipo `Notification` definido na [Biblioteca de Hubs de Notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Para usar esse tipo e a biblioteca, você deve carregar um arquivo *project.json* para seu aplicativo de funções. O arquivo project.json é um arquivo de texto JSON que terá esta aparência:

	{
	  "frameworks": {
	    ".NETFramework,Version=v4.6": {
	      "dependencies": {
	        "Microsoft.Azure.NotificationHubs": "1.0.4"
	      }
	    }
	  }
	}

Para saber mais sobre o carregamento do seu arquivo project.json, veja [carregando um arquivo project.json](http://stackoverflow.com/questions/36411536/how-can-i-use-nuget-packages-in-my-azure-functions).

Código de exemplo:

	using System;
	using System.Threading.Tasks;
	using Microsoft.Azure.NotificationHubs;
	 
	public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
	{
	   log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   notification = GetTemplateNotification(myQueueItem);
	}
	private static TemplateNotification GetTemplateNotification(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return new TemplateNotification(templateProperties);
	}

## Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Referência do desenvolvedor de C# do Azure Functions](functions-reference-csharp.md)
* [Referência do desenvolvedor de NodeJS do Azure Functions](functions-reference-node.md)

<!---HONumber=AcomDC_0518_2016-->