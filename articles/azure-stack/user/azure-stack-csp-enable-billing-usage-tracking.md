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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: fca08ef1d803c3bd47b0ae925c4dd12255175f2c
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630708"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Habilitar um provedor de serviços de nuvem gerenciar sua assinatura do Azure Stack

*Aplica-se a: sistemas integrados do Azure Stack*

Se você estiver usando o Azure Stack com um provedor de serviços de nuvem (CSP), você pode optar por gerenciar sua própria assinatura para acessar os recursos no Azure e no Azure Stack. Você também pode permitir que o provedor de gerenciar sua assinatura para você. Este artigo mostra como:

 * O acesso do provedor de serviço dê à sua assinatura.
 * Verifique se que o provedor de serviços pode gerenciar seu serviço.

> [!Note]
>  Se o CSP não está gerenciando sua conta, e você ignorar as etapas a seguir, o CSP não pode gerenciar sua assinatura do Azure Stack para você.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Gerenciar sua assinatura com um provedor de serviços de nuvem

Adicionar o CSP conforme **usuário** à sua assinatura.

1. Adicione seu CSP como usuário convidado com a função de usuário ao seu diretório de locatário.  Para obter etapas sobre como adicionar um usuário, consulte [adicionar novos usuários ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. O CSP cria a assinatura do Azure Stack local para você.
3. Você está pronto para começar a usar o Azure Stack.
4. O CSP deve criar um recurso em sua assinatura para verificar que eles também podem gerenciar seus recursos. Por exemplo, eles podem [criar uma máquina virtual do Windows com o portal do Azure Stack](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Habilitar o provedor de serviços de nuvem gerenciar sua assinatura usando direitos RBAC

Adicionar o CSP conforme **proprietário** à sua assinatura.

1. Adicione seu CSP como usuário convidado ao seu diretório de locatário.  Para obter etapas sobre como adicionar um usuário, consulte [adicionar novos usuários ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Adicione a função de proprietário para o usuário convidado CSP. Para obter etapas sobre como adicionar o usuário do CSP à sua assinatura, consulte [controle de acesso baseado em função para gerenciar o acesso aos recursos de sua assinatura do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. O CSP cria a assinatura do Azure Stack local para você.
4. Você está pronto para começar a usar o Azure Stack.
5. O CSP deve criar um recurso em sua assinatura para verificar que eles podem gerenciar seus recursos.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como recuperar informações de uso de recursos do Azure Stack, consulte [uso e cobrança do Azure Stack](../azure-stack-billing-and-chargeback.md).
