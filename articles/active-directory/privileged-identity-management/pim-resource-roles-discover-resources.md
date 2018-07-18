---
title: Descobrir e gerenciar recursos do Azure usando Privileged Identity Management | Microsoft Docs
description: Descreve como proteger os recursos do Azure usando o PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: aca218a33d148e9f53f405f9cda98a701a7443cc
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442518"
---
# <a name="discover-and-manage-azure-resources-by-using-privileged-identity-management"></a>Descobrir e gerenciar recursos do Azure usando Privileged Identity Management

Saiba como descobrir e gerenciar recursos do Azure ao usar o Privileged Identity Management (PIM) no Azure Active Directory (Azure AD). Essas informações podem ser úteis para organizações que já usam PIM para proteger os recursos do administrador e os proprietários de assinatura que desejam proteger os recursos de produção.

Ao configurar o PIM para recursos do Azure pela primeira vez, você precisa descobrir e selecionar os recursos a serem protegidos com PIM. Não há nenhum limite para o número de recursos que você pode gerenciar com o PIM. No entanto, é recomendável começar com seus recursos  mais importantes (produção).

> [!NOTE]
> Você só pode procurar e selecionar os recursos de assinatura para gerenciar usando o PIM. Quando você gerenciar uma assinatura no PIM, você também pode gerenciar recursos filhos na assinatura.

## <a name="discover-resources"></a>Descobrir recursos

No portal do Azure, vá até o painel **Privileged Identity Management**. No menu à esquerda, na seção **GERENCIAR**, selecione **Recursos do Azure**.

![O painel “Privileged Identity Management - Recursos do Azure”](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Se esta for a primeira vez que usa o PIM para recursos do Azure, antes execute a descoberta para localizar recursos para gerenciar. No painel **Descobrir recursos**, selecione o botão **Descobrir recursos** para iniciar a experiência de descoberta.

![Painel “Descobrir recursos”](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Se outro administrador de recursos ou diretório em sua organização já estiver gerenciando um recurso do Azure usando o PIM ou se você tiver uma atribuição de função qualificada para um recurso, a exibição de lista exibirá a mensagem **Descubra recursos ou ative uma atribuição de função qualificada para continuar**. 

![O botão "Descobrir recursos" no painel "Privileged Identity Manager - Recursos do Azure"](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

Quando você seleciona o botão **Descobrir recursos**, pelo menu superior ou no meio do painel, uma lista de assinaturas é exibida a qual você pode gerenciar. As assinaturas que são realçadas já estão protegidas por PIM.

> [!NOTE]
> Para impedir que outro administrador de recursos remova as configurações do PIM, depois que uma assinatura for definida como gerenciada, ela não poderá ter seu gerenciamento cancelado.

![Painel “Recursos do Azure - Descoberta”](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

Na coluna **RECURSO**, coloque o mouse sobre uma assinatura que você deseja proteger com o PIM. Em seguida, marque a caixa de seleção à esquerda do nome do recurso. Você pode selecionar várias assinaturas de uma vez.

![A lista de recursos no painel "Recursos do Azure - Descoberta"](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Para iniciar o processo de integração, no menu superior, selecione **Gerenciar recurso**.

![O botão “Gerenciar recurso” no painel "Recursos do Azure - Descoberta"](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

Os recursos selecionados agora são gerenciados pelo PIM. Para fechar a tela de descoberta, no canto superior direito, selecione **X**. Para começar a gerenciar configurações de PIM e atribuir membros, no menu na parte superior do painel **Privileged Identity Management - Recursos do Azure**, selecione o botão **Atualizar**.

![Botão "Atualizar" no menu superior do painel "Privileged Identity Management - Recursos do Azure"](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>Próximas etapas

- [Definir configurações de função](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções no PIM](pim-resource-roles-assign-roles.md)
