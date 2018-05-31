---
title: Adicionar um provedor de identidade multilocatário do Azure AD usando políticas personalizadas - Azure Active Directory B2C | Microsoft Docs
description: Adicionar um provedor de identidade multilocatário do Azure AD usando políticas personalizadas - Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: alexsi
editor: parakhj
ms.assetid: 33c64001-5261-4ed9-8f46-b09839165250
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/14/2018
ms.author: parakhj
ms.openlocfilehash: d5e5ab1262a9d33fcf34cce91113f39c8c8936f4
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200511"
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-in-to-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: permitir que os usuários entrem em um provedor de identidade multilocatário do Azure AD usando políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários usando o ponto de extremidade multilocatário do Azure Active Directory (Azure AD) por meio do uso de [políticas personalizadas](active-directory-b2c-overview-custom.md). Isso permite que os usuários de vários locatários do Azure AD entrem no Azure AD B2C sem configurar um provedor técnico para cada locatário. No entanto, os membros em qualquer desses locatários **não** será capaz de entrar. Para fazer isso, você terá que [configurar individualmente cada locatário](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
> Usamos "contoso.com" como o locatário do Azure AD da organização e "fabrikamb2c.onmicrosoft.com" como o locatário do Azure AD B2C nas instruções a seguir.

## <a name="prerequisites"></a>pré-requisitos

Conclua as etapas no artigo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

As etapas incluem:
     
1. Criar um locatário do Azure Active Directory B2C (Azure AD B2C).
1. Criar um aplicativo Azure AD B2C.    
1. Registrar dois aplicativos do mecanismo de políticas.  
1. Configurar chaves. 
1. Configurar o pacote de início.

## <a name="step-1-create-a-multi-tenant-azure-ad-app"></a>Etapa 1. Criar um aplicativo multilocatário do Azure AD

Para habilitar a entrada de usuários que usam o ponto de extremidade multilocatário do Azure AD, você precisa ter um aplicativo multilocatário registrado em um dos seus locatários do Azure AD. Neste artigo, mostraremos como criar um aplicativo multilocatário do Azure AD em seu locatário do Azure AD B2C. Em seguida, habilite a entrada para usuários com o uso do aplicativo multilocatário do Azure AD.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Na barra superior, selecione sua conta. Na lista do **Diretório**, escolha o locatário do Azure AD B2C para registrar o aplicativo do Azure AD (fabrikamb2c.onmicrosoft.com).
1. Selecione **Mais serviços** no painel esquerdo e pesquise por “Registros do aplicativo”.
1. Selecione **Novo registro de aplicativo**.
1. Insira um nome para seu aplicativo (por exemplo, `Azure AD B2C App`).
1. Selecione **Aplicativo Web/API** como o tipo de aplicativo.
1. Para a **URL de Logon**, insira a URL a seguir, em que `yourtenant` é substituído pelo nome do seu locatário do Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >O valor de "yourtenant" deve estar todo em letras maiúsculas no **URL de Logon**.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Salve a ID do aplicativo.
1. Selecione o aplicativo recém-criado.
1. Na folha **Configurações**, selecione **Propriedades**.
1. Defina **Multilocatário** como **Sim**.
1. Na folha **Configurações**, selecione **Chaves**.
1. Crie uma nova chave e salve-a. Você a usará nas etapas da próxima seção.

## <a name="step-2-add-the-azure-ad-key-to-azure-ad-b2c"></a>Etapa 2. Adicionar a chave do Azure AD ao Azure AD B2C

Você precisa registrar a chave do aplicativo nas configurações do Azure AD B2C. Para fazer isso:

1. Vá para o menu configurações do Azure AD B2C
1. Clique em **Estrutura de Experiência de Identidade** > **Chaves da Política**.
1. Selecione **+Adicionar**.
1. Selecione ou insira estas opções:
   * Selecione **Manual**.
   * Para o **Nome**, escolha um nome que corresponda ao nome do locatário do Azure AD (por exemplo, `AADAppSecret`).  O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
   * Cole a chave do aplicativo na caixa de texto **Segredo**.
   * Selecione **Assinatura**.
1. Clique em **Criar**.
1. Confirme que você criou a chave `B2C_1A_AADAppSecret`.

## <a name="step-3-add-a-claims-provider-in-your-base-policy"></a>Etapa 3. Adicionar um provedor de declarações à política base

Se quiser que os usuários entrem usando o Azure AD, você precisará definir o Azure AD como um provedor de declarações. Em outras palavras, você precisa especificar um ponto de extremidade com o qual o Azure AD B2C se comunicará. O ponto de extremidade fornecerá um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado. 

Você pode definir o Azure AD como um provedor de declarações adicionando o Azure AD ao nó `<ClaimsProvider>` no arquivo de extensão de sua política:

1. Abra o arquivo de extensão (TrustFrameworkExtensions.xml) no diretório de trabalho.
1. Localize a seção `<ClaimsProviders>`. Caso ela não exista, adicione-a sob o nó raiz.
1. Adicione um novo nó `<ClaimsProvider>` da seguinte maneira:

```XML
<ClaimsProvider>
  <Domain>commonaad</Domain>
  <DisplayName>Common AAD</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Common-AAD">
      <DisplayName>Multi-Tenant AAD</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <!-- Update the Client ID below to the Application ID -->
        <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="scope">openid</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
        <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

        <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
        <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->

      </Metadata>
      <CryptographicKeys>
      <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
        <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
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

1. Sob o nó `<ClaimsProvider>`, atualize o valor de `<Domain>` para um valor exclusivo que pode ser usado para distinguir de outros provedores de identidade.
1. Sob o nó `<TechnicalProfile>`, atualize o valor de `<DisplayName>`. Esse valor será exibido no botão de entrada em sua tela de entrada.
1. Atualize o valor de `<Description>`.
1. Defina `<Item Key="client_id">` como a ID do aplicativo no registro de aplicativo multilocatário do Azure AD.

### <a name="step-31-restrict-access-to-a-specific-list-of-azure-ad-tenants"></a>Etapa 3.1 Restringir o acesso à lista específica de locatários do Azure AD

> [!NOTE]
> Usando `https://sts.windows.net` como o valor para **ValidTokenIssuerPrefixes** permitirá que TODOS os usuários do Azure AD entrem no seu aplicativo.

Você precisa atualizar a lista de emissores de token válidos e restringir o acesso à lista específica de locatários do Azure AD onde os usuários podem entrar. Para obter os valores, você precisará examinar os metadados para cada um dos locatários específicos do Azure AD onde você gostaria que os usuários entrassem. O formato dos dados é semelhante ao seguinte: `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, onde `yourAzureADtenant` é o nome do locatário do Azure AD (contoso.com ou outro locatário do Azure AD).
1. Abra o navegador e acesse a URL de metadados.
1. No navegador, procure o objeto “emissor” e copie seu valor. Ele deverá ter esta aparência: `https://sts.windows.net/{tenantId}/`.
1. Cole o valor da chave `ValidTokenIssuerPrefixes`. Você pode adicionar várias, separando-as com uma vírgula. Um exemplo disso é comentado no exemplo do XML acima.

## <a name="step-4-register-the-azure-ad-account-claims-provider"></a>Etapa 4. Registrar o provedor de declarações da conta do Azure AD

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Etapa 4.1 Fazer uma cópia do jornada de usuário

Agora, você precisa adicionar o provedor de identidade do Azure AD a um dos percursos do usuário. Neste ponto, o provedor de identidade foi definido, mas não está disponível em nenhuma das telas de inscrição/entrada.

Para disponibilizá-lo, criaremos uma duplicata de um modelo de percurso do usuário existente e, depois, o modificaremos para que ele também tenha o provedor de identidade do Azure AD:

1. Abra o arquivo base da política (por exemplo, TrustFrameworkBase.xml).
1. Localize o elemento `<UserJourneys>` e copie todo o nó `<UserJourney>`, que inclui `Id="SignUpOrSignIn"`.
1. Abra o arquivo de extensão (por exemplo, TrustFrameworkExtensions.xml) e localize o elemento `<UserJourneys>`. Se o elemento não existir, adicione um.
1. Cole todo o nó `<UserJourney>` copiado como um filho do elemento `<UserJourneys>`.
1. Renomeie a ID do novo Percurso do Usuário (por exemplo, renomeie como `SignUpOrSignUsingAzureAD`). 

### <a name="step-42-display-the-button"></a>Etapa 4.2 Exibir o “botão”

O elemento `<ClaimsProviderSelection>` é análogo a um botão de provedor de identidade em uma tela de inscrição/conexão. Se você adicionar um elemento `<ClaimsProviderSelection>` do Azure AD, um novo botão será exibido quando um usuário chegar à página. Para adicionar este elemento:

1. Localize o nó `<OrchestrationStep>` que inclui `Order="1"` no percurso do usuário que você acabou de criar.
1. Adicione o seguinte:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. Defina `TargetClaimsExchangeId` com um valor apropriado. Recomendamos seguir a mesma convenção que os outros: *\[ClaimProviderName\]Exchange*.

### <a name="step-43-link-the-button-to-an-action"></a>Etapa 4.3 Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação destina-se a que o Azure AD B2C se comunique com o Azure AD para receber um token. Vincule o botão a uma ação vinculando o perfil técnico ao provedor de declarações do Azure AD:

1. Localize o `<OrchestrationStep>` que inclui `Order="2"` no nó `<UserJourney>`.
1. Adicione o seguinte:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. Atualize `Id` para o mesmo valor de `TargetClaimsExchangeId` na seção anterior.
1. Atualize `TechnicalProfileReferenceId` como a ID do perfil técnico que você criou anteriormente (Common-AAD).

## <a name="step-5-create-a-new-rp-policy"></a>Etapa 5: criar uma nova política RP

Agora, você precisa atualizar o arquivo RP (terceira parte confiável) que iniciará o percurso do usuário que você acabou de criar:
 
1. Faça uma cópia de o SignUpOrSignIn.xml em diretório de trabalho e renomeie-o (por exemplo, como SignUpOrSignInWithAAD.xml).  
1. Abra o novo arquivo e atualize o atributo `PolicyId` de `<TrustFrameworkPolicy>` com um valor exclusivo (por exemplo, SignUpOrSignInWithAAD). Esse será o nome da política (por exemplo, B2C\_1A\_SignUpOrSignInWithAAD). 
1. Modifique o atributo `ReferenceId` em `<DefaultUserJourney>` para que ele corresponda à ID do novo percurso do usuário criado (SignUpOrSignUsingContoso). 
1. Salve as alterações e carregue o arquivo. 

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Etapa 6: Carregar a política para o seu locatário

1. No [Portal do Azure](https://portal.azure.com), mude para o [contexto do locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e, em seguida, selecione **Azure AD B2C**.
1. Selecione **Estrutura de Experiência de Identidade**.
1. Selecione **Todas as Políticas**.
1. Selecione **Carregar Política**.
1. Marque a caixa de seleção **Substituir a política caso ela exista**.
1. Carregue o arquivo `TrustFrameworkExtensions.xml` e o arquivo RP (por exemplo, `SignUpOrSignInWithAAD.xml`) e verifique se eles são aprovados.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Etapa 7: Testar a política personalizada usando Executar Agora

1. Selecione **Configurações do Azure AD B2C** e selecione **Estrutura de Experiência de Identidade**.
    > [!NOTE]
    > Executar Agora exige que pelo menos um aplicativo esteja previamente registrado no locatário. Para saber como registrar aplicativos, confira os artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C.

1. Abra a política personalizada da RP (terceira parte confiável) carregada (*B2C\_1A\_SignUpOrSignInWithAAD*) e selecione **Executar agora**.
1. Agora você deve conseguir entrar usando a conta do Azure AD.

## <a name="optional-step-8-register-the-azure-ad-account-claims-provider-to-the-profile-edit-user-journey"></a>(Opcional) Etapa 8: registrar o provedor de declarações da conta do Azure AD para o percurso do usuário de Edição de Perfil

Você também deve adicionar o provedor de identidade da conta do Azure AD ao percurso do usuário `ProfileEdit`. Para disponibilizar o percurso do usuário, repita as Etapas de 4 a 6. Neste momento, selecione o nó `<UserJourney>` que contém `Id="ProfileEdit"`. Salve, carregue e teste sua política.

## <a name="troubleshooting"></a>solução de problemas

Para diagnosticar problemas, leia sobre [solução de problemas](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Próximas etapas

Envie seus comentários para [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
