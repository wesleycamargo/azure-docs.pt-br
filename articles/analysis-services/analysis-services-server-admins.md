---
title: Gerenciar administradores de servidor no Azure Analysis Services | Microsoft Docs
description: Saiba como gerenciar administradores de servidor para um servidor do Analysis Services no Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9d8f74bd66fc7c980c4fc5f83492aad7d8a4aa5c
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866960"
---
# <a name="manage-server-administrators"></a>Gerenciar administradores de servidor
Os administradores do servidor devem ser um usuário ou grupo de segurança válido no Azure AD (Azure Active Directory) para o locatário no qual o servidor reside. É possível usar **Administradores do Analysis Services** para seu servidor no Portal do Azure ou Propriedades do Servidor no SSMS para gerenciar os administradores do servidor. 

> [!NOTE]
> Grupos de segurança devem ter a propriedade `MailEnabled` definida como `True`.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Para adicionar administradores do servidor usando o Portal do Azure
1. No portal, para o seu servidor, clique em **Administradores do Analysis Services**.
2. Em **\<nome do servidor> - Administradores do Analysis Services**, clique em **Adicionar**.
3. Em **Adicionar Administradores de Servidor**, selecione contas de usuário do seu Azure Active Directory ou convide usuários externos por endereço de email.

    ![Administradores de servidor no portal do Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Para adicionar administradores do servidor usando o SSMS
1. Clique com botão direito do mouse em servidor > **Propriedades**.
2. Em **Propriedades do Analysis Server**, clique em **Segurança**.
3. Clique em **Adicionar** e, em seguida, insira o endereço de email de um usuário ou grupo em seu Azure AD.
   
    ![Adicionar administradores do servidor no SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>Próximas etapas 
[Autenticação e permissões de usuário](analysis-services-manage-users.md)  
[Gerenciar usuários e funções de banco de dados](analysis-services-database-users.md)  
[Controle de acesso baseado em função](../role-based-access-control/overview.md)  

