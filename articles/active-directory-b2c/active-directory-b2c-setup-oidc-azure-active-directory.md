---
title: 'Azure Active Directory B2C: adicionar um provedor do Azure AD usando políticas internas | Microsoft Docs'
description: Saiba como adicionar um provedor de identidade Open ID Connect (Azure AD)
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 7dac9545-d5f1-4136-a04d-1c5740aea499
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/27/2018
ms.author: parja
ms.openlocfilehash: 52a752df9cf199acf39596f49e7368bce27a8158
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2018
ms.locfileid: "32309011"
---
# <a name="azure-active-directory-b2c-sign-in-using-azure-ad-accounts-through-a-built-in-policy"></a>Azure Active Directory B2C: entrar usando contas do Azure AD por meio de uma política interna

>[!NOTE]
> Esse recurso está em uma versão prévia. Não use o recurso em ambientes de produção.

Este artigo mostra como habilitar a entrada para usuários de políticas internas de uma organização específica do Azure AD (Azure Active Directory).

## <a name="create-an-azure-ad-app"></a>Criar um aplicativo Azure AD

Para habilitar a entrada para usuários de uma organização específica do Azure AD, você precisa registrar um aplicativo no locatário organizacional do Azure AD.

>[!NOTE]
> Usamos "contoso.com" como o locatário do Azure AD da organização e "fabrikamb2c.onmicrosoft.com" como o locatário do Azure AD B2C nas instruções a seguir.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Na barra superior, selecione sua conta. Na lista **Diretório**, escolha o locatário do Azure AD organizacional em que deseja registrar seu aplicativo (contoso.com).
1. Selecione **Todos os serviços** no painel esquerdo e pesquise por “Registros do aplicativo”.
1. Selecione **Novo registro de aplicativo**.
1. Insira um nome para seu aplicativo (por exemplo, `Azure AD B2C App`).
1. Selecione **Aplicativo Web/API** como o tipo de aplicativo.
1. Para a **URL de Logon**, insira a URL a seguir, em que `yourtenant` é substituído pelo nome do seu locatário do Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >O valor de "yourtenant" deve estar todo em letras maiúsculas no **URL de Logon**.

    ```Console
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Salvar a ID do aplicativo, que você usará na próxima seção como a ID do cliente.
1. Na folha **Configurações**, selecione **Chaves**.
1. Insira uma **chave descrição** na seção **Senhas** e defina a **duração** para "Nunca expira". 
1. Clique em **Salvar**e anote a chave resultante **Valor**, que você usará na próxima seção como o segredo do cliente.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Configurar o Azure AD como um provedor de identidade em seu locatário

1. Na barra superior, selecione sua conta. Na lista do **Diretório**, escolha o locatário do Azure AD B2C (fabrikamb2c.onmicrosoft.com).
1. [Navegar até o menu de configurações do Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
1. No menu de configurações do Azure AD B2C, clique em **Provedores de identidade**.
1. Clique em **+Adicionar** , na parte superior da folha.
1. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, digite "Contoso Azure AD".
1. Clique em **Tipo de provedor de identidade**, selecione **Open ID Connect** e clique em **OK**.
1. Clique em **Configurar este provedor de identidade**
1. Para a **URL de metadados**, insira a URL a seguir, em que `yourtenant` é substituído pelo nome do seu locatário do Azure AD (por exemplo, `contoso.com`):

    ```Console
    https://login.microsoftonline.com/yourtenant/.well-known/openid-configuration
    ```
1. Para a **ID do cliente** e **Segredo do cliente**, insira a ID do aplicativo e a chave da seção anterior.
1. Mantenha o valor padrão de **Escopo**, que deve ser definido como `openid`.
1. Mantenha o valor padrão de **Tipo de resposta**, que deve ser definido como `code`.
1. Mantenha o valor padrão de **Modo de resposta**, que deve ser definido como `form_post`.
1. Opcionalmente, digite um valor para **Domínio** (por exemplo, `ContosoAD`). Esse é o valor a ser usado ao fazer referência a esse provedor de identidade usando *domain_hint* na solicitação. 
1. Clique em **OK**.
1. Clique em **Mapear essas declarações do provedor de identidade**.
1. Para **ID de usuário**, digite `oid`.
1. Para **Nome de exibição**, digite `name`.
1. Para **Nome**, digite `given_name`.
1. Para **Sobrenome**, digite `family_name`.
1. Para **E-mail**, digite `unique_name`
1. Clique em **OK** e em **Criar** para salvar sua configuração.

## <a name="next-steps"></a>Próximas etapas

Adicionar provedor de identidade recém-criado do Azure AD a uma política interna e fornecer comentários para [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
