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

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git
```

O aplicativo completo também é fornecido no final deste tutorial.

> [AZURE.WARNING] 	Para Visualização do B2C, você deve usar a mesma ID do Aplicativo/ID do cliente e políticas tanto para o servidor de tarefa API Web quanto para o cliente que se conecta a ele. Isso é verdadeiro para nossos tutoriais do Android e iOS. Se você tiver criado anteriormente um aplicativo em qualquer um desses guias de início rápido, use esses valores em vez de criar novos abaixo.

## 1. Obter um diretório AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e assim por diante.
Se você não tiver um, acesse [criar um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir.

## 2. Criar um aplicativo

Agora você precisa criar um aplicativo no diretório B2C, que dá ao AD do Azure algumas informações que ele precisa para se comunicar de forma segura com seu aplicativo. O aplicativo cliente e a API Web serão representados por uma única **ID do Aplicativo** nesse caso, pois abrangem um aplicativo lógico. Para criar um aplicativo, siga [estas instruções](active-directory-b2c-app-registration.md). Certifique-se de

- Incluir um **aplicativo Web/API Web** no aplicativo
- Digitar `http://localhost/TodoListService` como uma **URL de Resposta** – é a URL padrão para este exemplo de código.
- Criar um **Segredo do Aplicativo** para seu aplicativo e copiá-lo. Você precisará dele em breve.
- Copiar a **ID do Aplicativo** atribuída ao aplicativo. Você também precisará dela em breve.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3. Criar suas políticas

No AD B2C do Azure, cada experiência do usuário é definida por uma [**política**](active-directory-b2c-reference-policies.md). Este aplicativo contém três experiências de identidade: inscrição, entrada e entrada com o Facebook. Você precisará criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Ao criar suas três políticas, não se esqueça de:

- Escolha o **Nome de Exibição** e alguns outros atributos de inscrição em sua política de inscrição.
- Escolha as declarações de aplicativo **Nome de Exibição** e **ID do Objeto** em cada política. Você pode escolher outras declarações também.
- Copie o **Nome** de cada política após criá-lo. Ele deve ter o prefixo `b2c_1_`. Em breve, você precisará esses nomes de política. 

Depois de criar suas três políticas com êxito, você está pronto para criar o aplicativo.

Observe que este artigo não aborda como usar as políticas que você acabou de criar. Para saber mais sobre como as políticas funcionam no AD B2C do Azure, você deve começar com o [Tutorial de introdução do aplicativo Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).



## 4. Adicionar pré-requisitos ao seu diretório

Na linha de comando, altere o diretório para a pasta raiz se ainda não estiver lá e execute os seguintes comandos:

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

- Além disso, usamos `passport-azure-ad` em nossa Visualização no esqueleto do início rápido.

- `npm install passport-azure-ad`


Isso instalará as bibliotecas das quais o passport-azure-ad depende.

## 5. Configurar seu aplicativo para usar a estratégia passport-node-js
Aqui, configuraremos o middleware Express para usar o protocolo de autenticação OpenID Connect. O Passport será usado para emitir solicitações de entrada e saída, gerenciar a sessão do usuário e obter informações sobre o usuário, entre outras coisas.

-	Para começar, abra o arquivo `config.js` na raiz do projeto e insira os valores de configuração do aplicativo na seção `exports.creds`.
    -	`clientID:` é a **ID do Aplicativo** atribuída ao seu aplicativo no portal de registro.
    -	`returnURL` é o **URI de Redirecionamento** inserido no portal.
    - `tenantName:` é o **nome do locatário** do seu aplicativo, por exemplo, contoso.onmicrosoft.com

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

- Em seguida, abra o arquivo `app.js` na raiz do projeto e adicione a seguinte chamada para invocar a estratégia `OIDCStrategy` que vem com `passport-azure-ad`


```
JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Adicione um registro de log
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

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
    - A rota `/account` primeiro ***garantirá que sejamos autenticados*** (implementamos isso abaixo) e aprovará o usuário na solicitação para que possamos obter informações adicionais sobre ele.
    - A rota `/login` chamará nosso autenticador azuread-openidconnect de `passport-azuread` e, se não tiver êxito, redirecionará o usuário para /login
    - O `/logout` simplesmente chamará logout.ejs (e a rota), o que limpa os cookies e, em seguida, retorna o usuário para index.ejs


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

Temos nosso `app.js` concluído. Agora, precisamos só adicionar rotas e exibições que nos permitirão chamar as políticas de conexão e entrada, bem como tratar das rotas `/logout` e `/login` que criamos.

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

- Crie a exibição `/views/index.ejs` no diretório raiz. Este é um exemplo de página que chamará nossas políticas para entrada e saída que nos permitirá obter informações da conta. Observe que podemos usar o `if (!user)` condicional, pois o usuário que está sendo passado na solicitação é uma prova de que temos um usuário conectado.

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

- Crie a exibição `/views/account.ejs` no diretório raiz para que possamos exibir as informações adicionais que `passport-azuread` colocou na solicitação do usuário.

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

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um .zip aqui](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip), ou você pode cloná-lo do GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git
```

Agora você pode ir para tópicos mais avançados. Você pode desejar experimentar:

[Proteger uma API Web com o modelo B2C no node.js >>](active-directory-b2c-devquickstarts-webapi-nodejs.md)

<!--

For additional resources, check out:
You can now move onto more advanced B2C topics.  You may want to try:

[Calling a node.js Web API from a node.js Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!----HONumber=Oct15_HO1-->