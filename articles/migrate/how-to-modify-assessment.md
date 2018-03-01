---
title: "Personalizar as configurações de avaliação de Migrações para Azure | Microsoft Docs"
description: "Descreve como configurar e executar uma avaliação de migração de VMs VMware para o Azure usando o Planejador de Migrações para Azure"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/02/2017
ms.author: raynew
ms.openlocfilehash: 8babdbc30e062c7b289e90a674cec3222943e48d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="customize-an-assessment"></a>Personalizar uma avaliação

As [Migrações para Azure](migrate-overview.md) criam avaliações com configurações padrão. Depois de criar uma avaliação, você pode modificar essas configurações padrão usando as instruções neste artigo.


## <a name="edit-assessment-properties"></a>Editar propriedades de avaliação

1. Na página **Avaliações** do projeto de migração, selecione a avaliação e clique em **Editar propriedades**.
2. Modifique as propriedades de acordo com a tabela a seguir:

    **Configuração** | **Detalhes** | **Padrão**
    --- | --- | ---
    **Local de destino** | O local do Azure para o qual você deseja migrar. |  Oeste dos EUA 2 é o local padrão.
    **Redundância de armazenamento** | O tipo de redundância de armazenamento que as VMs do Azure usarão após a migração. | O [armazenamento com redundância local (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) é o valor padrão. As Migrações para Azure só oferecem suporte a avaliações com base em discos gerenciados, e discos gerenciados só oferecem suporte ao LRS, portanto a propriedade atualmente só tem a opção de LRS. 
    **Critério de dimensionamento** | O critério a ser usado pelas Migrações para Azure para redimensionar VMs para o Azure. Você pode fazer dimensionamento *com base no desempenho* ou dimensionar as VMs *como locais*, sem considerar o histórico de desempenho. | O dimensionamento com base no desempenho é a opção padrão.
    **Histórico de desempenho** | A duração a considerar para avaliar o desempenho das VMs. Essa propriedade só é aplicável quando o critério de dimensionamento é *dimensionamento com base no desempenho*. | O padrão é um dia.
    **Utilização de percentual** | O valor percentual da amostra de desempenho definido para ser considerado para o redimensionamento. Essa propriedade só é aplicável quando o critério de dimensionamento é *dimensionamento com base no desempenho*.  | O padrão é 95 por cento.
    **Tipo de preços** | É possível especificar o [tipo de preço (Básico/Standard)](../virtual-machines/windows/sizes-general.md) das VMs de destino do Azure. Por exemplo, se você estiver planejando migrar um ambiente de produção, deve considerar a camada Standard, que fornece VMs com baixa latência, mas pode custar mais. Por outro lado, caso tenha um ambiente de desenvolvimento de teste, talvez você deva considerar a camada Básica, que tem VMs com latência maior e custos mais baixos. | Por padrão, o tipo [Standard](../virtual-machines/windows/sizes-general.md) é usado.
    **Fator de conforto** | As Migrações para Azure consideram um buffer (fator de conforto) durante a avaliação. Esse buffer é aplicado sobre os dados de utilização da máquina para VMs (CPU, memória, disco e rede). O fator de conforto considera problemas como uso sazonal, histórico curto de desempenho e aumento provável do uso futuro.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização normalmente resulta em uma VM de dois núcleos. No entanto, com um fator de conforto de 2.0x, o resultado é uma VM de quatro núcleos. | A configuração padrão é 1.3 x.
    **Oferta** | A [Oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) em que você está inscrito. | [Pré-pago](https://azure.microsoft.com/offers/ms-azr-0003p/) é o padrão.
    **Moeda** | Moeda de cobrança. | O padrão é dólares americanos.
    **Desconto (%)** | Qualquer desconto específico da assinatura  recebido por você sobre a oferta do Azure. | A configuração padrão é 0%.
    **Benefício Híbrido do Azure** | Especifique se você tem a garantia do software e é qualificado para o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se definido como Sim, os preços do Windows Azure são considerados para VMs do Windows. | O padrão é Sim.

3. Clique em **Salvar** para atualizar a avaliação.


## <a name="next-steps"></a>Próximas etapas

[Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
