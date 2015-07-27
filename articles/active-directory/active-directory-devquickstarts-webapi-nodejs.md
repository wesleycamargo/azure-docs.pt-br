<properties
	pageTitle="Introdução ao NodeJS do AD do Azure | Microsoft Azure"
	description="Como criar uma API da Web do Node.js que se integre ao AD do Azure para autenticação."
	services="active-directory"
	documentationCenter="nodejs"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="brandwe"/>

# Introdução à API da Web para nó

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Este passo a passo lhe oferecerá uma maneira rápida e fácil de configurar um serviço de API REST que é integrado ao Active Directory do Azure para proteção de API usando o protocolo OAuth2. Os servidores de exemplo incluídos no download são projetados para execução em qualquer plataforma, mas são voltados a OSX e Linux.

No final deste passo a passo, você deve ser capaz de criar um servidor de API REST em execução, contando com os seguintes recursos:

* Um servidor node.js com uma interface REST API com JSON usando MongoDB como armazenamento persistente
* APIs REST aproveitando a proteção de API OAuth2, com tokens Bearer usando o Active Directory do Azure


Já lançamos todo o código-fonte para este exemplo em execução no GitHub sob uma licença do Apache 2.0, então fique à vontade para fazer a clonagem (ou melhor ainda, bifurcação), fornecer comentários e receber solicitações.

## Sobre módulos Node.js

Usaremos módulos Node.js neste passo a passo. Os módulos são pacotes carregáveis do JavaScript que fornecem funcionalidade específica para seu aplicativo. Você geralmente instala módulos usando a ferramenta de linha de comando NPM Node.js no diretório de instalação do NPM, mas alguns módulos, como o módulo HTTP, estão incluídos o pacote Node.js principal. Os módulos instalados são salvos no diretório node_modules, na raiz do diretório de instalação do seu Node.js. Cada módulo dentro do diretório node_modules mantém seu próprio diretório node_modules que contém todos os módulos dos quais ele depende e cada módulo requerido tem um diretório node_modules. Essa estrutura de diretório recursiva representa a cadeia de dependências.

Essa estrutura de cadeia de dependências resulta em um espaço maior do aplicativo, mas garante que todas as dependências sejam atendidas e que a versão dos módulos usados no desenvolvimento também seja usada na produção. Isso torna o comportamento do aplicativo de produção mais previsível e evita os problemas de controle de versão que podem afetar os usuários.

## Etapa 1: registrar um locatário do AD do Azure

Para usar esse exemplo, você precisará um locatário do Active Directory do Azure. Se você não tem certeza de o que é um locatário ou como obter um, consulte [Como obter um locatário do AD do Azure](active-directory-howto-tenant.md).

## Etapa 2: adicionar uma API da Web ao seu locatário

Depois de obter seu locatário do Active Directory do Azure, adicione este aplicativo de exemplo ao seu locatário, para que você possa usá-lo para proteger a API.

Para habilitar seu aplicativo a autenticar usuários, primeiro você precisará registrar um novo aplicativo em seu locatário.

- Entre no Portal de Gerenciamento do Azure.
- Clique em **Active Directory** no painel de navegação à esquerda.
- Selecione o locatário em que você deseja registrar o aplicativo.
- Clique na guia **Aplicativos** e clique em adicionar na lista de botões.
- Siga os prompts e crie um novo **Aplicativo Web e/ou WebAPI**.  

    - O **nome** do aplicativo descreverá seu aplicativo para os usuários finais
    -	A **URL de logon** é a URL base do seu aplicativo. O padrão do esqueleto é `https://localhost:8888`.
    - O **URI da ID do aplicativo** é um identificador exclusivo para seu aplicativo. A convenção é usar `https://<tenant-domain>/<app-name>`, por exemplo, `https://contoso.onmicrosoft.com/my-first-aad-app`
- Depois de concluir o registro, o AAD atribuirá a seu aplicativo um identificador de cliente único. Você precisará desse valor nas próximas seções, então copie-o da guia Configurar.

## Etapa 3: baixar node.js para sua plataforma
Para usar este exemplo com êxito, você deve ter uma instalação do Node.js em funcionamento.

Instale o Node.js a partir de [http://nodejs.org](http://nodejs.org).

## Etapa 4: instalar o MongoDB em sua plataforma

Para usar este exemplo com êxito, você deve ter uma instalação do MongoDB funcionando corretamente. Nós usaremos o MongoDB para tornar nossa API REST persistente entre instâncias de servidor.

Instalar o MongoDB a partir de [http://mongodb.org](http://www.mongodb.org).

**OBSERVAÇÃO:** este passo a passo presume que você usa os pontos de extremidade de servidor e de instalação padrão para MongoDB, que, no momento da redação deste artigo, é: mongodb://localhost


## Etapa 5: instalar os módulos Restify em sua API da Web

Usaremos Resitfy para criar nossa API REST. O Restify é uma estrutura de aplicativo do Node.js mínima e flexível, derivada do Express, que tem um conjunto robusto de recursos para a criação de APIs REST no Connect.

### Instalar Restify

Na linha de comando, altere os diretórios para o diretório azuread. Se o diretório **azuread** não existir, crie-o.

`cd azuread - or- mkdir azuread; cd azuread`

Digite o seguinte comando:

`npm install restify`

Este comando instala o Restify.

#### VOCÊ OBTEVE UM ERRO?

Ao usar npm em alguns sistemas operacionais, você poderá receber uma mensagem de Erro: EPERM, chmod '/usr/local/bin/...' e uma solicitação para tentar executar a conta como administrador. Se isso ocorrer, use o comando sudo para executar o npm com um nível de privilégio mais elevado.

#### VOCÊ OBTEVE UM ERRO COM RELAÇÃO AO DTRACE?

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
	└── bunyan@0.22.0 (mv@0.0.5)


## Etapa 6: instalar Passport.js em sua API da Web

[Passport](http://passportjs.org/) é middleware de autenticação para o Node.js. Extremamente flexível e modular, o Passport pode ser colocado sem impedimento em qualquer aplicativo Web baseado em Express ou Restify. Um conjunto abrangente de estratégias suportam a autenticação usando um nome de usuário e senha, Facebook, Twitter e mais. Desenvolvemos uma estratégia para o Active Directory do Azure. Instalaremos esse módulo e, em seguida, adicionaremos o plug-in de estratégia do Active Directory do Azure.

Na linha de comando, altere os diretórios para o diretório azuread.

Insira o comando a seguir para instalar o passport.js

`npm install passport`

A saída do comando deve ter aparência semelhante à seguinte:

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## Etapa 7: adicionar suporte para Token de portador Passport.js à sua API da Web

Em seguida, adicionaremos a estratégia de Portador, usando o passport-bearer-http, um manipulador de Portador para [Passport](http://passportjs.org/). Também adicionaremos suporte do manipulador de token JWT usando o node-jwt.

**OBSERVAÇÃO:** embora o OAuth2 forneça uma estrutura na qual qualquer tipo de token conhecido pode ser emitido, somente determinados tipos de token passaram a ser amplamente usados. Para proteger os pontos de extremidade, que mostraram ser tokens de portador. Os tokens de portador são o tipo de token mais amplamente emitido em OAuth2, e muitas implementações assumem que os tokens de portador são o único tipo de token emitido.

Na linha de comando, altere os diretórios para o diretório **azuread**.

Insira o comando a seguir para instalar os módulos Passport.js:

- `npm install passport-oauth`
- `npm install passport-http-bearer`
- `npm install node-jwt`

A saída do comando deve ter aparência semelhante à seguinte:

	ms-passport-wsfed-saml2@0.3.8 node_modules\passport-oauth  
	├── xtend@2.0.3
	├── xml-crypto@0.0.9
	├── xmldom@0.1.13
	└── xml2js@0.1.14 (sax@0.5.2)


## Etapa 8: adicionar módulos do MongoDB à sua API da Web

Usaremos o MongoDB como nosso repositório de dados. Por esse motivo, é preciso instalar tanto o plug-in amplamente utilizado para gerenciar modelos e esquemas chamado Mongoose quanto o driver de banco de dados para MongoDB, também chamado de MongoDB.


* `npm install mongoose`
* `npm install mongodb`

## Etapa 9: instalar módulos adicionais

Em seguida, instalaremos os módulos necessários restantes.


Na linha de comando, altere os diretórios para o diretório **azuread**, se você ainda não estiver nele:

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


## Etapa 10: criar um server.js com as suas dependências

O arquivo server.js fornecerá a maior parte da nossa funcionalidade para nosso servidor de API da Web. Vamos adicionar a maior parte do nosso código a esse arquivo. Para fins de produção, você poderia refatorar a funcionalidade em arquivos menores, como rotas separadas e controladores. Para esta demonstração, usaremos server.js para essa funcionalidade.

Na linha de comando, altere os diretórios para o diretório **azuread**, se você ainda não estiver nele:

`cd azuread`

Crie um arquivo `server.js` no nosso editor favorito e adicione as informações a seguir:

```Javascript
	'use strict';

	/**
 	* Module dependencies.
 	*/

	var fs = require('fs');
	var path = require('path');
	var util = require('util');
	var assert = require('assert-plus');
	var bunyan = require('bunyan');
	var getopt = require('posix-getopt');
	var mongoose = require('mongoose/');
	var restify = require('restify');
```

Salve o arquivo. Voltaremos a ele em breve.

## Etapa 11: criar um arquivo de configuração para armazenar as configurações do Active Directory do Azure

Esse arquivo de código passa os parâmetros de configuração do seu Portal do Active Directory do Azure ao Passport.js. Você criou esses valores de configuração quando adicionou a API da Web ao portal na primeira parte do passo a passo. Explicaremos o que é inserir os valores desses parâmetros depois que você tiver copiado o código.


Na linha de comando, altere os diretórios para o diretório **azuread**, se você ainda não estiver nele:

`cd azuread`

Crie um arquivo `config.js` no nosso editor favorito e adicione as informações a seguir:

```Javascript
// Don't commit this file to your public repos
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
    openid_configuration: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
    openid_keys: 'https://login.microsoftonline.com/common/discovery/keys', // For using Microsoft you should never need to change this. If absent will attempt to get from openid_configuration
}

```



**Observação:** você provavelmente nunca precisará alterar esses valores.

**Observação:** reverter as nossas chaves em intervalos frequentes. Certifique-se de estar extraindo sempre da URL "openid_keys" e de que o aplicativo pode acessar a Internet.


## Etapa 12: adicionar configuração ao arquivo server.js

Precisamos ler esses valores do arquivo de configuração que você acabou de criar em nosso aplicativo. Para fazer isso, podemos simplesmente adicionar o arquivo config.js como um recurso necessário em nosso aplicativo e, em seguida, definir as variáveis globais para esses valores no documento config.js

Na linha de comando, altere os diretórios para o diretório **azuread**, se você ainda não estiver nele:

`cd azuread`

Abra o arquivo `server.js` no nosso editor favorito e adicione as seguintes informações:

```Javascript
var config = require('./config');
```
Em seguida, adicione uma nova seção a `server.js` com o código a seguir:

```Javascript
/**
* Setup some configuration
*/
var mongoose = require('mongoose/');
var serverPort = process.env.PORT || 8888;
var serverURI = ( process.env.PORT ) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

```
## Etapa 13: criar um arquivo de ajuda metadata.js para auxiliar na análise de metadados/tokens

Como o objetivo é manter a lógica do aplicativo no arquivo server.js, faz sentido colocar alguns métodos auxiliares em um arquivo separado. Esses métodos simplesmente nos ajudam a analisar os metadados OpenID Connect e não estão relacionados ao cenário principal. É melhor deixá-los de lado. Vamos adicionar mais deles a esse arquivo conforme percorrermos todo o passo a passo.

***OBSERVAÇÃO:*** você notará que esse arquivo metadata.js analisa o XML para SAML e WS-Fed, bem como JSON para OpenID Connect. Isso é proposital, já que você usará esse arquivo em nossos outros exemplos também. Por hora, você pode ignorar isso com segurança.

Na linha de comando, altere os diretórios para o diretório **azuread**, se você ainda não estiver nele:

`cd azuread`

Crie um arquivo `metadata.js` no nosso editor favorito e adicione as informações a seguir:

```Javascript

'use strict';

var xml2js = require('xml2js');
var request = require('request');
var aadutils = require('./aadutils');
var async = require('async');

// Logging

var bunyan = require('bunyan');
var log = bunyan.createLogger({name: 'Microsoft OpenID Connect Passport Strategy'});

var Metadata = function (url, authtype) {


  if(!url) {
    throw new Error("Metadata: url is a required argument");
  }
  if(!authtype) {
    throw new Error('OIDCBearerStrategy requires an authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"');
  }

  this.url = url;
  this.metadata = null;
  this.authtype = authtype;
  log.info(authtype, 'Metadata requested for authentication type');
};

Object.defineProperty(Metadata, 'url', {
  get: function () {
    return this.url;
  }
});

Object.defineProperty(Metadata, 'saml', {
  get: function () {
    return this.saml;
  }
});

Object.defineProperty(Metadata, 'wsfed', {
  get: function () {
    return this.wsfed;
  }
});

Object.defineProperty(Metadata, 'oidc', {
  get: function () {
    return this.oidc;
  }
});


Object.defineProperty(Metadata, 'metadata', {
  get: function () {
    return this.metadata;
  }
});

Metadata.prototype.updateSamlMetadata = function(doc, next) {
  log.info('Request to update the SAML Metadata');
  try {

    this.saml = {};

    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var idp = aadutils.getElement(entity, 'IDPSSODescriptor');
    var signOn = aadutils.getElement(idp[0], 'SingleSignOnService');
    var signOff = aadutils.getElement(idp[0], 'SingleLogoutService');
    var keyDescriptor = aadutils.getElement(idp[0], 'KeyDescriptor');
    this.saml.loginEndpoint = signOn[0].$.Location;
    this.saml.logoutEndpoint = signOff[0].$.Location;

    // copy the x509 certs from the metadata
    this.saml.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.saml.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid SAMLP Federation Metadata ' + e.message));
  }
};

Metadata.prototype.updateOidcMetadata = function(doc, next) {
  log.info('Request to update the Open ID Connect Metadata');
  try {
    this.oidc = {};

    var issuer = doc['issuer'];
    var keyDescriptor = aadutils.getElement(idp[0], 'keys');

    // copy the x509 certs from the metadata
    this.oidc.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.oidc.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid Open ID Connect Federation Metadata ' + e.message));
  }
};


Metadata.prototype.updateWsfedMetadata = function(doc, next) {
  log.info('Request to update the WS Federation Metadata');
  try {
    this.wsfed = {};
    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var roles = aadutils.getElement(entity, 'RoleDescriptor');
    for(var i = 0; i < roles.length; i++) {
      var role = roles[i];
      if(role['fed:SecurityTokenServiceEndpoint']) {
        var endpoint = role['fed:SecurityTokenServiceEndpoint'];
        var endPointReference = aadutils.getFirstElement(endpoint[0],'EndpointReference');
        this.wsfed.loginEndpoint = aadutils.getFirstElement(endPointReference,'Address');

        var keyDescriptor = aadutils.getElement(role, 'KeyDescriptor');
        // copy the x509 certs from the metadata
        this.wsfed.certs = [];
        for (var j=0;j<keyDescriptor.length;j++) {
          this.wsfed.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
        }
        break;
      }
    }

    return next(null);
  } catch (e) {
    next(new Error('Invalid WSFED Federation Metadata ' + e.message));
  }
};

Metadata.prototype.fetch = function(callback) {
  var self = this;
  log.info("Fetching metadata from the provided metadata URL: " + self.url);
  async.waterfall([
    // fetch the Federation metadata for the AAD tenant
    function(next){
      request(self.url, function (err, response, body) {
        if(err) {
          next(err);
        } else if(response.statusCode !== 200) {
          next(new Error("Error:" + response.statusCode +  " Cannot get AAD Federation metadata from " + self.url));
        } else {
          log.info(body, "retreived");
          next(null, body);
        }
      });
    },
    function(body, next){
      // parse the AAD Federation metadata xml

      if(self.authtype == "saml" || self.authtype == "wsfed") {
      log.info(body, "Parsing XML retreived from the endpoint");
      var parser = new xml2js.Parser({explicitRoot:true});
      // Note: xml responses from Azure AAD have a leading \ufeff which breaks xml2js parser!
      parser.parseString(body.replace("\ufeff", ""), function (err, data) {
        self.metatdata = data;
        next(err);

      });
    } else if(self.authtype == "oidc") {
      log.info(body, "Parsing JSON retreived from the endpoint");
      JSON.parse(body, function (err, data) {
        self.metatdata = data;
        next(err);
      });

    } else {

       next(new Error("Error: No Authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"));
    }

    },

    function(next){
      if(self.authtype = "saml") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateSamlMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "wsfed") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateWsfedMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "oidc") {
      self.updateOidcMetadata(self.metadata, next);
    }},
  ], function (err) {
    // return err or success (err === null) to callback
    callback(err);
  });
};

exports.Metadata = Metadata;
```
Como você pode ver no código, ele simplesmente toma a URL openid passada por você em `config.js` e analisa-a em busca de informações que utilizaremos no arquivo `server.js`. Você está mais de bem-vindo ao investigar esse código e expanda-o se necessário.

### Carregar o arquivo metadata.js em seu server.js

Precisamos dizer ao nosso servidor de onde obter os métodos que você recém-escreveu.

Na linha de comando, altere os diretórios para o diretório **azuread**, se você ainda não estiver nele:

`cd azuread`

Abra o arquivo `server.js` no nosso editor favorito e adicione as seguintes informações:

```Javascript
var metadata = require('./metadata);
```
Em seguida, adicione essa chamada ao final da seção `Configuration` para enviar o documento de metadados em nosso `config.js` para o analisador que acabamos de escrever:

```Javascript
this.aadutils = new var Metadata = require('./metadata').Metadata;
```

## Etapa 14: adicionar as informações de esquema e modelo do MongoDB usando Moongoose

Agora todos os essa preparação passará a compensar, enquanto agrupamos esses três arquivos juntos em um serviço de API REST.

Para este passo a passo usaremos MongoDB para armazenar nossas tarefas conforme discutido em ***Etapa 4***.

Se você se lembrar do arquivo `config.js` criado na ***Etapa 11***, chamamos nosso banco de dados de `tasklist`, já que era isso que colocamos no final da nossa URL de conexão mogoose_auth_local. Você não precisa criar esse banco de dados com antecedência no MongoDB, ele criará isso para nós na primeira execução do nosso aplicativo de servidor (supondo que ele ainda não exista).

Agora que dissemos ao servidor qual banco de dados MongoDB, gostaríamos de usar, precisamos escrever algum código adicional para criar o modelo e o esquema para as tarefas do nosso servidor.

#### Discussão do modelo

Nosso modelo de esquema é muito simples e você o expande conforme necessário.

NAME - o nome de quem está atribuído à tarefa. Uma ***cadeia de caracteres***

TASK - a própria tarefa. Uma ***cadeia de caracteres***

DATE - a data em que a tarefa deverá ser concluída. Um valor ***DATETIME*** (data e hora)

COMPLETED - Se a tarefa está concluída ou não. Um ***BOOLEANO***

#### Criando o esquema no código


Na linha de comando, altere os diretórios para o diretório **azuread**, se você ainda não estiver nele:

`cd azuread`

Abra o arquivo `server.js` no nosso editor favorito e adicione as seguintes informações abaixo da entrada de configuração:

```Javascript
/**
*
* Connect to MongoDB
*/

global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;  
```
Isso se conectar ao servidor MongoDB e devolver um objeto de esquema para nós.

#### Usando o esquema, criar nosso modelo no código

Abaixo do código escrito acima, adicione o código a seguir:

```Javascript
/**
/ Here we create a schema to store our tasks. Pretty simple schema for now.
*/

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

## Etapa 15: adicionar nossas rotas a nosso servidor de API REST de tarefa

Agora que temos um modelo de banco de dados com o qual trabalhar, vamos adicionar as rotas que usaremos para nosso servidor de API REST.

### Sobre rotas em Restify

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

#### Adicionar rotas padrão ao nosso servidor

Vamos agora adicionar rotas CRUD básicas de Create (criar), Retrieve (extrair), Update (atualizar) e Delete (excluir).

Na linha de comando, altere os diretórios para o diretório **azuread**, se você ainda não estiver nele:

`cd azuread`

Abra o arquivo `server.js` no nosso editor favorito e adicione as seguintes informações abaixo das entradas do banco de dados que você criou acima:

```Javascript

/**
 *
 * APIs
 */

function createTask(req, res, next) {

	// Resitify currently has a bug which doesn't allow you to set default headers
  	// This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
  var _task = new Task();

        if (!req.params.task) {
                req.log.warn('createTask: missing task');
                next(new MissingTaskError());
                return;
        }


  _task.owner = req.params.owner;
   _task.task = req.params.task;
   _task.date = new Date();

  _task.save(function (err) {
  	if (err) {
        req.log.warn(err, 'createTask: unable to save');
        next(err);
    } else {
    res.send(201, _task);

			}
  });

  return next();

}


/**
 * Deletes a Task by name
 */
function removeTask(req, res, next) {

        Task.remove( { task:req.params.task }, function (err) {
                if (err) {
                        req.log.warn(err,
                                     'removeTask: unable to delete %s',
                                     req.params.task);
                        next(err);
                } else {
                        res.send(204);
                        next();
                }
        });
}

/**
 * Deletes all Tasks. A wipe
 */
function removeAll(req, res, next) {
        Task.remove();
        res.send(204);
        return next();
}    });
}


/**
 *
 *
 *
 */
function getTask(req, res, next) {


        Task.find(req.params.name, function (err, data) {
                if (err) {
                        req.log.warn(err, 'get: unable to read %s', req.params.name);
                        next(err);
                        return;
                }

                res.json(data);
        });

        return next();
}


/**
 * Simple returns the list of TODOs that were loaded.
 *
 */

function listTasks(req, res, next) {
  // Resitify currently has a bug which doesn't allow you to set default headers
  // This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

  console.log("server getTasks");

  Task.find().limit(20).sort('date').exec(function (err,data) {

    if (err)
      return next(err);

    if (data.length > 0) {
            console.log(data);
        }

    if (!data.length) {
            console.log('there was a problem');
            console.log(err);
            console.log("There is no tasks in the database. Did you initalize the database as stated in the README?");
        }

    else {

        res.json(data);

        }
  });

  return next();
}
```

### Adicionar algum tratamento de erro para as rotas

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


function TaskExistsError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 409,
                restCode: 'TaskExists',
                message: name + ' already exists',
                constructorOpt: TaskExistsError
        });

        this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 404,
                restCode: 'TaskNotFound',
                message: name + ' was not found',
                constructorOpt: TaskNotFoundError
        });

        this.name = 'TaskNotFoundError';
}

util.inherits(TaskNotFoundError, restify.RestError);
```


## Etapa 16: crie seu servidor!

Temos nosso banco de dados definido, temos nossas rotas estabelecidas e a última coisa a fazer é adicionar nossa instância de servidor que gerenciará nossas chamadas.

Restify (e Express) tem muita personalização profunda que você pode fazer para um servidor de API REST, mas novamente usaremos a configuração mais básica para atender aos nossos objetivos.

```Javascript
/**
 * Our Server
 */


var server = restify.createServer({
        name: "Azure Active Directroy TODO Server",
    version: "1.0.0",
    formatters: {
        'application/json': function(req, res, body){
            if(req.params.callback){
                var callbackFunctionName = req.params.callback.replace(/[^A-Za-z0-9_\.]/g, '');
                return callbackFunctionName + "(" + JSON.stringify(body) + ");";
            } else {
                return JSON.stringify(body);
            }
        },
        'text/html': function(req, res, body){
            if (body instanceof Error)
                        return body.stack;

                      if (Buffer.isBuffer(body))
                        return body.toString('base64');

                return util.inspect(body);
        },
        'application/x-www-form-urlencoded': function(req, res, body){
            if (body instanceof Error) {
                    res.statusCode = body.statusCode || 500;
                    body = body.message;
            } else if (typeof (body) === 'object') {
                body = body.task || JSON.stringify(body);
            } else {
                body = body.toString();
            }

        res.setHeader('Content-Length', Buffer.byteLength(body));
        return (body);
        }
    }
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

        // This lets us push JSON to the REST API endpoint as well. Maps x: y as /name:value

        server.use(restify.bodyParser({ mapParams: false }));

        /// Now the real handlers. Here we just CRUD

        server.get('/tasks', listTasks);
        server.head('/tasks', listTasks);
        server.get('/tasks/:name', getTask);
        server.head('/tasks/:name', getTask);
        server.post('/tasks/:name/:task', createTask);
        server.del('/tasks/:name/:task', removeTask);
        server.del('/tasks/:name', removeTask);
        server.del('/tasks', removeAll, function respond(req, res, next) { res.send(204); next(); });


        // Register a default '/' handler

        server.get('/', function root(req, res, next) {
                var routes = [
                        'GET     /',
                        'POST    /tasks/:name/:task',
                        'GET     /tasks',
                        'PUT     /tasks/:name',
                        'GET     /tasks/:name',
                        'DELETE  /tasks/:name/:task'
                ];
                res.send(200, routes);
                next();
        });

  server.listen(serverPort, function() {

  var consoleMessage = '\n Azure Active Directory Tutorial'
  consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++'
  consoleMessage += '\n %s server is listening at %s';
  consoleMessage += '\n Open your browser to %s/tasks\n';
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n'
  consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n'
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n'  

  console.log(consoleMessage, server.name, server.url, server.url, server.url);

});
```

## Etapa 17: antes de adicionar suporte a OAuth, vamos executar o servidor.

É uma boa ideia verificar se que não temos erros antes de continuar para a parte OAuth do passo a passo.

A maneira mais fácil de fazer isso é usando `curl` em uma linha de comando. Antes de fazermos isso, precisamos de um utilitário simples que nos permita analisar a saída como JSON. Para fazer isso, instale a ferramenta [json](https://github.com/trentm/json), já que todos os exemplos abaixo a utilizam.

	$npm install -g jsontool

Isso instala a ferramenta JSON globalmente. Agora você conseguiu isso, vamos experimentar com o servidor:

Primeiro, certifique-se de que sua instância do monogoDB está em execução.

	$sudo mongod

Em seguida, vá até o diretório e inicie a ondulação.

	$ cd azuread
	$ node server.js

	$ curl -isS http://127.0.0.1:8888 | json
	HTTP/1.1 200 OK
	Connection: close
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 145
	Date: Wed, 29 Jan 2014 03:41:24 GMT

	[
  	"GET     /",
  	"POST    /tasks/:owner/:task",
  	"GET     /tasks",
  	"DELETE  /tasks",
  	"PUT     /tasks/:owner",
  	"GET     /tasks/:owner",
  	"DELETE  /tasks/:task"
	]

Então, podemos adicionar uma tarefa deste modo:

	$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello

A resposta deve ser:

	HTTP/1.1 201 Created
	Connection: close
	Access-Control-Allow-Origin: *
	Access-Control-Allow-Headers: X-Requested-With
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 5
	Date: Tue, 04 Feb 2014 01:02:26 GMT

	Hello

E podemos pode listar tarefas para Brandon deste modo:

	$ curl -isS http://127.0.0.1:8888/tasks/brandon/

Se tudo isso funcionar, estamos prontos para adicionar OAuth ao servidor de API REST.

## Etapa 18: adicionar código do Passport.js ao nosso servidor de API REST

Agora que temos uma API REST em execução (aliás, parabéns por isso!) Vamos torná-lo útil para uso com o Azure AD.

Na linha de comando, altere os diretórios para o diretório **azuread**, se você ainda não estiver nele:

`cd azuread`

### Etapa 1: adicionar nossos módulos do Passport

Abra o arquivo `server.js` no nosso editor favorito e adicione as seguintes informações abaixo de onde você estabeleceu anteriormente que os módulos deveriam ser carregados: Isso é em direção à parte superior do arquivo e deve ser logo após a importação de `var aadutils = require('./aadutils');`.

```Javascript
/*
*
* Load our old friend Passport for OAuth2 flows
*/

var passport = require('passport')
  , OAuth2Strategy = require('passport-oauth').OAuth2Strategy;
```

### 2\. Informar o nosso servidor que estamos usando autenticação

Abra seu arquivo `server.js` no nosso editor favorito e adicione as seguintes informações **abaixo de server.get()**, onde você definiu suas rotas, mas acima do método **server.listen()**.


Precisamos dizer ao Restify para começar a usar seu `authorizationParser()` e examinar o conteúdo do cabeçalho Autorização.

```Javascript
        server.use(restify.authorizationParser());


```


### 3\. Adicionar o módulo Passport OAuth2 ao nosso código

Aqui usamos os parâmetros específicos de OAuth2 que adicionamos ao arquivo config.js. Se nosso arquivo `aadutils.js` fez seu trabalho analisando nosso documento de metadados de federação, todos esses valores devem estar preenchidos para nós mesmo que estejam em branco no arquivo config.js.

```Javascript
// Now our own handlers for authentication/authorization
// Here we only use Oauth2 from Passport.js

passport.use('provider', new OAuth2Strategy({
    authorizationURL: authEndpoint,
    tokenURL: tokenEndpoint,
    clientID: clientID,
    clientSecret: clientSecret,
    callbackURL: callbackURL
  },
  function(accessToken, refreshToken, profile, done) {
    User.findOrCreate({ UserId: profile.id }, function(err, user) {
      done(err, user);
    });
  }
));

// Let's start using Passport.js

server.use(passport.initialize());

```
### Etapa 4: adicionar rotas para autenticação OAuth

```Javascript
// Redirect the user to the OAuth 2.0 provider for authentication.  When
// complete, the provider will redirect the user back to the application at
//     /auth/provider/callback

server.get('/auth/provider', passport.authenticate('provider'));

// The OAuth 2.0 provider has redirected the user back to the application.
// Finish the authentication process by attempting to obtain an access
// token.  If authorization was granted, the user will be logged in.
// Otherwise, authentication has failed.

server.get('/auth/provider/callback',
  passport.authenticate('provider', { successRedirect: '/',
                                      failureRedirect: '/login' }));
```

### Etapa 5: adicionar um método auxiliar IsAuthenticated() às rotas

```Javascript
// Simple route middleware to ensure user is authenticated.
//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.

var ensureAuthenticated = function(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }
  res.redirect('/login');
};

```

### Etapa 6: adicionar um mecanismo de caching para os cookies

```Javascript
// Passport session setup.
//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});
```
### Etapa 7: por fim, proteger alguns pontos de extremidade

Você pode proteger pontos de extremidade, especificando a chamada passport.authenticate() com o protocolo que você deseja usar.

Vamos editar nosso roteiro em nosso código de servidor para fazer algo mais interessante:

```Javascript
server.get('/tasks', passport.authenticate('provider', { session: false }), listTasks);
```


## Etapa 19: executar o aplicativo de servidor novamente e certificar-se de que rejeitará você

Vamos usar `curl` novamente para ver se agora temos proteção OAuth2 para nossos pontos de extremidade. Faremos isso antes de executar qualquer um dos nossos SDKs de cliente para esse ponto de extremidade. Os cabeçalhos retornados devem ser suficientes para demonstrar a nós que estamos no caminho certo.

Primeiro, certifique-se de que sua instância do monogoDB está em execução.

	$sudo mongod

Em seguida, vá até o diretório e inicie a ondulação.

	$ cd azuread
	$ node server.js

Tente um comando GET básico:

	$ curl -isS http://127.0.0.1:8888/tasks/
	HTTP/1.1 302 Moved Temporarily
	Connection: close
	Location: https://login.windows.net/468a75f4-f9a7-4dc4-a527-4f4522734790/oauth2/authorize?response_type=code&redirect_uri=&client_id=123
	Content-Length: 0
	Date: Tue, 04 Feb 2014 02:15:14 GMT


Uma 302 é a resposta que você está procurando, já que indica que a camada do Passport está tentando redirecionar-se para o ponto de extremidade de autorização, que é exatamente o que você deseja.

## Parabéns! Você tem um serviço da API REST usando OAuth2!

Você já fez tudo que era possível com esse servidor sem usar um cliente compatível com OAuth2. Você precisará passar por um passo a passo adicional.

Se você estava apenas procurando obter informações sobre como implementar uma API REST usando Restify e OAuth2, você tem código mais do que suficiente para manter o desenvolvimento de seu serviço e aprender como compilar neste exemplo.

Se você estiver interessado nas próximas etapas na sua jornada ADAL, aqui estão alguns clientes ADAL suportados que recomendamos para que você continue trabalhando:

Simplesmente faça a clonagem para sua máquina de desenvolvedor e configure conforme indicado no passo a passo.

[ADAL para iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL para Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[ADAL para .Net](http://msdn.microsoft.com/library/windowsazure/jj573266.aspx)
 

<!---HONumber=July15_HO3-->