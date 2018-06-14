---
title: Gerenciar o acesso aos recursos do Azure com o PIM (Privileged Identity Management)
description: Saiba mais sobre como usar o Gerenciamento de Acesso Baseado em Função no PIM para acessar os recursos do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: fb0a1ff3821efd7114b509b72e143d5240b61b4c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204202"
---
# <a name="manage-access-to-azure-resources-with-privileged-identity-management"></a>Gerenciar acesso aos recursos do Azure com Privileged Identity Management

Para proteger contas privilegiadas contra ataques cibernéticos mal-intencionados, use o PIM (Azure Active Directory Privileged Identity Management) para reduzir o tempo de exposição de privilégios e aumentar a visibilidade de seu uso por meio de relatórios e alertas. O PIM faz isso limitando os usuários a apenas usar seus privilégios JIT (“Just-In-Time”) ou atribuindo privilégios por uma duração reduzida após a qual eles são revogados automaticamente. 

Agora você pode usar o PIM com o RBAC (Controle de Acesso Baseado em Função) para gerenciar, controlar e monitorar o acesso aos recursos do Azure. O PIM pode gerenciar a associação de funções internas e personalizadas para ajudá-lo a: 

- Habilitar o acesso “Just-In-Time” sob demanda aos recursos do Azure
- Expirar o acesso aos recursos automaticamente para os usuários e grupos atribuídos
- Atribuir o acesso temporário aos recursos do Azure para tarefas rápidas ou escalas de plantão
- Obter alertas quando novos usuários ou grupos recebem o acesso aos recursos e quando eles ativam atribuições qualificadas

Para obter mais informações, consulte [Visão geral do Controle de Acesso Baseado em Função no Azure PIM](../active-directory/privileged-identity-management/azure-pim-resource-rbac.md).