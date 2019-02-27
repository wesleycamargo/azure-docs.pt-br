---
title: Codificação na nuvem com Serviços de Mídia - Azure | Microsoft Docs
description: Este tópico descreve o processo de codificação ao usar os Serviços de Mídia do Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/17/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 52e7fdf6de25300d4f78ee9822aca4ad83f646e9
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408418"
---
# <a name="encoding-with-media-services"></a>Codificação com os Serviços de Mídia do Microsoft Azure

Os Serviços de Mídia do Azure permitem codificar os arquivos de mídia digital de alta qualidade em formatos que podem ser reproduzidos em uma grande variedade de navegadores e dispositivos. Por exemplo, talvez você queira transmitir por streaming o conteúdo nos formatos MPEG-DASH ou HLS da Apple. Este tópico fornece orientação sobre como codificar seu conteúdo com o Serviços de Mídia do Microsoft Azure v3.

Para codificar com os Serviços de Mídia v3 é necessário criar uma [Transformação](https://docs.microsoft.com/rest/api/media/transforms) e um [Trabalho](https://docs.microsoft.com/rest/api/media/jobs). Uma transformação define a receita para as configurações de codificação e saídas e, o trabalho é uma instância da receita. Para obter mais informações, consulte [Transformações e Trabalhos](transforms-jobs-concept.md)

Ao codificar com o Serviços de Mídia do Microsoft Azure, você usa predefinições para informar ao codificador como os arquivos de mídia de entrada devem ser processados. Por exemplo, é possível especificar a resolução de vídeo e/ou o número de canais de áudio que desejar no conteúdo codificado. 

Você pode iniciar de forma rápida com uma das predefinições internas recomendadas com base nas melhores práticas do setor ou pode optar por criar uma predefinição personalizada para segmentar os requisitos de dispositivo ou cenário específico. Para obter mais informações, consulte [Codificar com uma transformação personalizada](customize-encoder-presets-how-to.md). 

A partir de janeiro de 2019, ao codificar com o Media Encoder Standard para produzir arquivos MP4, um novo arquivo .mpi será gerado e adicionado à saída Ativo. Esse arquivo MPI destina-se a melhorar o desempenho dos cenários de empacotamento e [streaming dinâmicos](dynamic-packaging-overview.md).

> [!NOTE]
> Você não deve modificar nem remover o arquivo MPI, nem usar qualquer dependência em seu serviço na existência (ou não) desse arquivo.

## <a name="built-in-presets"></a>Predefinições internas

Os Serviços de Mídia atualmente dão suporte às predefinições de codificação internas a seguir:  

### <a name="builtinstandardencoderpreset-preset"></a>Predefinição de BuiltInStandardEncoderPreset

O [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) é usado para definir uma predefinição interna para codificar o vídeo de entrada com o Codificador Standard. 

As predefinições a seguir são atualmente têm suporte:

- **EncoderNamedPreset.AdaptiveStreaming** (recomendado). Para obter mais informações, consulte [geração automática de uma escada de taxa de bits ](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.AACGoodQualityAudio** - produz um único arquivo MP4 contendo apenas áudio estéreo codificado a 192 kbps.
- **EncoderNamedPreset.H264MultipleBitrate1080p** - produz um conjunto de 8 arquivos MP4 alinhados a GOP, variando de 6000 kbps a 400 kbps e áudio AAC estéreo. A resolução inicia em 1080p e diminui para 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p** - produz um conjunto de 6 arquivos MP4 alinhados a GOP, variando de 3400 kbps a 400 kbps e áudio AAC estéreo. A resolução inicia em 720p e diminui para 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD** - produz um conjunto de 5 arquivos MP4 alinhados a GOP, variando de 1600kbps a 400 kbps e áudio AAC estéreo. A resolução inicia em 480p e diminui para 360p.<br/><br/>Para mais informações, consulte [Carregar, codificar e transmitir arquivos por streaming](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset-preset"></a>Predefinição de StandardEncoderPreset

O [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) descreve as configurações a serem usadas ao codificar o vídeo de entrada com o Codificador Standard. Use essa predefinição ao personalizar as predefinições de Transformação. 

#### <a name="custom-presets"></a>Predefinições personalizadas

Os Serviços de Mídia oferece suporte completo para a personalização de todos os valores em predefinições, a fim de atender às suas necessidades e requisitos de codificação. Você usa a predefinição **StandardEncoderPreset** ao personalizar as predefinições de Transformação. Para obter explicações detalhadas e exemplos, consulte [Como personalizar predefinições de codificador](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>Codificação de escala na v3

Atualmente, os clientes precisam usar as APIs do portal do Azure ou do Media Services v2 para definir os RUs (conforme descrito em [Scaling media processing](../previous/media-services-scale-media-processing-overview.md)). 

## <a name="next-steps"></a>Próximas etapas

* [Transformações e Trabalhos](transforms-jobs-concept.md)
* [Carregar, codificar e transmitir usando os Serviços de Mídia do Microsoft Azure](stream-files-tutorial-with-api.md)
