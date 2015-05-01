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
	ms.date="03/05/2015" 
	ms.author="juliako"/>

#Codificando conteúdo sob demanda com os Serviços de Mídia do Azure

Este tópico faz parte do [fluxo de trabalho de vídeo sob demanda dos Serviços de Mídia](media-services-video-on-demand-workflow.md).

##Visão geral

Os Serviços de Mídia dão suporte aos seguintes codificadores:

- [Codificador de Mídia do Azure](#azure_media_encoder)
- [Fluxo de trabalho do Media Encoder Premium](#media_encoder_premium_workflow) (visualização pública)

A [seção a seguir](#compare_encoders) compara os recursos de codificação de ambos os codificadores.

Por padrão, cada conta dos Serviços de Mídia pode ter uma tarefa de codificação ativa por vez. Você pode reservar unidades de codificação que permitem ter várias tarefas de codificação em execução simultaneamente, uma para cada unidade reservada de codificação que você comprar. Para obter informações sobre o dimensionamento de unidades de codificação, consulte os tópicos do **Portal** e do **.NET** a seguir.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../includes/media-services-selector-scale-encoding-units.md)]

##<a id="azure_media_encoder"></a>Codificador de Mídia do Azure

[Formatos aos quais o Codificador dos Serviços de Mídia dá suporte](media-services-azure-media-encoder-formats.md) - discute os formatos de arquivo e transmissão aos quais o **Codificador dos Serviços de Mídia** dá suporte.

O **Codificador de Mídia do Azure** é configurado usando uma das cadeias de caracteres predefinidas de codificador descritas [aqui](https://msdn.microsoft.com/library/azure/dn619392.aspx). Você também pode obter os arquivos de predefinição reais do Codificador de Mídia do Azure [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder).

Codificar com o **Codificador de Mídia do Azure** usando **Portal de Gerenciamento**, **.NET** ou **API REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

####Outros tópicos relacionados

[Empacotamento dinâmico](https://msdn.microsoft.com/library/azure/jj889436.aspx) - descreve como codificar como MP4s de taxa de bits adaptável e fornecer dinamicamente Smooth Streaming, Apple HLS ou MPEG-DASH.

[Controlando nomes de arquivo de saída do Codificador dos Serviços de Mídia](https://msdn.microsoft.com/library/azure/dn303341.aspx)- descreve a convenção de nomenclatura de arquivos usada pelo Codificador de Mídia do Azure e como modificar os nomes de arquivos de saída.

[Codificando sua mídia com Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md) - descreve como codificar faixas de áudio usando a codificação Dolby Digital Plus.


##<a id="media_encoder_premium_wokrflow"></a>Fluxo de trabalho do Media Encoder Premium (visualização pública)

**Observação** processador de mídia de fluxo de trabalho do Media Encoder Premium discutido neste tópico, não está disponível na China.

[Formatos com suporte pelo fluxo de trabalho do Media Encoder Premium](media-services-premium-workflow-encoder-formats.md) - discute os formatos de arquivo e codecs com suporte pelo **fluxo de trabalho do Media Encoder Premium**.

O **fluxo de trabalho do Media Encoder Premium** é configurado usando fluxos de trabalho complexos. Arquivos de fluxo de trabalho podem ser criados usando o [Designer de Fluxo de Trabalho](media-services-workflow-designer.md) . 

Você pode obter arquivos de fluxo de trabalho padrão [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A pasta também contém a descrição desses arquivos.

Codificar com o **Fluxo de Trabalho do Media Encoder Premium** usando **.NET**. Para obter mais informações, consulte [Codificação avançada com o Fluxo de Trabalho do Media Encoder Premium](media-services-encode-with-premium-workflow.md).
 

##<a id="compare_encoders"></a>Comparar os codificadores

Esta seção compara os recursos de codificação do **Codificador de Mídia do Azure** e do **Fluxo de Trabalho do Media Encoder Premium**.

###Formatos de entrada

Formatos de arquivo/contêiner de entrada

<table border="1">
<tr><th>Input Container/File Formats</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>Adobe(r) Flash(r) F4V</td><td>Yes</td><td>No</td></tr>
<tr><td>MXF/SMPTE 377M</td><td>Yes</td><td>Limited</td></tr>
<tr><td>GXF</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-2 Transport Streams</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MPEG-2 Program Streams</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MPEG-4/MP4</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Windows Media/ASF</td><td>Yes</td><td>Yes</td></tr>
<tr><td>AVI (Uncompressed 8bit/10bit)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>3GPP/3GPP2</td><td>No</td><td>Yes</td></tr>
<tr><td>Smooth Streaming File Format (PIFF 1.3)</td><td>No</td><td>Yes</td></tr>
</table>

Codecs de vídeo de entrada

<table border="1">
<tr><th>Input Video Codecs</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AVC 8-bit/10-bit, up to 4:2:2, including AVCIntra</td><td>Yes</td><td>Only 8bit 4:2:0</td></tr>
<tr><td>Avid DNxHD (in MXF)</td><td>Yes</td><td>No</td></tr>
<tr><td>DVCPro/DVCProHD (in MXF)</td><td>Yes</td><td>No</td></tr>
<tr><td>JPEG2000</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-2 (up to 422 Profile and High Level; including variants such as XDCAM, XDCAM HD, XDCAM IMX, CableLabs(r) and D10)</td><td>Yes</td><td>Up to 422 Profile</td></tr>
<tr><td>MPEG-1</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Canopus HQ/HQX</td><td>No</td><td>Yes</td></tr>
</table>

Codecs de áudio de entrada

<table border="1">
<tr><th>Input Audio Codecs</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AES (SMPTE 331M and 302M, AES3-2003)</td><td>Yes</td><td>No</td></tr>
<tr><td>Dolby(r) E</td><td>Yes</td><td>No</td></tr>
<tr><td>Dolby(r) Digital (AC3)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Dolby(r) Digital Plus (E-AC3)</td><td>Yes</td><td>No</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE, and AAC-HEv2; up to 5.1)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MPEG Layer 2</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Windows Media Audio</td><td>Yes</td><td>Yes</td></tr>
<tr><td>WAV/PCM</td><td>Yes</td><td>Yes</td></tr>
</table>

###Formatos de saída

Formatos de contêiner/arquivo de saída

<table border="1">
<tr><th>Output Container/File Formats</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>Adobe(r) Flash(r) F4V</td><td>Yes</td><td>No</td></tr>
<tr><td>MXF (OP1a, XDCAM and AS02)</td><td>Yes</td><td>No</td></tr>
<tr><td>DPP (including AS11)</td><td>Yes</td><td>No</td></tr>
<tr><td>GXF</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-4/MP4</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Windows Media/ASF</td><td>Yes</td><td>Yes</td></tr>
<tr><td>AVI (Uncompressed 8bit/10bit)</td><td>Yes</td><td>No</td></tr>
<tr><td>Smooth Streaming File Format (PIFF 1.3)</td><td>Yes</td><td>Yes</td></tr>
</table>

Codecs de vídeo de saída

<table border="1">
<tr><th>Output Video Codecs</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AVC (H.264; 8-bit; up to High Profile, Level 5.2; 4K Ultra HD; AVC Intra)</td><td>Yes</td><td>Only 8bit 4:2:0 up to 1080p</td></tr>
<tr><td>Avid DNxHD (in MXF)</td><td>Yes</td><td>No</td></tr>
<tr><td>DVCPro/DVCProHD (in MXF)</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-2 (up to 422 Profile and High Level; including variants such as XDCAM, XDCAM HD, XDCAM IMX, CableLabs(r) and D10)</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-1</td><td>Yes</td><td>No</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Yes</td><td>Yes</td></tr>
<tr><td>JPEG thumbnail creation</td><td>Yes</td><td>Yes</td></tr>
</table>

Codecs de áudio de saída

<table border="1">
<tr><th>Output Audio Codecs</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AES (SMPTE 331M and 302M, AES3-2003)</td><td>Yes</td><td>No</td></tr>
<tr><td>Dolby(r) Digital (AC3)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Dolby(r) Digital Plus (E-AC3) up to 7.1</td><td>Yes</td><td>Up to 5.1</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE, and AAC-HEv2; up to 5.1)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MPEG Layer 2</td><td>Yes</td><td>No</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Yes</td><td>No</td></tr>
<tr><td>Windows Media Audio</td><td>Yes</td><td>Yes</td></tr>
</table>
##Artigos relacionados

- [Apresentando a codificação Premium nos Serviços de Mídia do Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Como usar a codificação Premium nos Serviços de Mídia do Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [Cotas e limitações](media-services-quotas-and-limitations.md)



<!--HONumber=52-->