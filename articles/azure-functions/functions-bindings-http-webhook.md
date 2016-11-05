---
title: Associações HTTP e de webhook do Azure Functions | Microsoft Docs
description: Entenda como usar gatilhos e associações HTTP e de webhook no Azure Functions.
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: ''
tags: ''
keywords: funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor

ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/22/2016
ms.author: chrande

---
# Associações HTTP e de webhook do Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e codificar gatilhos e associações HTTP e de webhook no Azure Functions.

[!INCLUDE [introdução](../../includes/functions-bindings-intro.md)]

## function.json para associações HTTP e de webhook
O arquivo *function.json* fornece as propriedades pertinentes à solicitação e à resposta.

Propriedades da solicitação HTTP:

* `name`: nome da variável usada no código de função para o objeto da solicitação (ou o corpo da solicitação no caso de funções do Node.js).
* `type`: deve ser definido como *httpTrigger*.
* `direction`: deve ser definido como *in*.
* `webHookType`: para gatilhos WebHook, os valores válidos são *github*, *slack* e *genericJson*. Para um gatilho HTTP que não seja um WebHook, defina essa propriedade como uma cadeia de caracteres vazia. Para saber mais sobre WebHooks, veja a seção [Gatilhos WebHook](#webhook-triggers) a seguir.
* `authLevel`: não se aplica a gatilhos WebHook. Defina como "função" para solicitar a chave de API, como "anônimo" para remover o requisito de chave de API ou como "admin" para exigir a chave mestra de API. Veja [Chaves de API](#apikeys) abaixo para saber mais.

Propriedades da resposta HTTP:

* `name`: nome da variável usada no código de função para o objeto de resposta.
* `type`: deve ser definido como *http*.
* `direction`: deve ser definido como *out*.

*function.json* de exemplo:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
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

## Gatilhos WebHook
Um gatilho WebHook é um gatilho HTTP que tem os seguintes recursos projetados para WebHooks:

* Para provedores específicos do WebHook (no momento, o GitHub e o Slack têm suporte), o tempo de execução do Functions valida a assinatura do provedor.
* Para as funções do Node.js, o tempo de execução do Functions fornece o corpo da solicitação em vez de o objeto da solicitação. Não há nenhum tratamento especial para as funções do C#, já que você controla o que é fornecido ao especificar o tipo de parâmetro. Se você especificar `HttpRequestMessage`, obterá o objeto da solicitação. Se você especificar um tipo POCO, o tempo de execução do Functions tentará analisar um objeto JSON no corpo da solicitação para preencher as propriedades do objeto.
* Para disparar uma função WebHook, a solicitação HTTP deverá incluir uma chave de API. Para os gatilhos HTTP não WebHook, esse requisito é opcional.

Para saber mais sobre como configurar um WebHook do GitHub, veja [GitHub Developer - criando WebHooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409).

## URL para disparar a função
Para disparar uma função, você pode enviar para uma URL uma solicitação HTTP que seja uma combinação da URL do aplicativo de funções e do nome da função:

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

## Chaves de API
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

> [!NOTE]
> Quando você estiver configurando um gatilho WebHook, não compartilhe a chave mestra com o provedor WebHook. Use uma chave que só funcione com a função que processa o WebHook. A chave mestra pode ser usada para disparar qualquer função, até mesmo as funções desabilitadas.
> 
> 

## Código C# de exemplo para uma função de gatilho HTTP
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

## Código F# de exemplo para uma função de gatilho HTTP
O código de exemplo procura um parâmetro `name` na cadeia de consulta ou no corpo da solicitação HTTP.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Você precisará de um arquivo `project.json` que usa o NuGet para fazer referência a assemblies `FSharp.Interop.Dynamic` e `Dynamitey`, como este:

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

Isso usará o NuGet para buscar suas dependências e fará referência a elas em seu script.

## Código Node.js de exemplo para uma função de gatilho HTTP
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

## Código C# de exemplo para uma função WebHook do GitHub
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

## Código F# de exemplo para uma função WebHook do GitHub
Este código de exemplo registra em log comentários sobre problemas do GitHub.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

## Código Node.js de exemplo para uma função WebHook do GitHub
Este código de exemplo registra em log comentários sobre problemas do GitHub.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## Próximas etapas
[!INCLUDE [próximas etapas](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->