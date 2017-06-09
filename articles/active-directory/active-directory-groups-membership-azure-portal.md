---

title: Gerenciar os grupos aos quais seu grupo pertence no Azure Active Directory | Microsoft Docs
description: "Os grupos podem conter outros grupos no Azure Active Directory. Veja como gerenciar essas associações."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e785c2d0-7724-47d4-a56e-c58280c08a14
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 08e04a6590176c4084ca47b4bd6cbb22500eca2d
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017


---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>Gerenciar a quais grupos um grupo pertence no seu locatário do Azure Active Directory
Os grupos podem conter outros grupos no Azure Active Directory. Veja como gerenciar essas associações.

## <a name="how-do-i-find-the-groups-my-group-is-a-member-of"></a>Como localizo os grupos dos quais meu grupo é um membro?
1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Escolha **Mais serviços**, insira **Usuários e grupos** na caixa de texto e selecione **Enter**.

   ![Abrir o gerenciamento de usuários](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
3. Na folha **Usuários e grupos**, escolha **Todos os grupos**.

   ![Abrir a folha de grupos](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
4. Na folha **Usuários e grupos - Todos os grupos** , escolha um grupo.
5. Na folha **Grupo – *nomedogrupo*** selecione **Associações de grupo** .

   ![Abrir a folha de associações de grupo](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
6. Para adicionar seu grupo como membro de outro grupo, na folha **Grupo - Associações de grupo**, selecione o comando **Adicionar**.
7. Selecione um grupo na folha **Selecionar Grupo** e pressione o botão **Selecionar** na parte inferior da folha. Você só pode adicionar o grupo a um grupo por vez. A caixa **Usuário** filtra a exibição com base na correspondência de sua entrada com qualquer parte de um nome de usuário ou dispositivo. Caracteres curinga não são aceitos nessa caixa.

   ![Adicionar uma associação de grupo](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. Para remover seu grupo como um membro de outro grupo, na folha **Grupo - Associações de grupo** , escolha um grupo.
9. Na folha ***groupname***, selecione o comando **Remover** e confirme sua escolha no prompt.

   ![comando remover associação](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. Quando terminar de alterar as associações de grupo para o grupo, escolha **Salvar**.

## <a name="additional-information"></a>Informações adicionais
Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Ver grupos existentes](active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionando membros](active-directory-groups-create-azure-portal.md)
* [Gerenciar configurações de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerenciar membros de um grupo](active-directory-groups-members-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](active-directory-groups-dynamic-membership-azure-portal.md)

