---
title: Gerenciar os grupos aos quais seu grupo pertence no Azure AD | Microsoft Docs
description: Os grupos podem conter outros grupos no Azure Active Directory. Veja como gerenciar essas associações.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 10/10/2017
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: 8a71677ae3ceb5617f0a817a8eff438d5e3f2774
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860295"
---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>Gerenciar a quais grupos um grupo pertence no seu locatário do Azure Active Directory
Os grupos podem conter outros grupos no Azure Active Directory. Veja como gerenciar essas associações.

## <a name="how-do-i-find-the-groups-of-which-my-group-is-a-member"></a>Como fazer para encontrar os grupos do qual meu grupo é um membro?
1. Entre no [centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que seja um administrador global para o diretório.
2. Selecione **Usuários e grupos**.

   ![Abrir imagem de usuários e grupos](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
1. Selecione **Todos os grupos**.

   ![Selecionar imagem de grupos](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
1. Selecione um grupo.
2. Selecionar **Associações de grupo**.

   ![Abrir a imagem de associações de grupo](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
1. Para adicionar seu grupo como membro de outro grupo, na folha **Grupo - Associações de grupo**, selecione o comando **Adicionar**.
2. Selecione um grupo na folha **Selecionar Grupo** e pressione o botão **Selecionar** na parte inferior da folha. Você só pode adicionar o grupo a um grupo por vez. A caixa **Usuário** filtra a exibição com base na correspondência de sua entrada com qualquer parte de um nome de usuário ou dispositivo. Caracteres curinga não são aceitos nessa caixa.

   ![Adicionar uma associação de grupo](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. Para remover seu grupo como um membro de outro grupo, na folha **Grupo - Associações de grupo** , escolha um grupo.
9. Selecione o comando **Remover** e confirme sua escolha no prompt.

   ![comando remover associação](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. Quando terminar de alterar as associações de grupo para o grupo, escolha **Salvar**.

## <a name="additional-information"></a>Informações adicionais
Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Ver grupos existentes](active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionando membros](active-directory-groups-create-azure-portal.md)
* [Gerenciar configurações de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerenciar membros de um grupo](active-directory-groups-members-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](../users-groups-roles/groups-dynamic-membership.md)
