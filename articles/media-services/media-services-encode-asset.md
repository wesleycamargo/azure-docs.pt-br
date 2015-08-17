<properties 
	pageTitle="Codificando conteúdo sob demanda com os Serviços de Mídia do Azure" 
	description="Este tópico fornece uma visão geral da codificação de conteúdo sob demanda com os Serviços de Mídia." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="juliako"/>

#Codificando conteúdo sob demanda com os Serviços de Mídia do Azure

Este tópico faz parte do [Fluxo de trabalho de vídeo sob demanda dos Serviços de Mídia](media-services-video-on-demand-workflow.md)

##Visão geral

Os Serviços de Mídia dão suporte aos seguintes codificadores:

- [Media Encoder Standard](#media_encoder_standard)
- [Codificador de Mídia do Azure](#azure_media_encoder)
- [Fluxo de trabalho do Media Encoder Premium](#media_encoder_premium_workflow)

A [seção a seguir](#compare_encoders) compara os recursos de codificação dos codificadores com suporte.

Por padrão, cada conta dos Serviços de Mídia pode ter uma tarefa de codificação ativa por vez. Você pode reservar unidades de codificação que permitem ter várias tarefas de codificação em execução simultaneamente, uma para cada unidade reservada de codificação que você comprar. Para saber mais sobre o dimensionamento de unidades de codificação, consulte os tópicos **Portal** e **.NET** a seguir.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

##<a id="media_encoder_standard"></a>Media Encoder Standard

[Formatos com suporte do Media Encoder Standard](media-services-media-encoder-standard-formats.md): discute os formatos de arquivo e transmissão aos quais o **Media Encoder Standard** dá suporte.

O **Media Encoder Standard** é configurado usando uma das predefinições do codificador descritas [aqui](http://go.microsoft.com/fwlink/?LinkId=618336) ou predefinições personalizadas baseadas [nessas](http://go.microsoft.com/fwlink/?LinkId=618336) predefinições.

Para saber mais, confira [este blog](http://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).

##<a id="azure_media_encoder"></a>Codificador de Mídia do Azure

[Formatos aos quais o Codificador dos Serviços de Mídia dá suporte](media-services-azure-media-encoder-formats.md): discute os formatos de arquivo e transmissão aos quais o **Codificador de Mídia do Azure** dá suporte.

O **Codificador de Mídia do Azure** é configurado usando uma das cadeias de caracteres predefinidas de codificador descritas [aqui](https://msdn.microsoft.com/library/azure/dn619392.aspx). Você também pode obter os arquivos de predefinição reais do Codificador de Mídia do Azure [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder).

Codifique-os com o **Codificador de Mídia do Azure** usando o **Portal de Gerenciamento**, o **.NET** ou **API REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####Outros tópicos relacionados

[Empacotamento dinâmico](https://msdn.microsoft.com/library/azure/jj889436.aspx): descreve como codificar como MP4s de taxa de bits adaptável e fornecer dinamicamente Smooth Streaming, Apple HLS ou MPEG-DASH.

[Controlando nomes de arquivo de saída do Codificador dos Serviços de Mídia](https://msdn.microsoft.com/library/azure/dn303341.aspx): descreve a convenção de nomenclatura de arquivos usada pelo Codificador de Mídia do Azure e como modificar os nomes de arquivos de saída.

[Codificando sua mídia com Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md): descreve como codificar faixas de áudio usando a codificação Dolby Digital Plus.


##<a id="media_encoder_premium_wokrflow"></a>Fluxo de trabalho do Codificador de Mídia Premium 

**Observação** o processador de mídia do Fluxo de trabalho do Media Encoder Premium discutido neste tópico não está disponível na China.

[Formatos com suporte pelo Fluxo de trabalho do Media Encoder Premium](media-services-premium-workflow-encoder-formats.md): discute os formatos de arquivo e codecs com suporte do **Fluxo de trabalho do Media Encoder Premium**.

O **fluxo de trabalho do Media Encoder Premium** é configurado usando fluxos de trabalho complexos. Os arquivos de fluxo de trabalho podem ser criados usando a ferramenta [Designer de Fluxo de Trabalho](media-services-workflow-designer.md).

Você pode obter arquivos de fluxo de trabalho padrão [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A pasta também contém a descrição desses arquivos.

Codificar com o **Fluxo de Trabalho do Media Encoder Premium** usando **.NET**. Para saber mais, confira [Codificação avançada com fluxo de trabalho do Media Encoder Premium](media-services-encode-with-premium-workflow.md).
 

##<a id="compare_encoders"></a>Comparar os codificadores

###<a id="billing"></a>Medidor de cobrança usado por cada codificador

Nome do processador de mídia|Preços aplicáveis|Observações
---|---|---
**Codificador de Mídia do Microsoft Azure** |CODIFICADOR HERDADO|As tarefas de codificação serão cobradas de acordo com a soma dos tamanhos de ativos de entrada e saída, em GB, a uma taxa especificada [aqui][1], abaixo da coluna CODIFICADOR HERDADO.
**Codificador de Mídia do Azure** |CODIFICADOR|As tarefas de codificação serão cobradas de acordo com o tamanho do ativo de saída, em GB, na taxa especificada [aqui][1], abaixo da coluna CODIFICADOR.
**Media Encoder Standard** |CODIFICADOR|As tarefas de codificação serão cobradas de acordo com o tamanho do ativo de saída, em GB, na taxa especificada [aqui][1], abaixo da coluna CODIFICADOR.
**Fluxo de trabalho do Media Encoder Premium** |CODIFICADOR PREMIUM|As tarefas de codificação serão cobradas de acordo com o tamanho do ativo de saída, em GB, na taxa especificada [aqui][1] abaixo da coluna CODIFICADOR PREMIUM.



Esta seção compara os recursos de codificação do **Codificador de Mídia do Azure**, do **Fluxo de Trabalho do Media Encoder Premium** e do **Media Encoder Standard**.


###Formatos de entrada

Formatos de arquivo/contêiner de entrada

Formatos de arquivo/contêiner de entrada|Fluxo de trabalho do Media Encoder Premium|Codificador de Mídia do Azure|Media Encoder Standard
---|---|---|---
Adobe® Flash® F4V|Sim|Não|Sim
MXF/SMPTE 377M|Sim|Limitado|Sim
GXF|Sim|Não|Sim
Fluxos de transporte de MPEG-2|Sim|Sim|Sim
Fluxos de programa MPEG-2|Sim|Sim|Sim
MPEG-4/MP4|Sim|Sim|Sim
Windows Media/ASF|Sim|Sim|Sim
AVI (8 bits/10 bits descompactado)|Sim|Sim|Sim
3GPP/3GPP2|Não|Sim|Sim
Formato de arquivo do Smooth Streaming (PIFF 1.3)|Não|Sim|Sim
[Gravação (DVR-MS) de vídeo Digital da Microsoft](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Não|Não|Sim
Matroska/WebM|Não|Não|Sim

Codecs de vídeo de entrada

Codecs de vídeo de entrada|Fluxo de trabalho do Media Encoder Premium|Codificador de Mídia do Azure|Media Encoder Standard
---|---|---|---
AVC de 8 bits/10 bits até 4:2:2, incluindo AVCIntra|Sim|Apenas 8 bits 4:2:0|8 bits 4:2:0 e 4:2:2
DNxHD ávido (em MXF)|Sim|Não|Sim
DVCPro/DVCProHD (em MXF)|Sim|Não|Sim
JPEG2000|Sim|Não|Sim
MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10)|Sim|Até perfil 422|Até perfil 422
MPEG-1|Sim|Sim|Sim
Windows Media Video/VC-1|Sim|Sim|Sim
Canopus HQ/HQX|Não|Sim|Não
MPEG-4, parte 2|Não|Não|Sim
[Theora](https://en.wikipedia.org/wiki/Theora)|Não|Não|Sim

Codecs de áudio de entrada

Codecs de áudio de entrada|Fluxo de trabalho do Media Encoder Premium|Codificador de Mídia do Azure|Media Encoder Standard
---|---|---|---
AES (SMPTE 331M e 302M, AES3-2003)|Sim|Não|Não
Dolby® E|Sim|Não|Não
Dolby® Digital (AC3)|Sim|Sim|Não
Dolby® Digital Plus (E-AC3)|Sim|Não|Não
AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)|Sim|Sim|Sim
MPEG Layer 2|Sim|Sim|Sim
MP3 (MPEG-1 Audio Layer 3)|Sim|Sim|Sim
Áudio do Windows Media|Sim|Sim|Sim
WAV/PCM|Sim|Sim|Sim
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Não|Não|Sim
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Não|Não|Sim
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Não|Não|Sim

###Formatos de saída

Formatos de contêiner/arquivo de saída

Formatos de contêiner/arquivo de saída|Fluxo de trabalho do Media Encoder Premium|Codificador de Mídia do Azure|Media Encoder Standard
---|---|---|---
Adobe® Flash® F4V|Sim|Não|Não
MXF (OP1a, XDCAM e AS02)|Sim|Não|Não
DPP (incluindo AS11)|Sim|Não|Não
GXF|Sim|Não|Não
MPEG-4/MP4|Sim|Sim|Sim
MPEG-TS|Sim|Não|Sim
Windows Media/ASF|Sim|Sim|Não
AVI (8 bits/10 bits descompactado)|Sim|Não|Não
Formato de arquivo do Smooth Streaming (PIFF 1.3)|Sim|Sim|Não

Codecs de vídeo de saída

Codecs de vídeo de saída|Fluxo de trabalho do Media Encoder Premium|Codificador de Mídia do Azure|Media Encoder Standard
---|---|---|---
AVC (H. 264; 8 bits; até perfil, nível elevado 5.2; 4K Ultra HD; Intra AVC)|Sim|Apenas 8 bits 4:2:0 até 1080p|Somente 8 bits 4:2:0
DNxHD ávido (em MXF)|Sim|Não|Não
DVCPro/DVCProHD (em MXF)|Sim|Não|Não
MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10)|Sim|Não|Não
MPEG-1|Sim|Não|Não
Windows Media Video/VC-1|Sim|Sim|Não
Criação de miniaturas JPEG|Sim|Sim|Não

Codecs de áudio de saída

Codecs de áudio de saída|Fluxo de trabalho do Media Encoder Premium|Codificador de Mídia do Azure|Media Encoder Standard
---|---|---|---
AES (SMPTE 331M e 302M, AES3-2003)|Sim|Não|Não
Dolby® Digital (AC3)|Sim|Sim|Não
Dolby ® Digital Plus (E-AC3) até 7.1|Sim|Até 5.1|Não
AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)|Sim|Sim|Sim
MPEG Layer 2|Sim|Não|Não
MP3 (MPEG-1 Audio Layer 3)|Sim|Não|Não
Áudio do Windows Media|Sim|Sim|Não

##Artigos relacionados

- [Apresentando a codificação Premium nos Serviços de Mídia do Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Como usar a codificação Premium nos Serviços de Mídia do Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [Cotas e limitações](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=06-->