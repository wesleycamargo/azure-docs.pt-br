---
title: Inicializar os aplicativos cliente (biblioteca de autenticação do Microsoft para JavaScript) | Azure
description: Saiba mais sobre a inicialização de aplicativos de cliente usando a biblioteca de autenticação da Microsoft para JavaScript (msal).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7990566ca9cd93e79b8356cfd15fda03a7469695
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138311"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inicializar os aplicativos cliente usando msal
Este artigo descreve a biblioteca de autenticação ao inicializar Microsoft para JavaScript (msal) com uma instância de um aplicativo de agente do usuário. O aplicativo do agente do usuário é um formulário do aplicativo de cliente público no qual o código do cliente é executado em um agente do usuário, como um navegador da web. Esses clientes não armazenarem segredos, como o contexto do navegador é acessível abertamente. Para saber mais sobre os tipos de aplicativos cliente e as opções de configuração de aplicativo, leia as [visão geral](msal-client-applications.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de inicializar um aplicativo, primeiro você precisa [registrá-lo com o portal do Azure](scenario-spa-app-registration.md) para que seu aplicativo pode ser integrado com a plataforma de identidade da Microsoft. Após o registro, talvez seja necessário as informações a seguir (que podem ser encontradas no portal do Azure):

- A ID do cliente (uma cadeia de caracteres que representa um GUID para seu aplicativo)
- A URL do provedor de identidade (a instância nomeada) e o público entrar para seu aplicativo. Esses dois parâmetros são coletivamente conhecidos como a autoridade.
- A ID do locatário se você estiver escrevendo um aplicativo de linha de negócios exclusivamente para sua organização (também chamado aplicativo de locatário único).
- Para aplicativos web, você precisará também definir o redirectUri onde o provedor de identidade retornará ao seu aplicativo com os tokens de segurança.

## <a name="initializing-applications"></a>Inicialização de aplicativos

Você pode usar a msal da seguinte maneira em um aplicativo simples do JavaScript/Typescript. Inicializar o contexto de autenticação da MSAL instanciando `UserAgentApplication` com um objeto de configuração. A configuração mínima necessária para inicializar a msal é a clientID do seu aplicativo que deve ser obtido no portal de registro de aplicativo.

Para redirecionar os métodos de autenticação com fluxos de (`loginRedirect` e `acquireTokenRedirect`), você precisará registrar explicitamente para o sucesso ou erro por meio de um retorno de chamada `handleRedirectCallback()` método. Isso é necessário porque os fluxos de redirecionamento não retornam promessas assim como os métodos com uma experiência de pop-up.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

Msal foi projetado para ter uma única instância e a configuração do `UserAgentApplication` para representar um contexto de autenticação único. Várias instâncias não são recomendadas como eles fazem com que as entradas de cache em conflito e o comportamento no navegador.

## <a name="configuration-options"></a>Opções de configuração

Msal tem uma configuração de objeto mostrado abaixo, que fornece um agrupamento das opções configuráveis disponíveis para a criação de uma instância de `UserAgentApplication`.

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

Abaixo está o conjunto total de opções configuráveis que têm suporte no momento no objeto de configuração:

- **clientID**: Obrigatório. O clientID do seu aplicativo, você deve obter isso do portal de registro de aplicativo.

- **Autoridade**: Opcional. Uma URL que indica um diretório que o MSAL pode solicitar tokens do. O valor padrão é: `https://login.microsoftonline.com/common`.
    * No Azure AD, é do formato https://&lt;instância&gt;/&lt;público&gt;, onde &lt;instância&gt; é o domínio do provedor de identidade (por exemplo, `https://login.microsoftonline.com`) e &lt;público&gt; é um identificador que representa o público entrar. Isso pode ser os seguintes valores:
        * `https://login.microsoftonline.com/<tenant>`-locatário é um domínio associado ao locatário, como contoso.onmicrosoft.com, ou o GUID que representa o `TenantID` propriedade do diretório usado apenas para conectar usuários de uma organização específica.
        * `https://login.microsoftonline.com/common`– Usado para conectar os usuários com o trabalho e contas de estudante ou uma conta pessoal da Microsoft.
        * `https://login.microsoftonline.com/organizations/`– Usado para conectar os usuários com contas corporativas e de estudante.
        * `https://login.microsoftonline.com/consumers/` – Usado para conectar os usuários com apenas conta pessoal da Microsoft (live).
    * B2C do Azure AD, ele é da forma `https://<instance>/tfp/<tenant>/<policyName>/`, onde a instância é o domínio do Azure AD B2C, locatário é o nome do locatário do Azure AD B2C, policyName é o nome da política B2C para aplicar.


- **validateAuthority**: Opcional.  Valide o emissor de tokens. O padrão é `true`. Para aplicativos B2C, já que o valor de autoridade é conhecido e pode ser diferente de acordo com a política, a validação de autoridade não funcionará e precisa ser definida como `false`.

- **redirectUri**: Opcional.  O URI de redirecionamento do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos URIs de redirecionamento que você registrou no portal, com exceção de que ele deve ser codificado por URL. Usa `window.location.href` como padrão.

- **postLogoutRedirectUri**: Opcional.  Redireciona o usuário para `postLogoutRedirectUri` depois de sair. O padrão é `redirectUri`.

- **navigateToLoginRequestUrl**: Opcional. Capacidade de desativar a navegação padrão para a página inicial após o logon. O padrão é true. Isso é usado apenas para fluxos de redirecionamento.

- **cacheLocation**: Opcional.  Define o armazenamento do navegador como `localStorage` ou `sessionStorage`. O padrão é `sessionStorage`.

- **storeAuthStateInCookie**: Opcional.  Esse sinalizador foi introduzido no msal v0.2.2 como uma correção para o [problemas de autenticação de loop](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) no Microsoft Internet Explorer e Microsoft Edge. Habilitar o sinalizador `storeAuthStateInCookie` True para tirar proveito disso corrigir. Quando isso for habilitado, a msal armazenará o estado de solicitação de autenticação necessário para a validação dos fluxos de autenticação de cookies do navegador. Por padrão, esse sinalizador é definido como `false`.

- **logger**: Opcional.  Um objeto de agente com uma instância de retorno de chamada que pode ser fornecido pelo desenvolvedor para consumir e publicar os logs de uma maneira personalizada. Para obter detalhes sobre como passar o objeto de agente, consulte [registro em log com a msal](msal-logging.md).

- **loadFrameTimeout**: Opcional.  O número de milissegundos de inatividade antes que uma resposta de renovação de tokens do AD do Azure deve ser considerada atingiu o tempo limite. Padrão é 6 segundos.

- **tokenRenewalOffsetSeconds**: Opcional. O número de milissegundos que define a janela de deslocamento necessária para renovar o token antes da expiração. Padrão é 300 milissegundos.

Eles só são aplicáveis a serem passados para baixo da biblioteca MSAL Angular wrapper:
- **unprotectedResources**: Opcional.  Matriz de URIs que são recursos desprotegidos. A MSAL não anexará um token para solicitações de saída que têm esses URI. Usa `null` como padrão.

- **protectedResourceMap**: Opcional.  Isso é o mapeamento de recursos para os escopos usados pela MSAL para anexar automaticamente os tokens de acesso nas chamadas à API da web. Um token de acesso é obtido do recurso. Portanto, você pode mapear um caminho de recurso específico da seguinte maneira: {"https://graph.microsoft.com/v1.0/me", ["Read"]}, ou a URL do aplicativo do recurso como: {"https://graph.microsoft.com/", ["Read", "mail.send"]}. Isso é necessário para chamadas CORS. Usa `null` como padrão.
