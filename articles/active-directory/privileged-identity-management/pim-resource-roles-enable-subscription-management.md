---
title: Habilitar gerenciamento de assinaturas em seu locatário – Azure | Microsoft Docs
description: Saiba como habilitar o gerenciamento de assinatura em seu locatário ao usar o Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 89bb6fd48c58b7672b7a2251a172cc169093d368
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189988"
---
# <a name="enable-subscription-management-in-your-tenant"></a>Habilitar gerenciamento de assinaturas em seu locatário

Como administrador global do seu diretório, você pode não ter acesso padrão a todos os recursos de assinatura em seu locatário. Este artigo descreve as etapas para ter acesso a todas as assinaturas em seu locatário. Ele também fornece uma abordagem recomendada para o restante compatível com os controles de segurança que sua organização exige depois de receber acesso.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>Quem pode habilitar o gerenciamento de assinaturas no meu diretório?

Cada usuário atribuído à função de administrador global deve seguir as etapas abaixo para habilitar o gerenciamento de assinaturas. Depois que tiver habilitado o gerenciamento de assinaturas por conta própria, você poderá adicionar outros administradores globais que talvez precisem também de acesso aos recursos. Não há nenhuma configuração de diretório que habilita o acesso para todos os membros da função de administrador global.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no Portal do Azure com uma conta que seja um membro qualificado ou ativo da função de administrador global. Se a conta for um administrador global qualificado, você deverá primeiro ativar a função antes de passar para a próxima etapa.

## <a name="access-the-azure-active-directory-admin-center"></a>Acesse o centro de administração do Azure Active Directory

Agora que está conectado ao portal do Azure como um administrador global, você pode editar as configurações que dão acesso às assinaturas do Azure. Navegue até o centro de administração do Azure Active Directory (Azure AD) e selecione **Propriedades**.

![Captura de tela do centro de administração do Azure AD, com Propriedades realçado](media/azure-pim-resource-rbac/aad_properties.png)

Na lista de propriedades, em **Administrador global pode gerenciar assinaturas do Azure** selecione **Sim**.

![Captura de tela da página Propriedades, com configuração alterada para Sim](media/azure-pim-resource-rbac/aad_properties_save.png)

Agora, a conta é automaticamente adicionada à função administrador de acesso do usuário para todos os recursos de assinatura no locatário.

## <a name="browse-to-azure-ad-pim"></a>Navegue até o PIM do Azure AD

 A partir daqui, vá para Azure AD Privileged Identity Management (PIM). Em **Gerenciar**, selecione **recursos do Azure**.

![Captura de tela do PIM, com recursos do Azure realçados](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>Gerenciar e descobrir recursos

Se a sua organização já estiver usando o PIM do Microsoft Azure Active Directory para proteger os administradores no Azure AD, você pode ver uma lista de assinaturas quando a folha for carregada.

![Captura de tela do PIM, com a lista de assinaturas mostrada na folha](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> Se você não vir nenhum recurso, confirme se:
>- Sua função de administrador global não expirou. 
>- Sua organização tem uma assinatura do Azure.

![Captura de tela do PIM, com a lista de recursos vazia](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>Configurar atribuições

Selecione uma assinatura na lista e se atribua (ou um grupo do qual você é membro) como um proprietário qualificado do recurso. 
[Leia mais sobre a atribuição de funções](pim-resource-roles-assign-roles.md).

Repita esse processo para cada recurso antes de avançar para a próxima etapa.

## <a name="clean-up-standing-access"></a>Limpar acesso permanente

Agora que você tem atribuições qualificadas para as assinaturas importantes em sua organização, você pode limpar acesso permanente ao desabilitar a opção nas propriedades do diretório.

![Captura de tela da página Propriedades, com configuração alterada para Não](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>Próximas etapas

- [Descobrir os recursos do Azure para gerenciar no PIM](pim-resource-roles-discover-resources.md)
- [Definir configurações de função de recurso do Azure no PIM](pim-resource-roles-configure-role-settings.md)
