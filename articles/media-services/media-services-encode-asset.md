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
	ms.date="07/16/2015" 
	ms.author="juliako"/>

#Codificando conteúdo sob demanda com os Serviços de Mídia do Azure

Este tópico faz parte do [Fluxo de trabalho de vídeo sob demanda dos Serviços de Mídia](media-services-video-on-demand-workflow.md)

##Visão geral

Os Serviços de Mídia dão suporte aos seguintes codificadores:

- [Media Encoder Standard](#media_encoder_standard)
- [Codificador de Mídia do Azure](#azure_media_encoder)
- [Fluxo de trabalho do Media Encoder Premium](#media_encoder_premium_workflow) (visualização pública)

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


##<a id="media_encoder_premium_wokrflow"></a>Fluxo de trabalho do Media Encoder Premium (visualização)

**Observação** o processador de mídia do Fluxo de trabalho do Media Encoder Premium discutido neste tópico não está disponível na China.

[Formatos com suporte pelo Fluxo de trabalho do Media Encoder Premium](media-services-premium-workflow-encoder-formats.md): discute os formatos de arquivo e codecs com suporte do **Fluxo de trabalho do Media Encoder Premium**.

O **fluxo de trabalho do Media Encoder Premium** é configurado usando fluxos de trabalho complexos. Os arquivos de fluxo de trabalho podem ser criados usando a ferramenta [Designer de Fluxo de Trabalho](media-services-workflow-designer.md).

Você pode obter arquivos de fluxo de trabalho padrão [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A pasta também contém a descrição desses arquivos.

Codificar com o **Fluxo de Trabalho do Media Encoder Premium** usando **.NET**. Para saber mais, confira [Codificação avançada com fluxo de trabalho do Media Encoder Premium](media-services-encode-with-premium-workflow.md).
 

##<a id="compare_encoders"></a>Comparar os codificadores

###<a id="billing"></a>Medidor de cobrança usado por cada codificador

<table border="1">
<tr><th>Nome do processador de mídia</th><th>Preços aplicáveis</th><th>Observações</th></tr>
<tr><td><b>Codificador de Mídia do Microsoft Azure</b></td><td>CODIFICADOR HERDADO</td><td>As tarefas de codificação serão cobradas de acordo com a soma dos tamanhos de ativos de entrada e saída, em GB, a uma taxa especificada <a href="http://azure.microsoft.com/en-us/pricing/details/media-services/">aqui</a>, abaixo da coluna CODIFICADOR HERDADO.</td></tr>
<tr><td><b>Codificador de Mídia do Azure</b></td><td>CODIFICADOR</td><td>As tarefas de codificação serão cobradas de acordo com o tamanho do ativo de saída, em GB, na taxa especificada <a href="http://azure.microsoft.com/en-us/pricing/details/media-services/">aqui</a>, abaixo da coluna CODIFICADOR.</td></tr>
<tr><td><b>Media Encoder Standard</b></td><td>CODIFICADOR</td><td>As tarefas de codificação serão cobradas de acordo com o tamanho do ativo de saída, em GB, na taxa especificada <a href="http://azure.microsoft.com/en-us/pricing/details/media-services/">aqui</a>, abaixo da coluna CODIFICADOR.</td></tr>
<tr><td><b>Fluxo de trabalho do Media Encoder Premium</b></td><td>CODIFICADOR PREMIUM</td><td>As tarefas de codificação serão cobradas de acordo com o tamanho do ativo de saída, em GB, na taxa especificada <a href="http://azure.microsoft.com/en-us/pricing/details/media-services/">aqui</a> abaixo da coluna CODIFICADOR PREMIUM.</td></tr>
</table>


Esta seção compara os recursos de codificação do **Codificador de Mídia do Azure**, do **Fluxo de Trabalho do Media Encoder Premium** e do **Media Encoder Standard**.


###Formatos de entrada

Formatos de arquivo/contêiner de entrada

<table border="1">
<tr><th>Formatos de arquivo/contêiner de entrada</th><th>Fluxo de trabalho do Media Encoder Premium</th><th>Codificador de Mídia do Azure
</th><th>Media Encoder Standard</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>Sim</td><td>Não</td><td>Sim</td></tr>
<tr><td>MXF/SMPTE 377M</td><td>Sim</td><td>Limitado</td><td>Sim</td></tr>
<tr><td>GXF</td><td>Sim</td><td>Não</td><td>Sim</td></tr>
<tr><td>Fluxos de transporte de MPEG-2</td><td>Sim</td><td>Sim</td><td>Sim</td></tr>
<tr><td>Fluxos de programa MPEG-2</td><td>Sim</td><td>Sim</td><td>Sim</td></tr>
<tr><td>MPEG-4/MP4</td><td>Sim</td><td>Sim</td><td>Sim</td></tr>
<tr><td>Windows Media/ASF</td><td>Sim</td><td>Sim</td><td>Sim</td></tr>
<tr><td>AVI (8 bits/10 bits descompactado)</td><td>Sim</td><td>Sim</td><td>Sim</td></tr>
<tr><td>3GPP/3GPP2</td><td>Não</td><td>Sim</td><td>Sim</td></tr>
<tr><td>Formato de arquivo do Smooth Streaming (PIFF 1.3)</td><td>Não</td><td>Sim</td><td>Sim</td></tr>
<tr><td><a href="https://msdn.microsoft.com/en-us/library/windows/desktop/dd692984(v=vs.85).aspx">Gravação (DVR-MS) de vídeo Digital da Microsoft</a></td><td>Não</td><td>Não</td><td>Sim</td></tr>
<tr><td>Matroska/WebM</td><td>Não</td><td>Não</td><td>Sim</td></tr></table>

Codecs de vídeo de entrada

<table border="1">
<tr><th>Codecs de vídeo de entrada</th><th>Fluxo de trabalho do Media Encoder Premium</th><th>Codificador de Mídia do Azure</th><th>Media Encoder Standard</th></tr>
<tr><td>AVC de 8 bits/10 bits até 4:2:2, incluindo AVCIntra</td><td>Sim</td><td>Apenas 8 bits 4:2:0</td><td>8 bits 4:2:0 e 4:2:2</td></tr>
<tr><td>DNxHD ávido (em MXF)</td><td>Sim</td><td>Não</td><td>Sim</td></tr>
<tr><td>DVCPro/DVCProHD (em MXF)</td><td>Sim</td><td>Não</td><td>Sim</td></tr>
<tr><td>JPEG2000</td><td>Sim</td><td>Não</td><td>Sim</td></tr>
<tr><td>MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10)</td><td>Sim</td><td>Até perfil 422</td><td>Até perfil 422</td></tr>
<tr><td>MPEG-1</td><td>Sim</td><td>Sim</td><td>Sim</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Sim</td><td>Sim</td><td>Sim</td></tr>
<tr><td>Canopus HQ/HQX</td><td>Não</td><td>Sim</td><td>Não</td></tr>
<tr><td>MPEG-4, parte 2</td><td>Não</td><td>Não</td><td>Sim</td></tr>
<tr><td><a href="https://en.wikipedia.org/wiki/Theora">Theora</a></td><td>Não</td><td>Não</td><td>Sim</td></tr>
</table>

Codecs de áudio de entrada

<table border="1">
<tr><th>Codecs de áudio de entrada</th><th>Fluxo de trabalho do Media Encoder Premium</th><th>Codificador de Mídia do Azure</th><th>Media Encoder Standard</th></tr>
<tr><td>AES (SMPTE 331M e 302M, AES3-2003)</td><td>Sim</td><td>Não</td><td>Não</td></tr>
<tr><td>Dolby® E</td><td>Sim</td><td>Não</td><td>Não</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>Sim</td><td>Sim</td><td>Não</td></tr>
<tr><td>Dolby® Digital Plus (E-AC3)</td><td>Sim</td><td>Não</td><td>Não</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)</td><td>Sim</td><td>Sim</td><td>Sim</td></tr>
<tr><td>MPEG Layer 2</td><td>Sim</td><td>Sim</td><td>Sim</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Sim</td><td>Sim</td><td>Sim</td></tr>
<tr><td>Áudio do Windows Media</td><td>Sim</td><td>Sim</td><td>Sim</td></tr>
<tr><td>WAV/PCM</td><td>Sim</td><td>Sim</td><td>Sim</td></tr>
<tr><td><a href="https://en.wikipedia.org/wiki/FLAC">FLAC</a></td><td>Não</td><td>Não</td><td>Sim</td></tr>
<tr><td><a href="https://en.wikipedia.org/wiki/Opus_(audio_format)">Opus</a></td><td>Não</td><td>Não</td><td>Sim</td></tr>
<tr><td><a href="https://en.wikipedia.org/wiki/Vorbis">Vorbis</a></td><td>Não</td><td>Não</td><td>Sim</td></tr>
</table>

###Formatos de saída

Formatos de contêiner/arquivo de saída

<table border="1">
<tr><th>Formatos de contêiner/arquivo de saída</th><th>Fluxo de trabalho do Media Encoder Premium</th><th>Codificador de Mídia do Azure</th><th>Media Encoder Standard</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>Sim</td><td>Não</td><td>Não</td></tr>
<tr><td>MXF (OP1a, XDCAM e AS02)</td><td>Sim</td><td>Não</td><td>Não</td></tr>
<tr><td>DPP (incluindo AS11)</td><td>Sim</td><td>Não</td><td>Não</td></tr>
<tr><td>GXF</td><td>Sim</td><td>Não</td><td>Não</td></tr>
<tr><td>MPEG-4/MP4</td><td>Sim</td><td>Sim</td><td>Sim</td></tr>
<tr><td>MPEG-TS</td><td>Sim</td><td>Não</td><td>Sim</td></tr>
<tr><td>Windows Media/ASF</td><td>Sim</td><td>Sim</td><td>Não</td></tr>
<tr><td>AVI (8 bits/10 bits descompactado)</td><td>Sim</td><td>Não</td><td>Não</td></tr>
<tr><td>Formato de arquivo do Smooth Streaming (PIFF 1.3)</td><td>Sim</td><td>Sim</td><td>Não</td></tr>
</table>

Codecs de vídeo de saída

<table border="1">
<tr><th>Codecs de vídeo de saída</th><th>Fluxo de trabalho do Media Encoder Premium</th><th>Codificador de Mídia do Azure</th><th>Media Encoder Standard</th></tr>
<tr><td>AVC (H. 264; 8 bits; até perfil, nível elevado 5.2; 4K Ultra HD; Intra AVC)</td><td>Sim</td><td>Apenas 8 bits 4:2:0 até 1080p</td><td>Somente 8 bits 4:2:0</td></tr>
<tr><td>DNxHD ávido (em MXF)</td><td>Sim</td><td>Não</td><td>Não</td></tr>
<tr><td>DVCPro/DVCProHD (em MXF)</td><td>Sim</td><td>Não</td><td>Não</td></tr>
<tr><td>MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10)</td><td>Sim</td><td>Não</td><td>Não</td></tr>
<tr><td>MPEG-1</td><td>Sim</td><td>Não</td><td>Não</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Sim</td><td>Sim</td><td>Não</td></tr>
<tr><td>Criação de miniaturas JPEG</td><td>Sim</td><td>Sim</td><td>Não</td></tr>
</table>

Codecs de áudio de saída

<table border="1">
<tr><th>Codecs de áudio de saída</th><th>Fluxo de trabalho do Media Encoder Premium</th><th>Codificador de Mídia do Azure</th><th>Media Encoder Standard</th></tr>
<tr><td>AES (SMPTE 331M e 302M, AES3-2003)</td><td>Sim</td><td>Não</td><td>Não</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>Sim</td><td>Sim</td><td>Não</td></tr>
<tr><td>Dolby ® Digital Plus (E-AC3) até 7.1</td><td>Sim</td><td>Até 5.1</td><td>Não</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)</td><td>Sim</td><td>Sim</td><td>Sim</td></tr>
<tr><td>MPEG Layer 2</td><td>Sim</td><td>Não</td><td>Não</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Sim</td><td>Não</td><td>Não</td></tr>
<tr><td>Áudio do Windows Media</td><td>Sim</td><td>Sim&lt;/td<td>Não</td></tr>
</table>

##Artigos relacionados

- [Apresentando a codificação Premium nos Serviços de Mídia do Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Como usar a codificação Premium nos Serviços de Mídia do Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [Cotas e limitações](media-services-quotas-and-limitations.md)

 

<!---HONumber=July15_HO4-->