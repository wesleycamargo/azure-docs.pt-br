<properties
	pageTitle="Referência do desenvolvedor de NodeJS do Azure Functions | Microsoft Azure"
	description="Entenda como desenvolver Azure Functions usando NodeJS."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"/>

<tags
	ms.service="functions"
	ms.devlang="nodejs"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/13/2016"
	ms.author="chrande"/>

# Referência do desenvolvedor de NodeJS do Azure Functions

A experiência de Nó/JavaScript para Azure Functions torna mais fácil exportar uma função que é passada para um objeto `context` para se comunicar com o tempo de execução e para receber e enviar dados por meio de associações.

Este artigo pressupõe que você já tenha lido a [referência do desenvolvedor do Azure Functions](functions-reference.md).

## Exportando uma função

Todas as funções do JavaScript devem exportar um único `function` via `module.exports` para o tempo de execução localizar a função e executá-la. Esta função sempre deve incluir um objeto `context`.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Associações de `direction === "in"` são passadas como argumentos de função, o que significa que você pode usar [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) para lidar dinamicamente com novas entradas (por exemplo, usando `arguments.length` para iterar por todas as suas entradas). Essa funcionalidade será muito conveniente se você tiver apenas um gatilho sem nenhuma entrada adicional, pois é possível acessar seus dados de gatilho de maneira previsível sem fazer referência ao objeto `context`.

Os argumentos sempre são passados para a função na ordem em que ocorrem em *function.json*, mesmo se você não especificá-los na sua instrução de exportações. Por exemplo, se você tiver `function(context, a, b)` e alterá-lo para `function(context, a)`, ainda poderá obter o valor de `b` no código de função, fazendo referência ao `arguments[3]`.

Todas as associações, independentemente da direção, também são transmitidas por todo o objeto `context` (veja abaixo).

## objeto de contexto

O tempo de execução usa um objeto `context` para passar dados de e para sua função e permitir que você se comunique com o tempo de execução.

O objeto de contexto sempre é o primeiro parâmetro para uma função e sempre deve ser incluído porque tem métodos como `context.done` e `context.log` que são necessários para usar corretamente o tempo de execução. Você pode nomear o objeto que você quiser (ou seja, `ctx` ou `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## context.bindings

O objeto `context.bindings` coleta todos os dados de entrada e de saída. Os dados são adicionados ao objeto `context.bindings` por meio da propriedade `name` da associação. Por exemplo, dada a seguinte definição de associação *function.json*, você pode acessar o conteúdo da fila por meio de `context.bindings.myInput`.

```json
    {
        "type":"queue",
        "direction":"in",
        "name":"myInput"
        ...
    }
```

```javascript
// myInput contains the input data which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

## `context.done([err],[propertyBag])`

A função `context.done` informa ao tempo de execução que você terminou de executar. Isso é importante para chamar quando você tiver terminado com a função. Caso contrário, o tempo de execução ainda não saberá que a função foi concluída.

A função `context.done` permite que você passe um erro definido pelo usuário de volta para o tempo de execução, bem como um recipiente de propriedades que substituirão as propriedades no objeto `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

## context.log(message)

O método `context.log` permite obter instruções de log de saída que são correlacionadas para fins de registro. Se você usar `console.log`, suas mensagens mostrarão apenas para o log de nível de processo, o que não será tão útil.

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

O método `context.log` dá suporte ao mesmo parâmetro de formato que o Nó [util.format method](https://nodejs.org/api/util.html#util_util_format_format). Desta forma, por exemplo, um código como este:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

pode ser escrito da seguinte forma:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## Gatilhos HTTP: context.req e context.res

No caso de gatilhos de HTTP, como é um padrão comum usar `req` e `res` para os objetos de solicitação e de resposta HTTP, decidimos facilitar o acesso àqueles no objeto de contexto, em vez de forçá-lo a usar toda o padrão de `context.bindings.name` completo.

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## Versão de nó e gerenciamento de pacote

A versão do nó está atualmente bloqueada em `5.9.1`. Estamos investigando a adição de suporte para mais versões torná-la configurável.

Você pode incluir pacotes em sua função, carregando um arquivo *package.json* na pasta da sua função no sistema de arquivos do aplicativo de função. Para obter instruções de como carregar um arquivo, consulte **Como atualizar os arquivos de aplicativo de função** do [tópico de referência do desenvolvedor do Azure Functions](functions-reference.md#fileupdate).

Você também pode usar `npm install` na interface de linha de comando do aplicativo de função SCM (Kudu):

1. Navegue para: `https://<function_app_name>.scm.azurewebsites.net`.

2. Clique em **Console de Depuração > CMD**.

3. Navegue até `D:\home\site\wwwroot<function_name>`.

4. Execute `npm install`.

Depois que os pacotes necessários são instalados, você os importa para a função das maneiras comuns (por exemplo, via `require('packagename')`)

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v5.9.1'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

## Variáveis de ambiente

Para obter uma variável de ambiente ou um valor de configuração do aplicativo, use `process.env`, conforme mostrado no exemplo de código a seguir:

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

## Suporte a TypeScript/CoffeeScript

Ainda não há suporte direto para compilação automática de TypeScript/CoffeeScript por meio do tempo de execução, por isso seria necessário manipulá-los fora do tempo de execução, no tempo de implantação.

## Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Referência do desenvolvedor de C# do Azure Functions](functions-reference-csharp.md)
* [Gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0525_2016-->