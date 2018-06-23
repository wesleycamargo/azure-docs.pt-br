---
title: Personalizar as configurações de avaliação de Migrações para Azure | Microsoft Docs
description: Descreve como configurar e executar uma avaliação de migração de VMs VMware para o Azure usando o Planejador de Migrações para Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/31/2018
ms.author: raynew
ms.openlocfilehash: 73dab9c7eca53ecce44d43a9607fcc7426f9de8d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715499"
---
# <a name="customize-an-assessment"></a>Personalizar uma avaliação

As [Migrações para Azure](migrate-overview.md) criam avaliações com configurações padrão. Depois de criar uma avaliação, você pode modificar essas configurações padrão usando as instruções neste artigo.


## <a name="edit-assessment-properties"></a>Editar propriedades de avaliação

1. Na página **Avaliações** do projeto de migração, selecione a avaliação e clique em **Editar propriedades**.
2. Modifique as propriedades de acordo com a tabela a seguir:

    **Configuração** | **Detalhes** | **Padrão**
    --- | --- | ---
    **Local de destino** | O local do Azure para o qual você deseja migrar.<br/><br/> Atualmente, as Migrações para Azure dão suporte a 30 regiões, incluindo Leste da Austrália, Sudeste da Austrália, Sul do Brasil, Canadá Central, Leste do Canadá, Índia Central, EUA Central, Leste da China, Norte da China, Ásia Oriental, Leste dos EUA, Alemanha Central, Nordeste da Alemanha, Leste dos EUA 2, Leste do Japão, Oeste do Japão, Coreia Central, Sul da Coreia, Centro-Norte dos EUA, Europa Setentrional, Centro-Sul dos EUA, Sudeste Asiático, Sul da Índia, Sul do Reino Unido, Oeste do Reino Unido, US Gov – Arizona, US Gov – Texas, US Gov – Virgínia, Centro-Oeste dos EUA, Europa Ocidental, Índia Ocidental, Oeste dos EUA e Oeste dos EUA 2. |  Oeste dos EUA 2 é o local padrão.
    **Tipo de armazenamento** | Você pode especificar o tipo dos discos que deseja alocar no Azure. Essa propriedade será aplicável quando o critério de dimensionamento for dimensionamento local. Especifique o tipo de disco de destino como discos gerenciados Premium ou Standard. Para o dimensionamento com base em desempenho, a recomendação de disco é feita automaticamente com base nos dados de desempenho das VMs. Observe que as Migrações para Azure são compatíveis apenas com discos gerenciados para avaliação de migração. | O valor padrão é discos gerenciados Premium (com o critério de dimensionamento *como dimensionamento local*).
    **Critério de dimensionamento** | O critério a ser usado pelas Migrações para Azure para redimensionar VMs para o Azure. Você pode fazer dimensionamento *com base no desempenho* ou dimensionar as VMs *como locais*, sem considerar o histórico de desempenho. | O dimensionamento com base no desempenho é a opção padrão.
    **Histórico de desempenho** | A duração a considerar para avaliar o desempenho das VMs. Essa propriedade só é aplicável quando o critério de dimensionamento é *dimensionamento com base no desempenho*. | O padrão é um dia.
    **Utilização de percentual** | O valor percentual da amostra de desempenho definido para ser considerado para o redimensionamento. Essa propriedade só é aplicável quando o critério de dimensionamento é *dimensionamento com base no desempenho*.  | O padrão é 95 por cento.
    **Série de VM** | Você pode especificar a série de VM que deseja considerar para o dimensionamento correto. Por exemplo, se você tiver um ambiente de produção que não pretende migrar para VMs da série A no Azure, poderá excluir a série A da lista ou da série e o dimensionamento correto será feito apenas na série selecionada. | Por padrão, todos as séries VM são selecionadas.
    **Tipo de preços** | É possível especificar o [tipo de preço (Básico/Standard)](../virtual-machines/windows/sizes-general.md) das VMs de destino do Azure. Por exemplo, se você estiver planejando migrar um ambiente de produção, deve considerar a camada Standard, que fornece VMs com baixa latência, mas pode custar mais. Por outro lado, caso tenha um ambiente de desenvolvimento de teste, talvez você deva considerar a camada Básica, que tem VMs com latência maior e custos mais baixos. | Por padrão, o tipo [Standard](../virtual-machines/windows/sizes-general.md) é usado.
    **Fator de conforto** | As Migrações para Azure consideram um buffer (fator de conforto) durante a avaliação. Esse buffer é aplicado sobre os dados de utilização da máquina para VMs (CPU, memória, disco e rede). O fator de conforto considera problemas como uso sazonal, histórico curto de desempenho e aumento provável do uso futuro.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização normalmente resulta em uma VM de dois núcleos. No entanto, com um fator de conforto de 2.0x, o resultado é uma VM de quatro núcleos. | A configuração padrão é 1.3 x.
    **Oferta** | A [Oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) em que você está inscrito. | [Pré-pago](https://azure.microsoft.com/offers/ms-azr-0003p/) é o padrão.
    **Moeda** | Moeda de cobrança. | O padrão é dólares americanos.
    **Desconto (%)** | Qualquer desconto específico da assinatura  recebido por você sobre a oferta do Azure. | A configuração padrão é 0%.
    **Benefício Híbrido do Azure** | Especifique se você tem a garantia do software e é qualificado para o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se definido como Sim, os preços do Windows Azure são considerados para VMs do Windows. | O padrão é Sim.

3. Clique em **Salvar** para atualizar a avaliação.


## <a name="next-steps"></a>Próximas etapas

[Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
