---
title: Codificação na nuvem com Serviços de Mídia do Microsoft Azure | Microsoft Docs
description: Este tópico descreve o processo de codificação ao usar os Serviços de Mídia do Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2018
ms.author: juliako
ms.openlocfilehash: 69c5516ee503d774b143bb2d83f09ea863a00b31
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "35765923"
---
# <a name="encoding-with-azure-media-services"></a>Codificação com Serviços de Mídia do Azure

Os Serviços de Mídia do Azure permitem codificar os arquivos de mídia digital de alta qualidade em formatos que podem ser reproduzidos em uma grande variedade de navegadores e dispositivos. Por exemplo, talvez você queira transmitir por streaming o conteúdo nos formatos MPEG-DASH ou HLS da Apple. Os Serviços de Mídia também permitem analisar o conteúdo de áudio ou vídeo. Este tópico fornece orientações sobre como codificar o conteúdo com Serviços de Mídia v3.

Para codificar com os Serviços de Mídia v3 é necessário criar uma transformação e um trabalho. Uma transformação define a receita para as configurações de codificação e saídas e, o trabalho é uma instância da receita. Para obter mais informações, consulte [Transformações e Trabalhos](transform-concept.md)

Ao codificar com Serviços de Mídia do Azure, você usará predefinições para informar ao codificador como os arquivos de mídia de entrada devem ser processados. Por exemplo, é possível especificar a resolução de vídeo e/ou o número de canais de áudio que desejar no conteúdo codificado. 

Você pode iniciar de forma rápida com uma das predefinições internas recomendadas com base nas melhores práticas do setor ou pode optar por criar uma predefinição personalizada para segmentar os requisitos de dispositivo ou cenário específico. 

Encontre detalhes sobre o codificador na [Especificação de OpenAPI](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2018-03-30-preview). 

## <a name="built-in-presets"></a>Predefinições internas

Os Serviços de Mídia atualmente dão suporte às predefinições de codificação internas a seguir:  

|**Nome da predefinição**|**Cenário**|**Detalhes**|
|---|---|---|
|**AudioAnalyzerPreset**|Analisar áudio|A predefinição aplica um conjunto predefinido de operações de análise baseadas em AI, incluindo transcrição de fala. Atualmente, a predefinição dá suporta ao processamento de conteúdo com uma única faixa do áudio.<br/>É possível especificar o idioma para a carga de áudio na entrada usando o formato BCP-47 de 'região de marca de idioma' (por exemplo, 'en-US'). A lista de idiomas com suporte é, 'en-US', 'en-GB', 'es-ES', 'es-MX', 'fr-FR', 'it-IT', 'ja-JP', 'pt-BR', 'zh-CN'.|
|**VideoAnalyzerPreset**|Analisar áudio e vídeo|Extraia insights (metadados avançados) de áudio e vídeo e gere um arquivo no formato JSON. É possível especificar se deseja extrair apenas insights de áudio ao processar um arquivo de vídeo. Para obter mais informações, consulte [Analisar vídeo](analyze-videos-tutorial-with-api.md).|
|**BuiltInStandardEncoderPreset**|Fluxo|Usado para definir uma predefinição interna para codificar o vídeo de entrada com o Codificador Standard. <br/>As predefinições a seguir são atualmente têm suporte:<br/>**EncoderNamedPreset.AdaptiveStreaming** (recomendado). Para obter mais informações, consulte [geração automática de uma escada de taxa de bits ](autogen-bitrate-ladder.md).<br/>**EncoderNamedPreset.AACGoodQualityAudio** - produz um único arquivo MP4 contendo apenas áudio estéreo codificado a 192 kbps.<br/>**EncoderNamedPreset.H264MultipleBitrate1080p** - produz um conjunto de 8 arquivos MP4 alinhados a GOP, variando de 6000 kbps a 400 kbps e áudio AAC estéreo. A resolução inicia em 1080p e diminui para 360p.<br/>**EncoderNamedPreset.H264MultipleBitrate720p** - produz um conjunto de 6 arquivos MP4 alinhados a GOP, variando de 3400 kbps a 400 kbps e áudio AAC estéreo. A resolução inicia em 720p e diminui para 360p.<br/>**EncoderNamedPreset.H264MultipleBitrateSD** - produz um conjunto de 5 arquivos MP4 alinhados a GOP, variando de 1600kbps a 400 kbps e áudio AAC estéreo. A resolução inicia em 480p e diminui para 360p.<br/><br/>Para mais informações, consulte [Carregar, codificar e transmitir arquivos por streaming](stream-files-tutorial-with-api.md).|
|**StandardEncoderPreset**|Fluxo|Descreve as configurações a serem usadas ao codificar o vídeo de entrada com o Codificador Standard. <br/>Use essa predefinição ao personalizar as predefinições de Transformação. Para obter mais informações, consulte [Como personalizar predefinições de Transformação](customize-encoder-presets-how-to.md).|

## <a name="custom-presets"></a>Predefinições personalizadas

Os Serviços de Mídia oferece suporte completo para a personalização de todos os valores em predefinições, a fim de atender às suas necessidades e requisitos de codificação. Você usa a predefinição **StandardEncoderPreset** ao personalizar as predefinições de Transformação. Para obter explicações detalhadas e exemplos, consulte [Como personalizar predefinições de codificador](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>Codificação de escala na v3

Atualmente, os clientes precisam usar o portal do Azure ou as APIs do AMS v2 para definir RUs (conforme descrito em [Colocação em escala do processamento de mídia](../previous/media-services-scale-media-processing-overview.md). 

## <a name="next-steps"></a>Próximas etapas

### <a name="tutorials"></a>Tutoriais

Os tutoriais a seguir mostram como codificar conteúdo com os Serviços de Mídia:

* [Carregar, codificar e transmitir usando Serviços de Mídia do Azure](stream-files-tutorial-with-api.md)
* [Analisar vídeos com os Serviços de Mídia do Azure](analyze-videos-tutorial-with-api.md)

### <a name="code-samples"></a>Exemplos de código

Os exemplos de código a seguir contêm código que mostra como codificar com os Serviços de Mídia:

* [.NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore)
* [CLI do Azure](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)

### <a name="sdks"></a>SDKs

É possível usar qualquer um dos seguintes SDKs dos Serviços de Mídia v3 com suporte para codificar o conteúdo.

* [CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [REST](https://docs.microsoft.com/rest/api/media/transforms)
* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices)
* [Python](https://docs.microsoft.com/python/api/overview/azure/media-services?view=azure-python)

