<properties
	pageTitle="Introdução ao AngularJS do AD do Azure | Microsoft Azure"
	description="Como compilar um aplicativo de página única Angular JS que se integre ao AD do Azure para entrar e que chame as APIs protegidas do AD do Azure usando OAuth."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="07/17/2015"
	ms.author="dastrock"/>


# Protegendo aplicativos de página única AngularJS com o AD do Azure

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

O AD do Azure faz com que seja simples e direto adicionar entrada e saída e proteger chamadas de API do OAuth para seus aplicativos de página única. Ele habilita seu aplicativo a autenticar usuários com suas contas do Active Directory e consuma qualquer API da Web protegida pelo AD do Azure, como as APIs do Office 365 ou a API do Azure.

Para aplicativos de javascript em execução em um navegador, o AD do Azure fornece a biblioteca de autenticação do Active Directory ou adal.js. A única finalidade da adal.js é tornar mais fácil para seu aplicativo obter tokens de acesso. Para demonstrar como é fácil, vamos compilar aqui um aplicativo de lista de tarefas AngularJS que:

- Conecta o usuário ao aplicativo usando o AD do Azure como o provedor de identidade.
- Exibe informações sobre o usuário.
- Chama com segurança a API de lista de tarefas do aplicativo usando tokens do AAD.
- Desconecta o usuário do aplicativo.

Para compilar o aplicativo em funcionamento completo, você precisará:

2. Registrar seu aplicativo no AD do Azure.
3. Instalar a ADAL e configurar o SPA.
5. Usar a ADAL para proteger as páginas no SPA.

Para começar, [baixe o esqueleto do aplicativo](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) ou [baixe o exemplo concluído](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Você também precisará de um locatário do AD do Azure no qual você possa criar usuários e registrar um aplicativo. Se você ainda não tiver um locatário [saiba como obter um](active-directory-howto-tenant.md).

## *1. Registrar o aplicativo DirectorySearcher*
Para habilitar seu aplicativo para autenticar usuários e obter tokens, primeiro você precisará registrá-lo em seu locatário do AD do Azure:

-	Entre no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com)
-	Clique em **Active Directory** no painel de navegação à esquerda
-	Selecione um locatário no qual registrar o aplicativo.
-	Clique na guia **Aplicativos** e clique em **Adicionar** na lista de botões.
-	Siga os prompts e crie um novo **Aplicativo Web e/ou WebAPI**.  

    -	O **Nome** do aplicativo descreverá seu aplicativo para os usuários finais.
    -	O **URI de redirecionamento** é o local no qual o AAD retornará tokens. O local padrão para esse exemplo é `https://localhost:44326/`
-	Depois de concluir o registro, o AAD atribuirá a seu aplicativo um **identificador de cliente**. Você precisará desse valor nas próximas seções, então copie-o da guia **Configurar**.
- A adal.js usa o fluxo de OAuth implícito para se comunicar com o AD do Azure. Você deve habilitar o fluxo implícito para seu aplicativo:
    - Baixe o manifesto do aplicativo, clicando em **Gerenciar manifesto**.
    - Abra o manifesto e localize a propriedade `oauth2AllowImplicitFlow`. Defina seu valor como `true`.
    - Salve e carregue o manifesto do aplicativo, clicando em **Gerenciar manifesto** mais uma vez.

## *2. Instalar a ADAL e configurar o SPA*
Agora que você tem um aplicativo no AD do Azure, você pode instalar a adal.js e escrever seu código relacionado à identidade.

-	Comece adicionando a adal.js ao projeto TodoSPA usando o console do gerenciador de pacotes:
  - Baixe [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) e adicione-a ao diretório do projeto `App/Scripts/`.
  - Baixe [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) e adicione-a ao diretório do projeto `App/Scripts/`.
  - Carregue cada script antes do final do `</body>` em `index.html`:

```js
...
<script src="App/Scripts/adal.js"></script>
<script src="App/Scripts/adal-angular.js"></script>
...
```

-	Para que a API de lista de tarefas do back-end do SPA aceite tokens do navegador, o back-end precisa de informações de configuração sobre o registro do aplicativo. No projeto TodoSPA, abra `web.config`. Substitua os valores dos elementos na seção `<appSettings>` para refletir os valores inseridos por você no Portal do Azure. Seu código fará referência a esses valores sempre que ele usar a ADAL.
    -	O `ida:Tenant` é o domínio do seu locatário do AD do Azure, por exemplo, contoso.onmicrosoft.com.
    -	O `ida:Audience` deve ser a **ID do cliente** do seu aplicativo que você copiou do portal.

## *3. Usar a ADAL para proteger as páginas no SPA*
A adal.js foi projetada para se integrar com provedores de rota e http AngularJS, que habilita proteger exibições individuais em seu SPA.

- Em `App/Scripts/app.js`, adicione o módulo adal.js:

```js
angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {
...
```
- Agora você pode inicializar o `adalProvider` com os valores de configuração de seu registro de aplicativo, também no `App/Scripts/app.js`:

```js
adalProvider.init(
  {
      instance: 'https://login.microsoftonline.com/',
      tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
      clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
      extraQueryParameter: 'nux=1',
      //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
  },
  $httpProvider
);
```
- Agora, para proteger a exibição de `TodoList` no aplicativo, somente uma linha de código é necessária - `requireADLogin`.

```js
...
}).when("/TodoList", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
        requireADLogin: true,
...
```

Agora você tem um aplicativo de página única seguro com a capacidade de fazer logon de usuários e emitir solicitações protegidas de token de portador para sua API de back-end. Quando um usuário clica no link `TodoList`, a adal.js o redireciona automaticamente ao AD do Azure para se conectar, se necessário. Além disso, a adal.js automaticamente anexa um access\_token para todas as solicitações ajax que são enviadas ao back-end do aplicativo. O disposto acima é o mínimo necessário para compilar um SPA com adal.js, mas há alguns outros recursos que são úteis em SPAs:

- Para emitir explicitamente solicitações de entrada e saída, você pode definir funções em seus controladores que invocam a adal.js. Em `App/Scripts/homeCtrl.js`:

```js
...
$scope.login = function () {
    adalService.login();
};
$scope.logout = function () {
    adalService.logOut();
};
...
```
- Você também poderá querer apresentar informações do usuário na interface do usuário do aplicativo. O serviço da ADAL já foi adicionado ao controlador `userDataCtrl`, por isso, você pode acessar o objeto `userInfo` na exibição associada, `App/Scripts/UserData.html`:

```js
<p>{{userInfo.userName}}</p>
<p>aud:{{userInfo.profile.aud}}</p>
<p>iss:{{userInfo.profile.iss}}</p>
...
```

- Também há muitos cenários nos quais você pode querer saber se o usuário está conectado ou não. Você também pode usar o objeto `userInfo` para coletar essas informações. Por exemplo, em `index.html`, você pode mostrar o botão "Logon" ou "Logout" com base no status de autenticação:

```js
<li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
<li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
```

Parabéns! Seu aplicativo de página única integrado ao AD do Azure está concluído. Ele pode autenticar os usuários, chamar com segurança seu back-end usando OAuth 2.0 e obter informações básicas sobre o usuário. Se você ainda não fez isso, agora é o momento de preencher seu locatário com alguns usuários. Execute o SPA da lista de tarefas e entre com um desses usuários. Adicione tarefas aos usuários para listar, sair e entrar novamente.

A adal.js facilita a incorporar todos esses recursos comuns de identidade comuns em seu aplicativo. Ele se encarrega de todo o trabalho difícil para você - gerenciamento de cache, suporte a protocolo OAuth, apresentação de uma IU de logon ao usuário, atualização de tokens expirados e mais.

Para referência, o exemplo concluído (sem seus valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Agora você pode passar para cenários de adicionais. Você pode desejar experimentar:

[Chamar uma API da Web CORS de um SPA >>](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=August15_HO6-->