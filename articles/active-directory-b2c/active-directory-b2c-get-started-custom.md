---
title: "Azure Active Directory B2C: Introdução às políticas personalizadas | Microsoft Docs"
description: "Um tópico sobre como começar a usar as políticas personalizadas do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 93fc922433f0ba60ee438db8d4dcb14527c208fc
ms.contentlocale: pt-br
ms.lasthandoff: 05/02/2017

---
# <a name="azure-active-directory-b2c-getting-started-with-custom-policies"></a>Azure Active Directory B2C: Introdução às políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Depois de concluir as etapas descritas neste artigo, a política personalizada dará suporte à inscrição ou conexão da “conta local” usando um email e uma senha. Você também preparará o ambiente para adicionar outros provedores de identidade (como Facebook ou Azure AD).  A conclusão dessas etapas é necessária para que todos os outros usos do Mecanismo de Experiência de Identidade do Azure AD B2C e vários conceitos básicos sejam apresentados.

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, verifique se você tem um locatário do Azure AD B2C. Um locatário do Azure AD B2C é um contêiner de todos os usuários, aplicativos, políticas e muito mais. Se você ainda não tiver um locatário, precisará [criar um](active-directory-b2c-get-started.md).

### <a name="confirming-your-b2c-tenant"></a>Confirmando seu locatário B2C

Como as políticas personalizadas ainda estão em visualização particular, confirme se seu locatário do Azure AD B2C está habilitado para o upload de políticas personalizadas:

1. No [portal do Azure](https://portal.azure.com), [alterne para o contexto do locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e abra a folha do Azure AD B2C.
1. Clique em **Todas as Políticas**.
1. Verifique se a opção **Carregar Política** está disponível.  Se o botão estiver desabilitado, envie um email para AADB2CPreview@microsoft.com.

## <a name="set-up-keys-for-your-custom-policy"></a>Configurar chaves para a política personalizada

A primeira etapa necessária para usar políticas personalizadas é configurar chaves.

[!INCLUDE [active-directory-b2c-setup-keys-custom.md](../../includes/active-directory-b2c-setup-keys-custom.md)]

## <a name="download-starter-pack-and-modify-policies"></a>Baixar o pacote de início e modificar políticas

Políticas personalizadas são um conjunto de arquivos XML que precisam ser carregados no locatário do Azure AD B2C. Fornecemos um pacote de início que você pode usar para começar. O pacote de início contém:

* O [arquivo base](active-directory-b2c-overview-custom.md#policy-files) da política. São necessárias algumas modificações na base.
* O [arquivo de extensão](active-directory-b2c-overview-custom.md#policy-files) da política.  Esse arquivo é o local em que a maioria das alterações de configuração é feita.

Vamos começar:

1. Baixe o “Pacote de Início da Política Personalizada do Azure AD B2C” no GitHub.  [Baixe o zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou execute

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Abra a pasta `SocialIDPAndLocalAccounts`.  O arquivo base (`TrustFrameworkBase.xml`) dessa pasta contém o conteúdo necessário para contas locais e sociais/corporativas. O conteúdo social não interfere nas etapas para ativação das contas locais.
1. Abra `TrustFrameworkBase.xml`.  Se você precisar de um editor de XML, tente [Baixar o Visual Studio Code](https://code.visualstudio.com/download), um editor de plataforma cruzada leve.
1. No elemento raiz `TrustFrameworkPolicy`, atualize os atributos `TenantId` e `PublicPolicyUri`, substituindo `{tenantName}` pelo locatário do Azure AD B2C:

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="{tenantName}.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://{tenantName}.onmicrosoft.com">
    ```

1. Salve o arquivo.
1. Abra `TrustFrameworkExtensions.xml` e faça as mesmas alterações substituindo `{tenantName}` pelo locatário do Azure AD B2C. Salve o arquivo.
1. Abra `SignUpOrSignIn.xml` e faça as mesmas alterações substituindo `{tenantName}` pelo locatário do Azure AD B2C. Salve o arquivo.

>[!NOTE]
>Caso seu editor de XML dê suporte à validação, valide os arquivos em relação ao arquivo de esquema XML `TrustFrameworkPolicy_0.3.0.0.xsd` localizado na pasta raiz do pacote de início. A validação de esquema XML identifica erros antes do upload.

## <a name="register-policy-engine-applications"></a>Registrar aplicativos do mecanismo de políticas

O Azure AD B2C exige o registro de dois aplicativos extras que são usados pelo mecanismo para a inscrição e conexão de usuários.

>[!NOTE]
>Abaixo, criamos dois aplicativos que permitem o logon usando contas locais: PolicyEngine (um aplicativo Web) e PolicyEngineProxy (um aplicativo nativo) com permissão delegada de PolicyEngine. Esta seção só é necessária para locatários do Azure AD B2C nos quais se espera o uso de contas locais.

### <a name="create-the-policy-engine-application"></a>Criar o aplicativo do mecanismo de políticas

1. No [portal do Azure](https://portal.azure.com), [alterne para o contexto do locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
1. Abra a folha `Azure Active Directory` (não a folha do Azure AD B2C). Talvez você precise clicar em **> Mais serviços** para encontrá-la.
1. Selecione **Registros do Aplicativo**.
1. Clique em **+ Novo registro de aplicativo**.
   * Nome: `PolicyEngine`
   * Tipo de aplicativo: `Web app/API`
   * URL de Logon: `https://login.microsoftonline.com/{tenantName}.onmicrosoft.com`, em que `{tenantName}` é o locatário do Azure AD B2C.
1. Clique em **Criar**.
1. Depois de criado, selecione o aplicativo `PolicyEngine` recém-criado, copie a ID do Aplicativo e salve-a para uso posterior.

### <a name="create-the-policy-engine-proxy-application"></a>Criar o aplicativo de proxy do mecanismo de políticas

1. Selecione **Registros do Aplicativo**.
1. Clique em **+ Novo registro de aplicativo**.
   * Nome: `PolicyEngineProxy`
   * Tipo de aplicativo: `Native`
   * URL de Logon: `https://login.microsoftonline.com/{tenantName}.onmicrosoft.com`, em que `{tenantName}` é o locatário do Azure AD B2C.
1. Clique em **Criar**.
1. Depois de criado, selecione o aplicativo `PolicyEngineProxy`, copie a ID do Aplicativo e salve-a para uso posterior.
1. Selecione **Permissões necessárias** e, em seguida, selecione **+ Adicionar**e **Selecionar uma API**.
1. Pesquise o nome `PolicyEngine`, selecione PolicyEngine nos resultados e clique em **Selecionar**.
1. Marque a caixa de seleção ao lado de `Access PolicyEngine` e clique em **Selecionar**.
1. Clique em **Concluído**.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Adicionar as IDs do Aplicativo à política personalizada

Para criar uma política personalizada com as contas locais habilitadas, você precisa adicionar os IDs do aplicativo ao arquivo de extensões (`TrustFrameworkExtensions.xml`).

1. No arquivo de extensões (`TrustFrameworkExtensions.xml`), localize o elemento `<TechnicalProfile Id="login-NonInteractive">`.
1. Substitua ambas as instâncias de `{Policy Engine Proxy Application ID}` pela ID do aplicativo do [aplicativo de proxy do mecanismo de políticas criado](#create-the-policy-engine-proxy-application).
1. Substitua ambas as instâncias de `{Policy Engine Application ID}` pela ID do aplicativo do [aplicativo do mecanismo de políticas criado](#create-the-policy-engine-application).
1. Salve o arquivo de extensões.

## <a name="upload-the-policies-to-your-tenant"></a>Carregar as políticas no locatário

1. No [portal do Azure](https://portal.azure.com), [alterne para o contexto do locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e abra a folha do Azure AD B2C.
1. Clique em **Todas as Políticas**.
1. Selecione **Carregar Política**

    >[!WARNING]
    >Os arquivos da política personalizada devem ser carregados na seguinte ordem:

1. Carregue `TrustFrameworkBase.xml`.
1. Carregue `TrustFrameworkExtensions.xml`.
1. Carregue `SignUpOrSignin.xml`.

Quando um arquivo é carregado, seu nome é precedido por `B2C_1A_`.  Por outro lado, as políticas internas começam com `B2C_1_`.

## <a name="test-the-custom-policy-using-run-now"></a>Testar a política personalizada usando a opção “Executar Agora”

1. Abra a **Folha do Azure AD B2C** e navegue para **Todas as políticas**.
1. Selecione a política personalizada carregada e clique no botão **Executar agora**.
1. Você deverá conseguir se inscrever usando um endereço de email.

## <a name="next-steps"></a>Próximas etapas

O arquivo base que usamos neste guia de introdução já contém uma parte do conteúdo de que você precisa para adicionar outros provedores de identidade. Para configurar o logon usando contas do Azure AD, [continue aqui](active-directory-b2c-setup-aad-custom.md).

## <a name="reference"></a>Referência

* Um **TP (Perfil Técnico)** é um elemento que define o nome de um ponto de extremidade, seus metadados, seu protocolo e os detalhes da troca de declarações que o Mecanismo de Experiência de Identidade deve executar.  A SignIn de Conta Local é o TechnicalProfile usado pelo Mecanismo de Experiência de Identidade para executar um logon de conta local.

