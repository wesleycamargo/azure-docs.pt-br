---
title: Gerenciar o acesso aos recursos do Azure com o PIM (Privileged Identity Management)
description: Saiba mais sobre como usar o Gerenciamento de Acesso Baseado em Função no PIM para acessar os recursos do Azure.
services: active-directory
documentationcenter: ''
author: skwan
manager: mtillman
editor: bryanla
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 1e74579ef2f0e18f23a40dfc573177938b9b726f
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2018
---
# <a name="manage-access-to-azure-resources-with-privileged-identity-management"></a>Gerenciar acesso aos recursos do Azure com Privileged Identity Management

Para proteger contas privilegiadas contra ataques cibernéticos mal-intencionados, use o PIM (Azure Active Directory Privileged Identity Management) para reduzir o tempo de exposição de privilégios e aumentar a visibilidade de seu uso por meio de relatórios e alertas. O PIM faz isso limitando os usuários a apenas usar seus privilégios JIT (“Just-In-Time”) ou atribuindo privilégios por uma duração reduzida após a qual eles são revogados automaticamente. 

Agora você pode usar o PIM com o RBAC (Controle de Acesso Baseado em Função) para gerenciar, controlar e monitorar o acesso aos recursos do Azure. O PIM pode gerenciar a associação de funções internas e personalizadas para ajudá-lo a: 

- Habilitar o acesso “Just-In-Time” sob demanda aos recursos do Azure
- Expirar o acesso aos recursos automaticamente para os usuários e grupos atribuídos
- Atribuir o acesso temporário aos recursos do Azure para tarefas rápidas ou escalas de plantão
- Obter alertas quando novos usuários ou grupos recebem o acesso aos recursos e quando eles ativam atribuições qualificadas

Para obter mais informações, consulte [Visão geral do Controle de Acesso Baseado em Função no Azure PIM](privileged-identity-management/azure-pim-resource-rbac.md).