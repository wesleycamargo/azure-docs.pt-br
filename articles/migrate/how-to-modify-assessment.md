---
title: "Personalizar as configurações de avaliação de Migrações para Azure | Microsoft Docs"
description: "Descreve como configurar e executar uma avaliação de migração de VMs VMware para o Azure usando o Planejador de Migrações para Azure"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: ce47790f6214864afdba33eb5cbe3a9e49b81cd5
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2017
---
# <a name="customize-an-assessment"></a>Personalizar uma avaliação

As [Migrações para Azure](migrate-overview.md) criam avaliações com configurações padrão. Depois de criar uma avaliação, você pode modificar essas configurações padrão usando as instruções neste artigo.


## <a name="edit-assessment-values"></a>Editar valores de avaliação

1. Na página **Avaliações** do projeto de Migrações para Azure, selecione a avaliação e clique em **Editar propriedades**.
2. Modifique as configurações de acordo com a tabela a seguir.

    **Configuração** | **Detalhes** | **Padrão**
    --- | --- | ---
    **Local de destino** | O local do Azure para o qual você deseja migrar. |  Oeste dos EUA 2 é o local padrão.
    **Redundância de armazenamento** | O tipo de armazenamento que as VMs do Azure usarão após a migração. | Somente a replicação [Armazenamento localmente redundante (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) tem suporte no momento.
    **Fator de conforto** | O fator de conforto é um buffer que é usado durante a avaliação. Use-o para explicar coisas como uso sazonal, histórico de desempenho curto, aumento provável no uso futuro. | A configuração padrão é 1.3 x.
    **Histórico de desempenho** | Tempo usado na avaliação do histórico de desempenho. | O padrão é um mês.
    **Utilização de percentual** | Valor percentual a ser considerado para o histórico de desempenho. | O padrão é 95%.
    **Tipo de preços** | Você pode especificar o [tipo de preço](https://azure.microsoft.com/blog/basic-tier-virtual-machines-2/) para uma máquina virtual.  | Por padrão, o tipo [Standard](../virtual-machines/windows/sizes-general.md) é usado.
    **Oferta** | [Ofertas do Azure](https://azure.microsoft.com/support/legal/offer-details/) que se aplicam. | [Pré-pago](https://azure.microsoft.com/offers/ms-azr-0003p/) é o padrão.
    **Moeda** | Moeda de cobrança. | O padrão é dólares americanos.
    **Desconto (%)** | Qualquer desconto específico da assinatura recebido em qualquer oferta. | A configuração padrão é 0%.
    **Benefício de Uso do Azure Híbrido** | Indica se você está inscrito no [Benefício de Uso Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se definido como Sim, os preços do Windows Azure são considerados para máquinas virtuais do Windows. | O padrão é Sim.

3. Clique em **Salvar** para atualizar a avaliação.


## <a name="next-steps"></a>Próximas etapas

[Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
