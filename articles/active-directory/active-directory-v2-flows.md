<properties
	pageTitle="Tipos de aplicativo do modelo de aplicativo v2.0 | Microsoft Azure"
	description="Os tipos de aplicativos e cenários compatíveis com a visualização pública do modelo de aplicativo v2.0 do AD do Azure."
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
	ms.date="09/11/2015"
	ms.author="dastrock"/>

# Visualização do modelo de aplicativo v2.0: Tipos de aplicativos
O modelo de aplicativo v2.0 oferece autenticação para uma variedade de arquiteturas de aplicativos modernos, que se baseiam os protocolos padrão do setor [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) e/ou [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). Esse documento descreve brevemente os tipos de aplicativos que você pode criar, independentemente do idioma ou da plataforma que você preferir. Ele ajudará você a compreender os cenários de alto níveis antes de [ir diretamente ao código](active-directory-appmodel-v2-overview.md#getting-started).

> [AZURE.NOTE]Essas informações se aplicam à visualização pública do modelo de aplicativo v2.0. Para obter instruções sobre como integrar-se ao serviço do AD do Azure disponível ao público geral, consulte o [Guia do Desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md).

## Noções básicas
Cada aplicativo que usa o modelo de aplicativo v2.0 precisará ser registrado em [apps.dev.microsoft.com](https://apps.dev.microsoft.com). O processo de registro de aplicativo vai coletar e atribuir alguns valores para seu aplicativo:

- Uma **Id de Aplicativo** que identifica exclusivamente o aplicativo
- Um **URI de Redirecionamento** que pode ser usado para direcionar as respostas de volta ao aplicativo
- Alguns outros valores específicos de cenário. Para obter mais detalhes, saiba como [registrar um aplicativo](active-directory-v2-app-registration.md).

Depois de registrado, o aplicativo se comunica com o AD do Azure enviando solicitações ao ponto de extremidade v2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

A interação de cada aplicativo com o ponto de extremidade v2.0 segue um padrão semelhante de alto nível:

1. O aplicativo direciona o usuário final ao ponto de extremidade v2.0 para entrada.
2. O usuário é autenticado inserindo seu nome de usuário e senha ou de outra forma.
3. O usuário autoriza que o aplicativo atue em seu nome, concedendo as permissões que o aplicativo solicita.
4. O aplicativo recebe um token de segurança de algum tipo do ponto de extremidade v2.0.
5. O aplicativo usa o token de segurança para acessar informações protegidas ou um recurso.
6. O servidor de recurso valida o token de segurança para garantir que o acesso possa ser concedido.
7. O aplicativo atualiza periodicamente o token de segurança.

<!-- TODO: Need a page for libraries to link to -->
Cada uma dessas sete etapas vai diferir ligeiramente com base no tipo de aplicativo que você está criando, e temos bibliotecas de código-fonte aberto que cuidam dos detalhes para você. Você pode saber mais sobre [permissões, autorização e escopos](active-directory-v2-scopes.md) ou ler para explorar alguns exemplos concretos.

## Aplicativos Web
Para aplicativos Web (.NET, PHP, Java, Ruby, Python, Node, etc.) que são acessados por meio de um navegador, o modelo de aplicativo v2.0 é compatível com a entrada do usuário usando o [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). No OpenID Connect, o aplicativo Web recebe um `id_token`, um token de segurança que verifica a identidade do usuário e fornece informações sobre o usuário na forma de declarações:

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

Além de entrada simples, um aplicativo de servidor Web talvez precise acessar algum outro serviço Web como uma API REST. Nesse caso, o aplicativo de servidor Web pode participar de um fluxo OpenID Connect e OAuth 2.0 combinado, usando o [fluxo do Código de Autorização do OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow). Esse cenário é abordado abaixo na [seção APIs da Web](#web-apis) e em nosso [tópico Introdução ao WebApp-WebAPI](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## APIs da Web
É possível usar o modelo de aplicativo v2.0 para proteger serviços da Web, bem como a API da Web RESTful do seu aplicativo. Em vez de cookies de sessão e id\_tokens, as APIs da Web usam access\_tokens do OAuth 2.0 para proteger os dados e autenticar as solicitações de entrada. O chamador de uma API da Web acrescenta um access\_token no cabeçalho de autorização de uma solicitação HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API da Web pode usar o access\_token para verificar a identidade do chamador da API e extrair informações sobre o chamador a partir de declarações que são codificadas no access\_token. É possível aprender sobre todos os tipos de tokens e declarações disponíveis para um aplicativo na [referência de token v2.0](active-directory-v2-tokens.md).

Uma API da Web pode oferecer aos usuários o poder de aceitar/recusar determinadas funcionalidades ou dados expondo permissões, também conhecidas como [escopos](active-directory-v2-scopes.md). Para um aplicativo de chamada obter permissão para um escopo, o usuário deve concordar com o escopo durante um fluxo. O ponto de extremidade v2.0 se encarregará de pedir permissão ao usuário e gravar essas permissões em todos os access\_tokens que a API da Web receber. A API da Web só precisa se preocupar em validar os access\_tokens recebidos em cada chamada e executar as verificações de autorização adequadas.

Uma API da Web pode receber access\_tokens de todos os tipos de aplicativos, incluindo aplicativos de servidor Web, aplicativos móveis e de desktop, aplicativos de página única, daemons do lado do servidor e até outras APIs da Web. Por exemplo, vejamos o fluxo completo para um aplicativo de servidor Web que chama uma API da Web.

![Imagem de raias da API da Web do aplicativo Web](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

Para saber mais sobre authorization\_codes, refresh\_tokens e as etapas detalhadas de obtenção de access\_tokens, leia sobre o [protocolo OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Para saber como proteger uma API da Web com o modelo de aplicativo v2.0 e access\_tokens do OAuth 2.0, confira os exemplos de código da API da Web em nossa [seção Introdução](active-directory-appmodel-v2-overview.md#getting-started).


## Aplicativos móveis e nativos
Os aplicativos que são instalados em um dispositivo, como aplicativos móveis e de desktop, geralmente precisam acessar serviços de back-end ou APIs da Web que armazenam dados e executam várias funções em nome de um usuário. Esses aplicativos podem adicionar entrada e autorização a serviços de back-end usando o modelo v2.0 e o [fluxo de Código de Autorização do OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Nesse fluxo, um aplicativo recebe um authorization\_code do ponto de extremidade v2.0 na entrada do usuário, o que representa a permissão do aplicativo para chamar serviços de back-end em nome do usuário conectado no momento. O aplicativo pode trocar o authoriztion\_code em segundo plano por um refresh\_token e um access\_token do OAuth 2.0. O aplicativo pode usar o access\_token para se autenticar em APIs da Web em solicitações HTTP, e pode usar o refresh\_token para obter novos access\_tokens quando os antigos expirarem.

![Imagem de raias do aplicativo Nativo](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## Aplicativos de página única (Javascript)
Muitos aplicativos modernos têm um front-end de Aplicativo de Página Única escrito principalmente em javascript e muitas vezes usando estruturas de SPA, como AngularJS, Ember.js, Durandal, etc. O modelo de aplicativo v2.0 do AD do Azure dá suporte a esses aplicativos usando o [Fluxo Implícito do OAuth 2.0](active-directory-v2-protocols-implicit.md).

Nesse fluxo, o aplicativo recebe tokens do v2.0 para autorizar o ponto de extremidade diretamente, sem executar qualquer troca entre servidores de back-end. Isso permite que todo o manuseio de lógica de autenticação e de sessão ocorra inteiramente no cliente javascript, sem executar redirecionamentos adicionais de página.

Para ver esse cenário em ação, experimente um destes exemplos de código de aplicativo de página única em nossa seção de [Introdução](active-directory-appmodel-v2-overview.md#getting-started).

## Limitações de visualização atuais
Esses tipos de aplicativos atualmente não são compatíveis com a visualização do modelo de aplicativo v2.0, mas estão no roteiro para compatibilidade em tempo para a disponibilidade geral. Restrições e limitações adicionais para a visualização pública do modelo de aplicativo v2.0 são descritas no [artigo de limitações de visualização v2.0](active-directory-v2-limitations.md).

### Aplicativos do lado do servidor/daemons
Os aplicativos que contêm processos de longa duração ou que operem sem a presença de um usuário também precisam de uma maneira de acessar recursos protegidos, como APIs da Web. Esses aplicativos podem se autenticar e obter tokens usando a identidade do aplicativo (em vez de a identidade delegada de um usuário) usando o [fluxo de credenciais do cliente OAuth 2.0](active-directory-v2-protocols.md#oauth2-client-credentials-grant-flow).

Esse fluxo não é compatível com o modelo de aplicativo v2.0, o que equivale a dizer que os aplicativos só podem obter tokens após a ocorrência de um fluxo de entrada do usuário interativo. O fluxo de credenciais de cliente será adicionado em breve. Se você quiser ver o fluxo de credenciais de cliente no serviço do AD do Azure disponível ao público geral, confira o [Exemplo de daemon no GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

### APIs de Web encadeadas (em nome de)
Muitas arquiteturas incluem uma API da Web que precisa chamar outra API da Web downstream, ambas protegidas pelo modelo de aplicativo v2.0. Este cenário é comum em clientes nativos que têm um back-end de API da Web que, por sua vez, chama um serviço Microsoft Online, como o Office 365 ou o Graph API.

Este cenário de API Web encadeada pode ter suporte usando a concessão Credencial de Portador Jwt do OAuth 2.0, também conhecido como [Fluxo Em Nome De](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow). No entanto, o fluxo Em Nome De não está implementado atualmente na visualização do modelo de aplicativo v2.0. Para ver como esse fluxo funciona no serviço do AD do Azure disponível ao público geral, confira o [exemplo de código Em Nome De no GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

<!---HONumber=AcomDC_1203_2015-->