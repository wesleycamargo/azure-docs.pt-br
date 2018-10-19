---
title: Tutorial - Criar um locatário do Azure Active Directory B2C | Microsoft Docs
description: Saiba como preparar para registrar os aplicativos criando um locatário do Azure Active Directory B2C usando o portal do Azure.
services: B2C
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7571e5f4d95320ab92fa3b69b0ea1f05ff9c771f
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408395"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Tutorial - Criar um locatário do Azure Active Directory B2C

Antes que os aplicativos possam interagir com o Azure AD (Azure Active Directory B2C), eles deverão ser registrados em um locatário que você gerencia.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um locatário do Azure AD B2C
> * Vincular seu locatário à sua assinatura

Saiba como registrar um aplicativo no próximo tutorial.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um locatário do Azure AD B2C

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém sua assinatura clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que o contém. Não é um diretório igual àquele que conterá seu locatário do Azure AD B2C.

    ![Alternar para o diretório de assinatura](./media/tutorial-create-tenant/switch-directory-subscription.png)

3. Escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.
4. Pesquise e selecione **Active Directory B2C**; depois, clique em **Criar**.
5. Escolha **Criar um novo locatário do Azure AD B2C**, insira um nome de organização e nome de domínio inicial, que é usado no nome do locatário, selecione o país (não pode ser alterado depois) e, em seguida, clique em **Criar**.

    ![Criar um locatário](./media/tutorial-create-tenant/create-tenant.png)

    Neste exemplo, o nome do locatário é contoso0926Tenant.onmicrosoft.com

6. Na página **Criar novo locatário do B2C ou vincular com um locatário existente**, escolha **Vincular um locatário existente do Azure AD B2C à minha assinatura do Azure**, selecione o locatário que criou, selecione sua assinatura, clique em **Criar novo** e insira um nome para o grupo de recursos que conterá o locatário, selecione o local e, a seguir, clique em **Criar**.
7. Para começar a usar seu novo locatário, verifique se está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que o contém.

    ![Alternar para o diretório do locatário](./media/tutorial-create-tenant/switch-directories.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar um locatário do Azure AD B2C
> * Vincular seu locatário à sua assinatura

> [!div class="nextstepaction"]
> [Habilite um aplicativo web para autenticar com as contas](active-directory-b2c-tutorials-web-app.md)