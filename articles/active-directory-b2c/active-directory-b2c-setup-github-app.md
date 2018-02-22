---
title: "Configuração do provedor de identidade GitHub - Azure AD B2C | Microsoft Docs"
description: "Forneça inscrição e entrada aos clientes com as contas do GitHub em seus aplicativos protegidos pelo Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 5518e135-36b3-4f86-9a01-be25f021ed22
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: parja
ms.openlocfilehash: e827bea257309f6d6dda1af4e68ccda5a26d30ef
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-github-accounts"></a>Azure Active Directory B2C: forneça inscrição e conexão aos consumidores com contas do GitHub

> [!NOTE]
> Esse recurso está em visualização.
> 

Esse artigo mostra como habilitar a entrada para os usuários com uma conta GitHub.

## <a name="create-a-github-oauth-application"></a>Crie um aplicativo GitHub OAuth

Para usar o GitHub como um provedor de identidade no Azure AD B2C, você precisará criar um aplicativo do GitHub OAuth e fornecer a ele os parâmetros certos.

1. Vá para o [configurações do desenvolvedor GitHub](https://github.com/settings/developers) depois de entrar no GitHub.
1. Clique em **Novo Aplicativo OAuth**
1. Insira um **nome do Aplicativo** e **URL da home page**.
1. Para a **URL de retorno de chamada da autorização**, insira `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Substitua **{tenant}** pelo nome do locatário o seu Azure AD B2C (por exemplo, B2C.onmicrosoft.com).

    >[!NOTE]
    >O valor de "locatário" deve estar todo em letras maiúsculas na **URL de Entrada**.

1. Clique em **Registrar aplicativo**.
1. Salve os valores de **ID do cliente** e **Segredo do cliente**. Você precisará deles na próxima seção.

## <a name="configure-github-as-an-identity-provider-in-your-azure-ad-b2c-tenant"></a>Configure o GitHub como um provedor de identidade em seu locatário do Azure AD B2C

1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no Portal do Azure.
1. Na folha de recursos do B2C, clique em **Provedores de identidade**.
1. Clique em **+Adicionar** , na parte superior da folha.
1. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira "GitHub".
1. Clique em **Tipo de provedor de identidade**, selecione **GitHub** e clique em **OK**.
1. Clique em **Configurar este provedor de identidade** e insira a ID do cliente e o segredo do cliente do aplicativo do GitHub OAuth criado anteriormente.
1. Clique em **OK** e em **Criar** para salvar a configuração do GitHub.

## <a name="next-steps"></a>Próximas etapas

Criar ou editar um [política interna](active-directory-b2c-reference-policies.md) e adicione o GitHub como um provedor de identidade.