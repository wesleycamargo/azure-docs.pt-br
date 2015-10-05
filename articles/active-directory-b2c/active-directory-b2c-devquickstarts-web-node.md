<properties
	pageTitle="Adicionar conexão a um aplicativo Web do nodeJS para visualização do B2C do Azure |Microsoft Azure"
	description="Como criar um aplicativo Web do Node.js que conecta os usuários com um locatário do B2C."
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
	ms.date="09/22/2015"
	ms.author="brandwe"/>

# Visualização do B2C: adicionar conexão a um aplicativo Web do nodeJS


[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]Este artigo não aborda como implementar conexão, registro e gerenciamento de perfil com o Azure AD B2C. Ele se concentra na chamada a APIs Web depois que o usuário já está autenticado. Se não estiver, você deve começar com o [tutorial de introdução ao aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para saber mais sobre os conceitos básicos do Azure AD B2C.

**Passport** é middleware de autenticação para o Node.js. Extremamente flexível e modular, o Passport pode ser colocado sem impedimento em qualquer aplicativo Web baseado em Express ou Restify. Um conjunto abrangente de estratégias suportam a autenticação usando um nome de usuário e senha, Facebook, Twitter e mais. Desenvolvemos uma estratégia para o Active Directory do Microsoft Azure. Instalaremos esse módulo e, em seguida, adicionaremos o plug-in `passport-azure-ad` do Active Directory do Microsoft Azure.

Para isso, você precisará:

1. Registrar um aplicativo com o Active Directory do Azure
2. Configure seu aplicativo para usar o plug-in azure-ad-passport do Passport.
3. Usar o Passport para emitir solicitações de entrada e saída ao AD do Azure.
4. Imprimir dados sobre o usuário.

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS). Para acompanhar, você pode [baixar o esqueleto do aplicativo como um .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) ou clonar o esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

The completed application is provided at the end of this tutorial as well.

> [AZURE.WARNING] 	For our B2C Preview you must use the same client ID/Application ID and policies for both the Web-API task server and the client that connects to it. This is true for our iOS and Android tutorials. If you have previously created an application in either of those quickstarts, please use those values instead of creating new ones below.

## 1. Get an Azure AD B2C directory

Before you can use Azure AD B2C, you must create a directory, or tenant.  A directory is a container for all your users, apps, groups, and so on.  If you don't have
one already, go [create a B2C directory](active-directory-b2c-get-started.md) before moving on.

## 2. Create an application

Now you need to create an app in your B2C directory, which gives Azure AD some information that it needs to securely communicate with your app.  Both the client app and web API will be represented by a single **Application ID** in this case, since they comprise one logical app.  To create an app,
follow [these instructions](active-directory-b2c-app-registration.md).  Be sure to

- Include a **web app/web api** in the application
- Enter `http://localhost/TodoListService` as a **Reply URL** - it is the default URL for this code sample.
- Create an **Application Secret** for your application and copy it down.  You will need it shortly.
- Copy down the **Application ID** that is assigned to your app.  You will also need it shortly.

    > [AZURE.IMPORTANT]
    You cannot use applications registered in the **Applications** tab on the [Azure Portal](https://manage.windowsazure.com/) for this.

## 3. Create your policies

In Azure AD B2C, every user experience is defined by a [**policy**](active-directory-b2c-reference-policies.md).  This app contains three 
identity experiences - sign-up, sign-in, and sign-in with Facebook.  You will need to create one policy of each type, as described in the 
[policy reference article](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).  When creating your three policies, be sure to:

- Choose the **Display Name** and a few other sign-up attributes in your sign-up policy.
- Choose the **Display Name** and **Object ID** application claims in every policy.  You can choose other claims as well.
- Copy down the **Name** of each policy after you create it.  It should have the prefix `b2c_1_`.  You'll need those policy names shortly. 

Once you have your three policies successfully created, you're ready to build your app.

Note that this article does not cover how to use the policies you just created.  If you want to learn about how policies work in Azure AD B2C,
you should start with the [.NET Web App getting started tutorial](active-directory-b2c-devquickstarts-web-dotnet.md).



## 4. Add pre-requisities to your directory

From the command-line, change directories to your root folder if not already there and run the following commands:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

- In addition, we've use `passport-azure-ad` for our Preview in the skeleton of the quickstart. 

- `npm install passport-azure-ad`


This will install the libraries that passport-azure-ad depend on.

## 5. Set up your app to use the passport-node-js strategy
Here, we'll configure the Express middleware to use the OpenID Connect authentication protocol.  Passport will be used to issue sign-in and sign-out requests, manage the user's session, and get information about the user, amongst other things.

-	To begin, open the `config.js` file in the root of the project, and enter your app's configuration values in the `exports.creds` section.
    -	The `clientID:` is the **Application Id** assigned to your app in the registration portal.
    -	The `returnURL` is the **Redirect URI** you entered in the portal.
    - The `tenantName:` is the **tenant name** of your app, e.g. contoso.onmicrosoft.com


- Next open `app.js` file in the root of the proejct and add the follwing call to invoke the `OIDCStrategy` strategy that comes with `passport-azure-ad`


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Adicione um registro de log var log = bunyan.createLogger({ name: 'Microsoft OIDC Example Web Application' }); ```

- Depois disso, use a estratégia que referenciamos para manipular nossas solicitações de logon

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
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
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

> [AZURE.IMPORTANT]O código acima usa qualquer usuário que tente se autenticar em nosso servidor. Isso é conhecido como registro automático. Em servidores de produção, não convém permitir que qualquer pessoa entre sem primeiro passar por um processo de registro que você decide. Esse geralmente é o padrão que você vê em aplicativos de consumidor que lhe permitem registrar-se com o Facebook, mas depois pedem que você preencha informações adicionais. Se esse não fosse um aplicativo de exemplo, poderíamos ter apenas extraído o email do objeto de token que é retornado e pedido que ele preenchesse informações adicionais. Como esse é um servidor de teste, basta adicioná-los ao banco de dados na memória.

- Em seguida, vamos adicionar os métodos que nos permitirão acompanhar os usuários conectados conforme exigido pelo Passport. Isso inclui a serialização e a desserialização de informações do usuário:

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
    
    res.redirect('/');
  });
```

## 4\. Usar o Passport para emitir solicitações de entrada e saída ao AD do Azure

Seu aplicativo agora está configurado corretamente para se comunicar com o ponto de extremidade v2.0 usando o protocolo de autenticação OpenID Connect. O `passport-azure-ad` cuidou de todos os detalhes para criar as mensagens de autenticação, validar tokens no Azure AD e manter a sessão do usuário. Tudo o que resta é dar aos usuários uma maneira de entrar, sair e coletar informações adicionais sobre o usuário conectado.

- Primeiramente, vamos adicionar os métodos padrão de logon, conta e logoff ao arquivo `app.js`:

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
    -	A rota `/` redirecionará para o modo de exibição index.ejs passando o usuário na solicitação (se houver)
    - A rota `/account` primeiro ***garantirá que sejamos autenticados*** (implementamos isso abaixo) e passará o usuário na solicitação para que possamos obter informações adicionais sobre ele.
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

- Por fim, vamos realmente criar o servidor em `app.js`:

```JavaScript

app.listen(3000);

```


## 5\. Criar estes modos de exibição e rotas no Express para chamar nossas políticas

Temos nosso `app.js` concluído. Agora, precisamos só adicionar rotas e modos de exibição que nos permitirão chamar as políticas de conexão e registro, bem como tratar das rotas `/logout` e `/login` que criamos.

- Crie a rota `/routes/index.js` no diretório raiz.

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- Crie a rota `/routes/user.js` no diretório raiz

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Essas rotas simples apenas passarão a solicitação para nossos modos de exibição, incluindo o usuário, se presente.

- Crie o modo de exibição `/views/index.ejs` no diretório raiz. Este é um exemplo de página que chamará nossas políticas para entrada e saída que nos permitirá obter informações da conta. Observe que podemos usar o `if (!user)` condicional, pois o usuário que está sendo passado na solicitação é uma prova de que temos um usuário conectado.

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login/?p=your facebook policy">Sign In with Facebook</a>
	<a href="/login/?p=your email sign-in policy">Sign In With Email</a>
	<a href="/login/?p=your email sign-up policy">Sign Up With Email</a>
<% } else { %>
	<h2>Hello, <%= user.displayName %>.</h2>
	<a href="/account">Account Info</a></br>
	<a href="/logout">Log Out</a>
<% } %>
```

- Crie o modo de exibição `/views/account.ejs` no diretório raiz para que possamos exibir as informações adicionais que `passport-azuread` colocou na solicitação do usuário.

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

Por fim, compile e execute seu aplicativo!

Execute `node app.js` e navegue até `http://localhost:3000`


Registre-se ou entre no aplicativo com o email ou Facebook. Saia e faça logon novamente como outro usuário.



##Próximas etapas

Para referência, o exemplo concluído (sem seus valores de configuração) [é fornecido como um .zip aqui](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

Agora você pode ir para tópicos mais avançados. Você pode desejar experimentar:

[Proteger uma API Web com o modelo B2C no node.js >>](active-directory-b2c-devquickstarts-webapi-nodejs.md)

<!--

For additional resources, check out:
You can now move onto more advanced B2C topics.  You may want to try:

[Calling a node.js Web API from a node.js Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Sept15_HO4-->