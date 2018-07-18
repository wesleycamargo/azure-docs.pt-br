---
title: Habilitar um provedor de serviços de nuvem gerenciar sua assinatura do Azure pilha | Microsoft Docs
description: Habilite o provedor de serviço para acessar uma assinatura na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: f0cff8f575b87872c0032854f1916b140d7fd62b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Habilitar um provedor de serviços de nuvem gerenciar sua assinatura do Azure pilha

*Aplica-se a: sistemas integrados de pilha do Azure*

Se você estiver usando a pilha do Azure com um provedor de serviços de nuvem (CSP), você poderá gerenciar sua própria assinatura para acessar os recursos no Azure e na pilha do Azure. Você também pode permitir que o provedor de gerenciar sua assinatura para você. Este artigo mostra como:

 * Concede acesso de provedor de serviço sua assinatura.
 * Verifique se que o provedor de serviços pode gerenciar seu serviço.

> [!Note]
>  Se o CSP não estiver gerenciando sua conta, e você ignorar as etapas a seguir, o CSP não pode gerenciar sua assinatura de pilha do Azure para você.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Gerenciar sua assinatura com um provedor de serviços de nuvem

Adicionar o CSP como **usuário** à sua assinatura.

1. Adicione seu CSP como usuário convidado com a função de usuário ao seu diretório do locatário.  Para obter etapas sobre como adicionar um usuário, consulte [adicionar novos usuários ao Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. O CSP cria a assinatura do Azure pilha local para você.
3. Você está pronto para começar a usar a pilha do Azure.
4. O CSP deve criar um recurso na sua assinatura para verificar que eles também podem gerenciar seus recursos. Por exemplo, eles podem [criar uma máquina virtual do Windows com o portal do Azure pilha](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Habilitar o provedor de serviços de nuvem gerenciar sua assinatura usando os direitos RBAC

Adicionar o CSP como **proprietário** à sua assinatura.

1. Adicione o CSP como usuário convidado para o diretório de locatário.  Para obter etapas sobre como adicionar um usuário, consulte [adicionar novos usuários ao Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Adicione a função de proprietário para o usuário convidado do CSP. Para obter etapas sobre como adicionar o usuário do CSP à sua assinatura, consulte [Use Role-Based o controle de acesso para gerenciar o acesso aos recursos da sua assinatura do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. O CSP cria a assinatura do Azure pilha local para você.
4. Você está pronto para começar a usar a pilha do Azure.
5. O CSP deve criar um recurso na sua assinatura para verificar que eles podem gerenciar seus recursos.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como recuperar informações de uso do recurso da pilha do Azure, consulte [uso e cobrança no Azure pilha](../azure-stack-billing-and-chargeback.md).
