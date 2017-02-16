---
title: "Comparação de codificadores de mídia sob demanda do Azure | Microsoft Docs"
description: "Este tópico compara as funcionalidades de codificação do **Media Encoder Standard** e do **Fluxo de Trabalho do Media Encoder Premium**."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: f6d6b7b1051a22bbc865b237905f8df84e832231
ms.openlocfilehash: dca8087230b261f14e876e49b9b0fb12579b96d0


---

# <a name="comparison-of-azure-on-demand-media-encoders"></a>Comparação de codificadores de mídia sob demanda do Azure

Este tópico compara as funcionalidades de codificação do **Media Encoder Standard** e do **Fluxo de Trabalho do Media Encoder Premium**.

### <a name="a-idbillingabilling-meter-used-by-each-encoder"></a><a id="billing"></a>Medidor de cobrança usado por cada codificador
| Nome do processador de mídia | Preços aplicáveis | Observações |
| --- | --- | --- |
| **Media Encoder Standard** |CODIFICADOR |As Tarefas de Codificação serão cobradas com base na duração total, em minutos, de todos os arquivos de mídia gerados como saída, na taxa especificada [aqui][1], na coluna CODIFICADOR. |
| **Fluxo de trabalho do Media Encoder Premium** |CODIFICADOR PREMIUM |As Tarefas de Codificação serão cobradas com base na duração total, em minutos, de todos os arquivos de mídia gerados como saída, na taxa especificada [aqui][1], na coluna CODIFICADOR PREMIUM. |

### <a name="input-containerfile-formats"></a>Formatos de arquivo/contêiner de entrada
| Formatos de arquivo/contêiner de entrada | Media Encoder Standard | Fluxo de trabalho do Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Sim |Sim |
| MXF/SMPTE 377M |Sim |Sim |
| GXF |Sim |Sim |
| Fluxos de transporte de MPEG-2 |Sim |Sim |
| Fluxos de programa MPEG-2 |Sim |Sim |
| MPEG-4/MP4 |Sim |Sim |
| Windows Media/ASF |Sim |Sim |
| AVI (8 bits/10 bits descompactado) |Sim |Sim |
| 3GPP/3GPP2 |Sim |Não |
| Formato de arquivo do Smooth Streaming (PIFF 1.3) |Sim |Não |
| [Gravação (DVR-MS) de vídeo Digital da Microsoft](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Sim |Não |
| Matroska/WebM |Sim |Não |
| QuickTime (.mov) |Sim |Não |

### <a name="input-video-codecs"></a>Codecs de vídeo de entrada
| Codecs de vídeo de entrada | Media Encoder Standard | Fluxo de trabalho do Media Encoder Premium |
| --- | --- | --- |
| AVC de 8 bits/10 bits até 4:2:2, incluindo AVCIntra |8 bits 4:2:0 e 4:2:2 |Sim |
| DNxHD ávido (em MXF) |Sim |Sim |
| DVCPro/DVCProHD (em MXF) |Sim |Sim |
| JPEG2000 |Sim |Sim |
| MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10) |Até perfil 422 |Sim |
| MPEG-1 |Sim |Sim |
| Windows Media Video/VC-1 |Sim |Sim |
| Canopus HQ/HQX |Não |Não |
| MPEG-4, parte 2 |Sim |Não |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Sim |Não |
| Apple ProRes 422 |Sim |Não |
| Apple ProRes 422 LT |Sim |Não |
| Apple ProRes 422 HQ |Sim |Não |
| Apple ProRes Proxy |Sim |Não |
| Apple ProRes 4444 |Sim |Não |
| Apple ProRes 4444 XQ |Sim |Não |

### <a name="input-audio-codecs"></a>Codecs de áudio de entrada
| Codecs de áudio de entrada | Media Encoder Standard | Fluxo de trabalho do Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331M e 302M, AES3-2003) |Não |Sim |
| Dolby® E |Não |Sim |
| Dolby® Digital (AC3) |Não |Sim |
| Dolby® Digital Plus (E-AC3) |Não |Sim |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1) |Sim |Sim |
| MPEG Layer 2 |Sim |Sim |
| MP3 (MPEG-1 Audio Layer 3) |Sim |Sim |
| Áudio do Windows Media |Sim |Sim |
| WAV/PCM |Sim |Sim |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Sim |Não |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Sim |Não |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Sim |Não |

### <a name="output-containerfile-formats"></a>Formatos de contêiner/arquivo de saída
| Formatos de contêiner/arquivo de saída | Media Encoder Standard | Fluxo de trabalho do Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Não |Sim |
| MXF (OP1a, XDCAM e AS02) |Não |Sim |
| DPP (incluindo AS11) |Não |Sim |
| GXF |Não |Sim |
| MPEG-4/MP4 |Sim |Sim |
| MPEG-TS |Sim |Sim |
| Windows Media/ASF |Não |Sim |
| AVI (8 bits/10 bits descompactado) |Não |Sim |
| Formato de arquivo do Smooth Streaming (PIFF 1.3) |Não |Sim |

### <a name="output-video-codecs"></a>Codecs de vídeo de saída
| Codecs de vídeo de saída | Media Encoder Standard | Fluxo de trabalho do Media Encoder Premium |
| --- | --- | --- |
| AVC (H. 264; 8 bits; até perfil, nível elevado 5.2; 4K Ultra HD; Intra AVC) |Somente 8 bits 4:2:0 |Sim |
| DNxHD ávido (em MXF) |Não |Sim |
| MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10) |Não |Sim |
| MPEG-1 |Não |Sim |
| Windows Media Video/VC-1 |Não |Sim |
| Criação de miniaturas JPEG |Não |Sim |

### <a name="output-audio-codecs"></a>Codecs de áudio de saída
| Codecs de áudio de saída | Media Encoder Standard | Fluxo de trabalho do Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331M e 302M, AES3-2003) |Não |Sim |
| Dolby® Digital (AC3) |Não |Sim |
| Dolby ® Digital Plus (E-AC3) até 7.1 |Não |Sim |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1) |Sim |Sim |
| MPEG Layer 2 |Não |Sim |
| MP3 (MPEG-1 Audio Layer 3) |Não |Sim |
| Áudio do Windows Media |Não |Sim |


## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Executar tarefas avançadas de codificação ao personalizar predefinições do Codificador de Mídia Padrão](media-services-custom-mes-presets-with-dotnet.md)
* [Cotas e limitações](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/



<!--HONumber=Jan17_HO2-->


