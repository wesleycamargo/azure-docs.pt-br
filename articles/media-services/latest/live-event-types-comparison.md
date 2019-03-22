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
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: 9952a7bbac1eb79de0d3425f839e3bd30196844e
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243883"
---
# <a name="live-event-types-comparison"></a>Comparação de tipos de Eventos ao Vivo

Nos Serviços de Mídia do Azure, um [Evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents) pode ser um dos dois tipos: codificação ativa e passagem. 

## <a name="types-comparison"></a>Comparação de tipos 

A tabela a seguir compara os recursos dos dois tipos de Evento ao Vivo.

| Recurso | Evento ao vivo de passagem | Evento ao vivo padrão |
| --- | --- | --- |
| A entrada de taxa de bits única é codificada em várias taxas de bits na nuvem |Não  |Sim |
| Resolução máxima de vídeo para feed de contribuição |4K (4096 x 2160 a 60 quadros / seg) |1080p de (1920 x 1088 em 30 quadros por segundo)|
| Camadas máximas recomendadas no feed de contribuição|Até 12|Um áudio|
| Camadas máximo na saída| Igual à entrada|Até 6 (veja abaixo as predefinições de sistema)|
| Largura de banda agregada máxima de feed de contribuição|60 Mbps|N/D|
| Taxa de bits máxima para uma única camada de contribuição |20 Mbps|20 Mbps|
| Suporte para várias faixas de áudio de idioma|Sim|Não |
| Codecs de vídeo de entrada com suporte |H.264/AVC e H.265/HEVC|H.264/AVC|
| Codecs de vídeo de saída com suporte|Igual à entrada|H.264/AVC|
| Suporte para a profundidade de bits de vídeo, entrada e saída|Até 10 bits incluindo HDR 10/HLG|8 bits|
| Codecs de áudio de entrada com suporte|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Codecs de áudio de saída com suporte|Igual à entrada|AAC-LC|
| Resolução máxima de vídeo do vídeo de saída|Igual à entrada|720p (em 30 quadros/segundo)|
| Protocolos de entrada|RTMP, MP4 fragmentado (Smooth Streaming)|RTMP, MP4 fragmentado (Smooth Streaming)|
| Preço|Confira a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique na guia “Vídeo ao vivo”|Confira a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique na guia “Vídeo ao vivo”|
| Tempo de execução máximo| 24 horas x 365 dias, linha ao vivo | Até 24 horas|
| Capacidade de passar por meio do embedded CEA 608/708 legendas de dados|Sim|Sim|
| Suporte para inserção de imagens fixas|Não |Não |
| Suporte para sinalização de anúncios via API| Não |Não |
| Suporte para o ad sinalização por meio de mensagens na faixa de SCTE-35|Sim|Sim|
| Capacidade de recuperação de interrupções breves no feed de contribuição|Sim|Não (O Evento ao Vivo começará a ficar com imagem fixa após + de 6 segundos sem dados de entrada)|
| Suporte para GOPs de entrada não uniforme|Sim|Não – entrada deve ter GOP duração fixa|
| Suporte para entrada de taxa de quadros variável|Sim|Não – a entrada deve ser uma taxa de quadros fixa. Pequenas variações são toleradas, por exemplo, durante cenas ricas em movimento. Mas o feed de contribuição não é possível descartar a taxa de quadros (por exemplo, para 15 quadros por segundo).|
| Desligamento automático do Evento ao Vivo quando há perda do feed de entrada|Não |Após 12 horas, se não houver nenhum LiveOutput em execução|

## <a name="system-presets"></a>Predefinições do sistema

Ao usar a codificação ao vivo (conjunto de eventos ao vivo **Standard**), a predefinição de codificação define como o fluxo de entrada é codificado em várias taxas de bits ou camadas. Atualmente, o único valor permitido para é a predefinição *Default720p* (padrão).

**Default720p** codificará o vídeo nas seis camadas a seguir.

### <a name="output-video-stream"></a>Fluxo de vídeo de saída

| Taxa de bits | Largura | Altura | MáxFPS | Perfil | Nome do fluxo de saída |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Alto |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Alto |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Alto |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Alto |Video_512x288_850kbps |
| 550 |384 |216 |30 |Alto |Video_384x216_550kbps |
| 200 |340 |192 |30 |Alto |Video_340x192_200kbps |

> [!NOTE]
> Se você precisar usar uma predefinição de codifica ao vivo personalizada, entre em contato com amshelp@microsoft.com. Você deve especificar a tabela desejada da resolução e taxas de bits. Verifique se há apenas uma camada em 720p e no máximo de 6 camadas.

### <a name="output-audio-stream"></a>Fluxo de áudio de saída

O áudio é codificado para AAC-LC estéreo a 128 kbps, taxa de amostragem de 48 kHz.

## <a name="next-steps"></a>Próximas etapas

[Visão geral da transmissão ao vivo](live-streaming-overview.md)
