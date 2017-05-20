---
title: "Azure Active Directory B2C: Adição de um provedor SAML do Salesforce usando políticas personalizadas | Microsoft Docs"
description: "Um tópico sobre as políticas personalizadas do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: gsacavdm
ms.assetid: d7f4143f-cd7c-4939-91a8-231a4104dc2c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/30/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: dee24deacbe69ada64519802c0eb1b83f565f57e
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017


---
# <a name="azure-active-directory-b2c-log-in-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C: Fazer logon usando contas do Salesforce via SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar o logon para usuários de uma organização específica do Salesforce por meio do uso de [políticas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-ad-b2c-setup"></a>Instalação do Azure AD B2C
Conclua todas as etapas que mostram como [começar a usar as políticas personalizadas](active-directory-b2c-get-started-custom.md).

Isso inclui:

1. Criar um locatário do Azure AD B2C.
1. Criar um aplicativo do Azure AD B2C.
1. Registrar dois aplicativos do mecanismo de políticas.
1. Configurar chaves.
1. Configurar o pacote de início.

### <a name="salesforce-setup"></a>Configuração do Salesforce
Este tutorial presume que você já tenha:
1. Inscreveu-se em uma conta do Salesforce. Você pode se inscrever em uma [Developer Edition gratuita](https://developer.salesforce.com/signup). 
1. [Configurar Meu Domínio](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) para sua organização do Salesforce.

## <a name="get-the-salesforce-saml-metadata"></a>Obter os metadados SAML do Salesforce
>[!NOTE]
> Este tutorial presume que você esteja usando a [Salesforce Lighting Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Logon no Salesforce](https://login.salesforce.com/) 
1. No menu à esquerda, em **Configurações**, expanda **Identidade** e clique em **Provedor de Identidade**
1. Clique em **Habilitar Provedor de Identidade**
1. **Selecione o certificado** você deseja que o Salesforce use ao se comunicar com o Azure AD B2C e clique em **Salvar**. Você pode usar o certificado padrão.
1. Clique no agora disponível botão **Baixar Metadados** e salve o arquivo de metadados que você usará em uma etapa posterior.

## <a name="add-a-saml-signing-certificate-to-azure-ad-b2c"></a>Adicionar um certificado de assinatura SAML ao Azure AD B2C
Você precisa armazenar e carregar o certificado do Salesforce em seu locatário do Azure AD B2C. Para fazer isso:

1. Abra o PowerShell e navegue para o diretório de trabalho `active-directory-b2c-advanced-policies`.
1. Alterne para a pasta com a ferramenta ExploreAdmin.

    ```powershell
    cd active-directory-b2c-advanced-policies\ExploreAdmin
    ```

1. Importe a ferramenta ExploreAdmin para o PowerShell.

    ```powershell
    Import-Module .\ExploreAdmin.dll
    ```

1. O comando a seguir, substitua `tenantName` pelo nome do seu locatário do Azure AD B2C (por exemplo, fabrikamb2c.onmicrosoft.com), `certificateId` por um nome para o certificado que será usado para fazer referência a ele na política posteriormente (por exemplo, ContosoSalesforceCert) e, finalmente, `pathToCert` e `password` pelo caminho e a senha do certificado. Execute o comando.

    ```PowerShell
    Set-CpimCertificate -TenantId {tenantName} -CertificateId {certificateId} -CertificateFileName {pathToCert} - CertificatePassword {password}
    ```

    Quando você executar o comando, lembre-se de entrar com a conta do administrador de onmicrosoft.com local ao locatário do Azure AD B2C. 

1. Feche o PowerShell.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Criar o provedor de declarações SAML do Salesforce em sua política base

Para permitir que os usuários façam logon usando o Salesforce, você precisa definir o Salesforce como um provedor de declarações. Em outras palavras, você precisa especificar o ponto de extremidade com o qual o Azure AD B2C se comunicará. O ponto de extremidade *fornecerá* um conjunto de *declarações* que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado. Faça isso adicionando um `<ClaimsProvider>` ao Salesforce no arquivo de extensão da política.

1. Abra o arquivo de extensão no diretório de trabalho (TrustFrameworkExtensions.xml).
1. Localize a seção `<ClaimsProviders>`. Caso ela não exista, adicione-a sob o nó raiz.
1. Adicione um novo `<ClaimsProvider>` da seguinte maneira:

    ```XML
    <ClaimsProvider>
      <Domain>contoso</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">
    <![CDATA[ <md:EntityDescriptor xmlns:md="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://contoso.com" validUntil="2026-10-05T23:57:13.854Z" xmlns:ds="http://www.w3.org/2000/09/xmldsig#"><md:IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"><md:KeyDescriptor use="signing"><ds:KeyInfo><ds:X509Data><ds:X509Certificate>MIIErDCCA….qY9SjVXdu7zy8tZ+LqnwFSYIJ4VkE9UR1vvvnzO</ds:X509Certificate></ds:X509Data></ds:KeyInfo></md:KeyDescriptor><md:NameIDFormat>urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified</md:NameIDFormat><md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://contoso.com/idp/endpoint/HttpPost"/><md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://contoso.com/idp/endpoint/HttpRedirect"/></md:IDPSSODescriptor></md:EntityDescriptor>]]>
            </Item>
          </Metadata>       
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="ContosoIdpSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="ContosoIdpSamlCert "/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
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

Para obter um token SAML do Salesforce, você precisa definir os protocolos que o Azure AD B2C deve usar para se comunicar com o Azure AD. Isso é feito no elemento `<TechnicalProfile>` do `<ClaimsProvider>`.

1. Atualize a ID do nó `<TechnicalProfile>`. Essa ID é usada para se referir a esse perfil técnico em outras partes da política.
1. Atualize o valor de `<DisplayName>`. Esse valor será exibido no botão Fazer Logon na tela de logon.
1. Atualize o valor de `<Description>`.
1. O Azure AD usa o protocolo OpenID Connect; portanto, verifique se `<Protocol>` é “SAML2”.

Você precisa atualizar a seção `<Metadata>` no XML acima para que ela reflita as definições de configuração do locatário específico do Azure AD. No XML, atualize os valores de metadados da seguinte maneira:

1. Atualize o valor do `<Item Key="PartnerEntity">` com o conteúdo do arquivo Metadata.xml baixado do Salesforce. **Encapsule-o com <![CDATA[ …metadata… ]]>**.

1. Na seção `<CryptographicKeys>` no XML acima, atualize o valor para ambos `StorageReferenceId` para a ID de certificado que você definiu (por exemplo, ContosoSalesforceCert).

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

A essa altura, você já terá configurado a política, de forma que o Azure AD B2C saiba como se comunicar com o diretório do Azure AD. Tente carregar o arquivo de extensão da política apenas para confirmar se ele não apresenta problemas até o momento. Para fazer isso:

1. Acesse a folha **Todas as Políticas** no locatário do Azure AD B2C.
1. Marque a caixa **Substituir a política caso ela exista**.
1. Carregue o arquivo de extensão (TrustFrameworkExtensions.xml) e garanta que ele é aprovado na validação.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Registrar o provedor de declarações SAML do Salesforce em um Percurso do Usuário

Agora você precisa adicionar o provedor de identidade SAML do Salesforce em um dos percursos do usuário. Neste ponto, o provedor de identidade foi definido, mas não está disponível em nenhuma das telas de inscrição/conexão. Para fazer isso, criaremos uma duplicata de um modelo de percurso do usuário existente e, depois, o modificaremos para que ele também tenha o provedor de identidade do Azure AD.

1. Abra o arquivo base da política (por exemplo, TrustFrameworkBase.xml)
1. Localize o elemento `<UserJourneys>` e copie todo o `<UserJourney>` com Id=“SignUpOrSignIn”.
1. Abra o arquivo de extensão (por exemplo, TrustFrameworkExtensions.xml) e localize o elemento `<UserJourneys>`. Se o elemento não existir, adicione um.
1. Cole todo o `<UserJourney>` copiado como um filho do elemento `<UserJourneys>`.
1. Renomeie a ID do novo `<UserJourney>` (ou seja, SignUpOrSignUsingContoso).

### <a name="display-the-button"></a>Exibir o “botão”

O elemento `<ClaimsProviderSelection>` é análogo a um botão de provedor de identidade em uma tela de inscrição/conexão. Ao adicionar um elemento `<ClaimsProviderSelection>` ao Salesforce, um novo botão será exibido quando um usuário chegar à página. Para fazer isso:

1. Encontre a `<OrchestrationStep>` com `Order="1"` no `<UserJourney>` que você acabou de criar.
1. Adicione o seguinte:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Defina `TargetClaimsExchangeId` com um valor apropriado. Recomendamos seguir a mesma convenção que os outros – *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Vincular o “botão” a uma ação

Agora que você implementou um “botão”, precisará vinculá-lo a uma ação. Nesse caso, a ação destina-se a que o Azure AD B2C se comunique com o Salesforce para receber um token SAML. Faça isso vinculando o perfil técnico ao provedor de declarações SAML do Salesforce.

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

## <a name="create-a-connected-app-in-salesforce"></a>Criar um aplicativo conectado no Salesforce
Você precisará registrar o Azure AD B2C como um Aplicativo Conectado no Salesforce.

1. [Logon no Salesforce](https://login.salesforce.com/) 
1. No menu à esquerda, em **Configurações**, expanda **Identidade** e clique em **Provedor de Identidade**
1. Na seção **Provedores de Serviço** na parte inferior, clique em **Os Provedores de Serviço agora são criados por meio de Aplicativos Conectados. Clique aqui.**
1. Forneça as **Informações Básicas** necessárias para seu Aplicativo Conectado.
1. Agora, na seção **Configurações do Aplicativo Web**:
    1. Marque **Habilitar SAML**
    1. Insira a seguinte URL no campo **ID da Entidade** e substitua o `tenantName`. 
    
        ```
        https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_base
        ```

    1. Insira a seguinte URL no campo **URL do ACS** e substitua o `tenantName`. 
        ```
        https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_base/samlp/sso/assertionconsumer
        ```

    1. Deixe todas as outras configurações com seus valores padrão
1. Role até a parte inferior e clique no botão **Salvar**


## <a name="troubleshooting"></a>Solucionar problemas

Teste a política personalizada que você acabou de carregar abrindo sua folha e clicando em “Executar agora”. Se algo falhar, consulte como [resolver problemas](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Próximas etapas
 
Envie seus comentários para [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).


