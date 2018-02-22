---
title: Tipos de cota na pilha do Azure | Microsoft Docs
description: "Examine os tipos diferentes de cota disponíveis para serviços e recursos na pilha do Azure."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 8/23/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: da4105fa88848b14e5d5d0289859cfd0f85c8fee
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="quota-types-in-azure-stack"></a>Tipos de cota na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

[Cotas](azure-stack-plan-offer-quota-overview.md#plans) definir os limites de recursos que uma assinatura de usuário pode provisionar ou consumir. Por exemplo, uma cota pode permitir que um usuário crie até cinco VMs. Cada recurso pode ter seus próprios tipos de cotas.

## <a name="compute-quota-types"></a>Tipos de cota de computação
| **Tipo** | **Valor padrão** | **Descrição** |
| --- | --- | --- |
| Número máximo de máquinas virtuais | 20 | O número máximo de máquinas virtuais que pode criar uma assinatura neste local. |
| Número máximo de núcleos de máquina virtual | 50 | O número máximo de núcleos que pode criar uma assinatura neste local (por exemplo, uma VM A3 tem quatro núcleos). |
| Define o número máximo de disponibilidade | 10 | O número máximo de conjuntos de disponibilidade que podem ser criados neste local. |
| Define o número máximo de escala de máquinas virtuais | 20 | O número máximo de conjuntos de escala de máquinas virtuais que podem ser criados neste local. |

> [!NOTE]
> Elas não são impostas nesta visualização técnica de computação.
> 
> 

## <a name="storage-quota-types"></a>Tipos de cota de armazenamento
| **Item** | **Valor padrão** | **Descrição** |
| --- | --- | --- |
| Capacidade máxima (GB) |500 |Capacidade total de armazenamento que pode ser consumida por uma assinatura neste local. |
| Número total de contas de armazenamento |20 |O número máximo de contas de armazenamento que pode criar uma assinatura neste local. |

## <a name="network-quota-types"></a>Tipos de cota de rede
| **Item** | **Valor padrão** | **Descrição** |
| --- | --- | --- |
| IPs públicos max |50 |O número máximo de IPs públicos que pode criar uma assinatura neste local. |
| Redes virtuais max |50 |O número máximo de redes virtuais que pode criar uma assinatura neste local. |
| Gateways de rede virtual max |1 |O número máximo de gateways de rede virtual (Gateways de VPN) que pode criar uma assinatura neste local. |
| Máximo de conexões de rede |2 |O número máximo de conexões de rede (ponto a ponto ou site a site) que pode criar uma assinatura em todos os gateways de rede virtual neste local. |
| Balanceadores de carga máxima |50 |O número máximo de balanceadores de carga que pode criar uma assinatura neste local. |
| Máximo de NICs |100 |O número máximo de interfaces de rede que pode criar uma assinatura neste local. |
| Grupos de segurança de rede máxima |50 |O número máximo de grupos de segurança de rede que pode criar uma assinatura neste local. |

## <a name="view-an-existing-quota"></a>Exibir uma cota existente
1. Clique em **mais serviços** > **provedores de recursos**.
2. Selecione o serviço com a cota que você deseja exibir.
3. Clique em **cotas**e selecione a cota que você deseja exibir.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre planos de ofertas e cotas.](azure-stack-plan-offer-quota-overview.md)

[Crie cotas ao criar um plano.](azure-stack-create-plan.md)
