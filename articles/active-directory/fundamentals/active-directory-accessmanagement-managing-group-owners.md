---
title: Adicionar ou remover proprietários do grupo – Azure Active Directory | Microsoft Docs
description: Instruções sobre como adicionar ou remover proprietários de grupos usando o Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2541a1d76b56f92b250fb422951769db7877213e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60249866"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Adicionar ou remover proprietários do grupo no Azure Active Directory
Os grupos do Active Directory do Azure (Azure AD) são de propriedade e gerenciados pelos proprietários do grupo. Os proprietários do grupo são atribuídos para gerenciar um grupo e seus membros por um proprietário de recurso (administrador). Os proprietários do grupo não precisam ser membros do grupo. Depois que um proprietário de grupo for atribuído, somente um proprietário de recurso poderá adicionar ou remover proprietários.

Em alguns casos, você, como administrador, pode decidir não atribuir um proprietário de grupo. Nesse caso, você se torna o proprietário do grupo. Além disso, os proprietários podem atribuir outros proprietários ao grupo, a menos que você tenha restringido isso nas configurações do grupo.

## <a name="add-an-owner-to-a-group"></a>Adicionar um proprietário a um grupo
Adicione outros proprietários de grupos a um grupo usando o Azure AD.

### <a name="to-add-a-group-owner"></a>Para adicionar um proprietário do grupo
1. Faça login no [portal do Azure](https://portal.azure.com) usando uma conta de administrador global para o diretório.

2. Selecione **Active Directory do Azure**, selecione **Grupos** e selecione o grupo ao qual você deseja adicionar um proprietário (neste exemplo, *política do MDM - Oeste*).

3. Na página **Política de MDM - Visão Geral do Oeste**, selecione **Proprietários**.

    ![Política do MDM – página de visão geral do Oeste com a opção de proprietários realçada](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Sobre o **proprietários de - Oeste - política de MDM** página, selecione **adicionar proprietários**e, em seguida, pesquise e selecione o usuário que será o novo proprietário do grupo e, em seguida, escolha **selecione**.

    ![Política do MDM - West - Página Proprietários com a opção Adicionar Proprietários destacada](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Depois de selecionar o novo proprietário, você pode atualizar a página **Proprietários** e ver o nome adicionado à lista de proprietários.

## <a name="remove-an-owner-from-a-group"></a>Remover um proprietário de um grupo
Remova um proprietário de um grupo usando o Azure AD.

### <a name="to-remove-an-owner"></a>Para remover um proprietário
1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de administrador Global para o diretório.

2. Selecione **Active Directory do Azure**, selecione **Grupos** e, em seguida, selecione o grupo para o qual você deseja remover um proprietário (neste exemplo, *política do MDM - Oeste*).

3. Na página **Política de MDM - Visão Geral do Oeste**, selecione **Proprietários**.

    ![Política do MDM – página de visão geral do Oeste com a opção de proprietários realçada](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Sobre o **proprietários de - Oeste - política de MDM** , selecione o usuário que você deseja remover como proprietário de um grupo, escolha **remover** na página de informações do usuário e selecione **Sim** para confirmar sua decisão.

    ![Página de informações do usuário com a opção de remover realçada](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Depois de remover o proprietário, você pode retornar para o **proprietários** página e ver o nome foi removido da lista de proprietários.

## <a name="next-steps"></a>Próximas etapas
- [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)

- [Cmdlets do Azure Active Directory para definir configurações de grupo](../users-groups-roles/groups-settings-cmdlets.md)

- [Usar grupos para atribuir acesso a um aplicativo de SaaS integrado](../users-groups-roles/groups-saasapps.md)

- [Integração de suas identidades locais com o Active Directory do Azure](../hybrid/whatis-hybrid-identity.md)

- [Cmdlets do Azure Active Directory para definir configurações de grupo](../users-groups-roles/groups-settings-v2-cmdlets.md)
