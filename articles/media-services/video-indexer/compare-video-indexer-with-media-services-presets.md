---
title: Comparação de predefinições Video Indexer e Azure Media Services v3 | Microsoft Docs
description: Este tópico compara as predefinições Video Indexer e Azure Media Services v3.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2019
ms.author: juliako
ms.openlocfilehash: 2c98f6d12f4868e5f90874fe3210fe5368f7ca2d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59270329"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Comparar as predefinições do Azure Media Services v3 e o Video Indexer 

Este artigo compara os recursos das **APIs do Video Indexer** e **APIs do Media Services v3**. 

Atualmente, há uma sobreposição entre os recursos oferecidos pelo [APIs do indexador de vídeo](https://api-portal.videoindexer.ai/) e o [APIs dos serviços de mídia v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). A tabela a seguir oferece a diretriz atual para entender as diferenças e semelhanças. 

## <a name="compare"></a>Comparar

|Recurso|APIs do indexador de vídeo |Analisador de vídeo e áudio Analyzer predefinições<br/>nas APIs do Media Services v3|
|---|---|---|
|Insights de mídia|[Aprimorado](video-indexer-output-json-v2.md) |[Conceitos básicos](../latest/intelligence-concept.md)|
|Experiências|Consulte a lista completa de recursos com suporte: <br/> [Visão geral](video-indexer-overview.md)|Retorna somente os insights de vídeo|
|Cobrança|[Preços dos serviços de mídia](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Preços dos serviços de mídia](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Conformidade|[ISO 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018), [SOC 1, 2,3](https://www.microsoft.com/TrustCenter/Compliance/SOC), [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa), [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp), [PCI](https://www.microsoft.com/trustcenter/compliance/pci)e [ HITRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust) certified. Para obter as atualizações mais recentes, visite [status atual de certificações do indexador de vídeo](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).|Os serviços de mídia está em conformidade com várias certificações. Fazer check-out [Offerings.pdf de conformidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) e procure "Serviços de mídia" ver se ele está em conformidade com um certificado de interesse.|
|Avaliação gratuita|Leste dos EUA|Não disponível|
|Disponibilidade de região|Leste dos EUA 2, Centro-Sul dos EUA, oeste dos EUA 2, Europa Setentrional, Europa Ocidental, Sudeste Asiático, Sudeste Asiático e Leste da Austrália.  Para obter as atualizações mais recentes, visite o [produtos por região](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) página.|Ver [status do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Próximas etapas

[Visão geral do indexador vídeo](video-indexer-overview.md)

[Visão geral dos Serviços de Mídia v3](../latest/media-services-overview.md)
