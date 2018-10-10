---
title: Referência do desenvolvedor de JavaScript do Azure Functions | Microsoft Docs
description: Entenda como desenvolver funções usando JavaScript.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, functions, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 03/04/2018
ms.author: glenga
ms.openlocfilehash: 24f7faa0fb111e4e537a7db3f5e1eea709d1ca59
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957720"
---
# <a name="azure-functions-javascript-developer-guide"></a>Guia do desenvolvedor de JavaScript do Azure Functions
Este guia contém informações sobre as complexidades de escrever Azure Functions com JavaScript.

Uma função JavaScript é uma `function` exportada que será executada quando disparada ([os gatilhos são configurados no function.json](functions-triggers-bindings.md)). Cada função recebe um objeto `context` que é usado para envio e recebimento de dados de associação, registro em log e comunicação com o tempo de execução.

Este artigo pressupõe que você já tenha lido a [Referência do desenvolvedor do Azure Functions](functions-reference.md). Também é recomendado que você tenha seguido um tutorial em "Inícios Rápidos" para [criar sua primeira função](functions-create-first-function-vs-code.md).

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pastas necessárias para um projeto JavaScript é semelhante à seguinte. Observe que esse padrão pode ser alterado: confira a seção [scriptFile](functions-reference-node.md#using-scriptfile) abaixo para obter mais detalhes.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
 | - bin
```

Na raiz do projeto, há um arquivo [host.json](functions-host-json.md) compartilhado que pode ser usado para configurar o aplicativo de funções. Cada função tem uma pasta com seu próprio arquivo de código (.js) e o arquivo de configuração de associação (function.json).

As extensões de associação necessárias na [versão 2.x](functions-versions.md) do tempo de execução do Functions são definidas no arquivo `extensions.csproj`, com os arquivos de biblioteca reais na pasta `bin`. Ao desenvolver localmente, você precisa [registrar as extensões de associação](functions-triggers-bindings.md#local-development-azure-functions-core-tools). Ao desenvolver funções no portal do Azure, esse registro é feito para você.

## <a name="exporting-a-function"></a>Exportando uma função

As funções JavaScript precisam ser exportadas por meio de [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (ou [`exports`](https://nodejs.org/api/modules.html#modules_exports)). No caso padrão, sua função exportada deve ser a única exportação de seu arquivo, ou seja, a exportação denominada `run` ou a denominada `index`. O local padrão da função é `index.js`, em que `index.js` compartilha o mesmo diretório pai que o `function.json` correspondente. Observe que o nome do diretório pai do `function.json` é sempre o nome da função. 

Para configurar o local do arquivo e o nome de exportação da função, leia [Configurando o ponto de entrada da função](functions-reference-node.md#configure-function-entry-point) abaixo.

O ponto de entrada da função exportada precisa sempre usar um objeto `context` como o primeiro parâmetro.

```javascript
// You must include a context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
// You can also use 'arguments' to dynamically handle inputs
module.exports = async function(context) {
    context.log('Number of inputs: ' + arguments.length);
    // Iterates through trigger and input binding data
    for (i = 1; i < arguments.length; i++){
        context.log(arguments[i]);
    }
};
```

Gatilhos e associações de entrada (associações de `direction === "in"`) podem ser passados para a função como parâmetros. Eles são passados para a função na mesma ordem em que são definidos *function.json*. Você também pode manipular dinamicamente as entradas usando o objeto JavaScript [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx). Por exemplo, se tiver `function(context, a, b)` e alterá-lo para `function(context, a)`, você ainda poderá obter o valor de `b` no código de função, fazendo referência ao `arguments[2]`.

Todas as associações, independentemente da direção, também são passadas no objeto `context` usando a propriedade `context.bindings`.

## <a name="context-object"></a>objeto de contexto
O tempo de execução usa um objeto `context` para passar dados de/para sua função e permitir que você se comunique com o tempo de execução.

O objeto `context` sempre é o primeiro parâmetro de uma função e deve ser incluído porque tem métodos como `context.done` e `context.log`, que são necessários para usar o tempo de execução corretamente. Você pode nomear o objeto de acordo com a sua preferência (por exemplo, `ctx` ou `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Propriedade context.bindings

```
context.bindings
```
Retorna um objeto nomeado que contém todos os dados de entrada e saída. Por exemplo, as seguintes definições de associação no *function.json* permitem que você acesse o conteúdo de uma fila de `context.bindings.myInput` e atribua as saídas a uma fila usando `context.bindings.myOutput`.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Observe que você pode optar por definir dados de associação de saída usando o método `context.done` em vez do objeto `context.binding` (veja abaixo).

### <a name="contextbindingdata-property"></a>Propriedade context.bindingData

```
context.bindingData
```
Retorna um objeto nomeado que contém dados de invocação de função e os metadados do gatilho (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Para obter um exemplo de metadados de gatilho, confira este [exemplo de hubs de eventos](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Método context.done
```
context.done([err],[propertyBag])
```

Informa ao tempo de execução que seu código terminou. Se a função usa a declaração JavaScript [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) (disponível ao usar o Node 8 ou superiores no Functions versão 2.x), você não precisa usar `context.done()`. O `context.done` retorno de chamada é chamado implicitamente.

Se a função não é uma função assíncrona, **você precisa chamar** `context.done` para informar ao tempo de execução que a função está concluída. A execução atingirá o tempo limite se ele estiver ausente.

O método `context.done` permite que você retorne um erro definido pelo usuário ao tempo de execução e um objeto JSON que contém dados de associação de saída. As propriedades passadas para `context.done` substituirão tudo o que estiver definido no objeto `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Método context.log  

```
context.log(message)
```
Permite que você grave em logs de função de streaming no nível de rastreamento padrão. No `context.log`, há métodos de registro adicionais disponíveis para permitir que você grave logs de função em outros níveis de rastreamento:


| Método                 | DESCRIÇÃO                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | Grava no registro em log no nível do erro, ou em um nível inferior.   |
| **warn(_message_)**    | Grava no registro em log no nível do aviso, ou em um nível inferior. |
| **info(_message_)**    | Grava no registro em log no nível da informação, ou em um nível inferior.    |
| **verbose(_message_)** | Grava no registro em log no nível detalhado.           |

O exemplo a seguir grava um log no nível de rastreamento de aviso:

```javascript
context.log.warn("Something has happened."); 
```
Você pode [configurar o limite do nível de rastreamento para registro em log](#configure-the-trace-level-for-console-logging) no arquivo host.json. Para obter mais informações sobre como gravar logs, confira [Gravando saídas de rastreamento](#writing-trace-output-to-the-console) abaixo.

Leia [Monitorado o Azure Functions](functions-monitoring.md) para saber mais sobre como exibir e consultar logs de função.

## <a name="binding-data-type"></a>Tipo de dados de associação

Para definir o tipo de dados para uma associação de entrada, use a propriedade `dataType` na definição de associação. Por exemplo, para ler o conteúdo de uma solicitação HTTP em formato binário, use o tipo `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

As opções para `dataType` são: `binary`, `stream` e `string`.

## <a name="writing-trace-output-to-the-console"></a>Gravar a saída de rastreamento no console 

No Functions, use os métodos `context.log` para gravar a saída de rastreamento no console. No Functions v2.x, as saídas de rastreamento por `console.log` são capturadas no nível do aplicativo de funções. Isso significa que as saídas de `console.log` não estão vinculados a uma invocação de função específica e, portanto, não são exibidas nos logs de uma função específica. No entanto, eles serão propagados ao Application Insights. No Functions v1.x, não é possível usar `console.log` para gravar no console. 

Quando você chama `context.log()`, sua mensagem é gravada no console no nível de rastreamento padrão, que é o nível de rastreamento de _informações_. O código a seguir grava no console no nível de rastreamento de informações:

```javascript
context.log({hello: 'world'});  
```

Esse código é equivalente ao código acima:

```javascript
context.log.info({hello: 'world'});  
```

Esse código grava no console no nível de erro:

```javascript
context.log.error("An error has occurred.");  
```

Como _erro_ é o nível de rastreamento mais alto, esse rastreamento é gravado na saída em todos os níveis de rastreamento enquanto o registro em log estiver habilitado.

Todos os métodos `context.log` dão suporte ao mesmo formato de parâmetro que o [método util.format](https://nodejs.org/api/util.html#util_util_format_format) de Node.js. Considere o código a seguir, que grava logs de função usando o nível de rastreamento padrão:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Você também pode escrever o mesmo código no formato a seguir:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Configurar o nível de rastreamento para o registro em log no console

O Functions permite a definição do nível de rastreamento de limite para gravar no console, o que facilita o controle do modo de gravação dos rastreamentos no console a partir de suas funções. Para definir o limite para todos os rastreamentos gravados no console, use a propriedade `tracing.consoleLevel` no arquivo host.json. Essa configuração se aplica a todas as funções em seu aplicativo de função. O exemplo a seguir define o limite de rastreamento para habilitar o registro em log detalhado:

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

Os valores de **consoleLevel** correspondem aos nomes dos métodos `context.log`. Para desabilitar todo o registro em log do rastreamento no console, defina **consoleLevel** como _off_. Para obter mais informações, consulte a [referência para host.json](functions-host-json.md).

## <a name="http-triggers-and-bindings"></a>Gatilhos e associações HTTP

HTTP e gatilhos de webhook e associações de saída HTTP usam objetos de solicitação e resposta para representar as mensagens HTTP.  

### <a name="request-object"></a>Objeto da solicitação

O objeto `context.req` (solicitação) tem as seguintes propriedades:

| Propriedade      | DESCRIÇÃO                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Um objeto que contém o corpo da solicitação.               |
| _headers_     | Um objeto que contém os cabeçalhos da solicitação.                   |
| _method_      | O método HTTP da solicitação.                                |
| _originalUrl_ | A URL da solicitação.                                        |
| _params_      | Um objeto que contém os parâmetros de roteamento da solicitação. |
| _query_       | Um objeto que contém os parâmetros da consulta.                  |
| _rawBody_     | O corpo da mensagem como uma cadeia de caracteres.                           |


### <a name="response-object"></a>Objeto de resposta

O objeto `context.res` (resposta) tem as seguintes propriedades:

| Propriedade  | DESCRIÇÃO                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Um objeto que contém o corpo da resposta.         |
| _headers_ | Um objeto que contém os cabeçalhos da resposta.             |
| _isRaw_   | Indica que a formatação foi ignorada para a resposta.    |
| _status_  | O código de status HTTP da resposta.                     |

### <a name="accessing-the-request-and-response"></a>Acessar a solicitação e a resposta 

Ao trabalhar com gatilhos HTTP, há várias maneiras de acessar os objetos de solicitação e resposta HTTP:

+ Das propriedades `req` e `res` no objeto `context`. Dessa forma, você pode usar o padrão convencional para acessar os dados HTTP a partir do objeto de contexto, em vez de usar o padrão `context.bindings.name` completo. O exemplo a seguir mostra como acessar os objetos `req` e `res` no `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ A partir das associações de entrada e saída nomeadas. Dessa forma, o gatilho e as associações de HTTP funcionam da mesma forma que qualquer outra associação. O exemplo a seguir define o objeto de resposta usando uma associação chamada `response`: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ _[Response only]_ Chamando `context.res.send(body?: any)`. Uma resposta HTTP é criada com a entrada `body` como o corpo da resposta. `context.done()` é chamado implicitamente.

+ _[Response only]_ Chamando `context.done()`. Um tipo especial de associação HTTP que retorna a resposta passada ao método `context.done()`. A seguinte associação de saída HTTP define um parâmetro de saída `$return`:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>Versão do nó

A tabela a seguir mostra a versão do Node.js usada por cada versão principal do tempo de execução do Functions:

| Versão do Functions | Versão do Node.js | 
|---|---|
| 1.x | 6.11.2 (bloqueada pelo tempo de execução) |
| 2. x  | _Active Directory LTS_ e _atual_ versões de Node. js (8.11.1 e 10.6.0 recomendado). Defina a versão usando a [configuração do aplicativo](functions-how-to-use-azure-function-app-settings.md#settings) WEBSITE_DEFAULT_NODE_VERSION.|

Veja versão atual que o tempo de execução está usando verificando a configuração de aplicativo acima ou imprimindo `process.version` de qualquer função.

## <a name="dependency-management"></a>Gerenciamento de dependências
Para usar as bibliotecas da comunidade no código JavaScript, como é mostrado no exemplo abaixo, você precisa garantir que todas as dependências sejam instaladas no aplicativo de funções no Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

Observe que você deve definir um arquivo `package.json` na raiz do aplicativo de funções. A definição de arquivo permite que todas as funções no aplicativo compartilhem os mesmos pacotes armazenados em cache, o que oferece o melhor desempenho. Se houver conflitos de versão, você poderá resolver o conflito adicionando um arquivo `package.json` na pasta de uma função específica.  

Há duas maneiras de instalar pacotes no aplicativo de funções: 

### <a name="deploying-with-dependencies"></a>Implantando com dependências
1. Instale todos os pacotes necessários localmente executando `npm install`.

2. Implante o código e verifique se a pasta `node_modules` está incluída na implantação. 


### <a name="using-kudu"></a>Usando o Kudu
1. Vá para `https://<function_app_name>.scm.azurewebsites.net`.

2. Clique em **Console de Depuração** > **CMD**.

3. Acesse `D:\home\site\wwwroot`e arraste o arquivo package.json para a pasta **wwwroot** na metade superior da página.  
    Também há outras maneiras de carregar arquivos em seu aplicativo de função. Para saber mais, confira [Como atualizar os arquivos do aplicativo de função](functions-reference.md#fileupdate). 

4. Depois que o arquivo package.json é carregado, execute o comando `npm install` no **console de execução remota do Kudu**.  
    Essa ação baixa os pacotes indicados no arquivo package.json e reinicia o aplicativo de função.

## <a name="environment-variables"></a>Variáveis de ambiente
Para obter uma variável de ambiente ou um valor de configuração do aplicativo, use `process.env`, conforme mostrado na função `GetEnvironmentVariable`:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

## <a name="configure-function-entry-point"></a>Configurar o ponto de entrada de função

As propriedades `scriptFile` e `entryPoint` do `function.json` podem ser usadas para configurar o local e o nome da função exportada. Isso poderá ser importante se o JavaScript for transcompilado.

### <a name="using-scriptfile"></a>Usando o `scriptFile`

Por padrão, uma função JavaScript é executada do `index.js`, um arquivo que compartilha o mesmo diretório pai que seu `function.json` correspondente.

`scriptFile` pode ser usado para obter uma estrutura de pasta como esta:
```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - nodeFunction.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

O `function.json` da `myNodeFunction` deve incluir uma propriedade `scriptFile` que aponte para o arquivo com a função exportada a ser executada.
```json
{
  "scriptFile": "../lib/nodeFunction.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Usando o `entryPoint`

No `scriptFile` (ou `index.js`), uma função precisa ser exportada usando `module.exports` para ser localizada e executada. Por padrão, a função que é executada quando disparada é a única exportação desse arquivo, a exportação denominada `run` ou a exportação denominada `index`.

Isso pode ser configurado usando `entryPoint` em `function.json`:
```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

No Functions v2.x, que dá suporte ao parâmetro `this` nas funções de usuário, o código da função poderia ser o seguinte:
```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };
    
    function logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

Neste exemplo, é importante observar que, embora um objeto esteja sendo exportado, não há nenhuma garantia de que o estado será preservado entre as execuções.

## <a name="considerations-for-javascript-functions"></a>Considerações para funções em JavaScript

Ao trabalhar com funções JavaScript, lembre-se das considerações nas seções a seguir.

### <a name="choose-single-vcpu-app-service-plans"></a>Escolher Planos do Serviço de Aplicativo de vCPU único

Ao criar um aplicativo de funções que usa o Plano do Serviço de Aplicativo, recomendamos que você selecione um plano de vCPU único em vez de um plano com vários vCPUs. Atualmente, o Functions executa funções em JavaScript com mais eficiência em VMs de vCPU único, e o uso de VMs maiores não produz os aprimoramentos de desempenho esperados. Quando for necessário, você poderá escalar horizontalmente manualmente adicionando mais instâncias de VM de vCPU único ou poderá habilitar o dimensionamento automático. Para saber mais, confira [Dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Suporte a TypeScript e CoffeeScript
Como ainda não há suporte direto para compilação automática de TypeScript ou CoffeeScript por meio do tempo de execução, esse suporte precisa ser manipulado fora do tempo de execução, no tempo de implantação. 

### <a name="cold-start"></a>Inicialização a frio
No desenvolvimento de Azure Functions no modelo de hospedagem sem servidor, as inicializações a frio são uma realidade. "Inicialização a frio" refere-se ao fato de que quando o aplicativo de funções é iniciado pela primeira vez após um período de inatividade, ele leva mais tempo para ser inicializado. Especificamente nas funções JavaScript com árvores de dependência grandes, isso pode causar uma grande lentidão. Para apressar o processo, se possível, [execute suas funções como um arquivo de pacote](run-functions-from-deployment-package.md). Vários métodos de implantação aceitam esse modelo por padrão, mas se você estiver enfrentando inicializações a frio grandes e não estiver executando por meio de um arquivo de pacote, isso poderá ser uma grande melhoria.

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os recursos a seguir:

* [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)

