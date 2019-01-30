---
title: Desenvolver aplicativos para o Azure Stack | Microsoft Docs
description: Considerações sobre desenvolvimento de aplicativos de criação de protótipos no Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 2f0d311851abe1c0fb01ec08dc82626805b35eb1
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251411"
---
# <a name="develop-for-azure-stack"></a>Desenvolver para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode começar a desenvolver aplicativos hoje em dia, mesmo se você não tiver acesso a um ambiente do Azure Stack. Porque o Azure Stack oferece serviços do Microsoft Azure que são executados em seu datacenter, você pode usar as ferramentas e processos semelhantes para desenvolver em relação à pilha do Azure, como você faria com o Azure.

## <a name="development-considerations"></a>Considerações sobre o Desenvolvimento

Com algumas preparações e usando as diretrizes nos tópicos a seguir, você pode usar o Azure para emular um ambiente do Azure Stack.

* No Azure, você pode criar modelos do Azure Resource Manager que são passíveis de implantação no Azure Stack. Ver [considerações sobre o modelo](azure-stack-develop-templates.md) para obter orientação sobre como desenvolver modelos para garantir a portabilidade.
* Há diferenças na disponibilidade do serviço e controle de versão de serviço do Azure e o Azure Stack. Você pode usar o [módulo de política do Azure Stack](azure-stack-policy-module.md) para restringir os tipos de recursos e a disponibilidade do serviço do Azure para o que está disponível no Azure Stack. Restringir serviços garante que seus aplicativos dependem de serviços disponíveis para o Azure Stack.
* O [modelos de início rápido do Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) são exemplos de cenários comuns que mostram como desenvolver modelos que podem ser implantados no Azure e o Azure Stack.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o desenvolvimento do Azure Stack, consulte os seguintes artigos:

* [Práticas recomendadas do Azure Resource Manager modelo](azure-stack-develop-templates.md)
* [Modelos de início rápido do Azure Stack no GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates)