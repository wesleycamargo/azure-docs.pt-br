---
title: Privileged Identity Management para Recursos do Azure - Usar funções personalizadas para as configurações de PIM de destino | Microsoft Docs
description: Descreve como usar funções personalizadas no PIM para recursos do Azure.
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
ms.openlocfilehash: 9ac14125b837f45634870941e72ea63e9921ca70
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="use-custom-roles-to-target-pim-settings"></a>Usar funções personalizadas para as configurações de PIM de destino

Pode ser necessário aplicar configurações de PIM estritas para alguns membros de uma função e dar, ao mesmo tempo, maior autonomia para outras pessoas. Considere um cenário em que sua organização contrata vários colaboradores contratados para ajudar no desenvolvimento de um aplicativo que será executado em uma assinatura do Azure. 

Como administrador de recursos, você gostaria que os funcionários de sua organização tivessem acesso qualificado sem aprovação necessária, mas todos os colaboradores contratados precisam solicitar aprovação quando solicitam ativação. Siga as etapas a seguir que descrevem o processo para habilitar as configurações de PIM de destino para as funções de recurso do Azure.

## <a name="create-the-custom-role"></a>Criar a função personalizada

[Use este guia para criar uma função personalizada para um recurso](../role-based-access-control-custom-roles.md).

Inclua um nome descritivo para que você possa facilmente se lembrar de qual função interna você pretende duplicar.

>[!NOTE]
>Certifique-se de que a função personalizada seja uma duplicação da função desejada e seu escopo corresponda à função interna.

## <a name="apply-pim-settings"></a>Aplicar configurações de PIM

Depois que a função é criada no seu locatário, navegue até o PIM e selecione o recurso que a função tem como escopo.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[Definir as configurações de função do PIM](pim-resource-roles-configure-role-settings.md) que devem ser aplicadas a esses membros

Por fim, [atribua funções](pim-resource-roles-assign-roles.md) ao grupo distinto de membros que você deseja ter como destino com essas configurações.

## <a name="next-steps"></a>Próximas etapas

[Examine os proprietários de assinatura](pim-resource-roles-perform-access-review.md)
