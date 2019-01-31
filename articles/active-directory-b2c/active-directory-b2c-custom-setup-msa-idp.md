---
title: Adicionar MSA (Conta da Microsoft) como um provedor de identidade usando políticas personalizadas no Azure Active Directory B2C | Microsoft Docs
description: Exemplo de como usar Microsoft como provedor de identidade usando o protocolo OIDC (OpenID Connect).
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 4bcc51c3efce95178dbb190eb86cb7ac8e224cd0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187514"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a entrada com uma conta Microsoft usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários de uma conta Microsoft usando as [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em [Introdução às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Se você ainda não tiver uma conta Microsoft, crie uma em [https://www.live.com/](https://www.live.com/).

## <a name="add-an-application"></a>Adicionar um aplicativo

Para usar uma conta Microsoft como um provedor de identidade no Azure AD B2C, será necessário adicionar um aplicativo de conta Microsoft.

1. Entre no [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) com suas credenciais da conta da Microsoft.
2. No canto superior direito, escolha **Adicionar um aplicativo**.
3. Insira um **Nome do Aplicativo** e, em seguida, clique em **Criar** 
4. Selecione **Gerar Nova Senha** e certifique-se de copiar a senha a ser usada ao configurar o provedor de identidade. Copie também a **ID do Aplicativo**. 
5. Insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URIs de redirecionamento**. Substitua `your-tenant-name` pelo nome do seu locatário.
6. Clique em **Salvar**.

## <a name="create-a-policy-key"></a>Criar uma chave de política

É necessário armazenar a senha gerada e registrada anteriormente no locatário do Azure AD B2C.

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Na página Visão Geral, selecione **Identity Experience Framework – VERSÃO PRÉVIA**.
5. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
6. Para **Opções**, escolha `Manual`.
7. Insira um **Nome** para a chave de política. Por exemplo, `MSASecret`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
8. Em **Segredo**, insira a senha que você gravou anteriormente.
9. Para **Uso de chave**, selecione `Signature`.
10. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Se você quiser que os usuários entrem usando uma conta Microsoft, defina a conta como um provedor de declarações com o qual o Azure AD B2C pode comunicar-se por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado. 

Você pode definir o Azure AD como um provedor de declarações, adicionando o elemento **ClaimsProvider** no arquivo de extensão de sua política.

1. Abra *TrustFrameworkExtensions.xml*.
2. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **ClaimsProvider** da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Microsoft application client id</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4.  Substitua o valor de **client_id** pela ID do aplicativo que você gravou anteriormente.
5.  Salve o arquivo.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Até este ponto, você configurou a política para que o Azure AD B2C saiba como comunicar-se com a conta Microsoft. Tente carregar o arquivo de extensão da política apenas para confirmar se ele não apresenta problemas até o momento.

1. Na página **Políticas Personalizadas** em seu locatário do Azure AD B2C, selecione **Carregar Política**.
2. Habilite **Substitua a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, o provedor de identidade já foi definido, mas não está disponível em nenhuma das telas de inscrição ou entrada. Para disponibilizá-lo, você criará uma duplicata de um percurso do usuário existente e, em seguida, modificará para que ele também tenha o provedor de identidade da conta Microsoft.

1. Abra o arquivo *TrustFrameworkBase.xml* do pacote inicial.
2. Localize e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions.xml* e localize o elemento **UserJourneys**. Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **UserJourney** que você copiou como filho do elemento **UserJourneys**.
5. Renomeie a ID do percurso do usuário. Por exemplo, `SignUpSignInMSA`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão do provedor de identidade em uma tela de inscrição ou de entrada. Se você adicionar um elemento **ClaimsProviderSelection** à conta Microsoft, um novo botão será exibido quando o usuário chegar à página.

1. No arquivo *TrustFrameworkExtensions.xml*, localize o elemento **OrchestrationStep** que inclui `Order="1"` no percurso do usuário que você criou.
2. Em **ClaimsProviderSelects**, adicione o elemento a seguir. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação é para o Azure AD B2C comunicar-se com a conta do Twitter para receber um token.

1. Localize o **OrchestrationStep** que inclui `Order="2"` no percurso do usuário.
2. Adicione o seguinte elemento **ClaimsExchange** usando o mesmo valor de **ID** usado para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```
    
    Atualize o valor de **TechnicalProfileReferenceId** para a **ID** do perfil técnico você já criou. Por exemplo, `MSA-OIDC`.

3. Salve o arquivo *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

A comunicação com o Azure AD B2C ocorre por meio de um aplicativo que você cria no seu locatário. Esta seção lista etapas opcionais que você pode concluir para criar um aplicativo de teste, caso ainda não tenha feito isso.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo, por exemplo *testapp1*.
6. Para **Aplicativo Web/API Web**, selecione `Yes` e, em seguida, insira `https://jwt.ms` para a **URL de resposta**.
7. Clique em **Criar**.

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou.

1. Faça uma cópia do *SignUpOrSignIn.xml* no diretório de trabalho e renomeie-a. Por exemplo, renomeie-a para *SignUpSignInMSA.xml*.
2. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInMSA`.
3. Atualize o valor de **PublicPolicyUri** com o URI da política. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_msa`
4. Atualize o valor do atributo **ReferenceId** em **DefaultUserJourney** para corresponder à ID do novo percurso do usuário que você criou (SignUpSignInMSA).
5. Salve suas alterações, carregue o arquivo e, em seguida, selecione a nova política na lista.
6. Verifique se o aplicativo Azure AD B2C que você criou está selecionado no campo **Selecionar aplicativo** e teste-o clicando em **Executar agora**.
