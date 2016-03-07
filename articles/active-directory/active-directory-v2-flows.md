<properties
	pageTitle="Tipos do ponto de extremidade v2.0 | Microsoft Azure"
	description="Os tipos de aplicativos e cenários com suporte no ponto de extremidade v2.0 do Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Tipos de ponto de extremidade v2.0
O ponto de extremidade v2.0 dá suporte à autenticação para uma variedade de arquiteturas de aplicativos modernos, que se baseiam nos protocolos padrão da indústria [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) e/ou [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). Esse documento descreve brevemente os tipos de aplicativos que você pode criar, independentemente do idioma ou da plataforma que você preferir. Ele ajudará você a compreender os cenários de alto níveis antes de [ir diretamente ao código](active-directory-appmodel-v2-overview.md#getting-started).

> [AZURE.NOTE]
	Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0. Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).

## Noções básicas
Todo aplicativo que usar o ponto de extremidade v2.0 precisará ser registrado em [apps.dev.microsoft.com](https://apps.dev.microsoft.com). O processo de registro de aplicativo vai coletar e atribuir alguns valores para seu aplicativo:

- Uma **Id de Aplicativo** que identifica exclusivamente o aplicativo
- Um **URI de Redirecionamento** que pode ser usado para direcionar as respostas de volta ao aplicativo
- Alguns outros valores específicos de cenário. Para obter mais detalhes, saiba como [registrar um aplicativo](active-directory-v2-app-registration.md).

Depois de registrado, o aplicativo se comunica com o Azure AD enviando solicitações ao ponto de extremidade v2.0 do Azure Active Directory. Fornecemos bibliotecas e estruturas de software livre que cuidam dos detalhes dessas solicitações, ou então você pode implementar a lógica de autenticação por conta própria criando solicitações para esses pontos de extremidade:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## Aplicativos Web
Para aplicativos Web (.NET, PHP, Java, Ruby, Python, Node, etc.) que são acessados por meio de um navegador, você pode realizar a entrada do usuário usando [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). No OpenID Connect, o aplicativo Web recebe um `id_token`, um token de segurança que verifica a identidade do usuário e fornece informações sobre o usuário na forma de declarações:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
	"name": "John Smith",
	"email": "john.smith@gmail.com",
	"oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
	...
}
```

É possível aprender sobre todos os tipos de tokens e declarações disponíveis para um aplicativo na [referência de token v2.0](active-directory-v2-tokens.md).

Em aplicativos de servidor Web, o fluxo de autenticação de entrada usa estas etapas de alto nível:

![Imagem de raias do aplicativo Web](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

A validação do id\_token usando uma chave de assinatura pública recebida do ponto de extremidade v2.0 é suficiente para garantir a identidade do usuário e definir um cookie de sessão que pode ser usado para identificar o usuário nas solicitações de página subsequentes.

Para ver esse cenário em ação, experimente um destes exemplos de código de entrada de aplicativo Web em nossa seção [Introdução](active-directory-appmodel-v2-overview.md#getting-started).

Além de entrada simples, um aplicativo de servidor Web talvez precise acessar algum outro serviço Web como uma API REST. Nesse caso, o aplicativo de servidor Web pode participar de um fluxo OpenID Connect e OAuth 2.0 combinado, usando o [fluxo do Código de Autorização do OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow). Esse cenário é abordado abaixo no [tópico Introdução ao WebApp-WebAPI](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## APIs da Web
É possível usar o ponto de extremidade v2.0 para proteger serviços Web, bem como a API Web RESTful do seu aplicativo. Em vez de cookies de sessão e id\_tokens, as APIs da Web usam access\_tokens do OAuth 2.0 para proteger os dados e autenticar as solicitações de entrada. O chamador de uma API da Web acrescenta um access\_token no cabeçalho de autorização de uma solicitação HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API da Web pode usar o access\_token para verificar a identidade do chamador da API e extrair informações sobre o chamador a partir de declarações que são codificadas no access\_token. É possível aprender sobre todos os tipos de tokens e declarações disponíveis para um aplicativo na [referência de token v2.0](active-directory-v2-tokens.md).

Uma API da Web pode oferecer aos usuários o poder de aceitar/recusar determinadas funcionalidades ou dados expondo permissões, também conhecidas como [escopos](active-directory-v2-scopes.md). Para um aplicativo de chamada obter permissão para um escopo, o usuário deve concordar com o escopo durante um fluxo. O ponto de extremidade v2.0 se encarregará de pedir permissão ao usuário e gravar essas permissões em todos os access\_tokens que a API da Web receber. A API da Web só precisa se preocupar em validar os access\_tokens recebidos em cada chamada e executar as verificações de autorização adequadas.

Uma API da Web pode receber access\_tokens de todos os tipos de aplicativos, incluindo aplicativos de servidor Web, aplicativos móveis e de desktop, aplicativos de página única, daemons do lado do servidor e até outras APIs da Web. O fluxo de alto nível de autenticação da Api Web é o seguinte:

![Imagem de Raias da API Web](../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Para saber mais sobre authorization\_codes, refresh\_tokens e as etapas detalhadas de obtenção de access\_tokens, leia sobre o [protocolo OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Para saber como proteger uma API Web com access\_tokens do OAuth2, confira os exemplos de código da API Web em nossa [seção de Introdução](active-directory-appmodel-v2-overview.md#getting-started).


## Aplicativos móveis e nativos
Os aplicativos que são instalados em um dispositivo, como aplicativos móveis e de desktop, geralmente precisam acessar serviços de back-end ou APIs da Web que armazenam dados e executam várias funções em nome de um usuário. Esses aplicativos podem adicionar credenciais e autorização a serviços de back-end usando o [fluxo de Código de Autorização do OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Nesse fluxo, um aplicativo recebe um authorization\_code do ponto de extremidade v2.0 na entrada do usuário, o que representa a permissão do aplicativo para chamar serviços de back-end em nome do usuário conectado no momento. O aplicativo pode trocar o authoriztion\_code em segundo plano por um refresh\_token e um access\_token do OAuth 2.0. O aplicativo pode usar o access\_token para se autenticar em APIs da Web em solicitações HTTP, e pode usar o refresh\_token para obter novos access\_tokens quando os antigos expirarem.

![Imagem de raias do aplicativo Nativo](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## Aplicativos de página única (javascript)
Muitos aplicativos modernos têm um front-end de SPA (Aplicativo de Página Única) escrito principalmente em javascript e que muitas vezes usa estruturas como AngularJS, Ember.js e Durandal, entre outras. O ponto de extremidade v2.0 do Azure AD dá suporte a esses aplicativos usando o [Fluxo Implícito do OAuth 2.0](active-directory-v2-protocols-implicit.md).

Nesse fluxo, o aplicativo recebe tokens do v2.0 para autorizar o ponto de extremidade diretamente, sem executar qualquer troca entre servidores de back-end. Isso permite que todo o manuseio de lógica de autenticação e de sessão ocorra inteiramente no cliente javascript, sem executar redirecionamentos adicionais de página.

![Imagem de Raias de Fluxo Implícito](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Para ver esse cenário em ação, experimente um destes exemplos de código de aplicativo de página única em nossa seção de [Introdução](active-directory-appmodel-v2-overview.md#getting-started).

## Limitações atuais
Esses tipos de aplicativos não têm suporte no momento no ponto de extremidade v2.0, mas estão no roteiro. Restrições e limitações adicionais do ponto de extremidade v2.0 são descritas no [artigo de limitações da v2.0](active-directory-v2-limitations.md).

### Aplicativos do lado do servidor/daemons
Os aplicativos que contêm processos de longa duração ou que operem sem a presença de um usuário também precisam de uma maneira de acessar recursos protegidos, como APIs da Web. Esses aplicativos podem se autenticar e obter tokens usando a identidade do aplicativo (em vez de a identidade delegada de um usuário) usando o fluxo de credenciais do cliente OAuth 2.0.

O fluxo de credenciais de clientes não tem suporte atualmente no ponto de extremidade v2.0. Para ver como esse fluxo funciona no serviço do Azure AD disponível ao público geral, confira o [exemplo de código daemon no GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

### APIs Web Encadeadas (em nome de)
Muitas arquiteturas incluem uma API Web que precisa chamar outra API Web downstream, ambas protegidas pelo ponto de extremidade v2.0. Este cenário é comum em clientes nativos que têm um back-end de API da Web que, por sua vez, chama um serviço Microsoft Online, como o Office 365 ou o Graph API.

Este cenário de API da Web encadeado pode ter suporte com o uso da concessão OAuth 2.0 Jwt Bearer Credential, também conhecida como [Fluxo Em Nome De](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow). No entanto, o fluxo Em Nome De não está implementado atualmente no ponto de extremidade v2.0. Para ver como esse fluxo funciona no serviço do AD do Azure disponível ao público geral, confira o [exemplo de código Em Nome De no GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

<!---HONumber=AcomDC_0224_2016-->