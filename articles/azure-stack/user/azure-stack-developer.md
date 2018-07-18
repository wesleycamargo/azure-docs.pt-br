---
title: Desenvolver aplicativos para a pilha do Azure | Microsoft Docs
description: Considerações sobre desenvolvimento de aplicativos de protótipo na pilha do Azure
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a1b5a90ca40ce2b19186220344b22ec0ae77e34b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
---
# <a name="develop-for-azure-stack"></a>Desenvolver para o Azure Stack

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode começar a desenvolver aplicativos hoje em dia, mesmo se você não tiver acesso a um ambiente de pilha do Azure. Porque a pilha do Azure fornece os serviços do Microsoft Azure que são executados em seu data center, você pode usar os processos e ferramentas semelhantes para desenvolver em relação a pilha do Azure, como você faria com o Azure. Com alguma preparação e usando as diretrizes nos tópicos a seguir, você pode usar o Azure para emular um ambiente de pilha do Azure.

* No Azure, você pode criar modelos de Gerenciador de recursos do Azure que são passíveis de implantação para a pilha do Azure. Consulte [considerações sobre o modelo](azure-stack-develop-templates.md) para obter orientação sobre o desenvolvimento de modelos para garantir a portabilidade.
* Há diferenças na disponibilidade do serviço e controle de versão do serviço entre o Azure e a pilha do Azure. Você pode usar o [módulo de política do Azure pilha](azure-stack-policy-module.md) para restringir os tipos de recursos e disponibilidade do serviço do Azure a que está disponível na pilha do Azure. Restringir serviços garante que seus aplicativos usam serviços disponíveis para a pilha do Azure.
* O [modelos de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates) são exemplos de cenários comuns que mostram como desenvolver modelos que podem ser implantados para o Azure e a pilha do Azure.
