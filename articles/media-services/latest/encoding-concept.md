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
ms.date: 02/27/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: de2c60d4449762c4a8fcc3e2f486130f3df37c7c
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243612"
---
# <a name="encoding-with-media-services"></a>Codificação com os Serviços de Mídia do Microsoft Azure

Os serviços de mídia do Azure permite que você codificar seus arquivos de mídia digital de alta qualidade em arquivos MP4 de taxa de bits adaptável para que seu conteúdo pode ser executado em uma ampla variedade de navegadores e dispositivos. Um trabalho de codificação bem-sucedida dos serviços de mídia cria saída de uma ativo com um conjunto de MP4s de taxa de bits adaptável e streaming de arquivos de configuração. Os arquivos de configuração incluem. ISM,. ismc, .mpi e outros arquivos que você não deve modificar. Quando o trabalho de codificação for concluído, você pode aproveitar [empacotamento dinâmico](dynamic-packaging-overview.md) e iniciar o streaming.

Para fazer vídeos na saída do ativo disponível para clientes de reprodução, você precisa criar uma **localizador de Streaming** e criar URLs de streaming. Em seguida, os clientes com base no formato especificado no manifesto, recebam o fluxo no protocolo que escolheram.

O diagrama a seguir mostra o streaming sob demanda com o fluxo de trabalho de empacotamento dinâmico.

![Empacotamento dinâmico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Este tópico fornece orientação sobre como codificar seu conteúdo com o Serviços de Mídia do Microsoft Azure v3.

## <a name="transforms-and-jobs"></a>Transformações e trabalhos

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

Para dimensionar o processamento de mídia, consulte [escala com CLI](media-reserved-units-cli-how-to.md).

## <a name="next-steps"></a>Próximas etapas

* [Codificar a partir de uma URL HTTPS usando predefinições](job-input-from-http-how-to.md)
* [Codificar um arquivo local usando os valores predefinidos internos](job-input-from-local-file-how-to.md)
* [Criar uma predefinição para seus requisitos específicos de cenário ou dispositivo de destino personalizada](customize-encoder-presets-how-to.md)
* [Carregar, codificar e transmitir usando os Serviços de Mídia do Microsoft Azure](stream-files-tutorial-with-api.md)
