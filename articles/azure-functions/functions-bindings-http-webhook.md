---
title: "Associações HTTP e de webhook do Azure Functions"
description: "Entenda como usar gatilhos e associações HTTP e de webhook no Azure Functions."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor, HTTP, API, REST"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: mahender
ms.openlocfilehash: 6b3da498a613d63515ecb624b87496cf536c0ebf
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2017
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Associações HTTP e de webhook do Azure Functions

Este artigo explica como trabalhar com associação de HTTP no Azure Functions. O Azure Functions dá suporte a associações de saída e de gatilhos de HTTP.

Um gatilho de HTTP pode ser personalizado para responder a [webhooks](https://en.wikipedia.org/wiki/Webhook). Um gatilho de webhook aceita apenas um conteúdo JSON e valida o JSON. Há versões especiais do gatilho de webhook que facilitam o tratamento de webhooks de determinados provedores, como GitHub e Slack.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="trigger"></a>Gatilho

Um gatilho de HTTP permite invocar uma função com uma solicitação HTTP. Você pode usar um gatilho de HTTP para criar APIs sem servidor e responder a webhooks. 

Por padrão, um gatilho HTTP responderá à solicitação com um código de status HTTP 200 OK e um corpo vazio. Para modificar a resposta, configure uma [associação de saída de HTTP](#http-output-binding).

## <a name="trigger---example"></a>Gatilho - exemplo

Consulte o exemplo específico a um idioma:

* [Pré-compilado C#](#trigger---c-example)
* [Script C#](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Gatilho - exemplo C#

O exemplo a seguir mostra uma [função C# pré-compilada](functions-dotnet-class-library.md) que procura um parâmetro `name` na cadeia de caracteres de consulta ou no corpo da solicitação HTTP.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]HttpRequestMessage req, 
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

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

### <a name="trigger---c-script-example"></a>Gatilho - exemplo de script C#

O exemplo a seguir mostra uma associação de gatilho em um arquivo *function.json* e uma [função de script de C#](functions-reference-csharp.md) que usa a associação. A função procura um parâmetro `name` na cadeia de consulta ou no corpo da solicitação HTTP.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

Aqui está o código de script C# que associa a um `HttpRequestMessage`:

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

Você pode vincular a um objeto personalizado em vez de `HttpRequestMessage`. Esse objeto é criado do corpo da solicitação, analisada como JSON. Da mesma forma, um tipo pode ser passado para a associação de saída da resposta HTTP e isso será retornado como o corpo da resposta, com um código de status 200.

```csharp
using System.Net;
using System.Threading.Tasks;

public static string Run(CustomObject req, TraceWriter log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public String name {get; set;}
}
}
```

### <a name="trigger---f-example"></a>Gatilho - Exemplo F#

O exemplo a seguir mostra uma associação de gatilho em um arquivo *function.json* e uma [função F#](functions-reference-fsharp.md) que usa a associação. A função procura um parâmetro `name` na cadeia de consulta ou no corpo da solicitação HTTP.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

O código F# é o seguinte:

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

Você precisa de um arquivo `project.json` que usa o NuGet para fazer referência aos assemblies `FSharp.Interop.Dynamic` e `Dynamitey`, como mostrado no exemplo a seguir:

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

### <a name="trigger---javascript-example"></a>Gatilho - exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função procura um parâmetro `name` na cadeia de consulta ou no corpo da solicitação HTTP.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

Aqui está o código JavaScript:

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
     
## <a name="trigger---webhook-example"></a>Gatilho - exemplo de webhook

Consulte o exemplo específico a um idioma:

* [Pré-compilado C#](#webhook---c-example)
* [Script C#](#webhook---c-script-example)
* [F#](#webhook---f-example)
* [JavaScript](#webhook---javascript-example)

### <a name="webhook---c-example"></a>Webhook - exemplo de C#

O exemplo a seguir mostra uma [função C# pré-compilada](functions-dotnet-class-library.md) que envia um HTTP 200 em resposta a uma solicitação JSON genérica.

```cs
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="webhook---c-script-example"></a>Webhook - exemplo de script C#

O exemplo a seguir mostra uma associação de gatilho de webhook em um arquivo *function.json* e uma [função C# script](functions-reference-csharp.md) que usa a associação. A função registra em log comentários sobre problemas do GitHub.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

Aqui está o código de script do C#:

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

### <a name="webhook---f-example"></a>Webhook - exemplo de F#

O exemplo a seguir mostra uma associação de gatilho de webhook em um arquivo *function.json* e uma [função F#](functions-reference-fsharp.md) que usa a associação. A função registra em log comentários sobre problemas do GitHub.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

O código F# é o seguinte:

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

### <a name="webhook---javascript-example"></a>Webhook - exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho de webhook em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função registra em log comentários sobre problemas do GitHub.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
```

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="trigger---attributes"></a>Gatilho – atributos

Para funções [C# pré-compiladas](functions-dotnet-class-library.md), use o atributo [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs), definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.Http](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http).

Você pode definir os métodos HTTP permitidos e o nível de autorização nos parâmetros do construtor do atributo, e existem propriedades para o modelo de rota e o tipo de webhook. Para saber mais informações sobre essas configurações, consulte [Gatilho - configuração](#trigger---configuration). Aqui está um atributo `HttpTrigger` em uma assinatura de método:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    ...
}
 ```

Para ver um exemplo completo, consulte [Gatilho – exemplo de C# pré-compilado](#trigger---c-example).

## <a name="trigger---configuration"></a>Gatilho – configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `HttpTrigger` atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **tipo** | n/d| Obrigatório – deve ser definido como `httpTrigger`. |
| **direction** | n/d| Obrigatório – deve ser definido como `in`. |
| **name** | n/d| Obrigatório – o nome da variável usado no código da função da solicitação ou do corpo da solicitação. |
| **authLevel** |  **AuthLevel** |Determina quais chaves, se houver, precisam estar presentes na solicitação para invocar a função. O nível de autorização pode ser um dos seguintes valores: <ul><li><code>anonymous</code>&mdash;Nenhuma chave API é obrigatória.</li><li><code>function</code>&mdash;Uma chave de API específica de função é obrigatória. Esse será o valor padrão se nenhum for fornecido.</li><li><code>admin</code>&mdash;A chave mestra é obrigatória.</li></ul> Para saber mais informações, veja a seção sobre [chaves de autorização](#authorization-keys). |
| **methods** |**Métodos** | Uma matriz dos métodos HTTP para a qual a função responde. Se não for especificada, a função responderá a todos os métodos HTTP. Consulte [personalização do ponto de extremidade http](#trigger---customize-the-http-endpoint). |
| **route** | **Route** | Define o modelo da rota, controlando para quais URLs de solicitação sua função responde. O valor padrão se nenhum for fornecido será `<functionname>`. Para saber mais informações, consulte [personalização do ponto de extremidade http](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** |Configure o gatilho HTTP para atuar como um receptor de [webhook](https://en.wikipedia.org/wiki/Webhook) para o provedor especificado. Não defina a propriedade `methods` se você definir essa propriedade. O tipo de webhook pode ser um dos seguintes valores:<ul><li><code>genericJson</code>&mdash;Um ponto de extremidade de webhook de finalidade geral sem lógica para um provedor específico. Essa configuração restringe as solicitações àquelas que usam HTTP POST e com o tipo de conteúdo `application/json`.</li><li><code>github</code>&mdash;A função responde a [webhooks do GitHub](https://developer.github.com/webhooks/). Não use a propriedade _authLevel_ com os webhooks do GitHub. Para saber mais informações, consulte a seção sobre webhooks do GitHub posteriormente neste artigo.</li><li><code>slack</code>&mdash;A função responde a [webhooks do Slack](https://api.slack.com/outgoing-webhooks). Não use a propriedade _authLevel_ com os webhooks do Slack. Para saber mais informações, consulte a seção sobre webhooks do Slack posteriormente neste artigo.</li></ul>|

## <a name="trigger---usage"></a>Gatilho - uso

Para funções C# e F#, você pode declarar o tipo de entrada do gatilho para ser `HttpRequestMessage` ou um tipo personalizado. Ao escolher `HttpRequestMessage`, você obterá acesso completo ao objeto de solicitação. Para um tipo personalizado, o Functions tenta analisar o corpo da solicitação JSON para definir as propriedades do objeto. 

Para as funções JavaScript, o tempo de execução do Functions fornece o corpo da solicitação em vez do objeto da solicitação. Para saber mais informações, consulte o [exemplo de gatilho do JavaScript](#trigger---javascript-example).

### <a name="github-webhooks"></a>Webhooks do GitHub

Para responder a webhooks do GitHub, primeiramente crie sua função com um Gatilho HTTP e defina a propriedade **webHookType** como `github`. Em seguida, copie a URL e a chave de API na página **Adicionar webhook** do seu repositório GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Para um exemplo, consulte [Criar uma função disparada pelo webhook do GitHub](functions-create-github-webhook-triggered-function.md).

### <a name="slack-webhooks"></a>Webhooks do Slack

O webhook do Slack gera um token em vez de permitir que você o especifique, de modo que é preciso configurar uma chave específica de função com o token do Slack. Consulte [Chaves de autorização](#authorization-keys).

### <a name="customize-the-http-endpoint"></a>Personalização do ponto de extremidade HTTP

Por padrão, quando você cria uma função para um gatilho HTTP ou WebHook, a função é endereçável com uma rota do formulário:

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Você pode personalizar essa rota usando a propriedade `route` opcional na associação de entrada do gatilho HTTP. Por exemplo, o seguinte arquivo *function.json* define uma propriedade `route` para um gatilho HTTP:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Usando esta configuração, a função é agora endereçável com a seguinte rota em vez da rota original.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

Isso permite que o código de função dê suporte a dois parâmetros no endereço, _category_ e _id_. Você pode usar qualquer [Restrição de rota de API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com seus parâmetros. O seguinte código de função em C# faz uso de ambos os parâmetros.

```csharp
public static Task<HttpResponseMessage> Run(HttpRequestMessage req, string category, int? id, 
                                                TraceWriter log)
{
    if (id == null)
        return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
    else
        return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
}
```

Aqui está o código de função do Node.js que usa os mesmos parâmetros de rota.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
} 
```

Por padrão, todas as rotas de função são prefixadas com *api*. Você também pode personalizar ou remover o prefixo usando a propriedade `http.routePrefix` em seu arquivo [host.json](functions-host-json.md). O exemplo a seguir remove o prefixo de rota *api* usando uma cadeia de caracteres vazia para o prefixo no arquivo *host.json*.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="authorization-keys"></a>Chaves de autorização

Os gatilhos HTTP permitem que você use chaves para aumentar a segurança. Um gatilho HTTP padrão pode usá-las como uma chave de API, exigindo que a chave esteja presente na solicitação. Os webhooks podem usar chaves para autorizar solicitações de várias maneiras, dependendo daquilo a que o provedor oferece suporte.

As chaves são armazenadas como parte do seu aplicativo de funções no Azure e criptografadas em repouso. Para exibir suas chaves, criar novas ou reverter chaves para novos valores, navegue para uma de suas funções no portal e selecione "Gerenciar". 

Há dois tipos de chave:

- **Chaves de host**: essas chaves são compartilhadas por todas as funções do aplicativo de funções. Quando usadas como uma chave de API, elas permitem acesso a qualquer função no aplicativo de funções.
- **Chaves de função**: essas chaves se aplicam apenas às funções específicas sob as quais elas foram definidas. Quando usadas como uma chave de API, elas permitem acesso apenas às funções em questão.

Cada chave é nomeada para referência e há uma chave padrão (chamada "default") no nível de função e de host. As chaves de função têm precedência sobre as chaves de host. Quando duas chaves forem definidas com o mesmo nome, a chave de função sempre será usada.

A **chave mestra** é uma chave de host padrão chamada "_master", definida para cada aplicativo de funções. Não é possível revogar essa chave. Ela fornece acesso administrativo às APIs de tempo de execução. O uso de `"authLevel": "admin"` no JSON de associação exigirá que essa chave seja apresentada na solicitação. Qualquer outra chave resultará em falha na autorização.

> [!IMPORTANT]  
> Devido às permissões elevadas concedidas pela chave mestra, você não deve compartilhar essa chave com terceiros nem distribuí-la em aplicativos cliente nativos. Tenha cuidado ao escolher o nível de autorização do administrador.

### <a name="api-key-authorization"></a>Autorização da chave de API

Por padrão, um gatilho HTTP requer uma chave de API na solicitação HTTP. Portanto, sua solicitação HTTP geralmente se parecerá como esta:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

A chave pode ser incluída em uma variável de cadeia de caracteres de consulta chamada `code`, como acima, ou pode ser incluída em um cabeçalho HTTP `x-functions-key`. O valor da chave pode ser qualquer chave de função definida para a função ou qualquer chave de host.

Você pode permitir solicitações anônimas, o que não exige chaves. Você também pode exigir que a chave mestra seja usada. Altere o nível de autorização padrão usando a propriedade `authLevel` no JSON de associação. Para saber mais informações, consulte [Gatilho - configuração](#trigger---configuration).

### <a name="keys-and-webhooks"></a>Chaves e webhooks

A autorização de webhook é tratada pelo componente receptor do webhook, parte do gatilho HTTP e o mecanismo varia com base no tipo de webhook. No entanto, cada mecanismo depende de uma chave. Por padrão, a chave de função chamada "default" será usada. Para usar uma chave diferente, configure o provedor do webhook para enviar o nome da chave com a solicitação de uma das seguintes maneiras:

- **Cadeia de caracteres de consulta**: o provedor passa o nome da chave no parâmetro de cadeia de caracteres de consulta `clientid`, como `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
- **Cabeçalho da solicitação**: o provedor passa o nome da chave no cabeçalho `x-functions-clientid`.

## <a name="trigger---hostjson-properties"></a>Gatilho - propriedades de host.json

O arquivo [host.json](functions-host-json.md) contém configurações que controlam o comportamento de gatilho de HTTP.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Saída

Use a associação de saída HTTP para responder ao remetente da solicitação HTTP. Essa associação requer um gatilho HTTP e permite que você personalize a resposta associada à solicitação do gatilho. Se a associação de saída HTTP não for fornecida, um gatilho HTTP retornará HTTP 200 OK com um corpo vazio. 

## <a name="output---configuration"></a>Saída - configuração

Para C# pré-compilado, não há nenhuma propriedade de configuração de associação de saída específica. Para enviar uma resposta HTTP, faça com que o tipo de retorno da função seja `HttpResponseMessage` ou `Task<HttpResponseMessage>`.

Para outras linguagens, uma associação de saída HTTP é definida por um objeto JSON na matriz `bindings` de function.json, como mostrado no exemplo a seguir:

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json*.

|Propriedade  |Descrição  |
|---------|---------|
| **tipo** |Deve ser definido como `http`. |
| **direction** | Deve ser definido como `out`. |
|**name** | O nome da variável usada no código de função para a resposta. |

## <a name="output---usage"></a>Saída - uso

Você pode usar o parâmetro de saída para responder ao chamador HTTP ou webhook. Você também pode usar os padrões de resposta de padrão do idioma. Para ver exemplos de respostas, consulte o [exemplo de gatilho](#trigger---example) e o [exemplo de webhook](#trigger---webhook-example).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
