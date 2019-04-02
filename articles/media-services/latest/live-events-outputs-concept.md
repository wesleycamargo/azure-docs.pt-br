---
title: Conceitos de transmissão ao vivo nos Serviços de Mídia do Azure - Eventos ao Vivo e Saídas Dinâmicas | Microsoft Docs
description: Este artigo fornece uma visão geral da transmissão ao vivo usando os Serviços de Mídia do Azure v3.
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
ms.date: 03/30/2019
ms.author: juliako
ms.openlocfilehash: 1d28701dd35b9d80fd52a1f102c53f2d59d63b09
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762347"
---
# <a name="live-events-and-live-outputs"></a>Eventos ao Vivo e Saídas Dinâmicas

O Azure Media Services permite entregar eventos ao vivo para seus clientes na nuvem do Azure. Para configurar os eventos de transmissão ao vivo nos serviços de mídia v3, você precisa entender os conceitos discutidos neste artigo. <br/>A lista das seções é listada no lado direito da página.

## <a name="live-events"></a>Eventos ao Vivo

[Eventos ao Vivo](https://docs.microsoft.com/rest/api/media/liveevents) são responsáveis pela ingestão e pelo processamento dos feeds de vídeo ao vivo. Quando você cria um Evento ao vivo, é criado um ponto de extremidade de entrada que pode ser usado para enviar um sinal ao vivo de um codificador remoto. O codificador dinâmico remoto envia o feed de contribuição para esse terminal de entrada usando o protocolo [RTMP](https://www.adobe.com/devnet/rtmp.html) ou [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (fragmented-MP4). Para o protocolo de ingestão Smooth Streaming, os esquemas de URL compatíveis são `http://` ou `https://`. Para o protocolo de ingestão RTMP, os esquemas de URL com suporte são `rtmp://` ou `rtmps://`. 

## <a name="live-event-types"></a>Tipos de eventos ao vivo

Um [Evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents) pode ser de dois tipos: de passagem e de codificação ativa. 

### <a name="pass-through"></a>Passagem

![passagem](./media/live-streaming/pass-through.svg)

Ao usar o **Evento ao vivo** de passagem, você depende de seu codificador dinâmico local para gerar um fluxo de vídeo com várias taxas de bits e enviá-lo como o feed de contribuição para o Evento ao vivo (usando o protocolo RTMP ou MP4 fragmentado). A seguir, o Evento ao vivo executa os fluxos de vídeo de entrada sem qualquer processamento adicional. Esse LiveEvent de passagem é otimizado para eventos ao vivo de longa duração ou streaming ao vivo linear 24x365. Ao criar esse tipo de Evento ao vivo, especifique Nenhum (LiveEventEncodingType.None).

Você pode enviar a contribuição em resoluções de até 4K e em uma taxa de quadros de 60 quadros / segundo, com codecs de vídeo H.264/AVC ou H.265/HEVC e AAC (AAC-LC, HE-AACv1 ou HE-AACv2) codec de áudio.  Confira o artigo [Comparação de tipos de Eventos ao Vivo](live-event-types-comparison.md) para saber mais.

> [!NOTE]
> Usar um método de passagem é a maneira mais econômica de fazer uma transmissão ao vivo quando você estiver fazendo vários eventos durante um longo período e já tiver investido em codificadores locais. Veja os detalhes de [preços](https://azure.microsoft.com/pricing/details/media-services/) .
> 

Veja um exemplo de código do .NET no [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Codificação ativa  

![Codificação ativa](./media/live-streaming/live-encoding.svg)

Usando a codificação dinâmica com os Serviços de Mídia, você configuraria seu codificador dinâmico local para enviar um vídeo de taxa de bits única como o feed de contribuição para o Evento ao vivo (usando o protocolo RTMP ou MP4 fragmentado). O Evento ao vivo codifica esse fluxo de entrada de taxa de bits única para um [fluxo de vídeo de taxa de bits múltipla](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), tornando-o disponível para entrega para reproduzir dispositivos através de protocolos como MPEG-DASH, HLS e Smooth Streaming. Ao criar esse tipo de Evento ao vivo, especifique o tipo de codificação como **Padrão** (LiveEventEncodingType.Standard).

Você pode enviar a alimentação de contribuição com resolução de até 1080p a uma taxa de quadros de 30 quadros/segundo, com codec de vídeo H.264/AVC e codec de áudio AAC (AAC-LC, HE-AAC v1 ou HE-AAC v2). Confira o artigo [Comparação de tipos de Eventos ao Vivo](live-event-types-comparison.md) para saber mais.

Ao usar a codificação ao vivo (Eventos ao vivo definidos como **Padrão**), a predefinição de codificação define como o fluxo de entrada é codificado em várias taxas de bits ou camadas. Para obter informações, confira [Predefinições do sistema](live-event-types-comparison.md#system-presets).

> [!NOTE]
> No momento, a única predefinição de valor permitida para o tipo Padrão de Evento ao vivo é *Default720p*. Caso precise usar uma predefinição de codificação ao vivo personalizada, entre em contato com amshelp@microsoft.com. Você deve especificar a tabela desejada da resolução e das taxas de bits. Verifique se há apenas uma camada em 720p e no máximo seis camadas.

## <a name="live-event-creation-options"></a>Opções de criação de Evento ao vivo

Ao criar um Evento ao vivo, você pode especificar as seguintes opções:

* O protocolo de streaming para o Evento ao vivo (atualmente, os protocolos RTMP e Smooth Streaming são compatíveis).<br/>Não é possível alterar a opção de protocolo enquanto o Evento ao vivo ou suas Saídas ao vivo associadas estiverem em execução. Se você precisar de protocolos diferentes, crie um Evento ao vivo separado para cada protocolo de streaming.  
* Restrições de IP sobre a ingestão e versão prévia. É possível definir os endereços IP que têm permissão para ingerir um vídeo neste Evento ao vivo. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo, '10.0.0.1'), um intervalo IP usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, '10.0.0.1/22) ou um intervalo IP usando um endereço IP e uma máscara de sub-rede com notação decimal com ponto (por exemplo, '10.0.0.1(255.255.252.0)').<br/>Se nenhum endereço IP for especificado e não houver definição de regra, nenhum endereço IP será permitido. Para permitir qualquer endereço IP, crie uma regra e defina 0.0.0.0/0.<br/>Os endereços IP devem estar em um dos formatos a seguir: endereço IPv4 com 4 números e intervalo de endereços CIDR.
* Ao criar o evento, é possível especificar para iniciá-lo automaticamente. <br/>Quando a inicialização automática é definida como verdadeira, o evento em tempo real será iniciado após a criação. A cobrança começa assim que o Evento ao vivo começa a ser transmitido. É necessário chamar explicitamente o recurso Parar no Evento ao vivo para parar a cobrança adicional. Como alternativa, você poderá iniciar o evento quando estiver pronto para iniciar o streaming. 

    Para saber mais, confira [Estados e cobrança do Evento ao vivo](live-event-states-billing.md).

## <a name="live-event-ingest-urls"></a>URLs de ingestão de Evento ao vivo

Após criar o Evento ao vivo, é possível obter URLs de ingestão que serão fornecidas ao codificador dinâmico local. O codificador dinâmico usa essas URLs para gerar a entrada de um fluxo ao vivo. Para saber mais, confira [Codificadores dinâmicos locais recomendados](recommended-on-premises-live-encoders.md). 

Você pode usar URLs intuitivas ou não intuitivas. 

* URL não intuitiva

    A URL não intuitiva é o modo padrão no AMS v3. Você possivelmente obterá o Evento ao vivo rapidamente, mas a URL de ingestão é conhecida apenas ao iniciar o evento ao vivo. A URL será alterada se você parar/iniciar o Evento ao vivo. <br/>A URL não intuitiva é útil em cenários em que um usuário final deseja fazer uma transmissão usando um aplicativo que deseja obter um evento ao vivo o mais rápido possível e ter uma URL de ingestão dinâmica não é um problema.
* URL intuitiva

    A URL intuitiva é preferida pelas grandes emissoras de mídia, que usam codificadores de transmissão de hardware e não querem reconfigurar seus codificadores ao iniciar o Evento ao vivo. Elas querem uma URL de ingestão preditiva, que não muda com o tempo.

> [!NOTE] 
> Para uma URL de ingestão ser preditiva, você precisa usar o modo "intuitivo" e passar seu próprio token de acesso (para evitar um token aleatório na URL).

### <a name="live-ingest-url-naming-rules"></a>Regras de nomenclatura de URL de ingestão dinâmica

A cadeia de caracteres *aleatória* abaixo é um número hexadecimal de 128 bits composto de 32 caracteres de “0” a “9” e “a” a “f”.<br/>
O *token de acesso* abaixo é o que você precisa especificar para a URL fixa. Ele também é um número hexadecimal de 128 bits.

#### <a name="non-vanity-url"></a>URL não intuitiva

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/<access token>`
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`
`https://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`

#### <a name="vanity-url"></a>URL intuitiva

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/<access token>`
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`

## <a name="live-event-preview-url"></a>URL de visualização do Evento ao vivo

Quando o **Evento ao vivo** começar a receber o feed de contribuição, você pode usar o ponto de extremidade de visualização para visualizar e validar que está recebendo a transmissão ao vivo antes de publicar. Depois de verificar se o fluxo de visualização é bom, você pode usar o Evento ao vivo para disponibilizar a transmissão ao vivo para entrega por meio de um ou mais **Pontos de extremidade de streaming** (pré-criados). Para conseguir isso, crie uma nova [Saída ao vivo](https://docs.microsoft.com/rest/api/media/liveoutputs) no **Evento ao vivo**. 

> [!IMPORTANT]
> Certifique-se de que o vídeo está fluindo para a URL de visualização antes de continuar!

## <a name="live-event-long-running-operations"></a>Operações de longa duração de evento ao vivo

Para obter detalhes, consulte [operações de longa execução](entities-overview.md#long-running-operations)

## <a name="live-outputs"></a>Saídas ao Vivo

Uma vez que o fluxo esteja fluindo para o Evento ao vivo, é possível começar o evento de transmissão criando um [Ativo](https://docs.microsoft.com/rest/api/media/assets), uma [Saída ao vivo](https://docs.microsoft.com/rest/api/media/liveoutputs) e um [Localizador de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators). A Saída ao vivo arquiva o fluxo e o disponibiliza para usuários por meio do [Ponto de extremidade de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

> [!NOTE]
> As Saídas ao Vivo começam na criação e terminam quando são excluídas. Ao excluir a Saída ao Vivo, você não está excluindo o Ativo subjacente nem o conteúdo no ativo. 

A relação entre um **Evento ao Vivo** e a respectiva **Saída ao vivo** é semelhante à difusão de televisão tradicional, em que um canal (**Evento ao Vivo**) representa uma transmissão constante de vídeo e uma gravação (**Saída ao vivo**) tem o escopo definido para um segmento de tempo específico (por exemplo, notícias noturnas das 18h30 às 19h00). Você pode gravar televisão usando um DVR (Gravador de Vídeo Digital) – o recurso equivalente no Evento ao Vivo é gerenciado por meio da propriedade **ArchiveWindowLength**. É uma duração de tempo ISO-8601 (por exemplo, PTHH: MM: SS), que especifica a capacidade do DVR e pode ser definida de um mínimo de 3 minutos a um máximo de 25 horas.

O objeto **Saída ao vivo** é como um gravador que captura e grava a transmissão ao vivo em um Ativo em sua conta dos Serviços de Mídia. O conteúdo gravado será mantido na conta de Armazenamento do Azure anexada à sua conta, no contêiner definido pelo recurso Ativo. Uma **Saída ao vivo** também permite que você controle algumas propriedades da transmissão ao vivo, como quanto do fluxo é mantido na gravação de arquivo (por exemplo, a capacidade do DVR na nuvem) e se os espectadores podem ou não assistir à exibição da transmissão ao vivo. O arquivo no disco é uma "janela" de arquivo circular que contém apenas a quantidade de conteúdo especificada na propriedade **archiveWindowLength** da **Saída ao vivo**. O conteúdo que fica fora dessa janela é automaticamente descartado do contêiner de armazenamento e não é recuperável. Você pode criar várias **Saídas ao vivo** (até três no máximo) em um **Evento ao vivo** com diferentes comprimentos e configurações de arquivamento.  

Caso tenha publicado o **Ativo** da **Saída ao vivo** usando um **Localizador de streaming**, o **Evento ao vivo** (até a duração da janela de DVR) continuará visível até a expiração ou exclusão do Localizador de streaming, o que ocorrer primeiro.

Para saber mais, confira [Usar o DVR na nuvem](live-event-cloud-dvr.md).

## <a name="next-steps"></a>Próximas etapas

[Tutorial de live streaming](stream-live-tutorial-with-api.md)
