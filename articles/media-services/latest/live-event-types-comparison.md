---
title: Tipos de LiveEvent do Azure Media Services | Microsoft Docs
description: Este artigo mostra uma tabela detalhada que compara os tipos do LiveEvent.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/27/2018
ms.author: juliako
ms.openlocfilehash: 3ed38ce75e5ee7c9f05533d64b28171482bfec51
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725925"
---
# <a name="liveevent-types-comparison"></a>Comparação dos tipos de LiveEvent

Nos Serviços de Mídia do Azure, um [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) pode ser um dos dois tipos: codificação e passagem ao vivo. 

## <a name="types-comparison"></a>Comparação de tipos 

A tabela a seguir compara os recursos dos dois tipos de LiveEvent.

| Recurso | LiveEvent de passagem | LiveEvent padrão |
| --- | --- | --- |
| A entrada de taxa de bits única é codificada em várias taxas de bits na nuvem |Não  |SIM |
| Resolução máxima de vídeo para feed de contribuição |4K (4096 x 2160 a 60 quadros / seg) |1080p de (1920 x 1088 em 30 quadros por segundo)|
| Camadas máximas recomendadas no feed de contribuição|Até 12|Um áudio|
| Camadas máximo na saída| Igual à entrada|Até 7|
| Largura de banda agregada máxima de feed de contribuição|60 Mbps|N/D|
| Taxa de bits máxima para uma única camada de contribuição |20 Mbps|20 Mbps|
| Suporte para várias faixas de áudio de idioma|SIM|Não |
| Codecs de vídeo de entrada com suporte |H.264/AVC e H.265/HEVC|H.264/AVC|
| Codecs de vídeo de saída com suporte|Igual à entrada|H.264/AVC|
| Suporte para a profundidade de bits de vídeo, entrada e saída|Até 10 bits incluindo HDR 10/HLG|8 bits|
| Codecs de áudio de entrada com suporte|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Codecs de áudio de saída com suporte|Igual à entrada|AAC-LC|
| Resolução máxima de vídeo do vídeo de saída|Igual à entrada|720p (em 30 quadros/segundo)|
| Protocolos de entrada|RTMP, MP4 fragmentado (Smooth Streaming)|RTMP, MP4 fragmentado (Smooth Streaming)|
| Preço|Confira a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique na guia “Vídeo ao vivo”|Confira a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique na guia “Vídeo ao vivo”|
| Tempo de execução máximo|24 x 365 live linear|24x7|
| Capacidade de passar por meio do embedded CEA 608/708 legendas de dados|SIM|SIM|
| Suporte para inserção de imagens fixas|Não |Não |
| Suporte para sinalização de anúncios via API| Não |Não |
| Suporte para o ad sinalização por meio de mensagens na faixa de SCTE-35|SIM|SIM|
| Capacidade de recuperação de interrupções breves no feed de contribuição|SIM|Não (O LiveEvent começará a exibir imagens fixas após um período superior a 6 segundos sem dados de entrada)|
| Suporte para GOPs de entrada não uniforme|SIM|Não – entrada deve ter GOP duração fixa|
| Suporte para entrada de taxa de quadros variável|SIM|Não – a entrada deve ser uma taxa de quadros fixa. Pequenas variações são toleradas, por exemplo, durante cenas ricas em movimento. Mas o feed de contribuição não é possível descartar a taxa de quadros (por exemplo, para 15 quadros por segundo).|
| Desligamento automático de LiveEvents quando há perda do feed de entrada|Não |Após 12 horas, se não houver nenhum LiveOutput em execução|

## <a name="next-steps"></a>Próximas etapas

[Visão geral da transmissão ao vivo](live-streaming-overview.md)
