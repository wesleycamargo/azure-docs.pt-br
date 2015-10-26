<properties
	pageTitle="Visualização do AD B2C do Azure | Microsoft Azure"
	description="Os tipos de aplicativos que você pode compilar na visualização AD B2C do Azure."
	services="active-directory-b2c"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="dastrock"/>

# Visualização do AD B2C do Azure: tipos de aplicativos

O AD B2C do Azure dá suporte à autenticação para uma variedade de arquiteturas de aplicativos modernos, que se baseiam nos protocolos padrão do setor [OAuth 2.0](active-directory-b2c-reference-protocols.md) e/ou [OpenID Connect](active-directory-b2c-reference-protocols.md). Esse documento descreve brevemente os tipos de aplicativos que você pode criar, independentemente do idioma ou da plataforma que você preferir. Ele ajudará você a compreender os cenários de alto níveis antes de [ir diretamente ao código](active-directory-b2c-overview.md#getting-started).

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Noções básicas
Todos os aplicativos que usam o AD B2C do Azure precisarão ser registrados no seu [diretório B2C](active-directory-b2c-get-started.md) por meio de [Portal de Visualização do Azure](https://portal.azure.com). O processo de registro de aplicativo vai coletar e atribuir alguns valores para seu aplicativo:

- Uma **ID de Aplicativo** que identifica exclusivamente o aplicativo
- Um **URI de Redirecionamento** que pode ser usado para direcionar as respostas de volta ao aplicativo
- Alguns outros valores específicos de cenário. Para obter mais detalhes, saiba como [registrar um aplicativo](active-directory-b2c-app-registration.md).

Depois de registrado, o aplicativo se comunica com o AD do Azure enviando solicitações ao ponto de extremidade v2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Cada solicitação enviada para o AD B2C do Azure especifica uma **política**. Uma política controla o comportamento do AD do Azure e permite que você use esses pontos de extremidade para criar um conjunto altamente personalizável de experiências do usuário. Algumas políticas comuns incluem inscrição, entrada, e políticas de edição de perfil. Se você não estiver familiarizado com as políticas, leia a [estrutura de política extensível](active-directory-b2c-reference-policies.md) do AD B2C do Azure antes de prosseguir.

A interação de cada aplicativo com o ponto de extremidade v2.0 segue um padrão semelhante de alto nível:

1. O aplicativo direciona o usuário final ao ponto de extremidade v2.0 para executar uma [política](active-directory-b2c-reference-policies.md).
2. O usuário conclui a política de acordo com a definição de política.
4. O aplicativo recebe um token de segurança de algum tipo do ponto de extremidade v2.0.
5. O aplicativo usa o token de segurança para acessar informações protegidas ou um recurso.
6. O servidor de recurso valida o token de segurança para garantir que o acesso possa ser concedido.
7. O aplicativo atualiza periodicamente o token de segurança.

<!-- TODO: Need a page for libraries to link to -->
Cada uma dessas etapas vai diferir ligeiramente com base no tipo de aplicativo que você está compilando, e temos bibliotecas de software livre que cuidam dos detalhes para você.

## Aplicativos Web
Para aplicativos Web (.NET, PHP, Java, Ruby, Python, Node, etc) hospedados em um servidor e acessados por meio de um navegador, o AD B2C do Azure dá suporte ao [OpenID Connect](active-directory-b2c-reference-protocols.md) para todas as experiências de usuário, incluindo entrada, inscrição e gerenciamento de perfil. Na implementação do OpenID Connect do AD B2C do Azure, seu aplicativo Web inicia essas experiências de usuário emitindo solicitações de autenticação para AD do Azure. O resultado da solicitação é um `id_token`, um token de segurança que representa a identidade do usuário e fornece informações sobre o usuário na forma de declarações:

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

É possível aprender sobre todos os tipos de tokens e declarações disponíveis para um aplicativo na [referência de token do B2C](active-directory-b2c-reference-tokens.md).

Em aplicativos Web, cada execução de um [política](active-directory-b2c-reference-policies.md) usa estas etapas de alto nível:

![Imagem de raias do aplicativo Web](./media/active-directory-b2c-apps/webapp.png)

A validação do id\_token usando uma chave de assinatura pública recebida do AD DO Azure é suficiente para garantir a identidade do usuário, e definir um cookie de sessão que pode ser usado para identificar o usuário em solicitações de páginas subsequentes.

Para ver esse cenário em ação, experimente um destes exemplos de código de entrada de aplicativo Web em nossa seção [Introdução](active-directory-b2c-overview.md#getting-started).

Além de entrada simples, um aplicativo de servidor Web talvez precise acessar algum serviço Web de back-end. Nesse caso, o aplicativo Web pode executar o [fluxo do OpenID Connect](active-directory-b2c-reference-oidc.md) um pouco diferente, e adquirir tokens usando códigos de autorização e tokens de atualização. Este cenário é descrito abaixo na [seção de APIs Web](#web-apis).

<!--, and in our [WebApp-WebAPI Getting Started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## APIs da Web
Você também pode usar AD B2C do Azure para proteger serviços Web, tais como a API Web RESTful. APIs Web podem usar o OAuth 2.0 para proteger seus dados e autenticar solicitações HTTP recebidas usando tokens. O chamador de uma API Web acrescenta um token no cabeçalho de autorização de uma solicitação HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API Web pode usar o token para verificar a identidade do chamador da API e extrair informações sobre o chamador por meio de declarações codificadas no token. É possível aprender sobre todos os tipos de tokens e declarações disponíveis para um aplicativo na [referência de token do AD B2C do Azure](active-directory-b2c-reference-tokens.md).

> [AZURE.NOTE]A visualização do AD B2C do Azure atualmente dá suporte apenas a APIs Web acessadas por seus próprios clientes conhecidos. Por exemplo, seu aplicativo completo pode incluir um aplicativo iOS e aplicativo do Android e um API Web de back-end. Essa arquitetura tem total suporte. O que não tem suporte atualmente permite que um cliente terceirizado, como outro aplicativo iOS, acesse também a mesma API Web. Na verdade, cada componente do aplicativo completo deve compartilhar uma única ID do aplicativo.

Uma API Web pode receber tokens de todos os tipos de aplicativos, incluindo aplicativos Web, aplicativos móveis e da área de trabalho, aplicativos de página única, daemons do lado do servidor e até outras APIs Web. Por exemplo, vejamos o fluxo completo de um aplicativo Web que chama uma API Web.

![Imagem de raias da API da Web do aplicativo Web](./media/active-directory-b2c-apps/webapi.png)

Para saber mais sobre authorization\_codes, refresh\_tokens e etapas detalhadas de obtenção de tokens, leia sobre o [Protocolo OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Para saber como proteger uma API Web com o AD B2C do Azure, confira os tutoriais da API Web em nossa [seção de Introdução](active-directory-b2c-overview.md#getting-started).
	
## Aplicativos móveis e nativos
Os aplicativos instalados em um dispositivo, como aplicativos móveis e da área de trabalho, geralmente precisam acessar serviços de back-end ou APIs Web em nome de um usuário. Você pode adicionar experiências de gerenciamento de identidade personalizadas a seus aplicativos nativos e chamar com segurança serviços de back-end usando o AD B2C do Azure e o [fluxo de código de autorização do OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Nesse fluxo, o aplicativo executa [políticas](active-directory-b2c-reference-policies.md) e recebe um authorization\_code do AD do Azure depois que o usuário conclui a política. O authorization\_code representa a permissão do aplicativo para chamar serviços de back-end em nome do usuário conectado no momento. O aplicativo pode trocar o authoriztion\_code em segundo plano por um refresh\_token e um access\_token. O aplicativo pode usar o id\_token para se autenticar em uma API Web de back-end em solicitações HTTP, e pode usar o refresh\_token para obter novos id\_tokens quando os antigos expirarem.

> [AZURE.NOTE]A visualização do AD B2C do Azure só dá suporte atualmente à obtenção de id\_tokens usados para acessar um serviço Web de back-end do próprio aplicativo. Por exemplo, seu aplicativo completo pode incluir um aplicativo iOS e aplicativo do Android e um API Web de back-end. Essa arquitetura tem total suporte. O que não tem suporte atualmente é permitir que o aplicativo iOS acesse uma API Web de terceiros usando access\_tokens do OAuth 2.0. Na verdade, cada componente do aplicativo completo deve compartilhar uma única ID do aplicativo.

![Imagem de raias do aplicativo Nativo](./media/active-directory-b2c-apps/native.png)

## Limitações de visualização atuais
Esses tipos de aplicativos atualmente não são compatíveis com a visualização do AD B2C do Azure, mas estão no roteiro para suporte em tempo para disponibilidade geral. Restrições e limitações adicionais para a visualização do AD B2C do Azure são descritas no [artigo de limitações](active-directory-b2c-limitations.md).

### Aplicativos de página única (Javascript)
Muitos aplicativos modernos têm um front-end de Aplicativo de Página Única escrito principalmente em javascript e muitas vezes usando estruturas de SPA, como AngularJS, Ember.js, Durandal, etc. O serviço AD do Azure disponível ao público em geral dá suporte a esses aplicativos usando o Fluxo Implícito do OAuth 2.0 - no entanto, esse fluxo ainda não está disponível no AD B2C do Azure. Em breve ele será adicionado.

### Aplicativos do lado do servidor/daemons
Os aplicativos que contêm processos de longa duração ou que operem sem a presença de um usuário também precisam de uma maneira de acessar recursos protegidos, como APIs da Web. Esses aplicativos podem se autenticar e obter tokens usando a identidade do aplicativo (em vez de a identidade delegada de um usuário) usando o fluxo de credenciais do cliente OAuth 2.0.

Esse fluxo não tem suporte atualmente no AD B2C do Azure, o que equivale a dizer que os aplicativos só podem obter tokens após a ocorrência de um fluxo de usuário interativo. O fluxo de credenciais de cliente será adicionado em breve.

### Cadeias de API Web (Em nome de)
Muitas arquiteturas incluem uma API da Web que precisa chamar outra API da Web downstream, ambas protegidas pelo AD B2C do Azure. Este cenário é comum em clientes nativos que têm um back-end de API Web que, por sua vez, chama um serviço Microsoft Online, como o Graph API do AD do Azure.

Este cenário de API Web encadeada pode ter suporte usando a concessão Credencial de Portador Jwt do OAuth 2.0, também conhecido como fluxo Em nome de. No entanto, o fluxo Em nome de não está implementado atualmente na visualização do AD B2C do Azure.

<!---HONumber=Oct15_HO3-->