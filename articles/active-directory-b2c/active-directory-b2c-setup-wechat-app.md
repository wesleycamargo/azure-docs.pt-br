---
title: A configuração do WeChat no Active Directory B2C do Azure | Microsoft Docs
description: Forneça inscrição e conexão para consumidores com contas do WeChat em seus aplicativos protegidos pelo Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a18d41a4f45b147790a17664156659d282e710d4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445919"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-wechat-accounts"></a>Azure Active Directory B2C: fornecer inscrição e conexão para consumidores com contas do WeChat

> [!NOTE]
> Esse recurso está em visualização.
> 

## <a name="create-a-wechat-application"></a>Criar um aplicativo WeChat

Para usar o WeChat como um provedor de identidade no Azure AD (Active Directory) B2C, você precisa criar um aplicativo WeChat e fornecer a ele os parâmetros certos. Para fazer isso, é necessário ter uma conta WeChat. Caso não tenha, você pode obter uma inscrevendo-se em um dos aplicativos móveis ou usando o número do seu QQ. Depois disso, registre sua conta no programa de desenvolvedores do WeChat. Você pode encontrar mais informações [aqui](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registrar um aplicativo WeChat

1. Acesse [https://open.weixin.qq.com/](https://open.weixin.qq.com/) e faça logon.
2. Clique em**管理中心**(Centro de gerenciamento).
3. Siga as etapas necessárias para registrar um novo aplicativo.
4. Para **授权 回调 域** (URL de retorno de chamada), insira `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Por exemplo, se seu `tenant_name` for contoso.onmicrosoft.com, defina a URL para ser `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
5. Localize e copie a **ID DO APLICATIVO** e a **CHAVE DO APLICATIVO**. Você precisará delas mais tarde.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Configurar o WeChat como um provedor de identidade em seu locatário
1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no Portal do Azure.
2. Na folha de recursos do B2C, clique em **Provedores de identidade**.
3. Clique em **+Adicionar** , na parte superior da folha.
4. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira "WeChat".
5. Clique em **Tipo de provedor de identidade**, selecione **WeChat** e clique em **OK**.
6. Clique em **Configurar este provedor de identidade**
7. Insira a **Chave do Aplicativo** que você copiou anteriormente como a **ID do Cliente**.
8. Insira o **Segredo do Aplicativo** que você copiou anteriormente como o **Segredo do Cliente**.
9. Clique em **OK** e em **Criar** para salvar sua configuração WeChat.

