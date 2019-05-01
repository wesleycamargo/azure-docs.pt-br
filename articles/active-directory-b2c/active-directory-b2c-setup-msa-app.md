---
title: Configurar a inscrição e a entrada com a conta da Microsoft – Azure Active Directory B2C | Microsoft Docs
description: Forneça inscrição e entrada aos consumidores com contas da Microsoft em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: d6557d8dccd3c61307bc3d29c86d98722d793170
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703760"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta da Microsoft usando o Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Criar um aplicativo de conta da Microsoft

Para usar uma conta da Microsoft como um [provedor de identidade](active-directory-b2c-reference-oidc.md) no Azure AD (Azure Active Directory) B2C, é preciso criar um aplicativo no locatário que o representa. Se ainda não tiver uma conta da Microsoft, consiga uma conta em [https://www.live.com/](https://www.live.com/).

1. Entre no [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) com suas credenciais da conta da Microsoft.
2. No canto superior direito, escolha **Adicionar um aplicativo**.
3. Insira um **Nome** para seu aplicativo. Por exemplo, *MSAapp1*.
4. Selecione **Gerar Nova Senha** e verifique se você copiou a senha a ser usada ao configurar o provedor de identidade. Copie também a **ID do Aplicativo**. 
5. Escolha **Adicionar plataforma** e, em seguida, escolha **Web**.
4. Insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URIs de redirecionamento**. Substitua `your-tenant-name` pelo nome do seu locatário.
5. Clique em **Salvar**.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configurar uma conta da Microsoft como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Escolha **Provedores de identidade** e escolha **Adicionar**.
5. Forneça um **Nome**. Por exemplo, insira *MSA*.
6. Escolha **Tipo do provedor de identidade**, marque **Conta da Microsoft** e clique em **OK**.
7. Escolha **Configurar este provedor de identidade** e insira o ID do Aplicativo que você registrou anteriormente como a **ID do Cliente** e insira a senha que você registrou como o **Segredo do Cliente** do aplicativo de conta da Microsoft criado anteriormente.
8. Clique em **OK** e em **Criar** para salvar a configuração da conta da Microsoft.

