---
title: B2C do AD do Azure (biblioteca de autenticação da Microsoft para .NET) | Azure
description: Saiba mais sobre as considerações específicas ao usar o Azure AD B2C com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
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
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c608518a9eb80d807297f010778ae452c0f61f5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075769"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Use MSAL.NET para conectar os usuários com identidades sociais

Você pode usar MSAL.NET para conectar usuários com identidades sociais usando [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c). B2C do AD do Azure é criado em torno a noção de políticas. MSAL.NET, especificar uma política converte ao fornecimento de uma autoridade.

- Quando você instancia o aplicativo cliente público, você precisa especificar a diretiva na autoridade.
- Quando você deseja aplicar uma política, você precisa chamar uma substituição de `AcquireTokenInteractive` que contém um `authority` parâmetro.

Esta página é para a MSAL 3.x. Se você estiver interessado em MSAL 2.x, consulte [especificidades do Azure AD B2C na MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Autoridade para um locatário do Azure AD B2C e a política

A autoridade a ser usada é `https://login.microsoftonline.com/tfp/{tenant}/{policyName}` onde:

- `tenant` é o nome do locatário do Azure AD B2C, 
- `policyName` o nome da política a ser aplicado (por exemplo "b2c_1_susi" para entrada-no/inscrição).

A orientação atual do Azure AD B2C é usar `b2clogin.com` como a autoridade. Por exemplo, `$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"`. Para obter mais informações, consulte este [documentação](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Criando uma instância do aplicativo

Ao criar o aplicativo, você precisa fornecer a autoridade.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://fabrikamb2c.b2clogin.com/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Adquirir um token para aplicar uma política

Uma API protegida em um aplicativo cliente público adquirindo um token para um B2C do AD do Azure exige que você usar as substituições com uma autoridade de:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

por:

- `policy` sendo uma das cadeias de caracteres anteriores (por exemplo `PolicySignUpSignIn`).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` é um método que localiza uma conta para uma determinada política. Por exemplo: 

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
   foreach (var account in accounts)
   {
    string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
    if (userIdentifier.EndsWith(policy.ToLower()))
     return account;
   }
   return null;
  }
  ```

Aplicar uma política (por exemplo permitindo que o usuário final editar seu perfil ou redefinir sua senha) atualmente é feito chamando `AcquireTokenInteractive`. No caso essas duas políticas, você não usa o token retornado / resultar de autenticação.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Caso especial de EditProfile e ResetPassword políticas

Quando você deseja fornecer uma experiência em que os usuários finais se entrar com uma identidade social e, em seguida, editar seu perfil você deseja aplicar a política do Azure AD B2C EditProfile. A maneira de fazer isso, é chamando `AcquireTokenInteractive` com a autoridade específica para essa política e um Prompt definido como `Prompt.NoPrompt` para evitar a caixa de diálogo de seleção de conta a ser exibida (como o usuário já está conectado)

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
 IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
 try
 {
  var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                               .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                               .WithPrompt(Prompt.NoPrompt),
                               .WithB2CAuthority(App.AuthorityEditProfile)
                               .ExecuteAsync();
  DisplayBasicTokenInfo(authResult);
 }
 catch
 {
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Credenciais de senha de proprietário recurso (ROPC) com o Azure AD B2C
Para obter mais detalhes sobre o fluxo ROPC, consulte este [documentação](v2-oauth-ropc.md).

Esse fluxo é **não recomendável** porque seu aplicativo pedir a um usuário para que sua senha não é seguro. Para obter mais informações sobre esse problema, consulte [deste artigo](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Usando o nome de usuário e senha, você estiver dando-up várias coisas:
- principais locatários de identidade moderno: senha obtém fished, reproduzidos. Como temos esse conceito de um segredo de compartilhamento pode ser interceptado. Isso é incompatível com sem senha.
- Os usuários que precisem de MFA não conseguirá entrar (pois não há nenhuma interação).
- Os usuários não será capazes de logon único.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Configurar o fluxo ROPC no Azure AD B2C
Em seu locatário do Azure AD B2C, crie um novo fluxo de usuário e selecione **entrar usando ROPC**. Isso permitirá que a política ROPC para seu locatário. Ver [configurar as credenciais de senha de proprietário do recurso de fluxo](/azure/active-directory-b2c/configure-ropc) para obter mais detalhes.

`IPublicClientApplication` contém um método:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Esse método aceita como parâmetros:
- O *escopos* para solicitar um token de acesso.
- Um *nome de usuário*.
- Uma SecureString *senha* para o usuário.

Lembre-se de usar a autoridade que contém a política ROPC.

### <a name="limitations-of-the-ropc-flow"></a>Limitações do fluxo de ROPC
 - O fluxo ROPC **funciona apenas para contas locais** (em que você se registra com o Azure AD B2C usando um email ou nome de usuário). Este fluxo não funciona se associando a qualquer um dos provedores de identidade com suporte pelo Azure AD B2C (Facebook, Google, etc.).
 - Atualmente, há **nenhum id_token retornado do Azure AD B2C** ao implementar o fluxo ROPC do MSA. Isso significa que um objeto de conta não pode ser criado, portanto, no cache, haverá nenhuma conta e nenhum usuário. O fluxo de AcquireTokenSilent não funcionará neste cenário. No entanto, ROPC não mostra uma interface do usuário, portanto, não haverá nenhum impacto sobre a experiência do usuário.

## <a name="google-auth-and-embedded-webview"></a>Autenticação do Google e incorporado Webview

Se você for um desenvolvedor do Azure AD B2C usando o Google como um provedor de identidade é recommand você usar o navegador do sistema, como o Google não permite [autenticação do embedded webviews](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Atualmente, `login.microsoftonline.com` é uma autoridade confiável com o Google. Usar essa autoridade funcionará com webview incorporado. No entanto usando `b2clogin.com` não é uma autoridade confiável com o Google, portanto, os usuários não poderão autenticar.

Forneceremos uma atualização para o wiki e isso [problema](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) se as coisas mudam.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Armazenando em cache com o Azure B2C do AD em MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Problema conhecido com o Azure AD B2C

MSAL.Net dá suporte a um [cache de token](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). O token de chave de cache baseia-se as declarações retornadas pelo provedor de identidade. No momento MSAL.Net precisa de duas declarações para criar uma chave de cache de token:  
- `tid` qual é a ID de locatário do Azure AD, e 
- `preferred_username` 

Ambas as essas declarações estão ausentes em muitos dos cenários do Azure AD B2C. 

O impacto para o cliente é que ao tentar exibir o campo de nome de usuário, você está obtendo "Ausente da resposta de token" como o valor? Nesse caso, isso é porque o Azure AD B2C não retorna um valor no token para o preferred_username devido a limitações com as contas sociais e os provedores de identidade externos (IdPs). Azure AD retorna um valor para preferred_username porque ele sabe quem é o usuário, mas para B2C do AD do Azure, porque o usuário pode entrar com uma conta local, Facebook, Google, GitHub, existe etc. não é um valor consistente para o Azure AD B2C a ser usado para preferred_username. Para desbloquear o MSAL de distribuir a compatibilidade de cache com a ADAL, decidimos usar "Ausente da resposta de token" em nosso lado, ao lidar com as contas do Azure AD B2C quando o token não retorna nada para preferred_username. MSAL deve retornar um valor para preferred_username manter a compatibilidade de cache entre as bibliotecas.

### <a name="workarounds"></a>Soluções alternativas

#### <a name="mitigation-for-the-missing-tenant-id"></a>Atenuação para a ID do locatário ausente

A solução alternativa sugerida é usar o [pela política de cache](#acquire-a-token-to-apply-a-policy)

Como alternativa, você pode usar o `tid` de declaração, se você estiver usando o [políticas personalizadas do B2C](https://aka.ms/ief), pois ele fornece a capacidade de retornar declarações adicionais para o aplicativo. Para saber mais sobre [transformação de declarações](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Mitigação para "Ausente da resposta de token"
Uma opção é usar a declaração de "nome" como o nome de usuário preferido. O processo é mencionado neste [B2C doc](/azure/active-directory-b2c/active-directory-b2c-reference-policies#frequently-asked-questions) -> "na coluna de declaração de retorno, escolha as declarações que você quer retornar nos tokens de autorização enviados ao aplicativo após uma experiência de edição de perfil bem-sucedida. Por exemplo, selecione o nome de exibição, CEP".

## <a name="next-steps"></a>Próximas etapas 

Para obter mais detalhes sobre a aquisição de tokens interativamente com MSAL.NET para aplicativos do Azure AD B2C são fornecidos no exemplo a seguir.

| Amostra | Plataforma | DESCRIÇÃO|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Um aplicativo Xamarin Forms simples, mostrando como usar MSAL.NET para autenticar usuários por meio do Azure AD B2C e acessar uma API Web com os tokens resultantes.|
