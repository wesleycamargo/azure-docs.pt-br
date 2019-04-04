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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 041e76ccecb4dd0fe9c060681609dfb92c03ec5a
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893139"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Comparar as predefinições do Azure Media Services v3 e o Video Indexer 

Este artigo compara os recursos das **APIs do Video Indexer** e **APIs do Media Services v3**. 

Atualmente, há uma sobreposição entre os recursos oferecidos pelo [APIs do indexador de vídeo](https://api-portal.videoindexer.ai/) e o [APIs dos serviços de mídia v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). A tabela a seguir oferece a diretriz atual para entender as diferenças e semelhanças. 

## <a name="compare"></a>Comparar

|Recurso|APIs do indexador de vídeo |Analisador de vídeo e áudio Analyzer predefinições<br/>nas APIs do Media Services v3|
|---|---|---|
|Insights de mídia|[Avançado](video-indexer-output-json-v2.md) |[Conceitos básicos](../latest/intelligence-concept.md)|
|Experiências|Consulte a lista completa de recursos com suporte: <br/> [Visão geral](video-indexer-overview.md)|Retorna somente os insights de vídeo|
|Cobrança|[Preços dos serviços de mídia](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Preços dos serviços de mídia](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Conformidade|[Conformidade do Azure](https://aka.ms/AzureCompliance)|Os serviços de mídia está em conformidade com várias certificações. Fazer check-out [Offerings.pdf de conformidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) e procure "Serviços de mídia" ver se ele está em conformidade com um certificado de interesse.|
|Avaliação gratuita|Leste dos EUA|Não disponível|
|Disponibilidade |Oeste dos EUA, leste da Ásia, norte da Europa|Ver [status do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Próximas etapas

[Visão geral do indexador vídeo](video-indexer-overview.md)

[Visão geral do Media Services v3](../latest/media-services-overview.md)
