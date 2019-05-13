---
title: Saiba como fazer a integração ao Azure AD B2C usando a MSAL (Biblioteca de Autenticação da Microsoft)
description: A MSAL (Biblioteca de Autenticação da Microsoft) permite que os desenvolvedores de aplicativos façam a integração ao Azure AD B2C e adquiram tokens para chamar APIs Web seguras. Essas APIs Web podem ser o Microsoft Graph, outras APIs da Microsoft, APIs Web de terceiros ou sua própria API Web.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87bd8834f840c2246bf3adc1d1f9cd9b8f635915
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65191014"
---
# <a name="integrate-microsoft-authentication-library-msal-with-azure-active-directory-b2c"></a>Integrar a MSAL (Biblioteca de Autenticação da Microsoft) com o Azure Active Directory B2C

A MSAL (Biblioteca de Autenticação da Microsoft) permite que os desenvolvedores de aplicativos autentiquem os usuários com identidades sociais e locais usando o [Azure AD (Azure Active Directory) B2C](https://docs.microsoft.com/azure/active-directory-b2c/). O Azure Active Directory (Azure AD) B2C é um serviço de gerenciamento de identidade que permite personalizar e controlar como os clientes se inscrevem, entram e gerenciam seus perfis ao usar seus aplicativos.

O Azure AD (Azure Active Directory) B2C também permite definir a marca e personalizar a IU (interface do usuário) de seus aplicativos para fornecer uma experiência perfeita para o cliente.

Este tutorial demonstra como usar a MSAL (Biblioteca de Autenticação da Microsoft) para integração com o Azure AD (Azure Active Directory) B2C.


## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não criou seu próprio [locatário do Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), crie um agora. É possível usar um locatário existente do Azure AD B2C. 

## <a name="javascript"></a>JavaScript

As etapas a seguir demonstram como um aplicativo de página única pode usar o Azure AD B2C para inscrição e conexão de usuário, bem como chamar uma API Web protegida.

### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo

Para implementar a autenticação, primeiramente você precisará registrar seu aplicativo. Para registrar seu aplicativo, siga [Registrar seu aplicativo](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) para obter etapas detalhadas.

### <a name="steps-2-download-applications"></a>Etapa 2: Baixar aplicativos

Baixar um arquivo zip ou clonar o exemplo do GitHub.
>git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git

### <a name="steps-3-authentication"></a>Etapa 3: Authentication

1. Abra o arquivo index.html no exemplo.

2. Configure a amostra com a ID do aplicativo e a chave que você anotou anteriormente ao registrar seu aplicativo. Altere as linhas de código a seguir substituindo os valores pelos nomes do diretório e de APIs:

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>",
        b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/hello/demo.read"],
        webApi: 'http://localhost:5000/hello',
  };

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

O nome do [fluxo de usuário](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) usado neste tutorial é B2C_1_signupsignin1. Se você estiver usando um nome de fluxo de usuário diferente, use seu nome de fluxo de usuário no valor de autoridade.


### <a name="configure-application-to-use-b2clogincom"></a>Configurar o aplicativo para usar `b2clogin.com`

Você pode usar `b2clogin.com` em vez de `login.microsoftonline.com` como uma URL de direcionamento ao configurar um provedor de identidade para inscrição e conexão em seu aplicativo do Azure AD (Azure Active Directory) B2C.

**`b2clogin.com`**, por exemplo, 
`https://your-tenant-name.b2clogin.com/your-tenant-guid` é usado para o seguinte:

- O espaço consumido no cabeçalho de cookie por serviços da Microsoft é reduzido.
- As URLs não incluem mais uma referência à Microsoft. Por exemplo, seu aplicativo do Azure AD B2C provavelmente refere-se a login.microsoftonline.com


 Para usar 'b2clogin.com', você precisa atualizar a configuração do seu aplicativo.  

1. Atualização do ValidateAuthority: defina a propriedade **validateAuthority** como `false`. Quando **validateAuthority** for definido como false, os redirecionamentos serão permitidos para b2clogin.com.

O exemplo a seguir mostra como definir a propriedade:
```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> Seu aplicativo do Azure AD B2C provavelmente refere-se a login.microsoftonline.com em vários locais, como suas referências de fluxo de usuário e pontos de extremidade do token. Certifique-se de que seu ponto de extremidade da autorização, ponto de extremidade do token e emissor foram atualizados para usar seu-nome-de-locatário.b2clogin.com.

Siga esse [exemplo de MSAL JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) sobre como usar a versão prévia da Biblioteca de Autenticação da Microsoft para JavaScript (msal.js) para obter um token de acesso e chamar uma API protegida pelo Azure AD B2C.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:

- [Políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Personalização da interface do usuário](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)