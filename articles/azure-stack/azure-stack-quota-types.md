---
title: Tipos de cotas no Azure Stack | Microsoft Docs
description: Revise os tipos diferentes de cota disponíveis para serviços e recursos no Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/27/2018
ms.author: brenduns
ms.reviewer: xiaofmao
ms.openlocfilehash: 172c32c1f3796ec95336543d7d0ea149e63cfb22
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331127"
---
# <a name="quota-types-in-azure-stack"></a>Tipos de cotas no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

[Cotas](azure-stack-plan-offer-quota-overview.md#plans) definem os limites de recursos que uma assinatura de usuário pode provisionar ou consumir. Por exemplo, uma cota pode permitir que um usuário criar até cinco VMs. Cada recurso pode ter seus próprios tipos de cotas.

## <a name="compute-quota-types"></a>Tipos de cota de computação

| **Tipo** | **Valor padrão** | **Descrição** |
| --- | --- | --- |
| Número máximo de máquinas virtuais | 20 | O número máximo de máquinas virtuais que pode criar uma assinatura neste local. |
| Número máximo de núcleos de máquina virtual | 50 | O número máximo de núcleos que pode criar uma assinatura neste local (por exemplo, uma VM A3 tem quatro núcleos). |
| Número máximo de conjuntos de disponibilidade | 10 | O número máximo de conjuntos de disponibilidade que podem ser criados nesse local. |
| Define o número máximo de dimensionamento de máquinas virtuais | 20 | O número máximo de conjuntos de dimensionamento de máquinas virtuais que podem ser criados nesse local. |

## <a name="storage-quota-types"></a>Tipos de cota de armazenamento

| **Item** | **Valor padrão** | **Descrição** |
| --- | --- | --- |
| Capacidade máxima (GB) |500 |Capacidade total de armazenamento que pode ser consumida por uma assinatura neste local. |
| Número total de contas de armazenamento |20 |O número máximo de contas de armazenamento que pode criar uma assinatura neste local. |

> [!NOTE]  
> Pode levar até duas horas antes de uma cota de armazenamento é imposta.

## <a name="network-quota-types"></a>Tipos de cota de rede

| **Item** | **Valor padrão** | **Descrição** |
| --- | --- | --- |
| Máximo IPs públicos |50 |O número máximo de IPs públicos que pode criar uma assinatura neste local. |
| Redes virtuais do máximo |50 |O número máximo de redes virtuais que pode criar uma assinatura neste local. |
| Gateways de rede virtual do máximo |1 |O número máximo de gateways de rede virtual (Gateways de VPN) pode criar uma assinatura neste local. |
| Máximo de conexões de rede |2 |O número máximo de conexões de rede (ponto a ponto ou site a site) que uma assinatura pode criar em todos os gateways de rede virtual neste local. |
| Balanceadores de carga máxima |50 |O número máximo de balanceadores de carga que pode criar uma assinatura neste local. |
| Máximo de NICs |100 |O número máximo de adaptadores de rede que pode criar uma assinatura neste local. |
| Grupos de segurança de rede máxima |50 |O número máximo de grupos de segurança de rede que pode criar uma assinatura neste local. |

## <a name="view-an-existing-quota"></a>Exibir uma cota existente

1. No painel do portal do administrador padrão, localize o **provedores de recursos** lado a lado.
2. Selecione o serviço com a cota que você deseja exibir, como **Compute** ou **armazenamento**.
3. Selecione **cotas**e, em seguida, selecione a cota que você deseja exibir.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre planos, ofertas e cotas.](azure-stack-plan-offer-quota-overview.md)
- [Criação de cotas ao criar um plano.](azure-stack-create-plan.md)
