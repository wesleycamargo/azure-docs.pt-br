---
title: "Introdução à entrada e saída no AD do Azure usando node.js"
description: Como criar um aplicativo Web MVC node.js Express que se integra ao AD do Azure para entrar.
services: active-directory
documentationcenter: nodejs
author: xerners
manager: mbaldwin
editor: 
ms.assetid: 81deecec-dbe2-4e75-8bc0-cf3788645f99
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 08/15/2016
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 5d2c5bf3c1d370329603ff55cf9f32367e29368a


---
# <a name="nodejs-web-app-sign-in--sign-out-with-azure-ad"></a>Entrar e sair do aplicativo Web NodeJS com o Azure AD
Aqui usaremos o Passport para:

* Entrada do usuário no aplicativo usando o Azure AD.
* Exibir algumas informações sobre o usuário.
* Desconectar o usuário do aplicativo.

**Passport** é middleware de autenticação para o Node.js. Extremamente flexível e modular, o Passport pode ser colocado sem impedimento em qualquer aplicativo Web baseado em Express ou Restify. Um conjunto abrangente de estratégias suportam a autenticação usando um nome de usuário e senha, Facebook, Twitter e mais. Desenvolvemos uma estratégia para o Active Directory do Microsoft Azure. Instalaremos esse módulo e, em seguida, adicionaremos o plug-in `passport-azure-ad` do Active Directory do Microsoft Azure.

Para isso, você precisará:

1. Registrar um aplicativo
2. Configurar seu aplicativo para usar a estratégia de Passport-azure-ad.
3. Usar o Passport para emitir solicitações de entrada e saída ao AD do Azure.
4. Imprimir dados sobre o usuário.

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS).  Para acompanhar, você pode [baixar o esqueleto do aplicativo como um .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) ou clonar o esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

O aplicativo completo também é fornecido no final deste tutorial.

## <a name="1-register-an-app"></a>1. Registrar um Aplicativo
* Faça logon no [Portal do Azure](https://portal.azure.com).
* Selecione o locatário em que você deseja registrar o aplicativo.
* Clique em **Azure Active Directory** no painel de navegação à esquerda.
* Clique na guia **Registros do Aplicativo** e clique em **Adicionar**.
* Siga os prompts e crie um novo **Aplicativo Web e/ou WebAPI**.
  * O **nome** do aplicativo descreverá seu aplicativo para os usuários finais
  * A **URL de logon** é a URL base do seu aplicativo.  O padrão do esqueleto é `http://localhost:3000/auth/openid/return``.
* Depois de concluir o registro, o AAD atribuirá a seu aplicativo uma ID do Aplicativo única.  Você precisará desse valor nas próximas seções, então copie-o da página do aplicativo.

## <a name="2-add-pre-requisities-to-your-directory"></a>2. Adicionar pré-requisitos ao seu diretório
Na linha de comando, altere o diretório para a pasta raiz se ainda não estiver lá e execute os seguintes comandos:

* `npm install express`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install restify`
* `npm install mongoose`
* `npm install bunyan`
* `npm install assert-plus`
* `npm install passport`
* Além disso, você também precisará do nosso `passport-azure-ad` :
* `npm install passport-azure-ad`

Isso instalará as bibliotecas das quais o passport-azure-ad depende.

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3. Configurar seu aplicativo para usar a estratégia passport-node-js
Aqui, configuraremos o middleware Express para usar o protocolo de autenticação OpenID Connect.  O Passport será usado para emitir solicitações de entrada e saída, gerenciar a sessão do usuário e obter informações sobre o usuário, entre outras coisas.

* Para começar, abra o arquivo `config.js` na raiz do projeto e insira os valores de configuração do aplicativo na seção `exports.creds`.

  * `clientID:` é a **ID do Aplicativo** atribuída ao seu aplicativo no portal de registro.
  * O `returnURL` é o **URI de Redirecionamento** inserido no portal.
  * O `clientSecret` é o segredo gerado no portal
* Em seguida, abra o arquivo `app.js` na raiz do projeto e adicione a seguinte chamada para invocar a estratégia `OIDCStrategy` que vem com `passport-azure-ad`

```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// add a logger

var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

* Depois disso, use a estratégia que referenciamos para manipular nossas solicitações de logon

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2)
//
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    if (!profile.email) {
      return done(new Error("No email found"), null);
    }
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
O Passport usa um padrão semelhante para todas as Estratégias (Twitter, Facebook etc.) que todos os gravadores de Estratégia seguem. Observando a estratégia, você verá que passamos a ela uma function() que tem um token e um done como parâmetros. A estratégia retorna corretamente para nós após concluir seu trabalho. Depois disso, vamos armazenar o usuário e acrescentar o token, para que não precisemos pedi-lo novamente.

> [!IMPORTANT]
> O código acima usa qualquer usuário que tente se autenticar em nosso servidor. Isso é conhecido como registro automático. Em servidores de produção, não convém permitir que qualquer pessoa entre sem primeiro passar por um processo de registro que você decide. Esse geralmente é o padrão que você vê em aplicativos de consumidor que lhe permitem registrar-se com o Facebook, mas depois pedem que você preencha informações adicionais. Se esse não fosse um aplicativo de exemplo, poderíamos ter apenas extraído o email do objeto de token que é retornado e pedido que ele preenchesse informações adicionais. Como esse é um servidor de teste, basta adicioná-los ao banco de dados na memória.
>
>

* Em seguida, vamos adicionar os métodos que nos permitirão acompanhar os usuários conectados conforme exigido pelo Passport. Isso inclui a serialização e a desserialização de informações do usuário:

```JavaScript

// Passport session setup. (Section 2)

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

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
   log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};
```

* Em seguida, vamos adicionar o código para carregar o mecanismo express. Aqui você vê que usamos o /views e o /routes padrão que o Express fornece.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

* Por fim, vamos adicionar as rotas que entregarão as solicitações de logon reais ao mecanismo `passport-azure-ad`:

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return
app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });
  ```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4. Usar o Passport para emitir solicitações de entrada e saída ao AD do Azure
Seu aplicativo agora está configurado corretamente para se comunicar com o ponto de extremidade v2.0 usando o protocolo de autenticação OpenID Connect.  `passport-azure-ad` cuidou de todos os detalhes para criar as mensagens de autenticação, validar tokens no Azure AD e manter a sessão do usuário.  Tudo o que resta é dar aos usuários uma maneira de entrar, sair e coletar informações adicionais sobre o usuário conectado.

* Primeiramente, vamos adicionar os métodos padrão de logon, conta e logoff ao arquivo `app.js` :

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

* Vamos examiná-los detalhadamente:

  * A rota `/` será redirecionada para o modo de exibição de index.ejs passando o usuário na solicitação (se houver)
  * A rota `/account` primeiro ***garantirá que sejamos autenticados*** (implementamos isso abaixo) e aprovará o usuário na solicitação para que possamos obter informações adicionais sobre ele.
  * A rota `/login` chamará nosso autenticador azuread-openidconnect de `passport-azuread` e, se não tiver êxito, redirecionará o usuário para /login
  * O `/logout` simplesmente chamará logout.ejs (e a rota), o que limpa os cookies e, em seguida, retorna o usuário para index. ejs
* Para a última parte do `app.js`, vamos adicionar o método EnsureAuthenticated que é usado em `/account` acima.

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}
```

* Por fim, vamos realmente criar o servidor em `app.js`:

```JavaScript

app.listen(3000);

```


## <a name="5-create-the-views-and-routes-in-express-to-display-our-user-in-the-website"></a>5. Criar as exibições e as rotas no express para exibir o usuário no site
Temos nosso `app.js` concluído. Agora, basta adicionar rotas e modos de exibição que mostram as informações que precisamos do usuário e lidar com as rotas `/logout` e `/login` criadas.

* Criar a rota `/routes/index.js` no diretório raiz.

```JavaScript
/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

* Crie a rota `/routes/user.js` no diretório raiz

```JavaScript
/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Essas rotas simples apenas passarão a solicitação para nossos modos de exibição, incluindo o usuário, se presente.

* Crie o modo de exibição `/views/index.ejs` no diretório raiz. Essa é uma página simples que chama os nossos métodos de logon e logoff e nos permite obter informações de conta. Observe que podemos usar o `if (!user)` condicional, pois o usuário que está sendo passado na solicitação é uma prova de que temos um usuário conectado.

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account Info</a></br>
    <a href="/logout">Log Out</a>
<% } %>
```

* Crie a exibição `/views/account.ejs` no diretório raiz para que possamos exibir as informações adicionais que `passport-azuread` colocou na solicitação do usuário.

```Javascript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

* Por fim, vamos melhorar a aparência adicionando um layout. Crie a exibição '/views/layout.ejs' sob o diretório raiz

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>Passport-OpenID Example</title>
    </head>
    <body>
        <% if (!user) { %>
            <p>
            <a href="/">Home</a> |
            <a href="/login">Log In</a>
            </p>
        <% } else { %>
            <p>
            <a href="/">Home</a> |
            <a href="/account">Account</a> |
            <a href="/logout">Log Out</a>
            </p>
        <% } %>
        <%- body %>
    </body>
</html>
```

Por fim, compile e execute seu aplicativo!

Execute `node app.js` e navegue até `http://localhost:3000`

Entre com uma conta pessoal da Microsoft ou uma conta corporativa ou de estudante e observe como a identidade do usuário é exibida na lista /account.  Agora você tem um aplicativo Web protegido por protocolos padrão do setor, que podem autenticar usuários com as respectivas contas pessoais e corporativas ou de estudante.

Para referência, o exemplo concluído (sem seus valores de configuração) [é fornecido como um .zip aqui](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Agora você pode ir para tópicos mais avançados.  Você pode desejar experimentar:

[Proteger uma API Web com o Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]



<!--HONumber=Dec16_HO5-->


