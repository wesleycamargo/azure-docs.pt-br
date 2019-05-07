---
title: Visão geral do empacotamento dinâmico dos Serviços de Mídia do Azure | Microsoft Docs
description: O tópico apresenta uma visão geral do empacotamento dinâmico nos Serviços de Mídia.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2019
ms.author: juliako
ms.openlocfilehash: 02c3fb309755964f1a4b196189b7742fa3f91a9a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148221"
---
# <a name="dynamic-packaging"></a>Empacotamento dinâmico

Os Serviços de Mídia do Microsoft Azure podem ser usados para fornecer vários formatos de arquivos de mídia de origem, formatos de streaming de mídia e formatos de proteção de conteúdo para diversas tecnologias de cliente (por exemplo, iOS e XBOX). Esses clientes entendem protocolos diferentes, por exemplo, o iOS requer um formato HTTP Live Streaming (HLS), enquanto o Xbox requer Smooth Streaming. Se você tiver um conjunto de taxa de bits adaptável (múltiplas taxas de bits) MP4 arquivos (mídia de Base ISO 14496-12) ou um conjunto de arquivos Smooth Streaming de taxa de bits adaptável que você deseja fornecer a clientes que entendam HLS, MPEG DASH ou Smooth Streaming, você pode tirar proveito de  **Empacotamento dinâmico**. O empacotamento é independente de resolução de vídeo, SD/HD/UHD - 4K têm suporte.

Nos serviços de mídia, uma [ponto de extremidade de Streaming](streaming-endpoint-concept.md) representa um dinâmico (just-in-time) empacotamento e a origem serviço que pode entregar seu conteúdo ao vivo e sob demanda diretamente a um aplicativo de player de cliente, usando um dos streaming comum protocolos de mídia (HLS ou DASH). Empacotamento dinâmico é um recurso que vem por padrão em todos os **pontos de extremidade de Streaming** (Standard ou Premium). 

Para aproveitar **empacotamento dinâmico**, você precisa ter uma **ativo** com um conjunto de arquivos MP4 de taxa de bits adaptável e streaming de arquivos de configuração necessários para o empacotamento dinâmico de serviços de mídia. Uma maneira de obter os arquivos é codificar seu arquivo mezanino (fonte) com os Serviços de Mídia. Para disponibilizar vídeos no ativo codificado para clientes de reprodução, você precisa criar uma **localizador de Streaming** e criar URLs de streaming. Em seguida, com base no formato especificado no manifesto do cliente streaming (HLS, DASH ou Smooth), você receba o fluxo no protocolo escolhido por você.

Como resultado você só precisa armazenar e pagar pelos arquivos em um único formato de armazenamento, e os Serviços de Mídia vão criar e fornecer a resposta apropriada com base nas solicitações de um cliente. 

Serviços de mídia, o empacotamento dinâmico é usado se você estiver transmitindo ao vivo ou sob demanda. 

## <a name="common-on-demand-workflow"></a>Fluxo de trabalho comum e sob demanda

A seguir é comum dos serviços de mídia fluxo de trabalho de streaming em que o empacotamento dinâmico é usado.

1. Carrega um arquivo de entrada (chamado de arquivo de mezanino). Por exemplo, MP4, MOV ou MXF (para obter a lista de formatos com suporte, consulte [formatos suportados pelo Media Encoder Standard](media-encoder-standard-formats.md).
2. Codifique o arquivo de mezanino para conjuntos de taxa de bits adaptável MP4 H.264.
3. Publicar o ativo que contém o conjunto MP4 de taxa de bits adaptável. Publicar com a criação de um **localizador de Streaming**.
4. Crie URLs diferentes formatos (HLS, Dash e Smooth Streaming) de destino. O **ponto de extremidade de Streaming** seria cuidar de atender o manifesto correto e as solicitações de todos esses formatos diferentes.

O diagrama a seguir mostra o streaming sob demanda com o fluxo de trabalho de empacotamento dinâmico.

![Empacotamento dinâmico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Codificar como MP4s de taxa de bits adaptável

Para obter informações sobre [como codificar um vídeo com os serviços de mídia](encoding-concept.md), consulte os exemplos a seguir:

* [Codificar a partir de uma URL HTTPS usando predefinições](job-input-from-http-how-to.md)
* [Codificar um arquivo local usando os valores predefinidos internos](job-input-from-local-file-how-to.md)
* [Criar uma predefinição para seus requisitos específicos de cenário ou dispositivo de destino personalizada](customize-encoder-presets-how-to.md)

Para obter uma lista dos codecs e formatos do Media Encoder Standard, consulte [codecs e formatos](media-encoder-standard-formats.md)

## <a name="common-live-streaming-workflow"></a>Live streaming fluxo de trabalho comum

Aqui estão as etapas para um fluxo de trabalho de streaming ao vivo:

1. Crie um [evento ao vivo](live-events-outputs-concept.md).
1. Obtenha as URLs de ingestão e configure seu codificador local para usar a URL para enviar a feed de contribuição.
1. Obtenha a URL de visualização e use-a para verificar se a entrada do codificador está sendo realmente recebida.
1. Criar um novo **ativo**.
1. Crie um **LiveOutput** e use o nome do ativo que você criou.<br/>A **Saída Dinâmica** arquivará o fluxo no **Ativo**.
1. Crie um **Localizador de Streaming** com os tipos internos da **Política de Streaming**.<br/>Se você pretende criptografar seu conteúdo, reveja a [Visão geral da proteção de conteúdo](content-protection-overview.md).
1. Liste os caminhos no **Localizador de Streaming** para retornar as URLs a serem usadas.
1. Obtenha o nome do host para o **ponto de extremidade de streaming** de onde você deseja transmitir.
1. Crie URLs diferentes formatos (HLS, Dash e Smooth Streaming) de destino. O **ponto de extremidade de Streaming** seria cuidar de atender o manifesto correto e as solicitações de todos esses formatos diferentes.

Um evento ao vivo pode ser um dos dois tipos: codificação ao vivo e passagem. Para obter detalhes sobre a transmissão ao vivo nos serviços de mídia v3, consulte [visão geral de transmissão ao vivo](live-streaming-overview.md).

O diagrama a seguir mostra a transmissão ao vivo com o fluxo de trabalho de empacotamento dinâmico.

![passagem](./media/live-streaming/pass-through.svg)

## <a name="delivery-protocols"></a>Protocolos de fornecimento

|Protocol|Exemplo|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Codecs de vídeo com suporte pelo empacotamento dinâmico

Empacotamento dinâmico oferece suporte a arquivos MP4 que contêm vídeo codificado com [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC ou AVC1) [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 ou hvc1).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Codecs de áudio suportados por embalagem dinâmica

Empacotamento dinâmico oferece suporte a arquivos MP4, que contém áudio codificado com [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(AC-3 melhorado ou E-AC3), Dolby Atmos, ou [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, LBR DTS, DTS HD, HD de DTS sem perdas). Streaming de conteúdo Dolby Atmos tem suporte para padrões como MP4 fragmentado de protocolo de MPEG-DASH com o formato comum de Streaming (CSF) ou o formato de aplicativo comum de mídia (CMAF) e por meio de Live Streaming HLS (HTTP) com CMAF.

> [!NOTE]
> O Dynamic Packaging não suporta arquivos que contenham áudio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (é um codec herdado).

## <a name="dynamic-encryption"></a>Criptografia dinâmica

**Criptografia dinâmica** permite que você criptografe dinamicamente seu conteúdo ao vivo ou sob demanda com o AES-128 ou qualquer um dos sistemas DRM (gerenciamento) três direitos digitais principais: Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados. Para obter mais informações, consulte [criptografia dinâmica](content-protection-overview.md).

## <a name="manifests"></a>Manifestos 
 
Os serviços de mídia dá suporte a HLS, MPEG DASH, Smooth Streaming protocolos. Como parte da **empacotamento dinâmico**, os manifestos do cliente streaming (Playlist do mestre de HLS, DASH MPD Media Presentation Description () e Smooth Streaming) são gerados dinamicamente com base no seletor de formato na URL. Consulte os protocolos de entrega no [esta seção](#delivery-protocols). 

Um arquivo de manifesto inclui o streaming de metadados, como: controlar o tipo (áudio, vídeo ou texto), acompanhe o nome, hora de início e término, taxa de bits (qualidades), idiomas da trilha, janela de apresentação (janela deslizante de duração fixa), codec de vídeo (FourCC). Também instrui o player a recuperar o próximo fragmento, fornecendo informações sobre os próximos fragmentos de vídeo executáveis disponíveis e sua localização. Fragmentos (ou segmentos) são os "pedaços" reais de um conteúdo de vídeo.

### <a name="hls-master-playlist"></a>Lista de reprodução HLS mestre

Aqui está um exemplo de um arquivo de manifesto HLS: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="dash-media-presentation-description-mpd"></a>Descrição de apresentação de mídia DASH (MPD)

Aqui está um exemplo de um manifesto DASH:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
### <a name="smooth-streaming"></a>Smooth Streaming

Aqui está um exemplo de um manifesto de Smooth Streaming:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

## <a name="dynamic-manifest"></a>Manifesto dinâmico

Filtragem dinâmica é usado para controlar o número de faixas, formatos, taxas de bits e janelas de tempo de apresentação que são enviadas para os jogadores. Para obter mais informações, consulte [filtros e manifestos dinâmicos](filters-dynamic-manifest-overview.md).

> [!NOTE]
> Atualmente, você não pode usar o portal do Azure para gerenciar recursos da v3. Use a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](developers-guide.md) com suporte.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, comentários, obtenha atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

[Carregar, codificar, transmitir vídeos](stream-files-tutorial-with-api.md)

