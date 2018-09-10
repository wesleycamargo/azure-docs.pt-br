---
title: Use o codificador padrão nos Serviços de Mídia do Azure para codificar vídeos usando uma escada de taxa de bits gerada automaticamente | Microsoft Docs
description: Este tópico mostra como usar o codificador padrão nos Serviços de Mídia para codificar uma entrada de vídeo com uma escada de taxa de bits gerada automaticamente, com base na resolução de entrada e na taxa de bits. A resolução de entrada e a taxa de bits nunca serão excedidas. Por exemplo, se a entrada for 720p em 3Mbps, a saída continuará 720p na melhor das hipóteses e iniciará com taxas menores que 3Mbps.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 6e447c04f4a94f2fb534ecb0605595a90816431e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638289"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Codificar com uma escada de taxa de bits gerada automaticamente

## <a name="overview"></a>Visão geral

Este artigo explica como usar o codificador padrão nos Serviços de Mídia para codificar uma entrada de vídeo em uma escada de taxa de bits gerada automaticamente (pares de taxa de bits e resolução), com base na resolução de entrada e na taxa de bits. Essa configuração de codificador integrada nunca excederá a resolução de entrada e a taxa de bits. Por exemplo, se a entrada for 720p em 720p, a saída continuará 720p na melhor das hipóteses e iniciará com taxas menores que 3 Mbps.

### <a name="encoding-for-streaming"></a>Codificar para streaming

Quando você usa o **AdaptiveStreaming** predefinido em **Transformar**, você obtém uma saída que é adequada para distribuição via protocolos de streaming como HLS e DASH. Ao usar esta predefinição, o serviço determina de forma inteligente quantas camadas de vídeos devem ser geradas e, em quais taxas de bits e a resolução. O conteúdo de saída contém arquivos MP4 onde áudio codificado AAC e vídeo codificado H.264 não são intercalados.

Para ver um exemplo de como essa predefinição é usada, consulte [Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Saída

Esta seção exibe três exemplos de camadas de vídeo de saída produzidas pelo codificador dos Serviços de Mídia como resultado da codificação com a predefinição do **AdaptiveStreaming**. Em todos os casos, a saída contém um arquivo MP4 somente de áudio com áudio estéreo codificado a 128 kbps.

### <a name="example-1"></a>Exemplo 1
Fonte com altura "1080" e taxa de quadros "29.970" produz seis camadas de vídeo:

|Camada|Altura|Largura|Taxa de bits (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exemplo 2
Fonte com altura "720" e taxa de quadros "23.970" produz cinco camadas de vídeo:

|Camada|Altura|Largura|Taxa de bits (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exemplo 3
Fonte com altura "360" e taxa de quadros "29.970" produz três camadas de vídeo:

|Camada|Altura|Largura|Taxa de bits (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
