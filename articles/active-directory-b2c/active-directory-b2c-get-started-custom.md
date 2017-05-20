---
title: "Azure Active Directory B2C: Introdução às políticas personalizadas | Microsoft Docs"
description: "Como começar a usar as políticas personalizadas do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: joroja;parahk;gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: b72db6a0eb8a8621be5f05da6028615d5d24ba1e
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017

---
# <a name="azure-active-directory-b2c-getting-started-with-custom-policies"></a>Azure Active Directory B2C: Introdução às políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Depois de concluir as etapas descritas neste artigo, a política personalizada dará suporte à inscrição ou conexão da “conta local” usando um email e uma senha. Você também preparará o ambiente para adicionar outros provedores de identidade (como Facebook ou Azure AD).  A conclusão dessas etapas é necessária para que todos os outros usos do Identity Experience Framework do Azure AD B2C e vários conceitos básicos sejam apresentados.

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, verifique se você tem um locatário do Azure AD B2C. Um locatário do Azure AD B2C é um contêiner de todos os usuários, aplicativos, políticas e muito mais. Se você ainda não tiver um locatário, precisará [criar um](active-directory-b2c-get-started.md). Recomendamos que todos os desenvolvedores concluam os passo a passos de política interna do Azure AD B2C e configurem seu aplicativo com políticas internas antes de continuar.  Seus aplicativos funcionarão com dois tipos de políticas com uma simples alteração para o nome da política para invocar a política personalizada.

O acesso à edição da política personalizada requer uma assinatura válida do Azure vinculada ao seu locatário.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Adicionar chaves de assinatura e criptografia ao seu locatário do B2C para uso por Políticas Personalizadas

1. Navegue até a folha Identity Experience Framework em suas configurações de locatário do Azure AD B2C.
2. Selecione **Chaves de Política** para exibir as chaves disponíveis no seu locatário.
3. Crie `B2C_1A_TokenSigningKeyContainer` se ele não existir:
 * Clique em **Adicionar**
 * Opções > `Generate`
 * Nome >`TokenSigningKeyContainer` O prefixo B2C_1A_ pode ser adicionado automaticamente.
 * Tipo de chave > `RSA`
 * Datas - usar valores padrão
 * Uso de chave > `Signature`
 * Clique em **Criar**
4. Crie `B2C_1A_TokenEncryptionKeyContainer` se ele não existir:
 * Clique em **Adicionar**
 * Opções > `Generate`
 * Nome >`TokenEncryptionKeyContainer` O prefixo B2C_1A_ pode ser adicionado automaticamente.
 * Tipo de chave > `RSA`
 * Datas - usar valores padrão
 * Uso de chave > `Encryption`
 * Clique em **Criar**
5. Crie `B2C_1A_FacebookSecret`. Se você já tiver um segredo do aplicativo Facebook, adicione-o como uma chave de política para o seu locatário.  Caso contrário, você deve criar a chave com valor de espaço reservado para que suas políticas de passam na validação.
 * Clique em **Adicionar**
 * Opções > `Manual`
 * Nome >`FacebookSecret` O prefixo B2C_1A_ pode ser adicionado automaticamente.
 * Segredo > Insira seu FacebookSecret de developers.facebook.com ou “0” como um espaço reservado. *Ele não é a ID do Facebook App*
 * Uso de chave > Assinatura
 * Clique em **Criar** e confirme a criação

## <a name="register-identity-experience-framework-applications"></a>Registrar aplicativos do Identity Experience Framework

O Azure AD B2C exige o registro de dois aplicativos extras que são usados pelo mecanismo para a inscrição e conexão de usuários.

>[!NOTE]
>Devemos criar dois aplicativos que permitam a entrada usando contas locais: IdentityExperienceFramework (um aplicativo Web) e ProxyIdentityExperienceFramework (um aplicativo nativo) com permissão delegada do aplicativo IdentityExperienceFramework. As contas locais só existem em seu locatário. Seus usuários finais entram com uma combinação exclusiva email:senha para acessar seus aplicativos de locatário registrados.

### <a name="create-the-identityexperienceframework-application"></a>Criar o aplicativo IdentityExperienceFramework

1. No [portal do Azure](https://portal.azure.com), [alterne para o contexto do locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
1. Abra a folha `Azure Active Directory` (não a folha do Azure AD B2C). Talvez você precise clicar em **> Mais serviços** para encontrá-la.
1. Selecione **Registros do Aplicativo**.
1. Clique em **+ Novo registro de aplicativo**.
   * Nome: `IdentityExperienceFramework`
   * Tipo de aplicativo: `Web app/API`
   * URL de Logon: `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, em que `yourtenant` é o nome de domínio do seu locatário do Azure AD B2C.
1. Clique em **Criar**.
1. Depois de criado, selecione o aplicativo `IdentityExperienceFramework` recém-criado, clique em **Propriedades**, copie a ID do Aplicativo e salve-a para uso posterior.

### <a name="create-the-proxy-identity-experience-framework-application"></a>Criar o aplicativo Identity Experience Framework do proxy

1. Selecione **Registros do Aplicativo**.
1. Clique em **+ Novo registro de aplicativo**.
   * Nome: `ProxyIdentityExperienceFramework`
   * Tipo de aplicativo: `Native`
   * URL de Logon: `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, em que `yourtenant` é o locatário do Azure AD B2C.
1. Clique em **Criar**.
1. Depois de criado, selecione o aplicativo `ProxyIdentityExperienceFramework`, clique em **Propriedades**, copie a ID do Aplicativo e salve-a para uso posterior.
1. Selecione **Permissões necessárias** e, em seguida, selecione **+ Adicionar**e **Selecionar uma API**.
1. Pesquise o nome `IdentityExperienceFramework`, selecione IdentityExperienceFramework nos resultados e clique em **Selecionar**.
1. Marque a caixa de seleção ao lado de `Access IdentityExperienceFramework` e clique em **Selecionar**.
1. Clique em **Concluído**.
1. Clique em **Conceder Permissões** e confirme **Sim**

## <a name="download-starter-pack-and-modify-policies"></a>Baixar o pacote de início e modificar políticas

Políticas personalizadas são um conjunto de arquivos XML que precisam ser carregados no locatário do Azure AD B2C. Fornecemos starter packs para você começar a trabalhar rapidamente. Cada starter pack abaixo tem os perfis técnicos e percursos do usuário mínimos necessários para alcançar os cenários conforme descrito:
 * LocalAccounts - permite o uso apenas de contas locais
 * SocialAccounts - permite o uso somente de contas sociais (ou federadas)
 * **SocialAndLocalAccounts** - usaremos essa neste passo a passo
 * SocialAndLocalAccountsWithMFA - as opções social, local e MFA estão incluídas aqui

Cada starterpack contém:

* O [arquivo base](active-directory-b2c-overview-custom.md#policy-files) da política. São necessárias algumas modificações na base.
* O [arquivo de extensão](active-directory-b2c-overview-custom.md#policy-files) da política.  Esse arquivo é o local em que a maioria das alterações de configuração é feita.
* [Arquivos de terceiras partes confiáveis](active-directory-b2c-overview-custom.md#policy-files) São os arquivos específicos de tarefa, chamados pelo aplicativo para tarefas específicas.

>[!NOTE]
>Caso seu editor de XML dê suporte à validação, valide os arquivos em relação ao arquivo de esquema XML `TrustFrameworkPolicy_0.3.0.0.xsd` localizado na pasta raiz do pacote de início. A validação de esquema XML identifica erros antes do upload.

Vamos começar:

1. Baixar o "active-directory-b2c-custom-policy-starterpack" do GitHub.  [Baixe o zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou execute

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Abra a pasta `SocialAndLocalAccounts`.  O arquivo base (`TrustFrameworkBase.xml`) dessa pasta contém o conteúdo necessário para contas locais e sociais/corporativas. O conteúdo social não interfere nas etapas para ativação das contas locais.
3. Abra `TrustFrameworkBase.xml`.  Se você precisar de um editor de XML, [experimente o Visual Studio Code](https://code.visualstudio.com/download), um editor de plataforma cruzada leve.
4. No elemento raiz `TrustFrameworkPolicy`, atualize os atributos `TenantId` e `PublicPolicyUri`, substituindo `yourtenant.onmicrosoft.com` pelo nome de domínio do seu locatário do Azure AD B2C:

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
>[!NOTE]
>O `PolicyId` é o nome da política que você verá no portal e o nome pelo qual este arquivo de política será referenciado por outros arquivos de política.

5. Salve o arquivo.
6. Abra `TrustFrameworkExtensions.xml` e faça as mesmas duas alterações substituindo `yourtenant.onmicrosoft.com` pelo locatário do Azure AD B2C. Faça a mesma substituição no elemento `<TenantId>` para um total de três alterações.  Salve o arquivo.
7. Abra `SignUpOrSignIn.xml` e faça as mesmas alterações substituindo `yourtenant.onmicrosoft.com` pelo locatário do Azure AD B2C em três locais. Salve o arquivo.
8. Abra os arquivos de segredo de senha e de edição de perfil e faça as mesmas alterações substituindo `yourtenant.onmicrosoft.com` pelo seu locatário do Azure AD B2C em três locais em cada arquivo. Salve os arquivos.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Adicionar as IDs do Aplicativo à política personalizada
Adicione as IDs de aplicativo para o arquivo de extensões (`TrustFrameworkExtensions.xml`).

1. No arquivo de extensões (`TrustFrameworkExtensions.xml`), localize o elemento `<TechnicalProfile Id="login-NonInteractive">`.
2. Substitua ambas as instâncias de `IdentityExperienceFrameworkAppId` pela ID do aplicativo do aplicativo Identity Experience Framework criado anteriormente. Veja um exemplo:

```xml
<Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
```

3. Substitua ambas as instâncias de `ProxyIdentityExperienceFrameworkAppId` pela ID do aplicativo da Identity Experience Framework de Proxy que você criou anteriormente
4. Salve o arquivo de extensões.

## <a name="upload-the-policies-to-your-tenant"></a>Carregar as políticas no locatário

1. No [portal do Azure](https://portal.azure.com), [alterne para o contexto do locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e abra a folha do Azure AD B2C.
2. Clique em **Identity Experience Framework**
3. Selecione **Carregar Política** para carregar arquivos de política

    >[!WARNING]
    >Os arquivos da política personalizada devem ser carregados na seguinte ordem:

1. Carregue `TrustFrameworkBase.xml`.
2. Carregue `TrustFrameworkExtensions.xml`.
3. Carregue `SignUpOrSignin.xml`.
4. Carregue os outros arquivos de política.

Quando um arquivo é carregado, o nome do arquivo de política é precedido por `B2C_1A_`.

## <a name="test-the-custom-policy-using-run-now"></a>Testar a política personalizada usando a opção “Executar Agora”

1. Abra as **Configurações do Azure AD B2C** e navegue até **Identity Experience Framework**.

>[!NOTE]
>**Executar agora** exige que pelo menos um aplicativo esteja previamente registrado no locatário. Os aplicativos devem ser registrados no locatário do B2C, usando a seleção de menu **Aplicativos** no B2C ou na Identity Experience Framework para invocar as políticas internas e personalizadas. Somente um registro por aplicativo é necessário. 

Saiba como registrar aplicativos nos artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C.  

2. Abra a política personalizada RP (Terceira Parte Confiável) que você carregou no `B2C_1A_signup_signin` e clique no botão **Executar agora**.


3. Você deverá conseguir se inscrever usando um endereço de email.
4. Entrar com a mesma conta para confirmar que você tem uma configuração correta

>[!NOTE]
>Uma causa comum de falha de entrada é um aplicativo IdentityExperienceFramework configurado incorretamente.


## <a name="next-steps"></a>Próximas etapas

### <a name="add-facebook-as-an-identity-provider"></a>Adicionar o Facebook como um provedor de identidade
Para configurar o Facebook:
1. [Configurar um aplicativo do Facebook em developers.facebook.com](active-directory-b2c-setup-fb-app.md)
2. [Adicionar o segredo do aplicativo Facebook ao seu locatário do Azure AD B2C](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies)
3. Adicione a id do aplicativo Facebook ao arquivo de política TrustFrameworkExtensions no `Item Key="client_id"`:

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  <Metadata>
  <!--Replace the value of client_id in this technical profile with your the Facebook App ID"-->
    <Item Key="client_id">00000000000000</Item>
```
4. Carregue o arquivo de política de TrustFrameworkExtensions.xml em seu locatário.
5. Teste usando **Executar agora** ou invoque a política diretamente do seu aplicativo registrado.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Adicionar o Azure Active Directory como um provedor de identidade
O arquivo base que usamos neste guia de introdução já contém uma parte do conteúdo de que você precisa para adicionar outros provedores de identidade. Para configurar o logon usando contas do Azure AD, [continue aqui](active-directory-b2c-setup-aad-custom.md).


## <a name="reference"></a>Referência

[Visão geral](active-directory-b2c-overview-custom.md) de Políticas Personalizadas no Azure AD B2C usando a Identity Experience Framework

