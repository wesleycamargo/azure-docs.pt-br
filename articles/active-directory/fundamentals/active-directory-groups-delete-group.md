---
title: Excluir um grupo - Active Directory do Azure | Microsoft Docs
description: Instruções sobre como criar um grupo usando o Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.openlocfilehash: 0e50abb4c7aa5641d92bf0a9f5c4516fc29125e2
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452557"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Excluir um grupo usando o Microsoft Azure Active Directory
Você pode excluir um grupo do Azure Active Directory (Microsoft Azure Active Directory) para qualquer número de motivos, mas normalmente, será porque você:

- atribuiu incorretamente o **Tipo de grupo** à opção errada.

- criou o grupo errado ou um grupo duplicado por engano. 

- não precisa mais do grupo.

## <a name="to-delete-a-group"></a>Para excluir um grupo
1. Faça login no [portal do Azure](https://portal.azure.com) usando uma conta de administrador global para o diretório.

2. Selecione **Active Directory do Azure** e, em seguida, selecione **Grupos**.

3. Na página **Grupos - Todos os grupos**, pesquise e selecione o grupo que você deseja excluir. Para essas etapas, usaremos **política de MDM - Leste**.

    ![Grupos - Todos os grupos, o nome do grupo destacado](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Na página **MDM policy - East Overview**, selecione **Delete**.

    O grupo é excluído do seu locatário do Azure Active Directory.

    ![Política de MDM - página Visão Geral Leste, opção de exclusão destacada](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Próximas etapas

- Se você excluir um grupo por engano, poderá criá-lo novamente. Para mais informações, consulte [Como criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md).

- Se você excluir um grupo do Office 365 por engano, você poderá restaurá-lo. Para obter mais informações, consulte [restaurar um grupo do Office 365 excluído](../users-groups-roles/groups-restore-deleted.md).
