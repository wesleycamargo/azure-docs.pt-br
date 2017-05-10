---
title: "Azure Active Directory B2C: Adicionando um provedor do Azure AD usando políticas personalizadas | Microsoft Docs"
description: "Um tópico sobre as políticas personalizadas do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 31f0dfe5-1ad0-4a25-a53b-8acc71bcea72
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: 29d30cacb29fee1b2c5b8ef523051fa543bee829
ms.contentlocale: pt-br
ms.lasthandoff: 04/28/2017


---
# <a name="azure-active-directory-b2c-log-in-using-azure-ad-accounts"></a>Azure Active Directory B2C: Fazer logon usando contas do Azure AD

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar o logon para usuários de uma organização específica do Azure AD por meio do uso de [políticas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Pré-requisitos

Conclua todas as etapas que mostram como [começar a usar as políticas personalizadas](active-directory-b2c-get-started-custom.md).

Isso inclui:

1. Criar um locatário do Azure AD B2C.
1. Criar um aplicativo do Azure AD B2C.
1. Registrar dois aplicativos do mecanismo de políticas.
1. Configurar chaves.
1. Configurar o pacote de início.

## <a name="create-an-azure-ad-app"></a>Criar um aplicativo do Azure AD

Para habilitar o logon para usuários de uma organização específica do Azure AD, você precisa registrar um aplicativo no locatário organizacional do Azure AD.

>[!NOTE]
> Designaremos o locatário organizacional do Azure AD como `contoso.com` e o locatário do Azure AD B2C como `fabrikamb2c.onmicrosoft.com`.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Na barra superior, clique em sua conta e, na lista **Diretório**, escolha o locatário organizacional do Azure AD no qual você deseja registrar o aplicativo (ou seja, contoso.com).
1. Clique em **Mais Serviços** na barra de navegação à esquerda e pesquise **Registros do aplicativo**.
1. Escolha **Novo registro de aplicativo**.
1. Digite um **Nome** para o aplicativo (por exemplo, Aplicativo do Azure AD B2C)
1. Selecione **Aplicativo Web/API** como o Tipo de aplicativo.
1. Para a “URL de Logon”, insira a URL abaixo, em que o `{tenantName}` deve ser substituído pelo nome de seu locatário do Azure AD B2C (ou seja, fabrikamb2c.onmicrosoft.com).

    ```
    https://login.microsoftonline.com/te/{tenantName}.onmicrosoft.com/oauth2/authresp
    ```

1. Salve a **ID do Aplicativo**.
1. Clique no aplicativo recém-criado.
1. Na folha Configurações, clique em **Chaves**.
1. Crie uma nova chave e salve-a para a próxima seção.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Adicionar a chave do Azure AD ao Azure AD B2C

Você precisa armazenar a chave do aplicativo de `contoso.com` em seu locatário do Azure AD B2C. Para fazer isso:

1. Abra o PowerShell e navegue para o diretório de trabalho `active-directory-b2c-advanced-policies`.
1. Alterne para a pasta com a ferramenta ExploreAdmin.

    ```powershell
    cd active-directory-b2c-advanced-policies\ExploreAdmin
    ```

1. Importe a ferramenta ExploreAdmin para o PowerShell.

    ```powershell
    Import-Module .\ExploreAdmin.dll
    ```

1. No comando a seguir, substitua `tenantName` pelo nome do locatário do Azure AD B2C (por exemplo, fabrikamb2c.onmicrosoft.com), `SecretReferenceId` por um nome que será usado para referenciar o segredo (por exemplo, ContosoAppSecret) e `ClientSecret` pela chave do aplicativo de `contoso.com`. Execute o comando.

    ```PowerShell
    Set-CpimKeyContainer -Tenant {tenantName} -StorageReferenceId {SecretReferenceId} -UnencodedAsciiKey {ClientSecret}
    ```

    Quando você executar o comando, lembre-se de entrar com a conta do administrador de onmicrosoft.com local ao locatário do Azure AD B2C. Se você receber um erro indicando que “TokenSigningKeyContainer” não pode ser encontrado, leia o guia de [introdução](active-directory-b2c-get-started-custom.md) novamente.

1. Feche o PowerShell.

## <a name="add-a-claims-provider-in-your-base-policy"></a>Adicionar um provedor de declarações à política base

Para permitir que os usuários façam logon usando o Azure AD, você precisa definir o Azure AD como um provedor de declarações. Em outras palavras, você precisa especificar um ponto de extremidade com o qual o Azure AD B2C se comunicará. O ponto de extremidade *fornecerá* um conjunto de *declarações* que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado. Faça isso adicionando o Azure AD como um `<ClaimsProvider>` no arquivo de extensão da política.

1. Abra o arquivo de extensão no diretório de trabalho (TrustFrameworkExtensions.xml).
1. Localize a seção `<ClaimsProviders>`. Caso ela não exista, adicione-a sob o nó raiz.
1. Adicione um novo `<ClaimsProvider>` da seguinte maneira:

    ```XML
    <ClaimsProvider>
        <Domain>Contoso</Domain>
        <DisplayName>Login using Contoso</DisplayName>
        <TechnicalProfiles>
        <TechnicalProfile Id="ContosoProfile">
            <DisplayName>Contoso Employee</DisplayName>
            <Description>Login with your Contoso account</Description>
            <Protocol Name="OpenIdConnect"/>
            <OutputTokenFormat>JWT</OutputTokenFormat>
            <Metadata>
                <Item Key="METADATA">https://login.windows.net/contoso.com/.well-known/openid-configuration</Item>
                <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
                <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
                <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
                <Item Key="response_types">id_token</Item>
                <Item Key="UsePolicyInRedirectUri">false</Item>
            </Metadata>
            <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="ContosoAppSecret"/>
            </CryptographicKeys>
            <OutputClaims>
                <OutputClaim ClaimTypeReferenceId="userId" PartnerClaimType="oid"/>
                <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
                <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
                <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
                <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
                <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="contosoAuthentication" />
                <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
            </OutputClaims>
            <OutputClaimsTransformations>
                <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
                <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
                <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
                <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
            </OutputClaimsTransformations>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Sob o nó `<ClaimsProvider>`, atualize o valor de `<Domain>` para um valor exclusivo que pode ser usado para distinguir de outros provedores de identidade.
1. Sob o nó `<ClaimsProvider>`, atualize o valor de `<DisplayName>` para um nome amigável do provedor de declarações. Esse valor não é usado atualmente.

### <a name="update-the-technical-profile"></a>Atualizar o perfil técnico

Para obter um token do ponto de extremidade do Azure AD, você precisa definir os protocolos que o Azure AD B2C deve usar para se comunicar com o Azure AD. Isso é feito no elemento `<TechnicalProfile>` do `<ClaimsProvider>`.

1. Atualize a ID do nó `<TechnicalProfile>`. Essa ID é usada para se referir a esse perfil técnico em outras partes da política.
1. Atualize o valor de `<DisplayName>`. Esse valor será exibido no botão Fazer Logon na tela de logon.
1. Atualize o valor de `<Description>`.
1. O Azure AD usa o protocolo OpenID Connect; portanto, verifique se `<Protocol>` é “OpenIDConnect”.

Você precisa atualizar a seção `<Metdata>` no XML acima para que ela reflita as definições de configuração do locatário específico do Azure AD. No XML, atualize os valores de metadados da seguinte maneira:

1. Defina `<Item Key="METADATA">` como `https://login.windows.net/{tenantName}/.well-known/openid-configuration`, em que `tenantName` é o nome do locatário do Azure AD (por exemplo, contoso.com).
1. Abra o navegador e navegue para a URL `Metadata` que você acabou de atualizar.
1. No navegador, procure o objeto “emissor” e copie seu valor. Ele deverá ter a aparência `https://sts.windows.net/{tenantId}/`.
1. Cole o valor de `<Item Key="ProviderName">` no XML.
1. Defina `<Item Key="client_id">` como a `Application ID` no registro de aplicativo.
1. Defina `<Item Key="IdTokenAudience">` como a `Application ID` no registro de aplicativo.
1. Verifique se `<Item Key="response_types">` está definido como `id_token`.
1. Verifique se `<Item Key="UsePolicyInRedirectUri">` está definido como `false`.

Você também precisa vincular o [segredo do Azure AD que você registrou no locatário do Azure AD B2C](#add-the-azure-ad-key-to-azure-ad-b2c) ao `<ClaimsProvider>` do Azure AD.

1. Na seção `<CryptographicKeys>` do XML acima, atualize o valor de `StorageReferenceId` para a ID de referência do segredo definido (por exemplo, ContosoAppSecret).

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

A essa altura, você já terá configurado a política, de forma que o Azure AD B2C saiba como se comunicar com o diretório do Azure AD. Tente carregar o arquivo de extensão da política apenas para confirmar se ele não apresenta problemas até o momento. Para fazer isso:

1. Acesse a folha **Todas as Políticas** no locatário do Azure AD B2C.
1. Marque a caixa **Substituir a política caso ela exista**.
1. Carregue o arquivo de extensão (TrustFrameworkExtensions.xml) e garanta que ele é aprovado na validação.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Registrar o provedor de declarações do Azure AD em um Percurso do Usuário

Agora você precisa adicionar o provedor de identidade do Azure AD em um dos percursos do usuário. Neste ponto, o provedor de identidade foi definido, mas não está disponível em nenhuma das telas de inscrição/conexão. Para fazer isso, criaremos uma duplicata de um modelo de percurso do usuário existente e, depois, o modificaremos para que ele também tenha o provedor de identidade do Azure AD.

1. Abra o arquivo base da política (por exemplo, TrustFrameworkBase.xml)
1. Localize o elemento `<UserJourneys>` e copie todo o `<UserJourney>` com Id=“SignUpOrSignIn”.
1. Abra o arquivo de extensão (por exemplo, TrustFrameworkExtensions.xml) e localize o elemento `<UserJourneys>`. Se o elemento não existir, adicione um.
1. Cole todo o `<UserJourney>` copiado como um filho do elemento `<UserJourneys>`.
1. Renomeie a ID do novo `<UserJourney>` (ou seja, SignUpOrSignUsingContoso).

### <a name="display-the-button"></a>Exibir o “botão”

O elemento `<ClaimsProviderSelection>` é análogo a um botão de provedor de identidade em uma tela de inscrição/conexão. Ao adicionar um elemento `<ClaimsProviderSelection>` do Azure AD, um novo botão será exibido quando um usuário chegar à página. Para fazer isso:

1. Encontre a `<OrchestrationStep>` com `Order="1"` no `<UserJourney>` que você acabou de criar.
1. Adicione o seguinte:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Defina `TargetClaimsExchangeId` com um valor apropriado. Recomendamos seguir a mesma convenção que os outros – *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Vincular o “botão” a uma ação

Agora que você implementou um “botão”, precisará vinculá-lo a uma ação. Nesse caso, a ação destina-se a que o Azure AD B2C se comunique com o Azure AD para receber um token. Faça isso vinculando o perfil técnico ao provedor de declarações do Azure AD.

1. Encontre a `<OrchestrationStep>` com `Order="2"` no nó `<UserJourney>`
1. Adicione o seguinte:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. Atualize `Id` para o mesmo valor que `TargetClaimsExchangeId` acima.
1. Atualize `TechnicalProfileReferenceId` para o `Id` do perfil técnico você criou anteriormente (por exemplo, ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Carregar o arquivo de extensão atualizado

Agora você concluiu a modificação do arquivo de extensão. Salve e carregue esse arquivo e verifique se todas as validações são bem-sucedidas.

### <a name="update-the-rp-file"></a>Atualizar o arquivo RP

Agora, você precisa atualizar o arquivo RP que iniciará o percurso do usuário que você acabou de criar.

1. Faça uma cópia do SignUpOrSignIn.xml no diretório de trabalho e renomeie-o (por exemplo, SignUpOrSignInWithAAD.xml).
1. Abra o novo arquivo e atualize o atributo `PolicyId` de `<TrustFrameworkPolicy>` com um valor exclusivo. Esse será o nome da política (por exemplo, SignUpOrSignInWithAAD).
1. Modifique o atributo `ReferenceId` em `<DefaultUserJourney>` para que ele corresponda à ID do novo percurso do usuário criado (por exemplo, SignUpOrSignUsingContoso).
1. Salve as alterações e carregue o arquivo.

## <a name="troubleshooting"></a>Solucionar problemas

Teste a política personalizada que você acabou de carregar abrindo sua folha e clicando em “Executar agora”. Se algo falhar, consulte como [resolver problemas](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Próximas etapas
 
Envie seus comentários para AADB2CPreview@microsoft.com.


