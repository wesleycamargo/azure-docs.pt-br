---
title: Privileged Identity Management para Recursos do Azure - Habilitar gerenciamento de assinaturas | Microsoft Docs
description: Saiba como os administradores globais podem gerenciar assinaturas no locatário.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/27/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4c6ae3da34fe5157314b8ea422591f7ecbd2a667
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="enable-subscription-management"></a>Habilitar gerenciamento de assinaturas

Como um Administrador Global do seu diretório, você pode não ter acesso padrão a todos os recursos de assinatura em seu locatário. Este artigo descreverá as etapas para lhe dar acesso a todas as assinaturas no seu locatário e uma abordagem recomendada para que continue em conformidade com os controles de segurança que sua organização exige após o recebimento do acesso.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>Quem pode habilitar o gerenciamento de assinaturas no meu diretório?

Cada usuário atribuído à função de Administrador global deve seguir as etapas abaixo para habilitar o gerenciamento de assinaturas. Depois que tiver habilitado o gerenciamento de assinaturas por conta própria, você poderá adicionar outros Administradores globais que talvez precisem também de acesso aos recursos. Não há nenhuma configuração de diretório que habilita o acesso para todos os membros da função de Administrador global.

## <a name="log-on-to-the-azure-portal"></a>Fazer logon no Portal do Azure

Comece fazendo login no Portal do Azure com uma conta que seja um membro qualificado ou ativo da função de Administrador global. Se a conta for um Administrador global qualificado, você deverá primeiro ativar a função antes de passar para a próxima etapa.

## <a name="access-the-azure-ad-admin-center"></a>Acesse o centro de administração do Microsoft Azure AD

Agora que está conectado ao Portal do Azure como um Administrador global, você pode editar as configurações que darão acesso às assinaturas do Azure. Navegue até o centro de administração do Microsoft Azure AD, localize e selecione a guia Propriedades no painel de navegação esquerdo.

![](media/azure-pim-resource-rbac/aad_properties.png)

Na lista de propriedades, alterne a opção intitulada "O Administrador global pode gerenciar assinaturas do Azure" para "Sim".

![](media/azure-pim-resource-rbac/aad_properties_save.png)

## <a name="navigate-to-azure-ad-pim"></a>Navegue até o PIM do Microsoft Azure AD

Com essa opção habilitada, a conta é automaticamente adicionada à função "Administrador de acesso do usuário" para todos os recursos de assinatura no locatário. A partir daí, navegue para o PIM do Microsoft Azure Active Directory e selecione os recursos do Azure na seção Gerenciar do painel de navegação esquerdo.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>Gerenciar e descobrir recursos

Se a sua organização já estiver usando o PIM do Microsoft Azure Active Directory para proteger os administradores no Azure Active Directory, você verá uma lista de assinaturas quando a folha for carregada.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> Se você não vir nenhum recurso, verifique se:
>- Sua função de Administrador global não expirou 
>- Sua organização tem uma assinatura do Azure

![](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>Configurar atribuições

Selecione uma assinatura na lista e se atribua (ou um grupo de qual você é membro) como um proprietário qualificado do recurso. 
[Leia mais sobre a atribuição de funções](pim-resource-roles-assign-roles.md).

Repita esse processo para cada recurso antes de avançar para a próxima etapa.

## <a name="clean-up-standing-access"></a>Limpar acesso permanente

Agora que você tem atribuições qualificadas para as assinaturas importantes em sua organização, você pode limpar acesso permanente ao desabilitar a opção nas propriedades do diretório:

![](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>Próximas etapas

[Descobrir recursos](pim-resource-roles-discover-resources.md)

[Definir configurações de função](pim-resource-roles-configure-role-settings.md)








