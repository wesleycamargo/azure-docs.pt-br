---
title: Codificação na nuvem com Serviços de Mídia do Microsoft Azure | Microsoft Docs
description: Este tópico descreve o processo de codificação ao usar os Serviços de Mídia do Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 11/05/2018
ms.author: juliako
ms.openlocfilehash: 0df13e3364cebe7cb5804b840889bca971b36be2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235047"
---
# <a name="encoding-with-media-services"></a>Codificação com os Serviços de Mídia do Microsoft Azure

Os Serviços de Mídia do Azure permitem codificar os arquivos de mídia digital de alta qualidade em formatos que podem ser reproduzidos em uma grande variedade de navegadores e dispositivos. Por exemplo, talvez você queira transmitir por streaming o conteúdo nos formatos MPEG-DASH ou HLS da Apple. Este tópico fornece orientação sobre como codificar seu conteúdo com o Serviços de Mídia do Microsoft Azure v3.

Para codificar com os Serviços de Mídia v3 é necessário criar uma transformação e um trabalho. Uma transformação define a receita para as configurações de codificação e saídas e, o trabalho é uma instância da receita. Para obter mais informações, consulte [Transformações e Trabalhos](transform-concept.md)

Ao codificar com o Serviços de Mídia do Microsoft Azure, você usa predefinições para informar ao codificador como os arquivos de mídia de entrada devem ser processados. Por exemplo, é possível especificar a resolução de vídeo e/ou o número de canais de áudio que desejar no conteúdo codificado. 

Você pode iniciar de forma rápida com uma das predefinições internas recomendadas com base nas melhores práticas do setor ou pode optar por criar uma predefinição personalizada para segmentar os requisitos de dispositivo ou cenário específico. Para obter mais informações, consulte [Codificar com uma transformação personalizada](customize-encoder-presets-how-to.md). 

## <a name="built-in-presets"></a>Predefinições internas

Os Serviços de Mídia atualmente dão suporte às predefinições de codificação internas a seguir:  

|**Nome da predefinição**|**Cenário**|**Detalhes**|
|---|---|---|
|**BuiltInStandardEncoderPreset**|Fluxo|Usado para definir uma predefinição interna para codificar o vídeo de entrada com o Codificador Standard. <br/>As predefinições a seguir são atualmente têm suporte:<br/>**EncoderNamedPreset.AdaptiveStreaming** (recomendado). Para obter mais informações, consulte [geração automática de uma escada de taxa de bits ](autogen-bitrate-ladder.md).<br/>**EncoderNamedPreset.AACGoodQualityAudio** - produz um único arquivo MP4 contendo apenas áudio estéreo codificado a 192 kbps.<br/>**EncoderNamedPreset.H264MultipleBitrate1080p** - produz um conjunto de 8 arquivos MP4 alinhados a GOP, variando de 6000 kbps a 400 kbps e áudio AAC estéreo. A resolução inicia em 1080p e diminui para 360p.<br/>**EncoderNamedPreset.H264MultipleBitrate720p** - produz um conjunto de 6 arquivos MP4 alinhados a GOP, variando de 3400 kbps a 400 kbps e áudio AAC estéreo. A resolução inicia em 720p e diminui para 360p.<br/>**EncoderNamedPreset.H264MultipleBitrateSD** - produz um conjunto de 5 arquivos MP4 alinhados a GOP, variando de 1600kbps a 400 kbps e áudio AAC estéreo. A resolução inicia em 480p e diminui para 360p.<br/><br/>Para mais informações, consulte [Carregar, codificar e transmitir arquivos por streaming](stream-files-tutorial-with-api.md).|
|**StandardEncoderPreset**|Fluxo|Descreve as configurações a serem usadas ao codificar o vídeo de entrada com o Codificador Standard. <br/>Use essa predefinição ao personalizar as predefinições de Transformação. Para obter mais informações, consulte [Como personalizar predefinições de Transformação](customize-encoder-presets-how-to.md).|

## <a name="custom-presets"></a>Predefinições personalizadas

Os Serviços de Mídia oferece suporte completo para a personalização de todos os valores em predefinições, a fim de atender às suas necessidades e requisitos de codificação. Você usa a predefinição **StandardEncoderPreset** ao personalizar as predefinições de Transformação. Para obter explicações detalhadas e exemplos, consulte [Como personalizar predefinições de codificador](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>Codificação de escala na v3

Atualmente, os clientes precisam usar as APIs do portal do Azure ou do Media Services v2 para definir os RUs (conforme descrito em [Scaling media processing](../previous/media-services-scale-media-processing-overview.md)). 

## <a name="next-steps"></a>Próximas etapas

### <a name="tutorials"></a>Tutoriais

O tutorial a seguir mostra como codificar seu conteúdo com o Serviços de Mídia do Microsoft Azure:

* [Carregar, codificar e transmitir usando os Serviços de Mídia do Microsoft Azure](stream-files-tutorial-with-api.md)

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

