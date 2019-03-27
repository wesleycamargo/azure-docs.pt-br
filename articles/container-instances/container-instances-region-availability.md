---
title: Disponibilidade de recursos das Instâncias de Contêiner do Azure
description: Disponibilidade de recursos de computação e de memória para o serviço de Instâncias de Contêiner do Azure em diferentes regiões do Azure.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 03/01/2019
ms.author: danlep
ms.openlocfilehash: 1ca23a95c746139963aa70ed20bb888152fd5cd8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554829"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilidade de recursos para Instâncias de Contêiner do Azure nas regiões do Azure

Este artigo fornece detalhes sobre a disponibilidade de recursos de computação e de memória de Instâncias de Contêiner do Azure em regiões do Azure. 

Os valores apresentados são o máximo de recursos disponíveis para a implantação de um [grupo de contêineres](container-instances-container-groups.md). Os valores estavam atualizados no momento da publicação. Para obter informações atualizadas, use a API de [Funcionalidades de Lista](/rest/api/container-instances/listcapabilities/listcapabilities). 

> [!NOTE]
> Os grupos de contêiner criados dentro desses limites de recursos estão sujeitas à disponibilidade dentro da região de implantação. Quando uma região está sob carga pesada, você pode enfrentar uma falha durante a implantação de instâncias. Para atenuar essa falha de implantação, tente implantar instâncias com configurações de recursos inferiores ou tente implantar em um momento posterior.

Para obter informações sobre cotas e outros limites em suas implantações, confira [cotas e limites para as Instâncias de Contêiner do Azure](container-instances-quotas.md).

## <a name="availability---general"></a>Disponibilidade – geral

| Local padrão | SO | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Canadá Central, EUA Central, Leste dos EUA 2 e Centro-Sul dos EUA | Linux | 4 | 16 |
| Leste dos EUA, Europa Setentrional, Europa Ocidental, Oeste dos EUA, Oeste dos EUA 2 | Linux | 4 | 14 |
| Leste do Japão | Linux | 2 | 8 |
| Leste da Austrália, Sudeste Asiático | Linux | 2 | 7 |
| Índia Central, Ásia Oriental, Centro-Norte dos EUA e Sul da Índia | Linux | 2 | 3,5 |
| Leste dos EUA, Europa Ocidental, Oeste dos EUA |  Windows | 4 | 14 |
| Leste da Austrália, Canadá Central, Índia Central, EUA Central, Ásia Oriental, Leste dos EUA 2, Leste do Japão, Centro-Norte dos EUA, Europa Setentrional, Centro-Sul dos EUA, Sul da Índia, Sudeste Asiático, Oeste dos EUA 2 |  Windows | 2 | 3,5 |

## <a name="availability---virtual-network-deployment-preview"></a>Disponibilidade – implantação da rede virtual (versão prévia)

As seguintes regiões e recursos estão disponíveis para um grupo de contêineres implantado em uma [rede virtual do Azure](container-instances-vnet.md) (versão prévia):

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Disponibilidade – recursos de GPU (versão prévia)

As seguintes regiões e recursos estão disponíveis para um grupo de contêineres implantados com [recursos de GPU](container-instances-gpu.md) (versão prévia):

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Próximas etapas

Informe à equipe se você gostaria de ver outras regiões ou uma maior disponibilidade de recursos em [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Para obter informações sobre como solucionar problemas de implantação de instâncias de contêiner, confira [Solucionar problemas de implantação com Instâncias de Contêiner do Azure](container-instances-troubleshooting.md).
