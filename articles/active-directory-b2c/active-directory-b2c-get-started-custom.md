---
title: Introdução às políticas personalizadas - Azure Active Directory B2C | Microsoft Docs
description: Saiba como começar a usar com políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b414529d7756812f1e1e16d2d0184c8472c0c55f
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916743"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Introdução às políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

As [políticas personalizadas](active-directory-b2c-overview-custom.md) são arquivos de configuração que definem o comportamento do locatário do Azure AD (Azure Active Directory) B2C. Neste artigo, você criará uma política personalizada que dá suporte para inscrição ou entrada de conta local usando um endereço de email e uma senha. Você também pode preparar o ambiente para adicionar provedores de identidade.

## <a name="prerequisites"></a>Pré-requisitos

- Se você ainda não tiver um, você precisará [criar um locatário do Azure AD B2C](tutorial-create-tenant.md) que esteja vinculado à sua assinatura do Azure.
- [Registrar seu aplicativo](tutorial-register-applications.md) no locatário que você criou para que ele possa se comunicar com o Azure AD B2C.

## <a name="add-signing-and-encryption-keys"></a>Adicionar chaves de criptografia e de assinatura

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
2. Verifique se que você estiver usando o diretório que contém o seu locatário do Azure AD B2C. Clique o **filtro de diretório e assinatura** no menu superior e escolher o diretório que contém o seu locatário. 
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Na página Visão Geral, selecione **Identity Experience Framework – VERSÃO PRÉVIA**.

### <a name="create-the-signing-key"></a>Criar a chave de assinatura

1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
2. Para **Opções**, escolha `Generate`.
3. Em **Nome**, insira `TokenSigningKeyContainer`. O prefixo `B2C_1A_` pode ser adicionado automaticamente.
4. Para **Tipo de chave**, selecione **RSA**.
5. Para **Uso de chave**, selecione **Assinatura**.
6. Clique em **Criar**.

### <a name="create-the-encryption-key"></a>Criar a chave de criptografia

1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
2. Para **Opções**, escolha `Generate`.
3. Em **Nome**, insira `TokenEncryptionKeyContainer`. O prefixo `B2C_1A`_ pode ser adicionado automaticamente.
4. Para **Tipo de chave**, selecione **RSA**.
5. Para o **Uso da chave**, selecione **Criptografia**.
6. Clique em **Criar**.

### <a name="create-the-facebook-key"></a>Criar a chave do Facebook

Se você já tiver um [segredo do aplicativo do Facebook](active-directory-b2c-setup-fb-app.md), adicione-o como uma chave de política em seu locatário. Caso contrário, você deve criar a chave com um valor de espaço reservado para que suas políticas passem na validação.

1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
2. Para **Opções**, escolha `Manual`.
3. Para **Nome**, insira `FacebookSecret`. O prefixo `B2C_1A_` pode ser adicionado automaticamente.
4. Em **Segredo**, digite o segredo do Facebook de developers.facebook.com ou `0` como um espaço reservado. Esse valor é o segredo, não a ID do aplicativo.
5. Para **Uso de chave**, selecione **Assinatura**.
6. Clique em **Criar**.

## <a name="register-identity-experience-framework-applications"></a>Registrar aplicativos do Identity Experience Framework

O Azure AD B2C exige o registro de dois aplicativos que são usados para a inscrição e entrada de usuários: IdentityExperienceFramework (um aplicativo Web) e ProxyIdentityExperienceFramework (um aplicativo nativo) com permissão delegada do aplicativo IdentityExperienceFramework. As contas locais só existem em seu locatário. Seus usuários entram com uma combinação exclusiva de endereço de email/senha para acessar seus aplicativos registrados por locatário.

### <a name="register-the-identityexperienceframework-application"></a>Registrar o aplicativo IdentityExperienceFramework

1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
2. Selecione **Novo registro de aplicativo**.
3. Para **Nome**, insira `IdentityExperienceFramework`.
4. Para **Tipo de aplicativo**, escolha **Aplicativo Web/API**.
5. Para **URL de logon**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, em que `your-tenant-name` é o nome de domínio de locatário do Azure AD B2C.
6. Clique em **Criar**. 
7. Após a criação, copie a ID do aplicativo e salve-a para uso posterior.

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrar o aplicativo ProxyIdentityExperienceFramework

1. Selecione **Registros de aplicativo** e selecione **Novo registro de aplicativo**.
2. Para **Nome**, insira `ProxyIdentityExperienceFramework`.
3. Para **Tipo de aplicativo**, escolha **Nativo**.
4. Para **URI de redirecionamento**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, em que `yourtenant` é o seu locatário do Azure AD B2C.
5. Clique em **Criar**. Após a criação, copie a ID do aplicativo e salve-a para uso posterior.
6. Na página Configurações, selecione **Permissões necessárias** e, em seguida, selecione **Adicionar**.
7. Escolher **selecionar uma API**, pesquise e selecione **IdentityExperienceFramework**e, em seguida, clique em **selecione**.
9. Marque a caixa de seleção ao lado de **Acessar IdentityExperienceFramework**, clique em **Selecionar** e, em seguida, clique em **Concluído**.
10. Selecione **Conceder Permissões** e, em seguida, confirme selecionando **Sim**.

## <a name="download-starter-pack-and-modify-policies"></a>Baixar o pacote de início e modificar políticas

Políticas personalizadas são um conjunto de arquivos XML que precisam ser carregados no locatário do Azure AD B2C. Pacotes de arquivos iniciais são fornecidos para ajudá-lo a começar a trabalhar rapidamente. Cada pacote de início da lista a seguir contém o menor número de perfis de técnicos e jornadas do usuário necessárias para alcançar os cenários descritos:

- LocalAccounts – permite o uso apenas de contas locais.
- SocialAccounts – permite o uso apenas de contas sociais (ou federadas).
- SocialAndLocalAccounts – permite o uso de contas locais e contas sociais.
- SocialAndLocalAccountsWithMFA – permite opções locais, sociais e de Autenticação Multifator.

Cada pacote de início contém:

- O arquivo base. São necessárias algumas modificações na base.
- O arquivo de extensão.  Esse arquivo é o local em que a maioria das alterações de configuração é feita.
- Os arquivos de terceira parte confiável. Os arquivos específicos da tarefa, chamados pelo seu aplicativo.

>[!NOTE]
>Caso seu editor de XML dê suporte à validação, valide os arquivos em relação ao esquema XML TrustFrameworkPolicy_0.3.0.0.xsd, localizado no diretório raiz do pacote de início. A validação de esquema XML identifica erros antes do upload.

1. [Baixe o arquivo .zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou execute:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

2. Na pasta SocialAndLocalAccounts, edite todos os arquivos substituindo `yourtenant` pelo nome do locatário. Por exemplo, `contosoTenant.onmicrosoft.com`. Se você precisar de um editor de XML, [experimente o Visual Studio Code](https://code.visualstudio.com/download), um editor de plataforma cruzada leve.

### <a name="add-application-ids-to-the-custom-policy"></a>Adicionar IDs de aplicativo à política personalizada

Adicione as IDs de aplicativo ao arquivo de extensões *TrustFrameworkExtensions.xml*.

1. Abra o arquivo *TrustFrameworkExtensions.xml* e localize o elemento `<TechnicalProfile Id="login-NonInteractive">`.
2. Substitua ambas as instâncias de `IdentityExperienceFrameworkAppId` pela ID do aplicativo do aplicativo Identity Experience Framework criado anteriormente. Substitua ambas as instâncias de `ProxyIdentityExperienceFrameworkAppId` pela ID do aplicativo do aplicativo Estrutura de Experiência de Identidade de Proxy que você criou anteriormente.
3. Salve o arquivo de extensões.

## <a name="upload-the-policies"></a>Fazer upload das políticas

1. Na página Políticas Personalizadas do Identity Experience Framework, selecione **Fazer Upload da Política**.
1. Nesta ordem, faça upload *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* e *PasswordReset.xml*. Quando um arquivo é carregado, o nome do arquivo de política é precedido por `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Na página Políticas Personalizadas, selecione **B2C_1A_signup_signin**.
2. Para **selecione o aplicativo** na página de visão geral da política personalizada, selecione o aplicativo web chamado *webapp1* que você registrou anteriormente. Certifique-se de que o **URL de resposta** é `https://jwt.ms`.
3. Selecione **Executar Agora**.
4. Você deverá conseguir se inscrever usando um endereço de email.
5. Entre com a mesma conta para confirmar que você tem a configuração correta.

## <a name="add-facebook-as-an-identity-provider"></a>Adicionar o Facebook como um provedor de identidade

1. Configurar um [aplicativo do Facebook](active-directory-b2c-setup-fb-app.md).
2. No arquivo *TrustFrameworkExtensions.xml*, substitua o valor de `client_id` pela ID do aplicativo do Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
3. Faça upload do arquivo *TrustFrameworkExtensions.xml* no seu locatário.
4. Teste usando **Executar Agora** ou invocando a política diretamente do seu aplicativo registrado.

## <a name="next-steps"></a>Próximas etapas

- Adicionar o Azure Active Directory como um provedor de identidade. O arquivo base usado neste guia de introdução já contém uma parte do conteúdo de que você precisa para adicionar outros provedores de identidade. Para obter informações sobre como configurar entradas, confira o artigo [Configurar inscrição e entrada com uma conta do Azure Active Directory usando políticas personalizadas do Active Directory B2C](active-directory-b2c-setup-aad-custom.md).
