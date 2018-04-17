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
ms.openlocfilehash: 26ba68be6d4932da77befaf7c968525393c0a033
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Habilitar um provedor de serviços de nuvem gerenciar sua assinatura do Azure pilha

*Aplica-se a: sistemas integrados de pilha do Azure*

Se você estiver usando a pilha do Azure com um provedor de serviços de nuvem (CSP), seu acesso aos recursos na sua assinatura do Azure e na pilha do Azure pode ser gerenciado pelo provedor. Ou você pode gerenciar sua própria assinatura. Este artigo analisa como você pode habilitar o provedor de serviços para acessar sua assinatura em seu nome ou para certificar-se de que o provedor de serviços pode gerenciar seu serviço.

> [!Note]  
>  Se as etapas a seguir são ignoradas e o CSP já não estiver gerenciando sua conta, o CSP não será capaz de gerenciar sua assinatura do Azure pilha em seu nome.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Gerenciar sua assinatura com um provedor de serviços de nuvem

1. Adicione seu CSP como usuário convidado com a função de usuário ao seu diretório do locatário.  Para obter etapas sobre como adicionar um usuário, consulte [adicionar novos usuários ao Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. O CSP irá criar a assinatura do Azure pilha local para você.
3. Você está pronto para começar a usar a pilha do Azure.
3. O CSP deve, em seguida, crie um recurso em sua assinatura para verificar que eles também podem gerenciar seus recursos. Por exemplo, eles podem [criar uma máquina virtual do Windows com o portal do Azure pilha](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Habilitar o provedor de serviços de nuvem gerenciar sua assinatura usando os direitos RBAC

Adicione o CSP como proprietário à sua assinatura. 

1. Adicione o CSP como usuário convidado. com a função de proprietário para o diretório de locatário.  Para obter etapas sobre como adicionar um usuário, consulte [adicionar novos usuários ao Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Adicione função de proprietário para o usuário convidado do CSP. Para obter etapas sobre como adicionar o usuário do CSP à sua assinatura, consulte [Use Role-Based o controle de acesso para gerenciar o acesso aos recursos da sua assinatura do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. O CSP irá criar a assinatura do Azure pilha local para você.
4. Você está pronto para começar a usar a pilha do Azure.
5. O CSP deve, em seguida, crie um recurso em sua assinatura para verificar que eles podem gerenciar seus recursos. 

## <a name="next-steps"></a>Próximas etapas

  - Para saber mais sobre como recuperar informações de uso do recurso da pilha do Azure, consulte [uso e cobrança no Azure pilha](../azure-stack-billing-and-chargeback.md).
