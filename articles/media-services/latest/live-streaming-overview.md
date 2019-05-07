---
title: Visão geral da transmissão ao vivo com os serviços de mídia do Azure v3 | Microsoft Docs
description: Este artigo fornece uma visão geral da transmissão ao vivo usando os serviços de mídia do Azure v3.
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
ms.date: 04/03/2019
ms.author: juliako
ms.openlocfilehash: 3cd22b89852706ed129373fd24ca9a9d9a27d25b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153370"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Transmissão ao vivo com os Serviços de Mídia do Azure v3

O Azure Media Services permite entregar eventos ao vivo para seus clientes na nuvem do Azure. Para transmitir seus eventos ao vivo com os Serviços de Mídia, você precisa do seguinte:  

- Uma câmera é usada para capturar o evento ao vivo.<br/>Para obter ideias de instalação, confira [Configuração da engrenagem de vídeo de evento simples e portátil]( https://link.medium.com/KNTtiN6IeT).

    Se você não tiver acesso a uma câmera, ferramentas, como [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) podem ser usados gerar um feed em tempo real a partir de um arquivo de vídeo.
- Um codificador de vídeo ao vivo que converte sinais de uma câmera (ou outro dispositivo, como um laptop) em um feed de contribuição enviado para os Serviços de Mídia. O feed de contribuição pode incluir sinais relacionados à publicidade, como os marcadores SCTE-35.<br/>Para obter uma lista dos codificadores de transmissão ao vivo recomendados, confira [Codificadores de transmissão ao vivo](recommended-on-premises-live-encoders.md). Além disso, confira este blog: [Live streaming production with OBS](https://link.medium.com/ttuwHpaJeT) (Produção de transmissão ao vivo com o OBS).
- Componentes nos Serviços de Mídia, que permitem a você ingerir, visualizar, empacotar, gravar, criptografar e transmitir o evento ao vivo para seus clientes ou para um CDN para distribuição posterior.

Este artigo fornece uma visão geral e a orientação da transmissão ao vivo com os serviços de mídia e links para outros artigos pertinentes.

> [!NOTE]
> Atualmente, você não pode usar o portal do Azure para gerenciar recursos da v3. Use a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](developers-guide.md) com suporte.

## <a name="dynamic-packaging"></a>Empacotamento dinâmico

Com os serviços de mídia, você pode aproveitar [empacotamento dinâmico](dynamic-packaging-overview.md), que permite que você visualize e transmitir suas transmissões ao vivo no [formatos MPEG DASH, HLS e Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) do feed de contribuição que estão sendo enviado para o serviço. Seus espectadores podem reproduzir a transmissão ao vivo com qualquer player compatível com HLS, DASH ou Smooth Streaming. Você pode usar o [Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) em seus aplicativos da Web ou móveis para fornecer seu fluxo em qualquer um desses protocolos.

## <a name="dynamic-encryption"></a>Criptografia dinâmica

Criptografia dinâmica permite criptografar dinamicamente seu conteúdo ao vivo ou sob demanda com o AES-128 ou qualquer um dos três sistemas DRM (gerenciamento) principais de direitos digitais: Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados. Para obter mais informações, consulte [criptografia dinâmica](content-protection-overview.md).

## <a name="dynamic-manifest"></a>Manifesto dinâmico

Filtragem dinâmica é usado para controlar o número de faixas, formatos, taxas de bits e janelas de tempo de apresentação que são enviadas para os jogadores. Para obter mais informações, consulte [filtros e manifestos dinâmicos](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Tipos de eventos ao vivo

Um evento ao vivo pode ser um dos dois tipos: codificação ao vivo e passagem. Para obter detalhes sobre a transmissão ao vivo nos serviços de mídia v3, consulte [eventos ao vivo e ao vivo saídas](live-events-outputs-concept.md).

### <a name="pass-through"></a>Passagem

![passagem](./media/live-streaming/pass-through.svg)

Ao usar o **Evento ao vivo** de passagem, você depende de seu codificador dinâmico local para gerar um fluxo de vídeo com várias taxas de bits e enviá-lo como o feed de contribuição para o Evento ao vivo (usando o protocolo RTMP ou MP4 fragmentado). A seguir, o Evento ao vivo executa os fluxos de vídeo de entrada sem qualquer processamento adicional. Tal uma passagem evento ao vivo é otimizado para eventos ao vivo de longa execução ou 24 x 365 linear à transmissão ao vivo. 

### <a name="live-encoding"></a>Codificação ativa  

![Codificação ativa](./media/live-streaming/live-encoding.svg)

Usando a codificação dinâmica com os Serviços de Mídia, você configuraria seu codificador dinâmico local para enviar um vídeo de taxa de bits única como o feed de contribuição para o Evento ao vivo (usando o protocolo RTMP ou MP4 fragmentado). O Evento ao vivo codifica esse fluxo de entrada de taxa de bits única para um [fluxo de vídeo de taxa de bits múltipla](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), tornando-o disponível para entrega para reproduzir dispositivos através de protocolos como MPEG-DASH, HLS e Smooth Streaming. 

## <a name="live-streaming-workflow"></a>Fluxo de trabalho de streaming ao vivo

Para entender o fluxo de trabalho de streaming ao vivo nos serviços de mídia v3, você precisa primeiro revisar e entende os conceitos a seguir: 

- [Pontos de Extremidade de Streaming](streaming-endpoint-concept.md)
- [Eventos ao Vivo e Saídas Dinâmicas](live-events-outputs-concept.md)
- [Localizadores de Streaming](streaming-locators-concept.md)

### <a name="general-steps"></a>Etapas gerais

1. Em sua conta de serviços de mídia, certifique-se a **ponto de extremidade de Streaming** (origem) está em execução. 
2. Crie um [evento ao vivo](live-events-outputs-concept.md). <br/>Ao criar o evento, é possível especificar sua inicialização automática. Como alternativa, você poderá iniciar o evento quando estiver pronto para iniciar o streaming.<br/> Quando a inicialização automática é definida como true, o Evento ao Vivo é iniciado logo após a criação. A cobrança começa assim que o Evento ao vivo começa a ser transmitido. É necessário chamar explicitamente o recurso Parar no Evento ao vivo para parar a cobrança adicional. Para saber mais, confira [Estados e cobrança do Evento ao vivo](live-event-states-billing.md).
3. Obtenha as URLs de ingestão e configure seu codificador local para usar a URL para enviar a feed de contribuição.<br/>Confira [Codificadores dinâmicos recomendados](recommended-on-premises-live-encoders.md).
4. Obtenha a URL de visualização e use-a para verificar se a entrada do codificador está sendo realmente recebida.
5. Crie um novo objeto de **ativo**.
6. Crie um **LiveOutput** e use o nome do ativo que você criou.<br/>A **Saída Dinâmica** arquivará o fluxo no **Ativo**.
7. Crie um **Localizador de Streaming** com os tipos internos da **Política de Streaming**.<br/>Se você pretende criptografar seu conteúdo, reveja a [Visão geral da proteção de conteúdo](content-protection-overview.md).
8. Liste os caminhos no **Localizador de Streaming** para retornar as URLs a serem usadas (elas são determinísticas).
9. Obter o nome do host para o **ponto de extremidade de Streaming** (origem) que você deseja transmitir do.
10. Combine a URL da etapa 8 com o nome do host na etapa 9 para obter a URL completa.
11. Caso deseje que o **Evento ao Vivo** deixe de ser visível, você precisará interromper a transmissão do evento e excluir o **Localizador de Streaming**.

## <a name="other-important-articles"></a>Outros artigos importantes

- [Codificadores dinâmicos recomendados](recommended-on-premises-live-encoders.md)
- [Usar um DVR de nuvem](live-event-cloud-dvr.md)
- [Comparação de recursos de tipos de Evento ao Vivo](live-event-types-comparison.md)
- [Estados e cobrança](live-event-states-billing.md)
- [Latência](live-event-latency.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, comentários, obtenha atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

* [Tutorial de live streaming](stream-live-tutorial-with-api.md)
* [Diretrizes de migração dos Serviços de Mídia v2 para v3](migrate-from-v2-to-v3.md)
