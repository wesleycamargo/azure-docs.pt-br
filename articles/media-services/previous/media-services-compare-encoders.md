---
title: Comparação de codificadores de mídia sob demanda do Azure | Microsoft Docs
description: Este tópico compara as funcionalidades de codificação do **Media Encoder Standard** e do **Fluxo de Trabalho do Media Encoder Premium**.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 13e8b3bcb7de4093116b3e2198b210950ac16d78
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Comparação de codificadores de mídia sob demanda do Azure

Este tópico compara as funcionalidades de codificação do **Media Encoder Standard** e do **Fluxo de Trabalho do Media Encoder Premium**.

## <a name="video-and-audio-processing-capabilities"></a>Funcionalidades de processamento de vídeo e áudio

A tabela a seguir compara as funcionalidades entre o MES (Media Encoder Standard) e o MEPW (Media Encoder Premium Workflow). 

|Recurso|Media Encoder Standard|Fluxo de trabalho do Media Encoder Premium|
|---|---|---|
|Aplicar a lógica condicional durante a codificação<br/>(por exemplo, se a entrada for HD, codificar como áudio 5.1)|Não |sim|
|Legendagem oculta|Não |[Sim](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® Professional Loudness Correction](http://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> com Dialogue Intelligence™|Não |sim|
|Desentrelaçamento, telecine inverso|Basic|Qualidade de difusão|
|Detectar e remover bordas pretas <br/>(formatos pillarbox e letterbox)|Não |sim|
|Geração de miniaturas|[Sim](media-services-dotnet-generate-thumbnail-with-mes.md)|[Sim](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Distorção/filtragem e costura de vídeos|[Sim](media-services-advanced-encoding-with-mes.md#trim_video)|sim|
|Sobreposições de áudio ou vídeo|[Sim](media-services-advanced-encoding-with-mes.md#overlay)|[Sim](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Sobreposições de elementos gráficos|De fontes de imagem|De fontes de imagem e texto|
|Várias faixas de idioma de áudio|Limitado|[Sim](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Medidor de cobrança usado por cada codificador
| Nome do processador de mídia | Preços aplicáveis | Observações |
| --- | --- | --- |
| **Media Encoder Standard** |CODIFICADOR |As Tarefas de Codificação serão cobradas com base na duração total, em minutos, de todos os arquivos de mídia gerados como saída, na taxa especificada [aqui][1], na coluna CODIFICADOR. |
| **Fluxo de trabalho do Media Encoder Premium** |CODIFICADOR PREMIUM |As Tarefas de Codificação serão cobradas com base na duração total, em minutos, de todos os arquivos de mídia gerados como saída, na taxa especificada [aqui][1], na coluna CODIFICADOR PREMIUM. |

## <a name="input-containerfile-formats"></a>Formatos de contêiner/arquivo de entrada
| Formatos de arquivo/contêiner de entrada | Media Encoder Standard | Fluxo de trabalho do Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |sim |sim |
| MXF/SMPTE 377M |sim |sim |
| GXF |sim |sim |
| Fluxos de transporte de MPEG-2 |sim |sim |
| Fluxos de programa MPEG-2 |sim |sim |
| MPEG-4/MP4 |sim |sim |
| Windows Media/ASF |sim |sim |
| AVI (8 bits/10 bits descompactado) |sim |sim |
| 3GPP/3GPP2 |sim |Não  |
| Formato de arquivo do Smooth Streaming (PIFF 1.3) |sim |Não  |
| [Gravação (DVR-MS) de vídeo Digital da Microsoft](https://msdn.microsoft.com/library/windows/desktop/dd692984) |sim |Não  |
| Matroska/WebM |sim |Não  |
| QuickTime (.mov) |sim |Não  |

## <a name="input-video-codecs"></a>Codecs de vídeo de entrada
| Codecs de vídeo de entrada | Media Encoder Standard | Fluxo de trabalho do Media Encoder Premium |
| --- | --- | --- |
| AVC de 8 bits/10 bits até 4:2:2, incluindo AVCIntra |8 bits 4:2:0 e 4:2:2 |sim |
| DNxHD ávido (em MXF) |sim |sim |
| DVCPro/DVCProHD (em MXF) |sim |sim |
| JPEG2000 |sim |sim |
| MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10) |Até perfil 422 |sim |
| MPEG-1 |sim |sim |
| Windows Media Video/VC-1 |sim |sim |
| Canopus HQ/HQX |Não  |Não  |
| MPEG-4, parte 2 |sim |Não  |
| [Theora](https://en.wikipedia.org/wiki/Theora) |sim |Não  |
| Apple ProRes 422 |sim |Não  |
| Apple ProRes 422 LT |sim |Não  |
| Apple ProRes 422 HQ |sim |Não  |
| Apple ProRes Proxy |sim |Não  |
| Apple ProRes 4444 |sim |Não  |
| Apple ProRes 4444 XQ |sim |Não  |

## <a name="input-audio-codecs"></a>Codecs de áudio de entrada
| Codecs de áudio de entrada | Media Encoder Standard | Fluxo de trabalho do Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331M e 302M, AES3-2003) |Não  |sim |
| Dolby® E |Não  |sim |
| Dolby® Digital (AC3) |Não  |sim |
| Dolby® Digital Plus (E-AC3) |Não  |sim |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1) |sim |sim |
| MPEG Layer 2 |sim |sim |
| MP3 (MPEG-1 Audio Layer 3) |sim |sim |
| Áudio do Windows Media |sim |sim |
| WAV/PCM |sim |sim |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |sim |Não  |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |sim |Não  |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |sim |Não  |

## <a name="output-containerfile-formats"></a>Formatos de contêiner/arquivo de saída
| Formatos de contêiner/arquivo de saída | Media Encoder Standard | Fluxo de trabalho do Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Não  |sim |
| MXF (OP1a, XDCAM e AS02) |Não  |sim |
| DPP (incluindo AS11) |Não  |sim |
| GXF |Não  |sim |
| MPEG-4/MP4 |sim |sim |
| MPEG-TS |sim |sim |
| Windows Media/ASF |Não  |sim |
| AVI (8 bits/10 bits descompactado) |Não  |sim |
| Formato de arquivo do Smooth Streaming (PIFF 1.3) |Não  |sim |

## <a name="output-video-codecs"></a>Codecs de vídeo de saída
| Codecs de vídeo de saída | Media Encoder Standard | Fluxo de trabalho do Media Encoder Premium |
| --- | --- | --- |
| AVC (H. 264; 8 bits; até perfil, nível elevado 5.2; 4K Ultra HD; Intra AVC) |Somente 8 bits 4:2:0 |sim |
| HEVC (H.265; 8 bits e 10 bits;)  |Não  |sim |
| DNxHD ávido (em MXF) |Não  |sim |
| MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10) |Não  |sim |
| MPEG-1 |Não  |sim |
| Windows Media Video/VC-1 |Não  |sim |
| Criação de miniaturas JPEG |sim |sim |
| Criação de miniaturas PNG |sim |sim |
| Criação de miniaturas BMP |sim |Não  |

## <a name="output-audio-codecs"></a>Codecs de áudio de saída
| Codecs de áudio de saída | Media Encoder Standard | Fluxo de trabalho do Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331M e 302M, AES3-2003) |Não  |sim |
| Dolby® Digital (AC3) |Não  |sim |
| Dolby ® Digital Plus (E-AC3) até 7.1 |Não  |sim |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1) |sim |sim |
| MPEG Layer 2 |Não  |sim |
| MP3 (MPEG-1 Audio Layer 3) |Não  |sim |
| Áudio do Windows Media |Não  |sim |

>[!NOTE]
>Se você codificar para Dolby® Digital (AC3), a saída só poderá ser gravada em um arquivo ISO MP4.

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Executar tarefas avançadas de codificação ao personalizar predefinições do Codificador de Mídia Padrão](media-services-custom-mes-presets-with-dotnet.md)
* [Cotas e limitações](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
