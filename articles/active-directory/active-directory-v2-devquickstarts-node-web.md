<properties
	pageTitle="Modelo de aplicativo v2.0 | Microsoft Azure"
	description="Como criar um aplicativo Web do Node JS que conecte usuários com a conta pessoal da Microsoft e as contas corporativas ou de estudante."
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
	ms.date="08/25/2015"
	ms.author="brandwe"/>

# Visualização do modelo de aplicativo v2.0: adicionar conexão a um aplicativo Web do nodeJS


  >[AZURE.NOTE]Essas informações se aplicam à visualização pública do modelo de aplicativo v2.0. Para obter instruções sobre como integrar-se ao serviço do AD do Azure disponível ao público geral, consulte o [Guia do Desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md).


Aqui usaremos o Passport para:

- Conectar o usuário ao aplicativo usando o AD do Azure e o modelo de aplicativo v2.0.
- Exibir algumas informações sobre o usuário.
- Desconectar o usuário do aplicativo.

**Passport** é middleware de autenticação para o Node.js. Extremamente flexível e modular, o Passport pode ser colocado sem impedimento em qualquer aplicativo Web baseado em Express ou Restify. Um conjunto abrangente de estratégias suportam a autenticação usando um nome de usuário e senha, Facebook, Twitter e mais. Desenvolvemos uma estratégia para o Active Directory do Microsoft Azure. Instalaremos esse módulo e, em seguida, adicionaremos o plug-in `passport-azure-ad` do Active Directory do Microsoft Azure.

Para isso, você precisará:

1. Registrar um aplicativo
2. Configurar seu aplicativo para usar a estratégia de Passport-azure-ad.
3. Usar o Passport para emitir solicitações de entrada e saída ao AD do Azure.
4. Imprimir dados sobre o usuário.

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs). Para acompanhar, você pode [baixar o esqueleto do aplicativo como um .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) ou clonar o esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

The completed application is provided at the end of this tutorial as well.

## 1. Register an App
Create a new app at [apps.dev.microsoft.com](https://apps.dev.microsoft.com), or follow these [detailed steps](active-directory-v2-app-registration.md).  Make sure to:

- Copy down the **Application Id** assigned to your app, you'll need it soon.
- Add the **Web** platform for your app.
- Enter the correct **Redirect URI**. The redirect URI indicates to Azure AD where authentication responses should be directed - the default for this tutorial is `http://localhost:3000/auth/openid/return`.

## 2. Add pre-requisities to your directory

From the command-line, change directories to your root folder if not already there and run the following commands:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`

- In addition, we've included a special `passport-azure-ad` for our Preview in the skeleton of the quickstart. You'll need to install the pre-requisites in there as well.

- `cd /lib/passport-azure-ad`
- `npm install`

This will install the libraries that passport-azure-ad depend on.

## 3. Set up your app to use the passport-node-js strategy
Here, we'll configure the Express middleware to use the OpenID Connect authentication protocol.  Passport will be used to issue sign-in and sign-out requests, manage the user's session, and get information about the user, amongst other things.

-	To begin, open the `config.js` file in the root of the project, and enter your app's configuration values in the `exports.creds` section.
    -	The `clientID:` is the **Application Id** assigned to your app in the registration portal.
    -	The `returnURL` is the **Redirect URI** you entered in the portal.
    - The `clientSecret` is the secret you generated in the portal
    - The `realm` is the **Redirect URI** you entered in the portal without the route. (example: http//localhost:3000)
    - The `issuer` is where you append the **Application Id** after, as an example: https://sts.windows.net/96702724-991f-4576-bc90-be9862749ac5/

- Next open `app.js` file in the root of the proejct and add the follwing call to invoke the `OIDStrategy` strategy that comes with `passport-azure-ad`


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy; ```

- Depois disso, use a estratégia que referenciamos para manipular nossas solicitações de logon

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 

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
    skipUserProfile: true // doesn't fetch user profile
  },
  function(iss, sub, email, claims, profile, accessToken, refreshToken, done) {
    log.info('We received claims of: ', claims);
    log.info('Example: Email address we received was: ', claims.preferred_username);
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(claims.preferred_username, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(claims);
          return done(null, claims);
        }
        return done(null, user);
      });
    });
  }
));
```
O Passport usa um padrão semelhante para todas as Estratégias (Twitter, Facebook etc.) que todos os gravadores de Estratégia seguem. Observando a estratégia, você verá que passamos a ela uma function() que tem um token e um done como parâmetros. A estratégia retorna corretamente para nós após concluir seu trabalho. Depois disso, vamos armazenar o usuário e acrescentar o token, para que não precisemos pedi-lo novamente.

> [AZURE.IMPORTANT]O código acima usa qualquer usuário que tente se autenticar em nosso servidor. Isso é conhecido como registro automático. Em servidores de produção, não convém permitir que qualquer pessoa entre sem primeiro passar por um processo de registro que você decide. Esse geralmente é o padrão que você vê em aplicativos de consumidor que lhe permitem registrar-se com o Facebook, mas depois pedem que você preencha informações adicionais. Se esse não fosse um aplicativo de exemplo, poderíamos ter apenas extraído o email do objeto de token que é retornado e pedido que ele preenchesse informações adicionais. Como esse é um servidor de teste, basta adicioná-los ao banco de dados na memória.

- Em seguida, vamos adicionar os métodos que nos permitirão acompanhar os usuários conectados conforme exigido pelo Passport. Isso inclui a serialização e a desserialização de informações do usuário:

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.preferred_username);
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
    if (user.preferred_username === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};

```

- Em seguida, vamos adicionar o código para carregar o mecanismo express. Aqui você vê que usamos o /views e o /routes padrão que o Express fornece.

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

- Por fim, vamos adicionar as rotas POST que entregarão as solicitações de logon reais ao mecanismo `passport-azure-ad`:

```JavaScript

// Our POST routes (Section 3)

// POST /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return
app.post('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
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

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});
```

## 4\. Usar o Passport para emitir solicitações de entrada e saída ao AD do Azure

Seu aplicativo agora está configurado corretamente para se comunicar com o ponto de extremidade v 2.0 usando o protocolo de autenticação OpenID Connect. O `passport-azure-ad` cuidou de todos os detalhes para criar as mensagens de autenticação, validando tokens do AD do Azure e mantendo a sessão do usuário. Tudo o que resta é dar aos usuários uma maneira de entrar, sair e coletar informações adicionais sobre o usuário conectado.

- Primeiro, vamos adicionar os métodos padrão de logon, conta e logoff ao arquivo `app.js`:

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

-	Vamos examiná-los detalhadamente:
    -	A rota `/` será redirecionada para o modo de exibição de index.ejs passando o usuário na solicitação (se houver)
    - A rota `/account` primeiro ***garantirá que sejamos autenticados*** (implementamos isso abaixo) e, em seguida, passará o usuário na solicitação de modo que possamos obter informações adicionais sobre o usuário.
    - A rota `/login` chamará nosso autenticador azuread-openidconnect de `passport-azuread` e, se não tiver êxito, redirecionará o usuário para /login
    - O `/logout` simplesmente chamará logout.ejs (e a rota), o que limpa os cookies e, em seguida, retorna o usuário para index. ejs


- Para a última parte do `app.js`, vamos adicionar o método EnsureAuthenticated que é usado em `/account` acima.

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

- Por fim, vamos realmente criar o próprio servidor em `app.js`:

```JavaScript

app.listen(3000);

```


## 5\. Criar as exibições e as rotas no express para exibir o usuário no site

Temos nosso `app.js` completo. Agora, basta adicionar rotas e modos de exibição que mostrarão as informações de que precisamos do usuário, bem como lidar com as rotas `/logout` e `/login` que criamos.

- Crie a rota `/routes/index.js` sob o diretório raiz.

```JavaScript
/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- Criar a rota `/routes/user.js` sob o diretório raiz

```JavaScript
/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Essas rotas simples apenas passarão a solicitação para nossos modos de exibição, incluindo o usuário, se presente.

- Crie a exibição `/views/index.ejs` sob o diretório raiz. Essa é uma página simples que nos permitirá capturar os métodos de logon e logoff e obter informações de conta. Observe que podemos usar o `if (!user)` condicional , pois o usuário que está sendo passado na solicitação é uma prova de que temos um usuário conectado.

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
	<h2>Hello, <%= user.name %>.</h2>
	<a href="/account">Account Info</a></br>
	<a href="/logout">Log Out</a>
<% } %>
```

- Crie a exibição `/views/account.ejs` sob o diretório raiz, de modo que possamos exibir informações adicionais que `passport-azuread` colocou na solicitação do usuário.

```Javascript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.name %></p>
<p>Profile ID: <%= user.sub %></p>
<a href="/logout">Log Out</a>
<% } %>
```

- Por fim, vamos melhorar a aparência adicionando um layout. Crie a exibição '/views/layout.ejs' sob o diretório raiz

```JavaScript

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


Entre com uma conta pessoal da Microsoft ou uma conta corporativa ou de estudante e observe como a identidade do usuário é exibida na lista /account. Agora você tem um aplicativo Web protegido por protocolos padrão do setor, que podem autenticar usuários com as respectivas contas pessoais e corporativas ou de estudante.

##Próximas etapas

Para referência, o exemplo concluído (sem seus valores de configuração) [é fornecido como um .zip aqui](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Agora você pode ir para tópicos mais avançados. Você pode desejar experimentar:

[Proteger uma API da Web com o modelo de aplicativo v 2.0 no node.js >>](active-directory-v2-devquickstarts-webapi-nodejs.md)

Para obter recursos adicionais, confira: - [A Visualização do Modelo de Aplicativo v2.0 >>](active-directory-appmodel-v2-overview.md) - [StackOverflow "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

<!---HONumber=August15_HO9-->