---
title: Definir URLs de redirecionamento para b2clogin.com – Azure Active Directory B2C | Microsoft Docs
description: Saiba como usar b2clogin.com nas URLs de redirecionamento do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5f706a086c3dfe24f22e63cfe84f330d866eca70
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703083"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Definir URLs de redirecionamento para b2clogin.com do Azure Active Directory B2C

Ao configurar um provedor de identidade para inscrição e entrada no aplicativo Azure AD (Azure Active Directory) B2C, será necessário especificar uma URL de redirecionamento. Anteriormente era usado login.microsoftonline.com, agora você deve estar usando b2clogin.com.

Usar b2clogin.com oferece benefícios adicionais, como:

- O espaço consumido no cabeçalho de cookie por serviços da Microsoft é reduzido.
- As URLs não incluem mais uma referência à Microsoft. Por exemplo, `https://your-tenant-name.b2clogin.com/tenant-id/oauth2/authresp`.

>[!NOTE]
> Você pode usar o nome do locatário e o GUID do locatário da seguinte maneira:
> * `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com` (que ainda se refere ao `onmicrosoft.com`)
> * `https://your-tenant-name.b2clogin.com/your-tenant-guid` (nesse caso, não há nenhuma referência para a Microsoft em todos os)
>
> No entanto, você não pode usar um _domínio personalizado_ para seu Azure Active Directory B2C do diretório de locatário, por exemplo, `https://your-tenant-name.b2clogin.com/your-custom-domain-name` seria _não_ funcione.

Considere estas configurações que estão sujeitas a alterações ao usar b2clogin.com:

- Defina as URLs de redirecionamento nos aplicativos do provedor de identidade para usar b2clogin.com. 
- Configure o aplicativo Active Directory B2C para usar b2clogin.com para referências de fluxo de usuário e pontos de extremidade de token. 
- Se você estiver usando MSAL, será necessário definir a propriedade **ValidateAuthority** para `false`.
- Certifique-se de alterar quaisquer **Origens permitidas** definidas nas configurações do CORS para [personalização da interface do usuário](active-directory-b2c-ui-customization-custom-dynamic.md).  

## <a name="change-redirect-urls"></a>Alterar URLs de redirecionamento

Para usar b2clogin.com, nas configurações do aplicativo do provedor de identidade, procure e altere a lista de URLs confiáveis para redirecionar de volta ao Azure AD B2C.  Atualmente, você provavelmente tenha configurado para redirecionar de volta a algum site login.microsoftonline.com. 

Será necessário alterar a URL de redirecionamento para que `your-tenant-name.b2clogin.com` seja autorizado. Substitua `your-tenant-name` pelo nome do locatário do Azure AD B2C e remova `/te` se existir na URL. Há pequenas variações nessa URL para cada provedor de identidade, portanto, verifique a página correspondente para obter a URL exata.

Você pode encontrar informações de configuração para provedores de identidade nos seguintes artigos:

- [Conta da Microsoft](active-directory-b2c-setup-msa-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Google](active-directory-b2c-setup-goog-app.md)
- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn](active-directory-b2c-setup-li-app.md)
- [Twitter](active-directory-b2c-setup-twitter-app.md)
- [GitHub](active-directory-b2c-setup-github-app.md)
- [Weibo](active-directory-b2c-setup-weibo-app.md)
- [QQ](active-directory-b2c-setup-qq-app.md)
- [WeChat](active-directory-b2c-setup-wechat-app.md)
- [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)
- [OIDC personalizado](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>Atualizar o aplicativo

O aplicativo Azure AD B2C provavelmente faz referência `login.microsoftonline.com` em vários locais, como as referências de fluxo de usuário e ponto de extremidade de token.  Certifique-se de que o ponto de extremidade de autorização, ponto de extremidade de token e emissor foram atualizados para usar `your-tenant-name.b2clogin.com`.  

## <a name="set-the-validateauthority-property"></a>Definir a propriedade ValidateAuthority

Se você estiver usando MSAL, defina a propriedade **ValidateAuthority** como `false`. Quando **ValidateAuthority** é definida como `false`, os redirecionamentos para b2clogin.com são permitidos. 

O exemplo a seguir mostra como definir a propriedade:

Em [MSAL for .Net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet):

```CSharp
 ConfidentialClientApplication client = new ConfidentialClientApplication(...); // can also be PublicClientApplication
 client.ValidateAuthority = false;
```

E na [MSAL for Javascript](https://github.com/AzureAD/microsoft-authentication-library-for-js):

```Javascript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```
