---
title: Tutorial - Criar um locatário do Azure Active Directory B2C | Microsoft Docs
description: Saiba como preparar para registrar os aplicativos criando um locatário do Azure Active Directory B2C usando o portal do Azure.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: cc48cd3eb40d93c26a68caf843a89f7bbfb46c6c
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236886"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Tutorial - Criar um locatário do Azure Active Directory B2C

Antes que os aplicativos possam interagir com o Azure AD (Azure Active Directory B2C), eles deverão ser registrados em um locatário que você gerencia.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um locatário do Azure AD B2C
> * Vincular seu locatário à sua assinatura

Saiba como registrar um aplicativo no próximo tutorial.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um locatário do Azure AD B2C

1. Escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.
2. Na caixa de pesquisa acima da lista de recursos do Microsoft Azure Marketplace, pesquise e selecione **Active Directory B2C** e, em seguida, clique em **Criar**.
3. Escolha **Criar um novo locatário do Azure AD B2C**, insira um nome de organização e nome de domínio inicial, que é usado no nome do locatário, selecione o país e, em seguida, clique em **Criar**. Certifique-se do país do locatário porque não poderá ser alterado posteriormente.

    ![Criar um locatário](./media/tutorial-create-tenant/create-tenant.png)

    Neste exemplo, o nome do locatário é contoso0522Tenant.onmicrosoft.com

Para iniciar o gerenciamento do novo locatário, clique na palavra **aqui** onde está escrito **Clique aqui para gerenciar o novo diretório**. Você verá uma mensagem de **Solução de problemas** informando que é necessário vincular a assinatura ao novo locatário. 

## <a name="link-your-tenant-to-your-subscription"></a>Vincular seu locatário à sua assinatura

Você precisa vincular o seu locatário do Azure AD B2C à sua assinatura do Azure para habilitar todas as funcionalidades e pagar pelos encargos de uso. Se você não vincular o locatário à assinatura, os aplicativos não funcionarão corretamente.

1. Verifique se você está usando o diretório que contém a assinatura que quer associar ao novo locatário, alternando o diretório no canto superior direito do portal do Azure.

    ![Mudar diretórios](./media/tutorial-create-tenant/switch-directories.png)

    E, em seguida, selecionando o diretório que contém a assinatura.

    ![Selecionar diretório](./media/tutorial-create-tenant/select-directory.png)

2. Escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.
3. Na caixa de pesquisa acima da lista de recursos do Microsoft Azure Marketplace, pesquise e selecione **Active Directory B2C** e, em seguida, clique em **Criar**.
4. Escolha **Vincular um locatário existente do Azure AD B2C à minha assinatura do Azure**, selecione o locatário que você criou, selecione a assinatura, insira *myContosoTenantRG* para o nome do grupo de recursos, aceite o local e clique em **Criar**.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar um locatário do Azure AD B2C
> * Vincular seu locatário à sua assinatura

> [!div class="nextstepaction"]
> [Habilite um aplicativo web para autenticar com as contas](active-directory-b2c-tutorials-web-app.md)