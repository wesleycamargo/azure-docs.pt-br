---
title: Tutorial - Criar um locatário do Azure Active Directory B2C | Microsoft Docs
description: Saiba como preparar para registrar os aplicativos criando um locatário do Azure Active Directory B2C usando o portal do Azure.
services: B2C
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5f406242197c15348cfd557fb9c960ad7d4bed19
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54846839"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Tutorial: Criar um locatário do Azure Active Directory B2C

Antes que os aplicativos possam interagir com o Azure AD (Azure Active Directory B2C), eles deverão ser registrados em um locatário que você gerencia.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um locatário do Azure AD B2C
> * Vincular seu locatário à sua assinatura

Saiba como registrar um aplicativo no próximo tutorial.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um locatário do Azure AD B2C

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém sua assinatura clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que o contém. Esse diretório é diferente daquele que conterá seu locatário do Azure AD B2C.

    ![Alternar para o diretório de assinatura](./media/tutorial-create-tenant/switch-directory-subscription.png)

3. Escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.
4. Pesquise e selecione **Active Directory B2C**; depois, clique em **Criar**.
5. Escolha **Criar um novo locatário do Azure AD B2C**, insira um nome de organização e nome de domínio inicial, que é usado no nome do locatário, selecione o país/região (não pode ser alterado depois) e, em seguida, clique em **Criar**.

    ![Criar um locatário](./media/tutorial-create-tenant/create-tenant.png)

    Neste exemplo, o nome do locatário é contoso0926Tenant.onmicrosoft.com

6. Na página **Criar novo locatário do B2C ou vincular a um locatário existente**, escolha **Vincular um locatário existente do Azure AD B2C à minha assinatura do Azure**, selecione o locatário que você criou, selecione sua assinatura e, depois, clique em **Criar novo**.
7. Insira um nome para o grupo de recursos que conterá o locatário, selecione a localização e, em seguida, clique em **Criar**.
8. Para começar a usar seu novo locatário, verifique se está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que o contém.

    ![Alternar para o diretório do locatário](./media/tutorial-create-tenant/switch-directories.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar um locatário do Azure AD B2C
> * Vincular seu locatário à sua assinatura

> [!div class="nextstepaction"]
> [Registrar seus aplicativos](tutorial-register-applications.md)