---
title: Usar funções personalizadas para recursos do Azure no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como usar funções personalizadas para recursos do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13aef9b180a671a9b42bbc6319c487be36652093
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60437339"
---
# <a name="use-custom-roles-for-azure-resources-in-pim"></a>Usar funções personalizadas para recursos do Azure no PIM

Você talvez precise aplicar configurações de Azure Active Directory (Azure AD) gerenciamento PIM (Privileged Identity) estrita para alguns membros de uma função, proporcionando maior autonomia para outras pessoas. Considere um cenário em que sua organização contrata vários colaboradores contratados para ajudar no desenvolvimento de um aplicativo que será executado em uma assinatura do Azure.

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

## <a name="next-steps"></a>Próximos passos

- [Definir configurações de função de recurso do Azure no PIM](pim-resource-roles-configure-role-settings.md)
- [Funções personalizadas no Azure](../../role-based-access-control/custom-roles.md)
