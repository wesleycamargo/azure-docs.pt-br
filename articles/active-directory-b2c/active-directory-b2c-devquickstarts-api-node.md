<properties
	pageTitle="Visualização do B2C: proteger uma API Web usando o Node.js | Microsoft Azure"
	description="Como criar uma API Web do Node.js que aceita tokens de um locatário do B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="brandwe"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
  	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Visualização do B2C: proteger uma API Web usando Node.js

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]


> [AZURE.NOTE] Este artigo não aborda como implementar entrada, inscrição e gerenciamento de perfil com o Azure AD B2C. Ele se concentra na chamada a APIs Web depois que o usuário já está autenticado. Você deverá começar com o [tutorial de introdução ao aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para saber mais sobre os conceitos básicos do Azure Active Directory B2C, caso ainda não tenha feito isso.


> [AZURE.NOTE]	Este exemplo foi escrito para que você se conecte a ele usando nosso [aplicativo de exemplo B2C para iOS.](active-directory-b2c-devquickstarts-ios.md) Primeiramente, explore este passo a passo e, depois, acompanhe usando esse exemplo.

**Passport** é middleware de autenticação para o Node.js. Extremamente flexível e modular, o Passport pode ser instalado sem impedimento em qualquer aplicativo Web baseado em Express ou Restify. Um conjunto abrangente de estratégias que dão suporte à autenticação usando um nome de usuário e uma senha, o Facebook, o Twitter e muito mais. Desenvolvemos uma estratégia para o Azure AD (Active Directory do Azure). Você instalará esse módulo e, em seguida, adicionará o plug-in `passport-azure-ad` do Azure AD.

Para fazer isso, você precisará:

1. Registrar um aplicativo com o Azure AD.
2. Configurar seu aplicativo para usar o plug-in `azure-ad-passport` do Passport.
3. Configurar um aplicativo cliente para chamar a API Web de "lista de tarefas pendentes".

O código para este tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs). Para acompanhá-lo, você pode [baixar o esqueleto do aplicativo como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/skeleton.zip) ou clonar o esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```

O aplicativo completo é fornecido no fim deste tutorial.

> [AZURE.WARNING] 	Para nossa visualização do B2C, você deve usar a mesma **ID do cliente**/**ID de aplicativo** e as mesmas políticas para o servidor de tarefa da API Web e para o cliente que se conecta a ele. Isso também se aplica a nossos tutoriais para Android e iOS. Se você tiver criado anteriormente um aplicativo em qualquer um desses guias de Início Rápido, use esses valores; não crie novos.


## Obter um diretório AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos etc. Se você ainda não tiver um, [crie um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir.

## Criar um aplicativo

Em seguida, você precisa criar um aplicativo no diretório B2C, que dá ao Azure AD algumas informações de que ele precisa para se comunicar de forma segura com o aplicativo. Nesse caso, o aplicativo cliente e a API Web serão representados por uma única **ID de aplicativo**, pois eles abrangem um aplicativo lógico. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). É necessário que você:

- Incluir um **aplicativo Web/api Web** no aplicativo
- Digite `http://localhost/TodoListService` como uma **URL de Resposta**. É a URL padrão deste exemplo de código.
- Crie um **Segredo de aplicativo** para seu aplicativo e copie-o. Você precisará dela mais tarde. Você precisará dele em breve. Observe que esse valor precisa ser [seguidas por caracteres de escape XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) antes de ser usado.
- Copie a **ID de aplicativo** atribuída ao aplicativo. Você também precisará dela mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Criar suas políticas

No Azure AD B2C, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Esse aplicativo contém três experiências de identidade: inscrição, entrada e entrada com o Facebook. Você precisa criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Ao criar as três políticas, não deixe de:

- Escolher o **Nome de exibição** e outros atributos de inscrição em sua política de inscrição.
- Escolher as declarações de aplicativo **Nome de exibição** e **ID de objeto** em cada política. Você pode escolher outras declarações também.
- Copie o **Nome** de cada política após criá-la. Ele deve ter o prefixo `b2c_1_`. Você precisará desses nomes de política mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as três políticas, você estará pronto para compilar o aplicativo.

Observe que este artigo não aborda como usar as políticas que você acabou de criar. Para saber mais sobre como as políticas funcionam no Azure AD B2C, inicie com o [tutorial de introdução ao aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Baixar o Node.js para sua plataforma

Para usar este exemplo com êxito, você deve ter uma instalação do Node.js em funcionamento.

Instale o Node.js de [nodejs.org](http://nodejs.org).

## Instalar o MongoDB para sua plataforma

Para usar este exemplo com êxito, você também deve ter uma instalação do MongoDB em funcionamento. Você usará o MongoDB para tornar a API REST persistente entre instâncias de servidor.

Instale o MongoDB de [mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Este passo a passo presume que você usará os pontos de extremidade de servidor e de instalação padrão para MongoDB que, no momento da redação deste artigo, são `mongodb://localhost`

## Instalar os módulos Restify na API Web

Você usará o Restify para criar a API REST. O Restify é uma estrutura de aplicativo do Node.js mínima e flexível, derivada do Express. Tem um conjunto robusto de recursos para a criação de APIs REST sobre o Connect.

### Instalar Restify

Na linha de comando, altere o diretório para `azuread`. Se o diretório `azuread` não existir, crie-o.

`cd azuread` ou `mkdir azuread;`

Digite o seguinte comando:

`npm install restify`

Este comando instala o Restify.

#### Você obteve um erro?

Em alguns sistemas operacionais, ao usar `npm`, você pode receber o erro `Error: EPERM, chmod '/usr/local/bin/..'` e uma solicitação para executar a conta como administrador. Se isso ocorrer, use o comando `sudo` para executar `npm` com um nível de privilégio mais elevado.

#### Você recebeu um erro DTrace?

Você pode ver algo assim ao instalar o Restify:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

O Restify fornece um mecanismo poderoso para rastreamento de chamadas REST usando o DTrace. No entanto, muitos sistemas operacionais não têm o DTrace disponível. Você pode ignorar com segurança esses erros.

A saída do comando deve ser semelhante ao seguinte:

	restify@2.6.1 node_modules/restify
	├── assert-plus@0.1.4
	├── once@1.3.0
	├── deep-equal@0.0.0
	├── escape-regexp-component@1.0.2
	├── qs@0.6.5
	├── tunnel-agent@0.3.0
	├── keep-alive-agent@0.0.1
	├── lru-cache@2.3.1
	├── node-uuid@1.4.0
	├── negotiator@0.3.0
	├── mime@1.2.11
	├── semver@2.2.1
	├── spdy@1.14.12
	├── backoff@2.3.0
	├── formidable@1.0.14
	├── verror@1.3.6 (extsprintf@1.0.2)
	├── csv@0.3.6
	├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
	└── bunyan@0.22.0 (mv@0.0.5)

## Instalar o Passport.js na API Web

[Passport](http://passportjs.org/) é middleware de autenticação para o Node.js. Extremamente flexível e modular, o Passport pode ser instalado sem impedimento em qualquer aplicativo Web baseado em Express ou Restify. Um conjunto abrangente de estratégias que dão suporte à autenticação usando um nome de usuário e uma senha, o Facebook, o Twitter e muito mais. Desenvolvemos uma estratégia para o Azure AD. Você instalará esse módulo e, em seguida, adicionará o plug-in de estratégia do Azure AD.

Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá.

Insira o seguinte comando para instalar o Passport:

`npm install passport`

A saída do comando deve ser semelhante ao seguinte:

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## Adicionar passport azuread à API Web

Em seguida, adicione a estratégia OAuth usando `passport-azuread`, um pacote de estratégias que conectam o Azure AD ao Passport. Use essa estratégia para tokens de portador no exemplo de API REST.

> [AZURE.NOTE] Embora o OAuth2 forneça uma estrutura na qual qualquer tipo de token conhecido pode ser emitido, somente determinados tipos de token passaram a ser amplamente usados. Os tokens para proteger pontos de extremidade são tokens de portador. Esses são o tipo mais amplamente emitido de token no OAuth2. Muitas implementações presumem que os tokens de portador sejam o único tipo de token emitido.

Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá.

Digite o seguinte comando para instalar o módulo `passport-azure-ad` do Passport:

`npm install passport-azure-ad`

A saída do comando deve ser semelhante ao seguinte:

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## Adicionar os módulos do MongoDB à API Web

Você usará o MongoDB como repositório de dados. Por esse motivo, você precisa instalar o Mongoose, um plug-in amplamente utilizado para gerenciamento de modelos e esquemas, e o driver de banco de dados para MongoDB, também chamado de MongoDB.

* `npm install mongoose`
* `npm install mongodb`

## Instalar módulos adicionais

Em seguida, instale os módulos necessários restantes.

Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Digite os seguintes comandos para instalar os módulos no diretório `node_modules`:

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## Criar um arquivo server.js com suas dependências

O arquivo `server.js` fornecerá a maior parte da funcionalidade para o servidor de API Web. Você adicionará a maior parte de nosso código a esse arquivo. Para fins de produção, você deve refatorar a funcionalidade em arquivos menores, como rotas separadas e controladores. Para este tutorial, use o server.js para essa funcionalidade.

Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Crie um arquivo `server.js` em um editor. Adicione as seguintes informações:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Salve o arquivo. Você retornará a ele mais tarde.

## Criar um arquivo config.js para armazenar as configurações do Azure AD

Esse arquivo de código passa os parâmetros de configuração do Portal do Azure AD para o arquivo `Passport.js`. Você criou esses valores de configuração ao adicionar a API Web ao portal na primeira parte do passo a passo. Explicaremos o que deve ser inserido nos valores desses parâmetros depois que você copiar o código.

Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Crie um arquivo `config.js` em um editor. Adicione as seguintes informações:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>',
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### Valores necessários

`IdentityMetadata`: esse é o local onde `passport-azure-ad` procurará os dados de configuração para o provedor de identidade. Ele também procurará aqui as chaves para validar os tokens Web JSON. Provavelmente não convém alterá-lo se você usa o Azure AD.

`audience`: o URI (uniform resource identifier) do portal que identifica seu serviço. Nosso exemplo usa `http://localhost/TodoListService`.

`tenantName`: o nome do locatário (por exemplo, **contoso.onmicrosoft.com**).

`policyName`: a política que você deseja que valide os tokens que chegam ao servidor. Essa deve ser a mesma política que você usou no aplicativo cliente para entrar.

> [AZURE.NOTE] Para nossa visualização do B2C, use as mesmas políticas em instalações de cliente e de servidor. Se já tiver concluído um passo a passo e criado essas políticas, você não precisará fazer isso novamente. Como concluiu o passo a passo, você não deve precisar configurar novas políticas para um passo a passo de cliente no site.

## Adicionar configuração ao arquivo server.js

Você precisa ler os valores do arquivo `config.js` recém-criado em seu aplicativo. Para fazer isso, adicione o arquivo `.config` como um recurso necessário ao aplicativo e, em seguida, defina as variáveis globais como aquelas no documento `config.js`.

Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Abra o arquivo `server.js` em um editor. Adicione as seguintes informações:

```Javascript
var config = require('./config');
```
Adicione uma nova seção a `server.js` que inclua o seguinte código:

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience
};
// array to hold logged-in users and the current logged-in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## Adicionar as informações de esquema e modelo do MongoDB usando Mongoose

A preparação anterior renderá frutos quando você reunir esses três arquivos em um serviço de API REST.

Para este passo a passo, use o MongoDB para armazenar suas tarefas, como discutido anteriormente.

No arquivo `config.js` criado, você chamou o banco de dados **tasklist**. Isso também foi o que você colocou no fim da URL de conexão `mongoose_auth_local`. Você não precisa criar esse banco de dados com antecedência no MongoDB. Ele criará o banco de dados para você na primeira execução do aplicativo de servidor, se ainda não existir.

Após informar ao servidor qual banco de dados do MongoDB deve ser usado, você precisa escrever código adicional para criar o modelo e o esquema para suas tarefas de servidor.

### Expandir o modelo

Este modelo de esquema é muito simples. Você pode expandi-lo conforme necessário.

`name`: quem está atribuído à tarefa. Essa é uma **cadeia de caracteres**.

`task`: a própria tarefa. Essa é uma **cadeia de caracteres**.

`date`: a data em que a tarefa deverá ser concluída. Esse é um **datetime**.

`completed`: se a tarefa foi concluída ou não. Esse é um **Booliano**.

### Criar o esquema no código

Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Abra o arquivo `server.js` em um editor. Adicione as seguintes informações abaixo da entrada de configuração:

```Javascript
// MongoDB setup
// Set up some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Isso se conectar ao servidor MongoDB e retornará um objeto de esquema.

### Usar o esquema para criar o modelo no código

Abaixo do código escrito acima, adicione o código a seguir:

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Primeiro você cria o esquema e, em seguida, cria um objeto de modelo que usará para armazenar os dados em todo o código ao definir suas **rotas**.

## Adicionar rotas para o servidor de tarefa da API REST

Agora que você tem um modelo de banco de dados com o qual trabalhar, adicione as rotas que usará para o servidor de API REST.

### Sobre rotas no Restify

As rotas funcionam no Restify exatamente da mesma forma como funcionam ao usar a pilha Express. Você define rotas usando o URI que espera que os aplicativos de cliente chamem. Na maioria dos casos, você deve definir as rotas em um arquivo separado. Para este tutorial, você colocará as rotas no arquivo `server.js`. Recomendamos que você coloque-as em seu próprio arquivo para uso em produção.

Um padrão típico para uma rota do Restify é:

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

Esse é o padrão no nível mais básico. O Restify e o Express fornecem funcionalidade muito mais aprofundada, como a definição de tipos de aplicativos e o roteamento complexo entre diferentes pontos de extremidade. Para este tutorial, manteremos essas rotas simples.

#### Adicionar rotas padrão a seu servidor

Agora você adicionará as rotas CRUD básicas de **criar**, **recuperar**, **atualizar** e **excluir**.

Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Abra o arquivo `server.js` em um editor. Adicione as seguintes informações abaixo das entradas do banco de dados que você criou acima:

```Javascript
/**
*
* APIs for our REST task server
*/
// Create a task
function createTask(req, res, next) {
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
// Create a new task model, fill it up and save it to Mongodb
var _task = new Task();
if (!req.params.task) {
req.log.warn({
params: p
}, 'createTodo: missing task');
next(new MissingTaskError());
return;
}
_task.owner = owner;
_task.task = req.params.task;
_task.date = new Date();
_task.save(function(err) {
if (err) {
req.log.warn(err, 'createTask: unable to save');
next(err);
} else {
res.send(201, _task);
}
});
return next();
}
// Delete a task by name
function removeTask(req, res, next) {
Task.remove({
task: req.params.task,
owner: owner
}, function(err) {
if (err) {
req.log.warn(err,
'removeTask: unable to delete %s',
req.params.task);
next(err);
} else {
log.info('Deleted task:', req.params.task);
res.send(204);
next();
}
});
}
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
function getTask(req, res, next) {
log.info('getTask was called for: ', owner);
Task.find({
owner: owner
}, function(err, data) {
if (err) {
req.log.warn(err, 'get: unable to read %s', owner);
next(err);
return;
}
res.json(data);
});
return next();
}
/// Simple returns the list of TODOs that were loaded.
function listTasks(req, res, next) {
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
log.info("listTasks was called for: ", owner);
Task.find({
owner: owner
}).limit(20).sort('date').exec(function(err, data) {
if (err)
return next(err);
if (data.length > 0) {
log.info(data);
}
if (!data.length) {
log.warn(err, "There is no tasks in the database. Add one!");
}
if (!owner) {
log.warn(err, "You did not pass an owner when listing tasks.");
} else {
res.json(data);
}
});
return next();
}
```

#### Adicionar tratamento de erro para as rotas

Você deve adicionar tratamento de erros, para que possa comunicar os problemas encontrados ao cliente de forma que ele possa entender.

Adicione o seguinte código abaixo do código que você escreveu acima:

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## Criar seu servidor

Agora você definiu o banco de dados e incluiu as rotas. A última coisa a fazer é adicionar a instância do servidor que gerenciará suas chamadas.

O Restify e Express fornecem personalização aprofundada para um servidor de API REST, mas você usará a configuração mais básica aqui.

```Javascript
/**
* Our server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that we don't drop data on uploads
server.pre(restify.pre.pause());
// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());
// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());
// Allow five requests/second by IP, and burst to 10
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## Adicionar as rotas ao servidor (sem autenticação)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the passport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method like this:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## Executar o servidor antes de adicionar suporte a OAuth

Você deve testar o servidor antes de adicionar a autenticação.

A maneira mais fácil de fazer isso é usando `curl` em uma linha de comando. Porém, antes de fazer isso, você precisa de um utilitário simples que permita analisar a saída como JSON. Primeiro, instale a ferramenta JSON.

`$npm install -g jsontool`

Isso instala a ferramenta JSON globalmente. Após instalar a ferramenta JSON, teste o servidor:

Verifique se sua instância do MongoDB está em execução.

`$sudo mongodb`

Altere para o diretório `azuread` e use `curl`.

`$ cd azuread` `$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 200 OK
Connection: close
Content-Type: application/json
Content-Length: 171
Date: Tue, 14 Jul 2015 05:43:38 GMT
[
"GET /",
"POST /tasks/:owner/:task",
"POST /tasks (for JSON body)",
"GET /tasks",
"PUT /tasks/:owner",
"GET /tasks/:owner",
"DELETE /tasks/:owner/:task"
]
```

Adicione uma tarefa:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

A resposta deve ser:

```Shell
HTTP/1.1 201 Created
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Content-Type: application/x-www-form-urlencoded
Content-Length: 5
Date: Tue, 04 Feb 2014 01:02:26 GMT
Hello
```
Você pode listar tarefas para o usuário "Brenda" desta forma:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Se isso funcionar, você estará pronto para adicionar OAuth ao servidor de API REST.

Você tem um servidor de API REST com o MongoDB.

## Adicionar autenticação a seu servidor de API REST

Agora que tem um servidor de API REST em execução, você pode torná-lo útil no Azure AD.

Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

### Use o OIDCBearerStrategy que está incluído no passport-azure-ad

Até agora, você criou um servidor ToDo do REST típico sem nenhum tipo de autorização. Agora você pode começar a juntar a autorização.

Primeiro, você precisa indicar que deseja usar o Passport. Adicione isso logo abaixo da configuração do servidor:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts Passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
Ao escrever APIs, você deve sempre vincular os dados a algo exclusivo do token que o usuário não possa falsificar. Quando o servidor armazena itens de ToDo, ele faz isso com base na **ID de objeto** do usuário no token (chamado por meio de token.oid) que é incluído no campo "proprietário". Isso garante que somente o usuário possa acessar seus itens ToDo e que ninguém mais possa acessar os itens ToDo que foram inseridos. Não há exposição na API do "proprietário". Portanto, um usuário externo pode solicitar os itens ToDo de outras pessoas, mesmo que estejam autenticados.

Em seguida, use a estratégia de portador que vem com o `passport-azure-ad`. (Vamos apenas examinar o código por enquanto.) Colocar isso depois que você leu acima:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementer.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
log.info('Found user: ', user);
return fn(null, user);
}
}
return fn(null, null);
};
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retrieved');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically as they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

O Passport usa um padrão para todas as suas estratégias (incluindo o Twitter e Facebook) que é semelhante às estratégias que todos os gravadores de estratégias seguem. Você o passa como um `function()` que tem `token` e `done` como parâmetros. A estratégia voltará para você depois que fizer seu trabalho. Em seguida, você deve armazenar o usuário e salvar o token para que não precise pedi-lo novamente.

> [AZURE.IMPORTANT]
O código acima usa qualquer usuário que se autentique em seu servidor. Isso é conhecido como registro automático. Em servidores de produção, não convém permitir que qualquer usuário entre sem primeiro passar por um processo de registro que você decidiu. Esse geralmente é o padrão em aplicativos de consumidor que lhe permitem registrar-se usando o Facebook, mas depois pedem que você preencha informações adicionais. Se esse não fosse um programa de linha de comando, poderíamos ter extraído o email do objeto de token que é retornado e pedido que os usuários preenchessem informações adicionais. Como esse é um servidor de teste, basta adicioná-los ao banco de dados na memória.

### Proteger pontos de extremidade

Você protege pontos de extremidade ao especificar a chamada `passport.authenticate()` usando o protocolo que deseja usar.

Você pode editar a rota no código de servidor para fazer algo mais interessante:

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## Executar o aplicativo de servidor novamente e verificar se ele o rejeita

Você pode usar `curl` novamente para ver se agora tem a proteção de OAuth2 para os pontos de extremidade. Faça isso antes de executar qualquer dos SDKs de cliente em relação a esse ponto de extremidade. Os cabeçalhos retornados devem ser suficientes para indicar que você está no caminho certo.

Verifique se a instância do MongoDB está em execução.

	$sudo mongodb

Altere para o diretório e use `curl`:

	$ cd azuread
	$ node server.js

Tente uma POSTAGEM básica:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Um erro 401 é a resposta desejada. Isso indica que a camada do Passport está tentando se redirecionar para o ponto de extremidade de autorização.


## Agora você tem um serviço de API REST que usa OAuth2

Você fez tudo o que podia com esse servidor sem usar um cliente compatível com OAuth2. Para isso, você precisará de um passo a passo adicional.

Se deseja apenas obter informações sobre como implementar uma API REST usando Restify e OAuth2, agora você tem código suficiente para continuar a desenvolver seu serviço e basear-se neste exemplo.

Para referência, o exemplo concluído (sem seus valores de configuração) [é fornecido como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/complete.zip). Você também pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```


## Próximas etapas

Agora você pode ir para tópicos mais avançados, como:

[Conectar-se a uma API Web usando o iOS com o B2C](active-directory-b2c-devquickstarts-ios.md)

<!---HONumber=AcomDC_0224_2016-->