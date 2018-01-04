---
title: "Disponibilidade de região e de recursos das Instâncias de Contêiner do Azure"
description: "Descubra quais regiões do Azure dão suporte à implantação de instâncias de contêiner, bem como os limites de CPU e memória para essas instâncias."
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 12/15/2017
ms.author: marsma
ms.openlocfilehash: ec7f469c47924f4ae22d6509996ca9cf498fc9ad
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2017
---
# <a name="region-availability-for-azure-container-instances"></a>Disponibilidade da região para Instâncias de Contêiner do Azure

Durante a versão prévia, as Instâncias de Contêiner do Azure estão disponíveis nas seguintes regiões com os limites de memória e CPU especificados.

| Local padrão | SO | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Europa Ocidental, Oeste dos EUA, Leste dos EUA | Linux | 4 | 14 |
| Europa Ocidental, Oeste dos EUA, Leste dos EUA | Windows | 4 | 14 |

## <a name="resource-availability"></a>Disponibilidade de recursos

As instâncias de contêiner criadas dentro desses limites de recursos estão sujeitas à disponibilidade dentro da região de implantação. Quando uma região está sob carga pesada, você pode enfrentar uma falha durante a implantação de instâncias.

Para atenuar essa falha de implantação, tente implantar instâncias com configurações de memória e CPU inferiores ou tente implantar em um momento posterior.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como solucionar problemas de implantação de instâncias de contêiner, consulte [Solucionar problemas de implantação com Instâncias de Contêiner do Azure](container-instances-troubleshooting.md).