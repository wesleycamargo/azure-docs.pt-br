---
title: Tipos de cotas no Azure Stack | Microsoft Docs
description: Exibir e editar os tipos de cota diferentes disponíveis para serviços e recursos no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: xiaofmao
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: 3d9376ba5945c97d18f6cf68c242d5217beee679
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58349698"
---
# <a name="quota-types-in-azure-stack"></a>Tipos de cotas no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

[Cotas](azure-stack-plan-offer-quota-overview.md#plans) definem os limites de recursos que uma assinatura de usuário pode provisionar ou consumir. Por exemplo, uma cota pode permitir que um usuário criar até cinco VMs. Cada recurso pode ter seus próprios tipos de cotas.

## <a name="compute-quota-types"></a>Tipos de cota de computação

| **Tipo** | **Valor padrão** | **Descrição** |
| --- | --- | --- |
| Número máximo de máquinas virtuais | 50 | O número máximo de máquinas virtuais que pode criar uma assinatura neste local. |
| Número máximo de núcleos de máquina virtual | 100 | O número máximo de núcleos que pode criar uma assinatura neste local (por exemplo, uma VM A3 tem quatro núcleos). |
| Número máximo de conjuntos de disponibilidade | 10 | O número máximo de conjuntos de disponibilidade que podem ser criados nesse local. |
| Define o número máximo de dimensionamento de máquinas virtuais | 100 | O número máximo de conjuntos de dimensionamento de máquinas virtuais que podem ser criados nesse local. |
| Capacidade máxima (em GB) de disco gerenciado standard | 2.048 | A capacidade máxima de discos gerenciados standard que podem ser criados nesse local. |
| Capacidade máxima (em GB) de disco gerenciado premium | 2.048 | A capacidade máxima de premium gerenciados de discos que podem ser criados nesse local. |

> [!NOTE]  
> Capacidade máxima de disco não gerenciado (blobs de páginas) é separada da cota de disco gerenciado, ele deve ser definido na cota de armazenamento.

## <a name="storage-quota-types"></a>Tipos de cota de armazenamento 

| **Item** | **Valor padrão** | **Descrição** |
| --- | --- | --- |
| Capacidade máxima (GB) |2.048 |Capacidade total de armazenamento (incluindo os blobs e todos os instantâneos associados, tabelas, filas) que pode ser consumida por uma assinatura neste local. |
| Número total de contas de armazenamento |20 |O número máximo de contas de armazenamento que pode criar uma assinatura neste local. |

> [!NOTE]  
> Pode levar até duas horas antes de uma cota de armazenamento é imposta. Capacidade máxima de disco gerenciado é separada da cota de armazenamento total, ele deve ser definido na cota de computação.

## <a name="network-quota-types"></a>Tipos de cota de rede

| **Item** | **Valor padrão** | **Descrição** |
| --- | --- | --- |
| IPs públicos máximo |50 |O número máximo de IPs públicos que pode criar uma assinatura neste local. |
| Redes virtuais máximo |50 |O número máximo de redes virtuais que pode criar uma assinatura neste local. |
| Gateways de rede virtual máximo |1 |O número máximo de gateways de rede virtual (Gateways de VPN) pode criar uma assinatura neste local. |
| Conexões de rede máxima |2 |O número máximo de conexões de rede (ponto a ponto ou site a site) que uma assinatura pode criar em todos os gateways de rede virtual neste local. |
| Balanceadores de carga máxima |50 |O número máximo de balanceadores de carga que pode criar uma assinatura neste local. |
| NICs máximo |100 |O número máximo de adaptadores de rede que pode criar uma assinatura neste local. |
| Grupos de segurança de rede máxima |50 |O número máximo de grupos de segurança de rede que pode criar uma assinatura neste local. |

## <a name="view-an-existing-quota"></a>Exibir uma cota existente

Há duas maneiras diferentes para exibir uma cota existente:

### <a name="plans"></a>Planos

1. No painel de navegação à esquerda do portal do administrador, selecione **planos**.
2. Selecione o plano que você gostaria de exibir os detalhes para o, clicando em seu nome.
3. Na folha que é aberta, selecione **serviços e cotas**.
4. Selecione a cota que você deseja ver clicando na **nome** coluna.

    [![Cotas](media/azure-stack-quota-types/quotas1sm.png "exibir cotas")](media/azure-stack-quota-types/quotas1.png#lightbox)

### <a name="resource-providers"></a>Provedores de recursos

1. No painel do portal do administrador padrão, localize o **provedores de recursos** lado a lado.
2. Selecione o serviço com a cota que você deseja exibir, como **Compute**, **rede**, ou **armazenamento**.
3. Selecione **cotas**e, em seguida, selecione a cota que você deseja exibir.

## <a name="edit-a-quota"></a>Editar uma cota

Há duas maneiras diferentes para editar uma cota:

### <a name="edit-a-plan"></a>Editar um plano

1. No painel de navegação à esquerda do portal do administrador, selecione **planos**.
2. Selecione o plano para o qual você deseja editar uma cota, clicando em seu nome.
3. Na folha que é aberta, selecione **serviços e cotas**.
4. Selecione a cota que você deseja editar clicando na **nome** coluna.
    [![Cotas](media/azure-stack-quota-types/quotas1sm.png "exibir cotas")](media/azure-stack-quota-types/quotas1.png#lightbox)

5. Na folha que é aberta, selecione **Editar na computação**, **Editar na rede**, ou **Editar no armazenamento**.
    ![Cotas](media/azure-stack-quota-types/quotas3.png "exibir cotas")

Como alternativa, você pode seguir este procedimento para editar uma cota:

1. No painel do portal do administrador padrão, localize o **provedores de recursos** lado a lado.
2. Selecione o serviço com a cota que você deseja modificar, como **Compute**, **rede**, ou **armazenamento**.
3. Em seguida, selecione **cotas**e, em seguida, selecione a cota que você deseja alterar.
4. Sobre o **as cotas de armazenamento definida**, **computação definir cotas**, ou **cotas de rede definida** painel (dependendo do tipo de cota que você escolheu para editar), edite os valores e, em seguida, selecione **Salvar**.

### <a name="edit-original-configuration"></a>Editar a configuração original
  
Você pode optar por editar a configuração original de uma cota em vez de [usando um plano de complemento](create-add-on-plan.md). Quando você edita uma cota, a nova configuração automaticamente se aplica globalmente para todos os planos que usam essa cota e todas as assinaturas existentes que usam esses planos. A edição de uma cota é diferente de quando você usa um plano de complemento para fornecer uma cota modificada, o que um usuário opta por se inscrever.

Os novos valores para a cota se aplicam globalmente a todos os planos que usam a cota de modificação e a todas as assinaturas existentes que usam esses planos.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre planos, ofertas e cotas.](azure-stack-plan-offer-quota-overview.md)
- [Criação de cotas ao criar um plano.](azure-stack-create-plan.md)
