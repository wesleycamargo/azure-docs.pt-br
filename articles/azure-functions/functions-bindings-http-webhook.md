---
title: "Associações HTTP e de webhook do Azure Functions | Microsoft Docs"
description: "Entenda como usar gatilhos e associações HTTP e de webhook no Azure Functions."
services: functions
documentationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor, HTTP, API, REST"
ms.assetid: 2b12200d-63d8-4ec1-9da8-39831d5a51b1
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2016
ms.author: mahender
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: f31c0eec6b570c4d9f798185f8f0f8c49a7e400d
ms.contentlocale: pt-br
ms.lasthandoff: 07/20/2017

---
# <a name="azure-functions-http-and-webhook-bindings"></a>Associações HTTP e de webhook do Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e trabalhar com gatilhos e associações HTTP no Azure Functions.
Com isso, você pode usar o Azure Functions para criar APIs sem servidor e responder a webhooks.

O Azure Functions fornece as seguintes associações:
- Um [gatilho HTTP](#httptrigger) permite invocar uma função com uma solicitação HTTP. Ele pode ser personalizado para responder a [webhooks](#hooktrigger).
- Uma [associação de saída HTTP](#output) permite responder à solicitação.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

<a name="httptrigger"></a>

## <a name="http-trigger"></a>Gatilho HTTP
O gatilho HTTP executará sua função em resposta a uma solicitação HTTP. Você pode personalizá-lo para responder a uma URL específica ou a um conjunto de métodos HTTP. Um gatilho HTTP também pode ser configurado para responder a webhooks. 

Usando o portal de Functions, você também pode começar agora mesmo usando um modelo predefinido. Selecione **Nova função** e escolha "API e Webhooks" na lista suspensa **Cenário**. Selecione um dos modelos e clique em **Criar**.

Por padrão, um gatilho HTTP responderá à solicitação com um código de status HTTP 200 OK e um corpo vazio. Para modificar a resposta, configure uma [associação de saída HTTP](#output)

### <a name="configuring-an-http-trigger"></a>Configuração de um gatilho HTTP
Um gatilho HTTP é definido com a inclusão de um objeto JSON semelhante ao que se segue na matriz `bindings` de function.json:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function",
    "methods": [ "get" ],
    "route": "values/{id}"
},
```
A associação dá suporte às seguintes propriedades:

* **name**: obrigatória — o nome da variável usada no código da função para a solicitação ou o corpo da solicitação. Veja [Como trabalhar com um gatilho HTTP no código](#httptriggerusage).
* **type**: obrigatória — deve ser definida como "httpTrigger".
* **direction**: obrigatória — deve ser definida como "in".
* _authLevel_: determina quais chaves, se houver, precisam estar presentes na solicitação para invocar a função. Veja [Como trabalhar com chaves](#keys) abaixo. O valor pode ser um dos seguintes:
    * _anonymous_: nenhuma chave API é obrigatória.
    * _function_: uma chave de API específica de função é obrigatória. Esse será o valor padrão se nenhum for fornecido.
    * _admin_: a chave mestra é obrigatória.
* **methods**: essa é uma matriz dos métodos HTTP para a qual a função responderá. Se não for especificada, a função responderá a todos os métodos HTTP. Veja [Personalização do ponto de extremidade HTTP](#url).
* **route**: define o modelo da rota, controlando para quais URLs de solicitação sua função responderá. O valor padrão se nenhum for fornecido será `<functionname>`. Veja [Personalização do ponto de extremidade HTTP](#url).
* **webHookType**: configura o gatilho HTTP para atuar como um receptor de webhook para o provedor especificado. A propriedade _methods_ não deverá ser definida se essa propriedade for escolhida. Veja [Respondendo a webhooks](#hooktrigger). O valor pode ser um dos seguintes:
    * _genericJson_: um ponto de extremidade de webhook de finalidade geral sem lógica para um provedor específico.
    * _github_: a função responderá a webhooks GitHub. A propriedade _authLevel_ não deverá ser definida se essa propriedade for escolhida.
    * _slack_: a função responderá a webhooks Slack. A propriedade _authLevel_ não deverá ser definida se essa propriedade for escolhida.

<a name="httptriggerusage"></a>
### <a name="working-with-an-http-trigger-from-code"></a>Como trabalhar com um gatilho HTTP no código
Para funções C# e F#, você pode declarar o tipo de entrada do gatilho para ser `HttpRequestMessage` ou um tipo personalizado. Ao escolher `HttpRequestMessage`, você obterá acesso completo ao objeto de solicitação. Para um tipo personalizado (como um POCO), Functions tentará analisar o corpo da solicitação como JSON para popular as propriedades do objeto.

Para as funções do Node.js, o tempo de execução do Functions fornece o corpo da solicitação em vez de o objeto da solicitação.

Veja [Exemplos de gatilho HTTP](#httptriggersample) para ver os exemplos de uso.


<a name="output"></a>
## <a name="http-response-output-binding"></a>Associação de saída de resposta HTTP
Use a associação de saída HTTP para responder ao remetente da solicitação HTTP. Essa associação requer um gatilho HTTP e permite que você personalize a resposta associada à solicitação do gatilho. Se a associação de saída HTTP não for fornecida, um gatilho HTTP retornará HTTP 200 OK com um corpo vazio. 

### <a name="configuring-an-http-output-binding"></a>Configuração de uma associação de saída HTTP
Um associação de saída HTTP é definida com a inclusão de um objeto JSON semelhante ao que se segue na matriz `bindings` de function.json:

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```
A associação contém as seguintes propriedades:

* **name**: obrigatória — o nome da variável usada no código de função para a resposta. Veja [Como trabalhar com uma associação de saída no código](#outputusage).
* **type**: obrigatória — deve ser definida como "http".
* **direction**: obrigatória — deve ser definida como "out".

<a name="outputusage"></a>
### <a name="working-with-an-http-output-binding-from-code"></a>Como trabalhar com uma associação de saída no código
Você pode usar o parâmetro de saída (por exemplo, "res") para responder ao chamador http ou webhook. Como alternativa, você pode usar o padrão `Request.CreateResponse()` (C#) ou `context.res` (Node.JS) para retornar a resposta. Para obter exemplos de como usar a segunda opção, veja [Exemplos de gatilho HTTP](#httptriggersample) e [Exemplos de gatilho Webhook](#hooktriggersample).


<a name="hooktrigger"></a>
## <a name="responding-to-webhooks"></a>Resposta a webhooks
Um gatilho HTTP com a propriedade _webHookType_ será configurada para responder a [webhooks](https://en.wikipedia.org/wiki/Webhook). A configuração básica usa a configuração "genericJson". Isso restringe as solicitações àquelas que usam HTTP POST e com o tipo de conteúdo `application/json`.

O gatilho também pode ser personalizado para um provedor de webhook específico (por exemplo, [GitHub](https://developer.github.com/webhooks/) e [Slack](https://api.slack.com/outgoing-webhooks)). Se um provedor for especificado, o tempo de execução de Functions poderá tratar da lógica de validação do provedor para você.  

### <a name="configuring-github-as-a-webhook-provider"></a>Configurando o GitHub como um provedor de webhook
Para responder a webhooks do GitHub, primeiramente crie sua função com um gatilho HTTP e defina a propriedade _webHookType_ como "github". Em seguida, copie a [URL](#url) e a [chave de API](#keys) na página **Adicionar webhook** do seu repositório GitHub. Veja a documentação sobre [como criar webhooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409) do GitHub para saber mais.

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="configuring-slack-as-a-webhook-provider"></a>Configuração do Slack como um provedor de webhook
O webhook do Slack gera um token em vez de permitir que você o especifique, de modo que é preciso configurar uma chave específica de função com o token do Slack. Veja [Como trabalhar com chaves](#keys).

<a name="url"></a>
## <a name="customizing-the-http-endpoint"></a>Personalização do ponto de extremidade HTTP
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

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

Isso permite que o código de função dê suporte a dois parâmetros no endereço, "category" e "id". Você pode usar qualquer [Restrição de rota de API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com seus parâmetros. O seguinte código de função em C# faz uso de ambos os parâmetros.

```csharp
    public static Task<HttpResponseMessage> Run(HttpRequestMessage request, string category, int? id, 
                                                    TraceWriter log)
    {
        if (id == null)
           return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
        else
           return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
    }
```

Aqui está o código de função do Node.js para usar os mesmos parâmetros de rota.

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

Por padrão, todas as rotas de função são prefixadas com *api*. Você também pode personalizar ou remover o prefixo usando a propriedade `http.routePrefix` em seu arquivo *host.json*. O exemplo a seguir remove o prefixo de rota *api* usando uma cadeia de caracteres vazia para o prefixo no arquivo *host.json*.

```json
    {
      "http": {
        "routePrefix": ""
      }
    }
```

Para obter informações detalhadas sobre como atualizar o arquivo *host.json* de sua função, consulte [Como atualizar os arquivos de aplicativo de funções](functions-reference.md#fileupdate). 

Para obter informações sobre outras propriedades que você pode configurar no seu arquivo *host.json*, consulte [referência host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).


<a name="keys"></a>
## <a name="working-with-keys"></a>Como trabalhar com chaves
HttpTriggers pode aproveitar as chaves para aumentar a segurança. Um HttpTrigger padrão pode usá-las como uma chave de API, exigindo que a chave esteja presente na solicitação. Os webhooks podem usar chaves para autorizar solicitações de várias maneiras, dependendo daquilo a que o provedor oferece suporte.

As chaves são armazenadas como parte do seu aplicativo de funções no Azure e criptografadas em repouso. Para exibir suas chaves, criar novas ou reverter chaves para novos valores, navegue para uma de suas funções dentro do portal e selecione "Gerenciar". 

Há dois tipos de chave:
- **Chaves de host**: essas chaves são compartilhadas por todas as funções do aplicativo de funções. Quando usadas como uma chave de API, elas permitem acesso a qualquer função no aplicativo de funções.
- **Chaves de função**: essas chaves se aplicam apenas às funções específicas sob as quais elas foram definidas. Quando usadas como uma chave de API, elas permitem acesso apenas às funções em questão.

Cada chave é nomeada para referência e há uma chave padrão (chamada "default") no nível de função e de host. A **chave mestra** é uma chave de host padrão chamada "_master", definida para cada aplicativo de funções e não pode ser revogada. Ela fornece acesso administrativo às APIs de tempo de execução. O uso de `"authLevel": "admin"` no JSON de associação exigirá que essa chave seja apresentada na solicitação; qualquer outra chave resultará em falha na autorização.

> [!NOTE]
> Devido às permissões elevadas concedidas pela chave mestra, você não deve compartilhar essa chave com terceiros nem distribuí-la em aplicativos cliente nativos. Tenha cuidado ao escolher o nível de autorização do administrador.
> 
> 

### <a name="api-key-authorization"></a>Autorização da chave de API
Por padrão, um HttpTrigger requer uma chave de API na solicitação HTTP. Portanto, sua solicitação HTTP geralmente se parecerá como esta:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

A chave pode ser incluída em uma variável de cadeia de caracteres de consulta chamada `code`, como acima, ou pode ser incluída em um cabeçalho HTTP `x-functions-key`. O valor da chave pode ser qualquer chave de função definida para a função ou qualquer chave de host.

Você pode optar por permitir solicitações sem chaves ou especificar que a chave mestra deve ser usada, bastando alterar a propriedade `authLevel` no JSON de associação (veja [Gatilho HTTP](#httptrigger)).

### <a name="keys-and-webhooks"></a>Chaves e webhooks
A autorização de webhook é tratada pelo componente receptor do webhook, parte do HttpTrigger, e o mecanismo varia com base no tipo de webhook. No entanto, cada mecanismo depende de uma chave. Por padrão, a chave de função chamada "default" será usada. Se desejar usar outra chave, você precisará configurar o provedor do webhook para enviar o nome da chave com a solicitação de uma das seguintes maneiras:

- **Cadeia de caracteres de consulta**: o provedor passa o nome da chave no parâmetro de cadeia de caracteres de consulta `clientid` (por exemplo, `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`).
- **Cabeçalho da solicitação**: o provedor passa o nome da chave no cabeçalho `x-functions-clientid`.

> [!NOTE]
> As chaves de função têm precedência sobre as chaves de host. Se duas chaves forem definidas com o mesmo nome, a chave de função será usada.
> 
> 


<a name="httptriggersample"></a>
## <a name="http-trigger-samples"></a>Exemplos de gatilho HTTP
Suponha que você tenha o seguinte gatilho HTTP na matriz `bindings` de function.json:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

Veja o exemplo específico de linguagem que procura um parâmetro `name` na cadeia de caracteres de consulta ou no corpo da solicitação HTTP.

* [C#](#httptriggercsharp)
* [F#](#httptriggerfsharp)
* [Node.js](#httptriggernodejs)


<a name="httptriggercsharp"></a>
### <a name="http-trigger-sample-in-c"></a>Exemplo de gatilho HTTP em C# #
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

Você também pode associar a um POCO, em vez de a um `HttpRequestMessage`. Isso será ser hidratado do corpo da solicitação, analisada como JSON. Da mesma forma, um tipo pode ser passado para a associação de saída da resposta HTTP e isso será retornado como o corpo da resposta, com um código de status 200.
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

<a name="httptriggerfsharp"></a>
### <a name="http-trigger-sample-in-f"></a>Exemplo de gatilho HTTP em F# #
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

Você precisa de um arquivo `project.json` que usa o NuGet para fazer referência aos assemblies `FSharp.Interop.Dynamic` e `Dynamitey`, como este:

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

<a name="httptriggernodejs"></a>
### <a name="http-trigger-sample-in-nodejs"></a>Exemplo de gatilho HTTP em Node.JS
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



<a name="hooktriggersample"></a>
## <a name="webhook-samples"></a>Exemplos de webhook
Suponha que você tenha o seguinte gatilho de webhook na matriz `bindings` de function.json:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

Veja o exemplo específico de linguagem que registra comentários de problemas do GitHub.

* [C#](#hooktriggercsharp)
* [F#](#hooktriggerfsharp)
* [Node.js](#hooktriggernodejs)

<a name="hooktriggercsharp"></a>

### <a name="webhook-sample-in-c"></a>Exemplo de webhook em C# #
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

<a name="hooktriggerfsharp"></a>

### <a name="webhook-sample-in-f"></a>Exemplo de webhook em F# #
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

<a name="hooktriggernodejs"></a>

### <a name="webhook-sample-in-nodejs"></a>Exemplo de webhook em Node.JS
```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```


## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


