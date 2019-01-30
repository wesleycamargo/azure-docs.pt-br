---
title: Visão geral da transmissão ao vivo usando os Serviços de Mídia do Azure | Microsoft Docs
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
ms.date: 01/22/2019
ms.author: juliako
ms.openlocfilehash: 3be7ad84cf0d45276c136465d7247ec43621aceb
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810951"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Transmissão ao vivo com os Serviços de Mídia do Azure v3

O Azure Media Services permite entregar eventos ao vivo para seus clientes na nuvem do Azure. Para transmitir seus eventos ao vivo com os Serviços de Mídia, você precisa do seguinte:  

- Uma câmera é usada para capturar o evento ao vivo.
- Um codificador de vídeo ao vivo que converte sinais da câmera (ou outro dispositivo, como um laptop) em um feed de contribuição enviado para os Serviços de Mídia. O feed de contribuição pode incluir sinais relacionados à publicidade, como os marcadores SCTE-35.
- Componentes nos Serviços de Mídia, que permitem a você ingerir, visualizar, empacotar, gravar, criptografar e transmitir o evento ao vivo para seus clientes ou para um CDN para distribuição posterior.

Este artigo fornece uma visão geral detalhada, orientação e inclui diagramas dos principais componentes envolvidos na transmissão ao vivo com os Serviços de Mídia.

## <a name="live-streaming-workflow"></a>Fluxo de trabalho de streaming ao vivo

Aqui estão as etapas para um fluxo de trabalho de streaming ao vivo:

1. Verifique se o **StreamingEndpoint** está em execução. 
2. Crie um **LiveEvent**. 
  
    Ao criar o evento, é possível especificar sua inicialização automática. Como alternativa, você poderá iniciar o evento quando estiver pronto para iniciar o streaming.<br/> Quando a inicialização automática é definida como true, o Evento ao Vivo é iniciado logo após a criação. Isso significa que a cobrança começa assim que o Evento ao Vivo começa a ser transmitido. Você deve chamar explicitamente Parar no recurso LiveEvent para interromper o faturamento adicional. Para obter mais informações, confira [Estados e cobrança do LiveEvent](live-event-states-billing.md).
3. Obtenha as URLs de ingestão e configure seu codificador local para usar a URL no envio do feed de contribuição.<br/>Confira [Codificadores dinâmicos recomendados](recommended-on-premises-live-encoders.md).
4. Obtenha a URL de visualização e use-a para verificar se a entrada do codificador está sendo realmente recebida.
5. Crie um novo objeto de **ativo**.
6. Crie um **LiveOutput** e use o nome do ativo que você criou.

     O **LiveOutput** arquivará a transmissão no **ativo**.
7. Crie um **StreamingLocator** com os tipos de **StreamingPolicy** internos.

    Se você pretende criptografar seu conteúdo, reveja a [Visão geral da proteção de conteúdo](content-protection-overview.md).
8. Liste os caminhos no **Localizador de Streaming** para retornar as URLs a serem usadas (elas são determinísticas).
9. Obtenha o nome do host para o **ponto de extremidade de streaming** de onde você deseja transmitir.
10. Combine a URL da etapa 8 com o nome do host na etapa 9 para obter a URL completa.
11. Se quiser que o **LiveEvent** deixe de ser visível, você precisará interromper o evento de streaming e excluir o **StreamingLocator**.

Para obter mais informações, confira o [Tutorial de transmissão ao vivo](stream-live-tutorial-with-api.md).

## <a name="overview-of-main-components"></a>Visão geral dos componentes principais

Para fornecer transmissões on-demand ou ao vivo com os Serviços de Mídia do Azure, você precisa ter pelo menos um [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints). Quando sua conta de Serviços de Mídia é criada, um StreamingEndpoint **padrão** é adicionado à sua conta no estado **Parado**. Você precisa iniciar o StreamingEndpoint do qual deseja transmitir seu conteúdo para seus espectadores. Você pode usar o **StreamingEndpoint** padrão ou criar outro **StreamingEndpoint** personalizado com as configurações de CDN e configuração desejadas. Você pode decidir ativar vários StreamingEndpoints, cada um segmentando um CDN diferente e fornecendo um nome de host exclusivo para a entrega de conteúdo. 

Nos Serviços de Mídia, [LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents) são responsáveis pela ingestão e processamento dos feeds de vídeo ao vivo. Quando você cria um LiveEvent, é criado um terminal de entrada que pode ser usado para enviar um sinal ao vivo de um codificador remoto. O codificador dinâmico remoto envia o feed de contribuição para esse terminal de entrada usando o protocolo [RTMP](https://www.adobe.com/devnet/rtmp.html) ou [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (fragmented-MP4). Para o protocolo de ingestão Smooth Streaming, os esquemas de URL com suporte são `http://` ou `https://`. Para o protocolo de ingestão RTMP, os esquemas de URL com suporte são `rtmp://` ou `rtmps://`. Para obter mais informações, consulte [Codificadores de transmissão ao vivo recomendados](recommended-on-premises-live-encoders.md).<br/>
Ao criar um **LiveEvent**, você poderá especificar os endereços IP permitidos em um dos seguintes formatos: endereço IPv4 com quatro números e intervalo de endereços CIDR.

Quando o **LiveEvent** começar a receber o feed de contribuição, você poderá usar o ponto de extremidade de visualização (URL de visualização para visualizar e validar que está recebendo a transmissão ao vivo antes de publicar mais. Depois de verificar se o fluxo de visualização é bom, você pode usar o LiveEvent para disponibilizar a transmissão ao vivo para entrega por meio de um ou mais **StreamingEndpoints** (pré-criados). Para conseguir isso, crie uma nova [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) no **LiveEvent**. 

O objeto **LiveOutput** é como um gravador que captura e grava a transmissão ao vivo em um Ativo em sua conta de Serviços de Mídia. O conteúdo gravado será mantido na conta de Armazenamento do Azure anexada à sua conta, no contêiner definido pelo recurso Ativo.  O **LiveOuput** também permite controlar algumas propriedades da transmissão ao vivo, como quanto do fluxo é mantido na gravação do arquivo (por exemplo, a capacidade do DVR da nuvem). O arquivo no disco é uma "janela" de arquivo circular que contém apenas a quantidade de conteúdo especificada na propriedade **archiveWindowLength** da **LiveOutput**. O conteúdo que fica fora dessa janela é automaticamente descartado do contêiner de armazenamento e não é recuperável. Você pode criar várias LiveOutputs (até três no máximo) em um LiveEvent com diferentes comprimentos e configurações de arquivamento.  

Com os Serviços de Mídia, você pode aproveitar o **Dynamic Packaging**, que permite visualizar e transmitir suas transmissões ao vivo nos [formatos MPEG DASH, HLS e Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) do feed de contribuição que você envia para o serviço. Seus espectadores podem reproduzir a transmissão ao vivo com qualquer player compatível com HLS, DASH ou Smooth Streaming. Você pode usar o [Player de Mídia do Azure](http://amp.azure.net/libs/amp/latest/docs/index.html) em seus aplicativos da Web ou móveis para fornecer seu fluxo em qualquer um desses protocolos.

Com os Serviços de Mídia, você pode entregar seu conteúdo criptografado dinamicamente (**Criptografia Dinâmica**) com a criptografia AES-128 (Advanced Encryption Standard) ou qualquer um dos três principais sistemas de DRM: Microsoft PlayReady, Google Widevine e Apple FairPlay. Os Serviços de Mídia também fornecem um serviço para entrega de chaves AES e licenças DRM a clientes autorizados. Para obter mais informações sobre como criptografar seu conteúdo com os Serviços de Mídia, consulte [Protegendo a visão geral do conteúdo](content-protection-overview.md)

Se desejar, você também pode aplicar a Filtragem dinâmica, que pode ser usada para controlar o número de faixas, formatos, taxas de bits e janelas de tempo de apresentação que são enviadas aos jogadores. Para obter mais informações, consulte [Filtros e manifestos dinâmicos](filters-dynamic-manifest-overview.md).

Para obter informações sobre novos recursos de transmissão ao vivo na v3, confira [Orientação de migração para mudar dos Serviços de Mídia v2 para v3](migrate-from-v2-to-v3.md).

## <a name="liveevent-types"></a>Tipos de LiveEvent

Um [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) pode ser de dois tipos: de passagem e codificação ativa. 

### <a name="pass-through"></a>Passagem

![passagem](./media/live-streaming/pass-through.png)

Ao usar o LiveEvent de passagem, você depende de seu codificador local para gerar um fluxo de vídeo com várias taxas de bits e enviá-lo como o feed de contribuição para o LiveEvent (usando o protocolo RTMP ou fragmentado-MP4). O LiveEvent, em seguida, realiza os fluxos de vídeo de entrada sem qualquer processamento adicional. Esse LiveEvent de passagem é otimizado para eventos ao vivo de longa duração ou streaming ao vivo linear 24x365. Ao criar esse tipo de LiveEvent, especifique None (LiveEventEncodingType.None).

Você pode enviar a contribuição em resoluções de até 4K e em uma taxa de quadros de 60 quadros / segundo, com codecs de vídeo H.264/AVC ou H.265/HEVC e AAC (AAC-LC, HE-AACv1 ou HE-AACv2) codec de áudio.  Veja o artigo [ Comparação de tipos e limitações do LiveEvent ](live-event-types-comparison.md) para mais detalhes.

> [!NOTE]
> Usar um método de passagem é a maneira mais econômica de fazer uma transmissão ao vivo quando você estiver fazendo vários eventos durante um longo período e já tiver investido em codificadores locais. Veja os detalhes de [preços](https://azure.microsoft.com/pricing/details/media-services/) .
> 

Veja um exemplo em tempo real no [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Codificação ativa  

![Codificação ativa](./media/live-streaming/live-encoding.png)

Ao usar a codificação ao vivo com os Serviços de Mídia, você configuraria seu codificador dinâmico local para enviar um único vídeo de taxa de bits como o feed de contribuição para o LiveEvent (usando o protocolo RTMP ou Fragmented-Mp4). O LiveEvent codifica esse fluxo de entrada de taxa de bits única para um fluxo de vídeo de [taxa de bits múltipla](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), tornando-o disponível para entrega para reproduzir dispositivos através de protocolos como MPEG-DASH, HLS e Smooth Streaming. Ao criar esse tipo de LiveEvent, especifique o tipo de codificação como **Standard** (LiveEventEncodingType.Standard).

Você pode enviar a alimentação de contribuição com resolução de até 1080p a uma taxa de quadros de 30 quadros/segundo, com codec de vídeo H.264/AVC e codec de áudio AAC (AAC-LC, HE-AAC v1 ou HE-AAC v2). Veja o artigo [ Comparação de tipos e limitações do LiveEvent ](live-event-types-comparison.md) para mais detalhes.

## <a name="liveevent-types-comparison"></a>Comparação dos tipos de LiveEvent

O artigo a seguir contém uma tabela que compara os recursos dos dois tipos de LiveEvent: [Comparação](live-event-types-comparison.md).

## <a name="liveoutput"></a>LiveOutput

Uma [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) permite que você controle as propriedades da transmissão ao vivo, como quanto do fluxo é gravado (por exemplo, a capacidade do DVR na nuvem) e se os espectadores podem ou não assistir à exibição transmissão ao vivo. A relação entre um relacionamento **LiveEvent** e seu **LiveOutput** é similar à transmissão de televisão tradicional, onde um canal (**LiveEvent**) representa um fluxo constante de vídeo e uma gravação (**LiveOutput**) tem o escopo definido para um segmento de tempo específico (por exemplo, notícias noturnas das 18h30 às 19h00). Você pode gravar televisão usando um gravador de vídeo digital (DVR) - o recurso equivalente no LiveEvents é gerenciado por meio da propriedade ArchiveWindowLength. É uma duração de tempo ISO-8601 (por exemplo, PTHH: MM: SS), que especifica a capacidade do DVR e pode ser definida de um mínimo de 3 minutos a um máximo de 25 horas.

> [!NOTE]
> **LiveOutput** s inicie a criação e pare quando for excluído. Quando exclui o **LiveOutput**, você não está excluindo o **ativo** subjacente e o conteúdo no ativo. 
>
> Caso tenha publicado o ativo **LiveOutput** usando um **StreamingLocator**, o **LiveEvent** (até a duração da janela DVR) continuará visível até a expiração ou exclusão do **StreamingLocator**, o que ocorrer primeiro.

Para obter mais informações, consulte [Usando o DVR na nuvem](live-event-cloud-dvr.md).

## <a name="streamingendpoint"></a>StreamingEndpoint

Uma vez que o fluxo está fluindo para o **LiveEvent**, você pode começar o evento de transmissão criando um **ativo**, **LiveOutput**, e **StreamingLocator**. Isso arquivará o fluxo e o disponibilizará aos espectadores por meio do [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints).

Quando sua conta dos Serviços de Mídia é criada, um ponto de extremidade de streaming padrão é adicionado à sua conta em estado Parado. Para iniciar seu conteúdo de streaming e tirar proveito do empacotamento dinâmico e da criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar em estado Executando.

## <a name="a-idbilling-liveevent-states-and-billing"></a><a id="billing" /> Estados do LiveEvent e faturamento

Um LiveEvent começa o faturamento assim que seu estado passar para **Execução**. Para interromper o evento ao vivo do faturamento, você precisa interromper o evento ao vivo.

Para obter informações detalhadas, consulte [estados e cobrança](live-event-states-billing.md).

## <a name="latency"></a>Latency

Para obter informações detalhadas sobre a latência do LiveEvents, consulte [Latência](live-event-latency.md).

## <a name="next-steps"></a>Próximas etapas

- [Comparação de tipos de LiveEvent](live-event-types-comparison.md)
- [Estados e cobrança](live-event-states-billing.md)
- [Latência](live-event-latency.md)
