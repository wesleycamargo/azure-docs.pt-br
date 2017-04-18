---
title: API Web NodeJS do Azure AD v2.0 | Microsoft Docs
description: Como criar uma API Web do Node JS que aceite tokens da conta pessoal da Microsoft e das contas corporativas ou de estudante.
services: active-directory
documentationcenter: nodejs
author: xerners
manager: mbaldwin
editor: 
ms.assetid: 0b572fc1-2aaf-4cb6-82de-63010fb1941d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 09/16/2016
ms.author: xerners
translationtype: Human Translation
ms.sourcegitcommit: 3e0bb32a6c60011d71606c896cc506f430bc3c27
ms.openlocfilehash: b0862b778cf02120f66414be13d0b9e07709f45f
ms.lasthandoff: 03/29/2017


---
# <a name="secure-a-web-api-using-nodejs"></a>Proteger uma API Web usando node.js
> [!NOTE]
> Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0.  Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
> 
> 

Com o ponto de extremidade v2.0 do Azure Active Directory, você pode proteger uma API Web usando tokens de acesso [OAuth 2.0](active-directory-v2-protocols.md) , permitindo que usuários com contas pessoais, corporativas ou de estudante da Microsoft acessem sua API Web com segurança.

**Passport** é middleware de autenticação para o Node.js. Extremamente flexível e modular, o Passport pode ser colocado sem impedimento em qualquer aplicativo Web baseado em Express ou Restify. Um conjunto abrangente de estratégias suportam a autenticação usando um nome de usuário e senha, Facebook, Twitter e mais. Desenvolvemos uma estratégia para o Active Directory do Microsoft Azure. Instalaremos esse módulo e, em seguida, adicionaremos o plug-in `passport-azure-ad` do Active Directory do Microsoft Azure.

## <a name="download"></a>Baixar
O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs).  Para acompanhar, você pode [baixar o esqueleto do aplicativo como um .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip) ou clonar o esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

O aplicativo completo também é fornecido no final deste tutorial.

## <a name="1-register-an-app"></a>1. Registrar um aplicativo
Crie um novo aplicativo em [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou siga estas [etapas detalhadas](active-directory-v2-app-registration.md).  Não se esqueça de:

* Copiar a **ID do Aplicativo** designada ao seu aplicativo, você precisará dela logo.
* Adicione a plataforma **Móvel** de seu aplicativo.
* Copie o **URI de Redirecionamento** do portal. Você deve usar o valor padrão de `urn:ietf:wg:oauth:2.0:oob`.

## <a name="2-download-nodejs-for-your-platform"></a>2: baixar node.js para sua plataforma
Para usar este exemplo com êxito, você deve ter uma instalação do Node.js em funcionamento.

Instale o Node.js a partir de [http://nodejs.org](http://nodejs.org).

## <a name="3-install-mongodb-on-to-your-platform"></a>3: instalar o MongoDB em sua plataforma
Para usar este exemplo com êxito, você deve ter uma instalação do MongoDB funcionando corretamente. Nós usaremos o MongoDB para tornar nossa API REST persistente entre instâncias de servidor.

Instalar o MongoDB a partir de [http://mongodb.org](http://www.mongodb.org).

> [!NOTE]
> Este passo a passo presume que você usa os pontos de extremidade de servidor e de instalação padrão para MongoDB, que, no momento da redação deste artigo, é: mongodb://localhost
> 
> 

## <a name="4-install-the-restify-modules-in-to-your-web-api"></a>4: instalar os módulos Restify em sua API Web
Usaremos Resitfy para criar nossa API REST. O Restify é uma estrutura de aplicativo do Node.js mínima e flexível, derivada do Express, que tem um conjunto robusto de recursos para a criação de APIs REST no Connect.

### <a name="install-restify"></a>Instalar Restify
Na linha de comando, altere os diretórios para o diretório azuread. Se o diretório **azuread** não existir, crie-o.

`cd azuread` - ou- `mkdir azuread;`

Digite o seguinte comando: 

`npm install restify`

Este comando instala o Restify.

#### <a name="did-you-get-an-error"></a>Você obteve um erro?
Ao usar o npm em alguns sistemas operacionais, você poderá receber uma mensagem de erro: EPERM, chmod '/usr/local/bin/..' e uma solicitação para tentar executar a conta como administrador. Se isso ocorrer, use o comando sudo para executar o npm com um nível de privilégio mais elevado.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>Você obteve um erro relacionado ao DTrace?
Você pode ver algo assim ao instalar Restify:

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


O Restify fornece um mecanismo eficiente para rastrear chamadas REST usando o DTrace. No entanto, muitos sistemas operacionais não têm o DTrace disponível. Você pode ignorar com segurança esses erros.

A saída desse comando deve ser semelhante ao seguinte:

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
    └── bunyan@0.22.0(mv@0.0.5)


## <a name="5-install-passportjs-into-your-web-api"></a>5: instalar Passport.js em sua API da Web
[Passport](http://passportjs.org/) é middleware de autenticação para o Node.js. Extremamente flexível e modular, o Passport pode ser colocado sem impedimento em qualquer aplicativo Web baseado em Express ou Restify. Um conjunto abrangente de estratégias suportam a autenticação usando um nome de usuário e senha, Facebook, Twitter e mais. Desenvolvemos uma estratégia para o Active Directory do Azure. Instalaremos esse módulo e, em seguida, adicionaremos o plug-in de estratégia do Active Directory do Azure.

Na linha de comando, altere os diretórios para o diretório azuread.

Insira o comando a seguir para instalar o passport.js

`npm install passport`

A saída do comando deve ter aparência semelhante à seguinte:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6: Adicionar AD de Azure Passport à sua API da Web
Em seguida, adicionaremos a estratégia de OAuth, usando o passport-azuread, um conjunto de estratégias que conectam o Azure Active Directory com o Passport. Usaremos essa estratégia para Tokens de portador neste exemplo de API Rest.

> [!NOTE]
> Embora o OAuth2 forneça uma estrutura na qual qualquer tipo de token conhecido pode ser emitido, somente determinados tipos de token passaram a ser amplamente usados. Para proteger os pontos de extremidade, que mostraram ser tokens de portador. Os tokens de portador são o tipo de token mais amplamente emitido em OAuth2, e muitas implementações assumem que os tokens de portador são o único tipo de token emitido.
> 
> 

Na linha de comando, altere os diretórios para o diretório azuread.

Digite o seguinte comando para instalar o módulo Passport.js passport-azure-ad:

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

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7: adicionar módulos do MongoDB à sua API da Web
Usaremos o MongoDB como nosso repositório de dados. Por esse motivo, é preciso instalar tanto o plug-in amplamente utilizado para gerenciar modelos e esquemas chamado Mongoose quanto o driver de banco de dados para MongoDB, também chamado de MongoDB.

* `npm install mongoose`
* `npm install mongodb`

## <a name="8-install-additional-modules"></a>8: instalar módulos adicionais
Em seguida, instalaremos os módulos necessários restantes.

Na linha de comando, altere os diretórios para o diretório **azuread** , se você ainda não estiver nele:

`cd azuread`

Digite os comandos a seguir para instalar os seguintes módulos em seu diretório node_modules:

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

## <a name="9-create-a-serverjs-with-your-dependencies"></a>9: criar um server.js com as suas dependências
O arquivo server.js fornecerá a maior parte da nossa funcionalidade para nosso servidor de API da Web. Vamos adicionar a maior parte do nosso código a esse arquivo. Para fins de produção, você poderia refatorar a funcionalidade em arquivos menores, como rotas separadas e controladores. Para esta demonstração, usaremos server.js para essa funcionalidade.

Na linha de comando, altere os diretórios para o diretório **azuread** , se você ainda não estiver nele:

`cd azuread`

Crie um arquivo `server.js` no nosso editor favorito e adicione as informações a seguir:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
```

Salve o arquivo. Voltaremos a ele em breve.

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10: criar um arquivo de configuração para armazenar as configurações do AD do Azure
Esse arquivo de código passa os parâmetros de configuração do seu Portal do Active Directory do Azure ao Passport.js. Você criou esses valores de configuração quando adicionou a API da Web ao portal na primeira parte do passo a passo. Explicaremos o que é inserir os valores desses parâmetros depois que você tiver copiado o código.

Na linha de comando, altere os diretórios para o diretório **azuread** , se você ainda não estiver nele:

`cd azuread`

Crie um arquivo `config.js` no nosso editor favorito e adicione as informações a seguir:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
issuer: 'https://sts.windows.net/**<your application id>**/',
audience: '<your redirect URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For using Microsoft you should never need to change this.
};

```



### <a name="required-values"></a>Valores necessários
*IdentityMetadata*: é onde passport-azure-ad vai procurar os dados de configuração para o IdP, bem como as chaves para validar os tokens JWT. Provavelmente, você não deseja alterar isso se estiver usando o Active Directory do Azure.

*audience*: o URI de redirecionamento do portal.

> [!NOTE]
> Revertemos as nossas chaves em intervalos frequentes. Certifique-se de estar extraindo sempre da URL "openid_keys" e de que o aplicativo pode acessar a Internet.
> 
> 

## <a name="11-add-configuration-to-your-serverjs-file"></a>11: adicionar configuração ao arquivo server.js
Precisamos ler esses valores do arquivo de configuração que você acabou de criar em nosso aplicativo. Para fazer isso, podemos simplesmente adicionar o arquivo config.js como um recurso necessário em nosso aplicativo e, em seguida, definir as variáveis globais para esses valores no documento config.js

Na linha de comando, altere os diretórios para o diretório **azuread** , se você ainda não estiver nele:

`cd azuread`

Abra o arquivo `server.js` no nosso editor favorito e adicione as seguintes informações:

```Javascript
var config = require('./config');
```
Em seguida, adicione uma nova seção a `server.js` com o código a seguir:

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
identityMetadata: config.creds.identityMetadata,
issuer: config.creds.issuer,
audience: config.creds.audience
};
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="step-12-add-the-mongodb-model-and-schema-information-using-moongoose"></a>12: adicionar as informações de esquema e modelo do MongoDB usando Moongoose
Agora todos os essa preparação passará a compensar, enquanto agrupamos esses três arquivos juntos em um serviço de API REST.

Para este passo a passo, usaremos o MongoDB para armazenar nossas tarefas, conforme discutido na ***Etapa 4***.

Se você chamar novamente o arquivo config.js criado na Etapa 11, chamaremos nosso banco de dados *tasklist*, já que foi isso que colocamos no final da nossa URL de conexão mogoose_auth_local. Você não precisa criar esse banco de dados com antecedência no MongoDB, ele criará isso para nós na primeira execução do nosso aplicativo de servidor (supondo que ele ainda não exista).

Agora que dissemos ao servidor qual banco de dados MongoDB, gostaríamos de usar, precisamos escrever algum código adicional para criar o modelo e o esquema para as tarefas do nosso servidor.

#### <a name="discussion-of-the-model"></a>Discussão do modelo
Nosso modelo de esquema é muito simples e você o expande conforme necessário.

NAME - o nome de quem está atribuído à tarefa. Uma ***cadeia de caracteres***

TASK - a própria tarefa. Uma ***cadeia de caracteres***

DATE - a data em que a tarefa deverá ser concluída. UM VALOR ***DATETIME***

COMPLETED - Se a tarefa está concluída ou não. UM VALOR ***BOOLEANO***

#### <a name="creating-the-schema-in-the-code"></a>Criando o esquema no código
Na linha de comando, altere os diretórios para o diretório **azuread** , se você ainda não estiver nele:

`cd azuread`

Abra o arquivo `server.js` no nosso editor favorito e adicione as seguintes informações abaixo da entrada de configuração:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Isso se conectar ao servidor MongoDB e devolver um objeto de esquema para nós.

#### <a name="using-the-schema-create-our-model-in-the-code"></a>Usando o esquema, criar nosso modelo no código
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
Como você pode ver no código, criamos nosso esquema e, em seguida, criamos um objeto de modelo que será usado para armazenar nossos dados por todo o código quando definimos nossas ***Rotas***.

## <a name="step-13-add-our-routes-for-our-task-rest-api-server"></a>Etapa 13: adicionar nossas rotas a nosso servidor de API REST de tarefa
Agora que temos um modelo de banco de dados com o qual trabalhar, vamos adicionar as rotas que usaremos para nosso servidor de API REST.

### <a name="about-routes-in-restify"></a>Sobre rotas em Restify
Rotas funcionam em Restify exatamente da mesma maneira que funcionam usando a pilha Express. Você define rotas usando o URI que você espera que os aplicativos de cliente chamem. Normalmente, você define suas rotas em um arquivo separado. Para nossos propósitos, colocaremos nossas rotas no arquivo server.js. Recomendamos que você coloque-as em seu próprio arquivo para uso em produção.

Eis um padrão típico para uma rota de Restify:

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


Este é o padrão no nível mais básico. Resitfy (e Express) fornecem funcionalidade muito mais aprofundada, como definir tipos de aplicativos e fazer roteamento complexo entre diferentes pontos de extremidade. Para nossos propósitos, manteremos essas rotas de modo muito simples.

#### <a name="add-default-routes-to-our-server"></a>Adicionar rotas padrão ao nosso servidor
Vamos agora adicionar rotas CRUD básicas de Create (criar), Retrieve (extrair), Update (atualizar) e Delete (excluir).

Na linha de comando, altere os diretórios para o diretório **azuread** , se você ainda não estiver nele:

`cd azuread`

Abra o arquivo `server.js` no nosso editor favorito e adicione as seguintes informações abaixo das entradas do banco de dados que você criou acima:

```Javascript
/**
*
* APIs for our REST Task server
*/
// Create a task
function createTask(req, res, next) {
// Resitify currently has a bug which doesn't allow you to set default headers
// This headers comply with CORS and allow us to mongodbServer our response to any origin
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
// Resitify currently has a bug which doesn't allow you to set default headers
// This headers comply with CORS and allow us to mongodbServer our response to any origin
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

### <a name="add-some-error-handling-for-the-routes"></a>Adicionar algum tratamento de erro para as rotas
Faz sentido adicionar algum tratamento de erros, para que seja possível comunicar ao cliente o problema que encontramos de modo que o cliente possa entender.

Adicione o código a seguir abaixo do código que você escreveu acima:

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


## <a name="step-14-create-your-server"></a>Etapa 14: crie seu servidor!
Temos nosso banco de dados definido, temos nossas rotas estabelecidas e a última coisa a fazer é adicionar nossa instância de servidor que gerenciará nossas chamadas.

Restify (e Express) tem muita personalização profunda que você pode fazer para um servidor de API REST, mas novamente usaremos a configuração mais básica para atender aos nossos objetivos.

```Javascript
/**
* Our Server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directroy TODO Server",
version: "2.0.1"
});
// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());
// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());
// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());
// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());
// Allow 5 requests/second by IP, and burst to 10
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
## <a name="15-adding-the-routes-without-authentication-for-now"></a>15: adicionar rotas (sem autenticação por enquanto)
```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers are protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the pasport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiement removing API protection
/* by removing the passport.authenticate() method like so:
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
## <a name="16-before-we-add-oauth-support-lets-run-the-server"></a>16: antes de adicionar suporte a OAuth, vamos executar o servidor.
Testar o servidor antes de adicionar autenticação

A maneira mais fácil de fazer isso é usando ondulação em uma linha de comando. Antes de fazermos isso, precisamos de um utilitário simples que nos permita analisar a saída como JSON. Para fazer isso, instale a ferramenta json, já que todos os exemplos abaixo a utilizam.

`$npm install -g jsontool`

Isso instala a ferramenta JSON globalmente. Agora que você conseguiu fazer isso, vamos mexer com o servidor:

Primeiro, certifique-se de que sua instância do monogoDB está em execução.

`$sudo mongod`

Em seguida, vá até o diretório e inicie a ondulação.

`$ cd azuread`
`$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 2.0OK
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

Então, podemos adicionar uma tarefa deste modo:

`$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

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
E podemos pode listar tarefas para Brandon deste modo:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Se tudo isso funcionar, estamos prontos para adicionar OAuth ao servidor de API REST.

**Você tem um servidor de API REST com o MongoDB!**

## <a name="17-add-authentication-to-our-rest-api-server"></a>17: adicione autenticação ao nosso servidor de API REST
Agora que temos uma API REST em execução (aliás, parabéns por isso!) Vamos torná-lo útil para uso com o Azure AD.

Na linha de comando, altere os diretórios para o diretório **azuread** , se você ainda não estiver nele:

`cd azuread`

### <a name="1-use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>1: usar o oidcbearerstrategy que está incluído no passport-azure-ad
Até agora, criamos um servidor típico TODO do REST sem nenhum tipo de autorização. Aqui é onde começamos a juntar as peças.

Primeiro, precisamos indicar que queremos usar o Passport. Coloque isso logo após a configuração do servidor:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [!TIP]
> Ao escrever APIs você deve sempre vincular os dados a algo exclusivo do token que o usuário não poderá falsificar. Quando esse servidor armazena itens TODO, armazena-os com base na ID da assinatura do usuário no token que colocamos no campo "proprietário" (chamado por meio de token.sub). Isso garante que somente esse usuário pode acessar seus TODOs e ninguém pode acessar os TODOs inseridos. Não há exposição na API do "proprietário" então um usuário externo pode solicitar outros TODOs mesmo se estiverem autenticados.
> 
> 

Em seguida, vamos usar a estratégia de Open ID Connect Bearer que vem com o passport-azure-ad. Apenas olhe o código por enquanto, eu o explicarei daqui a pouco. Colocar isso depois que você leu acima:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementor.
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
log.info(token, 'was the token retreived');
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

O Passport usa um padrão semelhante para todas as Estratégias (Twitter, Facebook etc.) que todos os gravadores de Estratégia seguem. Observando a estratégia, você verá que passamos a ela uma function() que tem um token e um done como parâmetros. A estratégia retorna corretamente para nós após concluir seu trabalho. Depois disso, vamos armazenar o usuário e acrescentar o token, para que não precisemos pedi-lo novamente.

> [!IMPORTANT]
> O código acima usa qualquer usuário que tente se autenticar em nosso servidor. Isso é conhecido como registro automático. Em servidores de produção, não convém permitir que qualquer pessoa entre sem primeiro passar por um processo de registro que você decide. Esse geralmente é o padrão que você vê em aplicativos de consumidor que lhe permitem registrar-se com o Facebook, mas depois pedem que você preencha informações adicionais. Se esse não fosse um programa de linha de comando, poderíamos ter apenas extraído o e-mail do objeto de token que é retornado e pedido que ele preenchesse informações adicionais. Como esse é um servidor de teste, basta adicioná-los ao banco de dados na memória.
> 
> 

### <a name="2-finally-protect-some-endpoints"></a>2. Por fim, proteger alguns pontos de extremidade
Você pode proteger pontos de extremidade, especificando a chamada passport.authenticate() com o protocolo que você deseja usar.

Vamos editar nosso roteiro em nosso código de servidor para fazer algo mais interessante:

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again-and-ensure-it-rejects-you"></a>18: executar o aplicativo de servidor novamente e certificar-se de que rejeitará você
Vamos usar `curl` novamente para ver se agora temos proteção OAuth2 para nossos pontos de extremidade. Faremos isso antes de executar qualquer um dos nossos SDKs de cliente para esse ponto de extremidade. Os cabeçalhos retornados devem ser suficientes para demonstrar a nós que estamos no caminho certo.

Primeiro, certifique-se de que sua instância do monogoDB está em execução.

    $sudo mongod

Em seguida, vá até o diretório e inicie a ondulação.

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

Um 401 é a resposta que você está procurando, já que indica que a camada do Passport está tentando redirecionar-se para o ponto de extremidade de autorização, que é exatamente o que você deseja.

## <a name="congratulations-you-have-a-rest-api-service-using-oauth2"></a>Parabéns! Você tem um serviço da API REST usando OAuth2!
Você já fez tudo que era possível com esse servidor sem usar um cliente compatível com OAuth2. Você precisará passar por um passo a passo adicional.

Se você estava apenas procurando obter informações sobre como implementar uma API REST usando Restify e OAuth2, você tem código mais do que suficiente para manter o desenvolvimento de seu serviço e aprender como compilar neste exemplo.

## <a name="next-steps"></a>Próximas etapas
Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um .zip aqui](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Agora você pode ir para tópicos mais avançados.  Você pode desejar experimentar:

[Proteger um aplicativo Web do Node.js usando o ponto de extremidade v2.0 >>](active-directory-v2-devquickstarts-node-web.md)

Para obter recursos adicionais, consulte:

* [Guia do desenvolvedor do v2.0 >>](active-directory-appmodel-v2-overview.md)
* [Marca “azure-active-directory” do StackOverflow >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para nossos produtos
Recomendamos que você obtenha notificações sobre a ocorrência de incidentes de segurança visitando [esta página](https://technet.microsoft.com/security/dd252948) e assinando os alertas do Security Advisory.


