---
title: Privileged Identity Management para Recursos do Azure - Descobrir e gerenciar recursos do Azure | Microsoft Docs
description: Descreve como proteger os recursos do Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 78650e47ec92aa144e4ccc8c57f309240bf31ee3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="discover-and-manage-azure-resources"></a>Descobrir e gerenciar os recursos do Azure

Se a organização já estiver usando o PIM do Azure AD para proteger os administradores no seu diretório ou você for um proprietário de assinatura que deseja proteger os recursos de produção, você está no local certo.

Quando habilita o PIM para recursos do Azure pela primeira vez, você precisa descobrir e selecionar os recursos a serem protegidos com PIM. Não há nenhum limite para o número de recursos que você pode gerenciar com o PIM, mas é recomendável começar com seus recursos (produção) mais importantes primeiro.

> [!Note]
> Somente os recursos de assinatura podem ser pesquisados e selecionados para gerenciamento. Optar por gerenciar uma assinatura no PIM permitirá o gerenciamento de todos os recursos filhos também.

## <a name="discover-resources"></a>Descobrir recursos

Navegue para o PIM do Azure AD e selecione os recursos do Azure na seção Gerenciar do painel de navegação esquerdo.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Se esta for a primeira vez que usa o PIM para recursos do Azure, você precisará executar a descoberta para localizar recursos para gerenciar.
Clique no botão "Descobrir recursos" no centro da tela para iniciar a experiência de descoberta.

![](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Se outro administrador de recursos ou diretório em sua organização já estiver gerenciando um recurso do Azure com PIM ou tiver uma atribuição de função qualificada para um recurso, a exibição de lista conterá a mensagem "Descubra recursos ou ative uma atribuição de função qualificada para continuar". 

![](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

Quando selecionar o botão na barra de ação ou no meio da tela para descobrir recursos, você verá uma lista de assinaturas disponíveis para gerenciamento. Neste ponto, se você vir assinaturas realçadas, será indicado que elas são protegidas pelo PIM.

> [!Note]
> Para impedir que outro administrador de recursos remova as configurações do PIM, quando uma assinatura for gerenciada, ela não poderá ter seu gerenciamento cancelado.

![](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

Passe o cursor sobre uma assinatura que você deseja proteger com PIM e marque a caixa na extremidade esquerda da linha. Você pode selecionar várias assinaturas de uma vez.

![](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Para iniciar o processo de integração, selecione o botão "Gerenciar recursos" na barra na parte superior da tela.

![](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

Os recursos selecionados agora são gerenciados pelo PIM. Fechar a tela de descoberta usando o "X" no canto superior direito da página e clique em Atualizar na barra na parte superior da tela Gerenciar recursos do Azure para começar a gerenciar as configurações de PIM e atribuir membros.

![](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>Próximas etapas

[Definir configurações de função](pim-resource-roles-configure-role-settings.md)

[Atribuir funções no PIM](pim-resource-roles-assign-roles.md)
