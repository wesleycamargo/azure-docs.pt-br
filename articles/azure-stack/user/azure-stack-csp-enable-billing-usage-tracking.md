---
title: Habilitar um provedor de serviços de nuvem gerenciar sua assinatura do Azure Stack | Microsoft Docs
description: Habilite o provedor de serviço para acessar uma assinatura no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: 5d7398853e20702aef450e2532784f0dca7aac57
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246595"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Habilitar um provedor de serviços de nuvem gerenciar sua assinatura do Azure Stack

*Aplica-se a: Sistemas integrados do Azure Stack*

Se você usar o Azure Stack com um provedor de serviços de nuvem (CSP), você pode optar por gerenciar sua própria assinatura para acessar os recursos no Azure e no Azure Stack. Você também pode permitir que o provedor de gerenciar sua assinatura para você. Este artigo mostra como:

* O acesso do provedor de serviço dê à sua assinatura.
* Verifique se que o provedor de serviços pode gerenciar seu serviço.

> [!NOTE]
> Se o CSP não está gerenciando sua conta, e você ignorar as etapas a seguir, o CSP não pode gerenciar sua assinatura do Azure Stack para você.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Gerenciar sua assinatura com um provedor de serviços de nuvem

Adicionar o CSP conforme **usuário** à sua assinatura.

1. Adicione seu CSP como usuário convidado com a função de usuário ao seu diretório de locatário. Para obter as etapas adicionar um usuário, consulte [adicionar novos usuários ao Azure Active Directory](../../active-directory/add-users-azure-active-directory.md)
2. O CSP cria a assinatura do Azure Stack local para você. Você está pronto para começar a usar o Azure Stack.
3. O CSP deve criar um recurso em sua assinatura para verificar que eles também podem gerenciar seus recursos. Por exemplo, eles podem [criar uma máquina virtual do Windows com o portal do Azure Stack](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Habilitar o provedor de serviços de nuvem gerenciar sua assinatura usando direitos RBAC

Adicionar o CSP conforme **proprietário** à sua assinatura.

1. Adicione seu CSP como usuário convidado ao seu diretório de locatário. Para obter as etapas adicionar um usuário, consulte [adicionar novos usuários ao Azure Active Directory](../../active-directory/add-users-azure-active-directory.md)
2. Adicione a **proprietário** função ao usuário convidado CSP. Para obter as etapas adicionar o usuário do CSP à sua assinatura, consulte [controle de acesso baseado em função para gerenciar o acesso aos recursos de sua assinatura do Azure](../../role-based-access-control/role-assignments-portal.md). O CSP cria a assinatura do Azure Stack local para você. Você está pronto para começar a usar o Azure Stack.
3. O CSP deve criar um recurso em sua assinatura para verificar que eles podem gerenciar seus recursos.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como recuperar informações de uso de recursos do Azure Stack, consulte [uso e cobrança do Azure Stack](../azure-stack-billing-and-chargeback.md).
