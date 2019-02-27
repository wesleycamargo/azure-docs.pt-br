---
title: Configurar a inscrição e a entrada com a conta do GitHub – Azure Active Directory B2C | Microsoft Docs
description: Forneça a inscrição e entrada aos consumidores com contas do GitHub em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: f1c1ac91c08fe27445f4b9631500543d1d0287bd
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56427268"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do GitHub usando o Azure Active Directory B2C

> [!NOTE]
> Esse recurso está em visualização.
> 

Para usar uma conta do GitHub como um [provedor de identidade](active-directory-b2c-reference-oauth-code.md) no Azure AD (Azure Active Directory) B2C, você precisará criar um aplicativo no locatário que o representa. Se ainda não tiver uma conta do GitHub, obtenha uma em [https://www.github.com/](https://www.github.com/).

## <a name="create-a-github-oauth-application"></a>Crie um aplicativo GitHub OAuth

1. Entrar no site de [Desenvolvedor do GitHub](https://github.com/settings/developers) com suas credenciais do GitHub.
2. Selecione **Aplicativos do OAuth** e, em seguida, selecione **Novo Aplicativo do OAuth**.
3. Insira um **nome do Aplicativo** e **URL da home page**.
4. Insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URL de retorno de chamada de autorização**. Substitua `your-tenant-name` pelo nome de seu locatário do Azure AD B2C. Use todas as letras minúsculas, ao inserir o nome do locatário, mesmo se o locatário estiver definido com letras maiúsculas no Azure AD B2C.
5. Clique em **Registrar aplicativo**.
6. Copie os valores de **ID do cliente** e **Segredo do cliente**. Você precisará delas para adicionar o provedor de identidade ao locatário.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Configurar a conta do GitHub como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Escolha **Provedores de identidade** e escolha **Adicionar**.
5. Forneça um **Nome**. Por exemplo, insira *GitHub*.
6. Selecione **Tipo de provedor de identidade**, selecione **GitHub (versão prévia)** e clique em **OK**.
7. Selecione **Configurar este provedor de identidade** e insira a ID do Cliente que você registrou anteriormente como a **ID do Cliente** e insira o Segredo do Cliente que você registrou como o **Segredo do Cliente** do aplicativo de conta do GitHub criado anteriormente.
8. Clique em **OK** e em **Criar** para salvar a configuração de conta do GitHub.
