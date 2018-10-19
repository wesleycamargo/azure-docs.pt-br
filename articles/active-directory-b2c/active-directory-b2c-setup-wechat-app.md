---
title: Configurar a inscrição e entrada com a conta do WeChat usando o Azure Active Directory B2C | Microsoft Docs
description: Forneça a inscrição e entrada aos consumidores com contas do WeChat em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 45afd0d4c038123a1daf5f57de18e5f2799faa31
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181253"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do WeChat usando o Azure Active Directory B2C

> [!NOTE]
> Esse recurso está em visualização.
> 

## <a name="create-a-wechat-application"></a>Criar um aplicativo WeChat

Para usar uma conta do WeChat como um provedor de identidade no Azure AD (Azure Active Directory) B2C, você precisará criar um aplicativo no locatário que o representa. Se ainda não tiver uma conta do WeChat, consiga uma conta em [http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registrar um aplicativo WeChat

1. Entre em [https://open.weixin.qq.com/](https://open.weixin.qq.com/) com as credenciais do WeChat.
2. Escolha **管理中心** (Centro de gerenciamento).
3. Siga as etapas para registrar um novo aplicativo.
4. Insira `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **授权回调域** (URL de retorno de chamada). Por exemplo, se o nome do locatário é contoso, defina a URL para ser `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
5. Copie a **ID DO APLICATIVO** e a **CHAVE DO APLICATIVO**. Você precisará delas para adicionar o provedor de identidade ao locatário.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Configurar o WeChat como um provedor de identidade em seu locatário

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Escolha **Provedores de identidade** e escolha **Adicionar**.
5. Forneça um **Nome**. Por exemplo, insira *WeChat*.
6. Escolha **Tipo de provedor de identidade**, marque **WeChat (versão prévia)** e clique em **OK**.
7. Escolha **Configurar este provedor de identidade** e insira o ID DO APLICATIVO que você registrou anteriormente como a **ID do Cliente** e insira a CHAVE DO APLICATIVO que você registrou como o **Segredo do Cliente** do aplicativo de conta do WeChat criado anteriormente.
8. Clique em **OK** e em **Criar** para salvar sua configuração WeChat.

