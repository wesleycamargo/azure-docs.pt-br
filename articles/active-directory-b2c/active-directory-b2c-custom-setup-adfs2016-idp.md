---
title: Adicionar ADFS como um provedor de identidade SAML usando políticas personalizadas no Azure Active Directory B2C | Microsoft Docs
description: Configurar o ADFS 2016 usando o protocolo SAML e as políticas personalizadas no Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2c2e6861fda42a9e8c1aabcba303bfede47ac3c1
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669219"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Adicionar ADFS como um provedor de identidade SAML usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada de uma conta de usuário do ADFS usando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure AD (Azure Active Directory) B2C.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas no artigo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Adicionar a chave de aplicativo da conta do ADFS ao Azure AD B2C

A federação com uma conta do ADFS exige que um segredo do cliente da conta confie no Azure AD B2C em nome do aplicativo. Você precisa armazenar o certificado ADFS em seu locatário do Azure AD B2C. 

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C alternando para ele no canto superior direito do portal do Azure. Selecione **Mudar de Diretório**, e, em seguida, escolha o diretório que contém o locatário que você criou. Neste tutorial, o diretório *contoso* é usado e contém o locatário nomeado *contoso0522Tenant.onmicrosoft.com*.

    ![Mudar diretórios](./media/active-directory-b2c-custom-setup-adfs2016-idp/switch-directories.png)

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**. Agora, você deve estar usando o locatário.
4. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
5. Selecione **Chaves da Política** para exibir as chaves disponíveis no locatário e clique em **Adicionar**.
6. Escolha **Upload** como a Opção.
7. Insira `ADFSSamlCert` para o nome. O prefixo `B2C_1A_` pode ser adicionado automaticamente.
8. Procure e selecione o arquivo .pfx do certificado com a chave privada. Este certificado com a chave privada deve ser o mesmo que foi emitido e usado para a terceira parte confiável do ADFS.
9. Clique em **Criar** e confirme que você criou a chave `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Adicionar um provedor de declarações à política de extensão

Se você quiser que os usuários entrem usando uma conta do ADFS, será necessário definir a conta como um provedor de declarações. Você faz isso, especificando um ponto de extremidade com o qual o Azure AD B2C comunica-se. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Defina o ADFS como um provedor de declarações, adicionando o elemento **ClaimsProvider** ao arquivo de política de extensão.

1. Abra o arquivo de políticas *TrustFrameworkExtensions.xml* no diretório de trabalho. Se um editor de XML for necessário, [experimente o Visual Studio Code](https://code.visualstudio.com/download), que é um editor de plataforma cruzada leve.
2. Adicione o seguinte XML no elemento **ClaimsProviders** e substitua **your-ADFS-domain** pelo nome de domínio do ADFS e substitua o valor da declaração de saída **identityProvider** pelo DNS (Valor arbitrário que indica o domínio) e salve o arquivo. 

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

## <a name="register-the-claims-provider-for-sign-up-and-sign-in"></a>Registrar o provedor de declarações para inscrever-se e entrar

Para disponibilizar o provedor de identidade da conta do ADFS nas páginas de inscrição e de entrada, é necessário adicioná-lo ao percurso do usuário **SignUpOrSignIn**. 

Faça uma cópia de um percurso do usuário de modelo existente e, em seguida, modifique-o para incluir o provedor de identidade do ADFS:

>[!NOTE]
>Se você copiou anteriormente o elemento **UserJourneys** do arquivo base da política para o arquivo de extensão (*TrustFrameworkExtensions.xml*), você poderá ignorar essa seção.

1. Abra o arquivo base da sua política. Por exemplo, *TrustFrameworkBase.xml*.
2. Copie todo o conteúdo do elemento **UserJourneys**.
3. Abra o arquivo de extensão (*TrustFrameworkExtensions.xml*) e cole todo o conteúdo do elemento **UserJourneys** que você copiou no arquivo de extensão.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelections** define a lista de seleções do provedor de declarações e a ordem.  O elemento **ClaimsProviderSelection** é análogo a um botão do provedor de identidade em uma página de inscrição e de entrada. Se você adicionar um elemento **ClaimsProviderSelection** para uma conta do ADFS, um novo botão será exibido quando um usuário visualizar a página. Para adicionar este elemento:

1. No elemento **UserJourney** com um identificador de `SignUpOrSignIn` nos percursos do usuário que você copiou, localize o elemento **OrchestrationStep** de `Order="1"`.
2. Adicione o seguinte elemento **ClaimsProviderSelection** ao elemento **ClaimsProviderSelections**:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação destina-se a que o Azure AD B2C se comunique com a conta do ADFS para receber um token. Vincule o botão a uma ação vinculando o perfil técnico ao provedor de declarações da conta do ADFS:

1. Localize o **OrchestrationStep** de `Order="2"` no elemento **UserJourney**.
2. Adicione o seguinte elemento **ClaimsExchange** ao elemento **ClaimsExchanges**:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

> [!NOTE]
> * Certifique-se de que `Id` tenha o mesmo valor que `TargetClaimsExchangeId` na seção anterior.
> * Certifique-se de que `TechnicalProfileReferenceId` esteja definido para o perfil técnico que você criou anteriormente (Contoso-SAML2).


## <a name="optional-register-the-claims-provider-for-profile-edit"></a>[Opcional] Registre o provedor de declarações para edição de perfil

Também é possível adicionar o provedor de identidade da conta do ADFS ao percurso do usuário da edição de perfil.

### <a name="display-the-button"></a>Exibir o botão

1. Abra o arquivo de extensão da sua política. Por exemplo, *TrustFrameworkExtensions.xml*.
2. No elemento **UserJourney** com um identificador `ProfileEdit` nos percursos do usuário que você copiou, localize o elemento **OrchestrationStep** de `Order="1"`.
3. Adicione o seguinte elemento **ClaimsProviderSelection** ao elemento **ClaimsProviderSelections**:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

1. Localize o **OrchestrationStep** de `Order="2"` no elemento **UserJourney**.
2. Adicione o seguinte elemento **ClaimsExchange** ao elemento **ClaimsExchanges**:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

## <a name="upload-the-policy-to-your-tenant"></a>Carregar a política ao seu locatário

1. No portal do Azure, selecione **Todas as políticas**.
2. Selecione **Carregar Política**.
3. Habilite **Substituir a política, se existir**.
4. Procure e selecione o arquivo de política *TrustFrameworkExtensions.xml* e, em seguida, selecione **Upload**. Certifique-se de que a validação seja realizada com êxito.


## <a name="configure-an-adfs-relying-party-trust"></a>Configurar uma Confiança de Terceira Parte Confiável do ADFS

Para usar o ADFS como um provedor de identidade no Azure AD B2C, é necessário criar uma confiança de terceira parte confiável do ADFS com os metadados SAML do Azure AD B2C. O exemplo a seguir mostra um endereço de URL para os metadados do SAML de um perfil técnico do Azure AD B2C:

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Substitua os valores a seguir:

- **your-tenant** pelo nome do locatário, como your-tenant.onmicrosoft.com.
- **your-policy** pelo nome da política. Use a política em que você configura o perfil técnico do provedor SAML ou uma política que herda dessa política.
- **your-technical-profile** pelo nome do perfil técnico do provedor de identidade SAML.
 
Abra um navegador e navegue até a URL. Certifique-se de digitar a URL correta e ter acesso ao arquivo de metadados XML.

Para adicionar uma nova confiança de terceira parte confiável usando o snap-in Gerenciamento do ADFS e definir manualmente as configurações, execute o procedimento a seguir em um servidor de federação. Associação em **Administradores** ou equivalente no computador local é o mínimo necessário para concluir esse procedimento. Examine os detalhes sobre como usar as contas apropriadas e associações de grupos em [Grupos Padrão Locais e de Domínio](http://go.microsoft.com/fwlink/?LinkId=83477).

1. No Gerenciador do Servidor, selecione **Ferramentas** e, em seguida, selecione **Gerenciamento do ADFS**.
2. Selecione **Adicionar Relação de Confiança de Terceira Parte Confiável**.
3. Na **Página inicial**, escolha **Com reconhecimento de declaração** e, em seguida, clique em **Iniciar**.
4. Na página **Selecionar Fonte de Dados**, selecione **Importar dados sobre a terceira parte confiável publicados online ou em uma rede local**, forneça a URL de metadados do Azure AD B2C e clique em **Avançar**.
5. Na página **Especificar Nome para Exibição**, insira um **Nome de exibição**, em **Notas**, insira uma descrição para essa confiança de terceira parte confiável e clique em **Avançar**.
6. Na página **Escolher Política de Controle de Acesso**, selecione uma política e, em seguida, clique em **Avançar**.
7. Na página **Pronto para Adicionar Objeto de Confiança**, examine as configurações e, em seguida, clique em **Avançar** para salvar as informações de seu objeto de confiança de terceira parte confiável.
8. Na página **Concluir**, clique em **Fechar**, essa ação exibe automaticamente a caixa de diálogo **Editar Regras de Declaração**.
9. Selecione **Adicionar Regra**.  
10. Em **Modelo de regra de declaração**, selecione **Enviar atributos do LDAP como declarações**.
11. Forneça um **nome de Regra de declaração**. Para o **Repositório de atributos**, selecione **Selecionar Active Directory**, adicione as seguintes declarações e clique em **Concluir** e **OK**.

    ![Definir propriedades de regra](./media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)

12.  Com base no tipo de certificado, talvez seja necessário definir o algoritmo de HASH. Na janela de propriedades de confiança de terceira parte confiável (Demonstração B2C), selecione a guia **Avançado** e altere o **Secure Hash Algorithm** para `SHA-1` ou `SHA-256` e clique em **Ok**.  

### <a name="update-the-relying-party-metadata"></a>Atualizar os metadados de terceira parte confiável

Alterar o perfil técnico do SAML exige que você atualize o ADFS com a versão de metadados atualizada. Não é necessário atualizar os metadados ao criar o aplicativo de terceira parte confiável, mas, ao fazer uma alteração, atualize os metadados no ADFS.

1. No Gerenciador do Servidor, selecione **Ferramentas** e, em seguida, selecione **Gerenciamento do ADFS**.
2. Selecione a terceira parte confiável que você criou, selecione **Atualização dos metadados da federação** e, em seguida, clique em **Atualizar**. 

### <a name="test-the-policy-by-using-run-now"></a>Teste a política usando Executar Agora

1.  Abra as **Configurações do Azure AD B2C** e acesse a **Estrutura de Experiência de Identidade**.
2.  Abra **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) que você carregou. Selecione **Executar Agora**. Você deve conseguir entrar usando a conta do ADFS.

## <a name="download-the-complete-policy-files"></a>Baixar os arquivos da política completa

Opcional: é possível criar o cenário usando seus próprios arquivos de política personalizada, após concluir as etapas em [Introdução às Políticas Personalizadas](active-directory-b2c-get-started-custom.md). Por exemplo, arquivos, consulte [Arquivos de exemplo de política apenas para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app).
