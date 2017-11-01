---
title: "Azure Active Directory B2C: adicionar LinkedIn como um provedor de identidade OAuth2 usando políticas personalizadas"
description: "Um artigo de instruções sobre como configurar o aplicativo LinkedIn usando o protocolo OAuth2 e políticas personalizadas"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/23/2017
ms.author: yoelh
ms.openlocfilehash: f72fac677aa7d461f174b2b06db69df235273375
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C: adicionar LinkedIn como um provedor de identidade usando políticas personalizadas
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários de uma conta do LinkedIn usando [políticas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Pré-requisitos
Conclua as etapas no artigo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="step-1-create-a-linkedin-account-application"></a>Etapa 1: Criar um aplicativo de conta do LinkedIn
Para usar o LinkedIn como provedor de identidade no Azure AD B2C (Azure Active Directory B2C), é necessário primeiro criar um aplicativo do LinkedIn e fornecê-lo com os parâmetros corretos. Você pode registrar um aplicativo do LinkedIn indo até a [página de inscrição do LinkedIn](https://LinkedIn.com/signup).

1. Vá para o site [Gerenciamento de aplicativos do LinkedIn](https://www.linkedin.com/secure/developer?newapp=), entre com suas credenciais de conta do LinkedIn e selecione **Criar Aplicativo**.

    ![Conta do LinkedIn – criar aplicativo](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2. Na página **Criar um Novo Aplicativo**, faça o seguinte:

    a. Digite o **Nome da Empresa**, um **Nome** descritivo para a empresa e uma **Descrição** do seu novo aplicativo.

    b. Carregue o **Logotipo do Aplicativo**.

    c. Selecione um **Uso do Aplicativo**.

    d. Na caixa **URL do Site**, cole **https://login.microsoftonline.com**.

    e. Digite seu endereço de **Email Comercial** e seu número de **Telefone Comercial**.

    f. Na parte inferior da página, leia e aceite os termos de uso e selecione **Enviar**.

    ![Conta do LinkedIn – configurar propriedades do aplicativo](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3. Selecione **Autenticação** e, em seguida, observe os valores de **ID do Cliente** e **Segredo do Cliente**.

4. Na caixa **URLs de Redirecionamento Autorizado**, cole **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp**. Substitua {*tenant*} por seu nome de locatário (por exemplo, contosob2c.onmicrosoft.com). Certifique-se de que você está usando o esquema HTTPS. 

    ![Conta do LinkedIn – definir URLs de redirecionamento autorizadas](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    >[!NOTE]
    >O segredo do cliente é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.

5. Selecione **Adicionar**.

6. Selecione **Configurações**, altere o **Status do aplicativo** para **Live** e, em seguida, selecione **Atualizar**.

    ![Conta do LinkedIn – definir o status do aplicativo](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>Etapa 2: Adicionar a chave de aplicativo do LinkedIn ao Azure AD B2C
A federação com contas do LinkedIn exige um segredo do cliente para a conta do LinkedIn confiar no Azure AD B2C em nome do aplicativo. Para armazenar o segredo do aplicativo do LinkedIn no seu locatário do Azure AD B2C, faça o seguinte:  

1. No seu locatário do Azure AD B2C, selecione **Configurações de B2C** > **Estrutura de Experiência de Identidade**.

2. Para exibir as chaves disponíveis no seu locatário, selecione **Chaves de Política**.

3. Selecione **Adicionar**.

4. Na caixa **Opções**, selecione **Carregar**.

5. Na caixa **Nome**, digite **B2cRestClientCertificate**.  
    O prefixo *B2C_1A_* pode ser adicionado automaticamente.

6. Na caixa **Segredo**, insira o segredo de aplicativo do LinkedIn do [Portal de Registro de Aplicativo](https://apps.dev.microsoft.com).

7. Para o **Uso da chave**, selecione **Criptografia**.

8. Selecione **Criar**. 

9. Confirme que você criou a chave `B2C_1A_LinkedInSecret`.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Etapa 3: adicionar um provedor de declarações à sua política de extensão
Se quiser que os usuários entrem usando a conta do LinkedIn, você deverá definir o LinkedIn como um provedor de declarações. Em outras palavras, você deve especificar pontos de extremidade com os quais o Azure AD B2C se comunique. Os pontos de extremidade fornecem um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Defina o LinkedIn como um provedor de declarações adicionando o nó `<ClaimsProvider>` ao seu arquivo de política da extensão:

1. No diretório de trabalho, abra o arquivo de política de extensão *TrustFrameworkExtensions.xml*. 

2. Pesquise o elemento `<ClaimsProviders>`.

3. No elemento `<ClaimsProviders>`, adicione o seguinte trecho de código XML: 

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
            <Item Key="ClaimsEndpointAccessTokenName">oauth2_access_token</Item>
            <Item Key="ClaimsEndpointFormatName">format</Item>
            <Item Key="ClaimsEndpointFormat">json</Item>
            <Item Key="scope">r_emailaddress r_basicprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
            <!--<OutputClaim ClaimTypeReferenceId="jobTitle" PartnerClaimType="headline" />-->
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

4. Substitua o valor *client_id* pela ID de cliente do aplicativo do LinkedIn.

5. Salve o arquivo.

## <a name="step-4-register-the-linkedin-account-claims-provider"></a>Etapa 4: Registrar o provedor de declarações da conta do LinkedIn
Você configurou o provedor de identidade. No entanto, ele ainda não está disponível em nenhuma das janelas de inscrição ou entrada. Agora você deve adicionar o provedor de identidade da conta do LinkedIn ao percurso de usuário `SignUpOrSignIn` do usuário.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Etapa 4.1: Fazer uma cópia do percurso do usuário
Para disponibilizar o percurso do usuário, você cria uma duplicata de um modelo de percurso do usuário existente e, em seguida, adiciona o provedor de identidade do LinkedIn:

>[!NOTE]
>Se você tiver copiado o elemento `<UserJourneys>` do arquivo de base da sua política para o arquivo de extensão *TrustFrameworkExtensions.xml*, poderá ir para essa seção.

1. Abra o arquivo base da política (por exemplo, TrustFrameworkBase.xml).

2. Pesquise o elemento `<UserJourneys>`, selecione todo o conteúdo do nó `<UserJourney>` e selecione **Recortar** para mover o texto selecionado para a área de transferência.

3. Abra o arquivo de extensão (por exemplo, TrustFrameworkExtensions.xml) e pesquise o elemento `<UserJourneys>`. Se o elemento não existir, adicione-o.

4. Cole o conteúdo inteiro do nó `<UserJourney>`, que você moveu para a área de transferência na etapa 2, no elemento `<UserJourneys>`.

### <a name="step-42-display-the-button"></a>Etapa 4.2: Exibir o “botão”
O elemento `<ClaimsProviderSelections>` define a lista de opções de seleção de provedor de declarações e sua ordem. O nó `<ClaimsProviderSelection>` é análogo a um botão de provedor de identidade em uma página de inscrição ou entrada. Se você adicionar um nó `<ClaimsProviderSelection>` à conta do LinkedIn, um novo botão será exibido quando um usuário chegar à página. Para adicionar esse elemento, faça o seguinte:

1. Pesquise o nó `<UserJourney>` que contém `Id="SignUpOrSignIn"` no percurso do usuário copiado.

2. Localize o nó `<OrchestrationStep>` que inclui `Order="1"`.

3. No elemento `<ClaimsProviderSelections>`, adicione o seguinte trecho de código XML:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Etapa 4.3: Vincular o botão a uma ação
Agora que implementou um botão, você deve vinculá-lo a uma ação. Nesse caso, a ação é para o Azure AD B2C se comunicar com a conta do LinkedIn para receber um token. Vincule o botão a uma ação vinculando o perfil técnico ao provedor de declarações da conta do LinkedIn:

1. Pesquise o nó `<OrchestrationStep>` que contém `Order="2"` no nó `<UserJourney>`.

2. No elemento `<ClaimsExchanges>`, adicione o seguinte trecho de código XML:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    >[!NOTE]
    >* Certifique-se de que o `Id` tenha o mesmo valor de `TargetClaimsExchangeId` na seção anterior.
    >* Verifique se a ID `TechnicalProfileReferenceId` está definida para o perfil técnico criado anteriormente (LinkedIn-OAuth).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Etapa 5: Carregar a política para o seu locatário
1. No [Portal do Azure](https://portal.azure.com), mude para o [contexto do locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e, em seguida, selecione **Azure AD B2C**.

2. Selecione **Estrutura de Experiência de Identidade**.

3. Selecione **Todas as Políticas**.

4. Selecione **Carregar Política**.

5. Marque a caixa de seleção **Substituir a política caso ela exista**.

6. Carregue os arquivos *TrustFrameworkBase.xml* e *TrustFrameworkExtensions.xml* e certifique-se de que eles passem na validação.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Etapa 6: Testar a política personalizada usando Executar Agora
1. Selecione **Configurações do Azure AD B2C** e selecione **Estrutura de Experiência de Identidade**.

    >[!NOTE]
    >Executar Agora exige que pelo menos um aplicativo esteja previamente registrado no locatário. Para saber como registrar aplicativos, confira os artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C.

2. Abra **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) carregada e selecione **Executar agora**.  
    Agora você deve conseguir entrar usando a conta do LinkedIn.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-the-profile-edit-user-journey"></a>Etapa 7: (Opcional) Registrar o provedor de declarações da conta do LinkedIn para o percurso do usuário de Edição de Perfil
Você também deve adicionar o provedor de identidade da conta do LinkedIn ao percurso do usuário `ProfileEdit`. Para disponibilizar o percurso do usuário, repita a “Etapa 4”. Neste momento, selecione o nó `<UserJourney>` que contém `Id="ProfileEdit"`. Salve, carregue e teste sua política.

## <a name="optional-download-the-complete-policy-files"></a>(Opcional) Baixar os arquivos da política completa
Depois de concluir o passo a passo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md), recomendamos que você crie o cenário usando seus próprios arquivos de política personalizados. Fornecemos os [Arquivos de política de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app) como referência.
