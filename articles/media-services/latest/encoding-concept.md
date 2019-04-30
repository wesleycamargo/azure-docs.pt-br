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
ms.date: 04/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ed798995807f4037f0127b08e25e04bdd0340d42
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103534"
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

- **EncoderNamedPreset.AACGoodQualityAudio** - produz um único arquivo MP4 contendo apenas áudio estéreo codificado a 192 kbps.
- **EncoderNamedPreset.AdaptiveStreaming** (recomendado). Para obter mais informações, consulte [geração automática de uma escada de taxa de bits ](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental** -expõe uma predefinição experimental para codificação com suporte a conteúdo. Dado qualquer conteúdo de entrada, o serviço tenta determinar automaticamente o número ideal de camadas, taxa de bits apropriada e as configurações de resolução para entrega por streaming adaptável. Os algoritmos subjacentes continuará a evoluir ao longo do tempo. A saída conterá arquivos MP4 com vídeo e áudio intercalados. Para obter mais informações, consulte [Experimental predefinição de codificação com suporte a conteúdo](cae-experimental.md).
- **EncoderNamedPreset.H264MultipleBitrate1080p** - produz um conjunto de 8 arquivos MP4 alinhados a GOP, variando de 6000 kbps a 400 kbps e áudio AAC estéreo. A resolução inicia em 1080p e diminui para 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p** - produz um conjunto de 6 arquivos MP4 alinhados a GOP, variando de 3400 kbps a 400 kbps e áudio AAC estéreo. A resolução inicia em 720p e diminui para 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD** - produz um conjunto de 5 arquivos MP4 alinhados a GOP, variando de 1600kbps a 400 kbps e áudio AAC estéreo. A resolução inicia em 480p e diminui para 360p.
- **EncoderNamedPreset.H264SingleBitrate1080p** -produz um arquivo MP4 em que o vídeo é codificado com o codec de h. 264 6750 kbps e uma altura de 1080 pixels da imagem e áudio estéreo codificado com o codec AAC-LC a 64 kbps.
- **EncoderNamedPreset.H264SingleBitrate720p** -produz um arquivo MP4 em que o vídeo é codificado com o codec de h. 264 4500 kbps e uma altura de imagem de 720 pixels e o áudio estéreo codificado com o codec AAC-LC a 64 kbps.
- **EncoderNamedPreset.H264SingleBitrateSD** -produz um arquivo MP4 em que o vídeo é codificado com o codec de h. 264 2200 kbps e uma altura de 480 pixels da imagem e áudio estéreo codificado com o codec AAC-LC a 64 kbps.

Para ver a lista mais atualizada de predefinições, consulte [predefinições para ser usado para codificar vídeos](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Para ver como os valores predefinidos são usados, fazer check-out [carregando, codificação e streaming de arquivos](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset-preset"></a>Predefinição de StandardEncoderPreset

O [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) descreve as configurações a serem usadas ao codificar o vídeo de entrada com o Codificador Standard. Use essa predefinição ao personalizar as predefinições de Transformação. 

#### <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, as seguintes considerações se aplicam:

- Todos os valores de altura e largura no conteúdo de AVC devem ser um múltiplo de 4.
- Serviços de mídia do Azure v3, todas as taxas de bits de codifica são em bits por segundo. Isso é diferente das predefinições com nossas APIs v2, que usado quilobits por segundo, como a unidade. Por exemplo, se a taxa de bits na versão 2 foi especificada como 128 (quilobits/segundo), na v3 ele deve ser definido como 128000 (bits/segundo).

#### <a name="examples"></a>Exemplos

Os Serviços de Mídia oferece suporte completo para a personalização de todos os valores em predefinições, a fim de atender às suas necessidades e requisitos de codificação. Para obter exemplos que mostram como personalizar as predefinições do codificador, consulte:

- [Personalizar as predefinições com .NET](customize-encoder-presets-how-to.md)
- [Personalizar as predefinições com a CLI](custom-preset-cli-howto.md)
- [Personalizar as predefinições com REST](custom-preset-rest-howto.md)

## <a name="scaling-encoding-in-v3"></a>Codificação de escala na v3

Para dimensionar o processamento de mídia, consulte [escala com CLI](media-reserved-units-cli-how-to.md).

## <a name="provide-feedback"></a>Fornecer comentários

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

* [Codificar a partir de uma URL HTTPS usando predefinições](job-input-from-http-how-to.md)
* [Codificar um arquivo local usando os valores predefinidos internos](job-input-from-local-file-how-to.md)
* [Criar uma predefinição para seus requisitos específicos de cenário ou dispositivo de destino personalizada](customize-encoder-presets-how-to.md)
* [Carregar, codificar e transmitir usando os Serviços de Mídia do Microsoft Azure](stream-files-tutorial-with-api.md)
