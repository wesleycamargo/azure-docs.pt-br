---
title: Nuvens e regiões em que os Serviços de Mídia do Azure v3 estão disponíveis | Microsoft Docs
description: Este artigo discute as nuvens e regiões do Azure em que os Serviços de Mídia do Azure v3 estão disponíveis.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/11/2019
ms.author: juliako
ms.openlocfilehash: d176152429ecac1ed4e570533f1bc0426cc7655f
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767445"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Nuvens e regiões em que os Serviços de Mídia do Azure v3 existem

Os Serviços de Mídia do Azure v3 estão disponíveis por meio do manifesto do Azure Resource Manager no Azure global, no Azure Governamental, no Azure na Alemanha e no Azure China 21Vianet. No entanto, nem todos os recursos dos Serviços de Mídia estão disponíveis em todas as nuvens do Azure. Este documento descreve as disponibilidades dos principais componentes do Serviços de Mídia v3.

## <a name="feature-availability-in-azure-clouds"></a>Disponibilidade de recursos em nuvens do Azure

| Recurso|Regiões Globais do Azure | Azure Government|Azure Alemanha|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Disponível | Não disponível | Não disponível | Não disponível |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Disponível | Não disponível | Não disponível | Não disponível |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Disponível | Não disponível | Não disponível | Não disponível |
| [StandardEncoderPreset](encoding-concept.md) | Disponível | Disponível | Disponível | Disponível |
| [LiveEvents](live-streaming-overview.md) | Disponível | Disponível | Disponível | Disponível |
| [StreamingEndpoints](streaming-endpoint-concept.md) | Disponível | Disponível | Disponível | Disponível |

## <a name="regionsgeographieslocations"></a>Regiões/geografias/locais

* [Regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Produtos por região](https://azure.microsoft.com/global-infrastructure/services/)
* [Geografias do Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="region-code-name"></a>Nome do código da região 

Quando precisa fornecer o parâmetro de **localização**, você precisa fornecer o nome do código de região como o valor da **localização**. Para obter o nome do código da região na qual a conta está localizada e para a qual a chamada deverá ser encaminhada, execute a seguinte linha na [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```bash
az account list-locations
```

Depois de executar a linha mostrada acima, você obterá uma lista de todas as regiões do Azure. Navegue para a região do Azure que tenha o *displayName* que você está procurando e use seu valor *name* para o parâmetro **location**.

Por exemplo, para a região Oeste dos EUA 2 (exibida abaixo) do Azure, você usará "westus2" para o parâmetro **location**.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Próximas etapas

[Visão geral dos Serviços de Mídia v3](media-services-overview.md)
