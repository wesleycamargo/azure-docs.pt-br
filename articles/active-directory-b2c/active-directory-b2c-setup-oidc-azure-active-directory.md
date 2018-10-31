---
title: Configurar contas do Azure Active Directory de entrada como uma política interna no Azure Active Directory B2C | Microsoft Docs
description: Configurar contas do Azure Active Directory de entrada como uma política interna no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: beb2d618d93f4c599f946194bd483326471065f4
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49944796"
---
# <a name="set-up-sign-in-azure-active-directory-accounts-a-built-in-policy-in-azure-active-directory-b2c"></a>Configurar contas do Azure Active Directory de entrada como uma política interna no Azure Active Directory B2C

>[!NOTE]
> Esse recurso está em uma versão prévia. Não use o recurso em ambientes de produção.

Este artigo mostra como habilitar a entrada de usuários de uma organização do Azure AD (Azure Active Directory) específica usando uma política interna no Azure Active Directory (Azure AD) B2C.

## <a name="create-an-azure-ad-app"></a>Criar um aplicativo Azure AD

Para habilitar a entrada para usuários de uma organização específica do Azure AD, você precisa registrar um aplicativo no locatário organizacional do Azure AD, que não é o mesmo que o seu locatário do B2C do Azure AD.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD clicando nos filtros de pastas e de assinatura no menu superior e, então, escolhendo o diretório que contém o locatário do Azure AD.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
4. Selecione **Novo registro de aplicativo**.
5. Insira um nome para seu aplicativo. Por exemplo, `Azure AD B2C App`.
6. Para o **Tipo de aplicativo**, selecione `Web app / API`.
7. Para a **URL de Logon**, digite a seguinte URL em letras minúsculas, em que `your-B2C-tenant-name` é substituído pelo nome do seu locatário do B2C do Azure AD. Por exemplo `https://fabrikam.b2clogin.com/fabrikam.b2clogin.com/oauth2/authresp`:

    ```
    https://your-tenant-name.b2clogin.com/your-B2C-tenant-name.b2clogin.com/oauth2/authresp
    ```

    Agora todas as URLs devem estar usando [b2clogin.com](b2clogin.md).

8. Clique em **Criar**. Copie a **ID do aplicativo** a ser usada posteriormente.
9. Selecione o aplicativo e, em seguida, selecione **Configurações**.
10. Selecione **Chaves**, insira a descrição da chave, selecione uma duração e, em seguida, clique em **Salvar**. Copie o valor da chave exibido para ser usado mais tarde.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurar o Azure AD como um provedor de identidade

1. Verifique se você está usando o diretório que contém o locatário do B2C do Azure AD clicando nos **filtros de pastas e de assinatura** no menu superior e escolhendo o diretório que contém seu locatário do B2C do Azure AD.
2. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, pesquise e selecione **Azure AD B2C**.
3. Escolha **Provedores de identidade** e escolha **Adicionar**.
4. Insira um **Nome**. Por exemplo, digite "Contoso Azure AD".
5. Selecione **Tipo de provedor de identidade**, **Open ID Connect (versão prévia)** e, em seguida, clique em **OK**.
6. Clique em **Configurar este provedor de identidade**
7. Para a **URL dos metadados**, insira a seguinte URL, substituindo `your-AD-tenant-domain` pelo nome de domínio do locatário do Azure AD. Por exemplo `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. Para **ID do cliente**, insira a ID do aplicativo que você registrou anteriormente e, para **Segredo do cliente**, insira o valor da chave que você registrou anteriormente.
9. Opcionalmente, digite um valor para **Domain_hint**. Por exemplo, `ContosoAD`. Esse é o valor a ser usado ao fazer referência a esse provedor de identidade usando *domain_hint* na solicitação. 
10. Clique em **OK**.
11. Selecione **Mapear declarações do provedor de identidade** e defina as seguintes declarações:
    
    - Para **ID de usuário**, digite `oid`.
    - Para **Nome de exibição**, digite `name`.
    - Para **Nome**, digite `given_name`.
    - Para **Sobrenome**, digite `family_name`.
    - Para **Email**, digite `unique_name`.

12. Clique em **OK** e, em seguida, em **Criar** para salvar sua configuração.
