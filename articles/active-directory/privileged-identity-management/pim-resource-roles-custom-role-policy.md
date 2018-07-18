---
title: Usar funções personalizadas para as configurações de PIM de destino para os recursos do Azure | Microsoft Docs
description: Descreve como usar funções personalizadas para recursos do Azure com PIM.
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
ms.openlocfilehash: f086d8038e6d27990c49749438ee05e3e39a5aec
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442881"
---
# <a name="use-custom-roles-to-target-privileged-identity-management-settings"></a>Usar funções personalizadas para as configurações de PIM de destino

Pode ser necessário aplicar configurações de PIM (Privileged Identity Management) estritas para alguns membros de uma função e dar, ao mesmo tempo, maior autonomia para outras pessoas. Considere um cenário em que sua organização contrata vários colaboradores contratados para ajudar no desenvolvimento de um aplicativo que será executado em uma assinatura do Azure.

Como administrador de recursos, você quer que os funcionários sejam qualificados para o acesso sem a necessidade de aprovação. No entanto, todos os colaboradores contratados devem ser aprovados quando solicitam acesso aos recursos da organização.

Siga as etapas descritas na próxima seção para definir as configurações de PIM de destino para as funções de recurso do Azure.

## <a name="create-the-custom-role"></a>Criar a função personalizada

Para criar uma função personalizada para um recurso, siga as etapas descritas em [Criar funções personalizadas para o Controle de Acesso Baseado em Função do Azure](../role-based-access-control-custom-roles.md).

Ao criar uma função personalizada, inclua um nome descritivo para que você possa facilmente se lembrar de qual função interna você pretende duplicar.

> [!NOTE]
> Certifique-se de que a função personalizada seja uma duplicação da função interna que você quer duplicar e que seu escopo corresponda à função interna.

## <a name="apply-pim-settings"></a>Aplicar configurações de PIM

Depois que a função é criada em seu locatário, no portal do Azure, vá para o painel **Privileged Identity Management - Recursos do Azure**. Selecione o recurso ao qual a função se aplica.

![O painel “Privileged Identity Management - Recursos do Azure”](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[Definir as configurações de função do PIM](pim-resource-roles-configure-role-settings.md) que devem ser aplicadas a esses membros da função.

Por fim, [atribua funções](pim-resource-roles-assign-roles.md) ao grupo distinto de membros que você deseja ter como destino com essas configurações.

## <a name="next-steps"></a>Próximas etapas

[Revisar proprietários de assinatura e acesso](pim-resource-roles-perform-access-review.md)
