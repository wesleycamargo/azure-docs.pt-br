<properties 
	pageTitle="Codecs e formatos padrão do Codificador de Mídia" 
	description="Este tópico fornece uma visão geral dos codecs e formatos do Codificador de Mídia do Azure." 
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
	ms.date="07/08/2015" 
	ms.author="juliako"/>

#Codecs e formatos padrão do codificador de mídia


Este documento contém uma lista dos formatos de arquivo de importação e importação mais comuns que você pode usar com o Codificador de Mídia padrão.


[Formatos de Importação do Codificador de Mídia ](#import_formats)

[Formatos de Exportação do Codificador de Mídia](#export_formats)


##<a id="import_formats"></a>Formatos de Importação do Codificador de Mídia 

A seção a seguir lista os codecs e formatos de arquivo com suporte para importação.


##Codecs de vídeo:

- MPEG-2
- H.264
- YUV420 descompactado, ou mezzanine
- DNxHD
- VC-1/WMV9
- MPEG-4, parte 2
- JPEG 2000
- Theora

###Codecs de áudio

- PCM
- AAC (AAC-LC, AAC-HE e AAC-HEv2)
- WMA9/Pro
- MP3 (MPEG-1 Audio Layer 3)
- FLAC
- Opus
- Vorbis
 
###Formatos

<table border="1">
<tr><th>Formato de arquivo</th><th>Extensões de arquivo</th></tr>
<tr><td>FLV (com H.264 e codecs AAC) </td><td>.flv</td></tr>
<tr><td>MP4/ISMV</td><td>*.ismv</td></tr>
<tr><td>MPEG2-PS, MPEG2-TS, 3GP</td><td>.ts, .ps, .3gp</td></tr>
<tr><td>MXF</td><td>.mxf</td></tr>
<tr><td>WMV/ASF</td><td>.mwv, .asf</td></tr>
<tr><td>DVR-MS</td><td>.dvr-ms </td></tr>
<tr><td>AVI</td><td>.avi</td></tr>
<tr><td>Matroska</td><td>.mkv</td></tr>
<tr><td>GXF</td><td>.gxf</td></tr>
<tr><td>WAVE/WAV </td><td>.wav</td></tr>
</table>

##<a id="export_formats"></a>Formatos de Exportação do Codificador de Mídia

A tabela a seguir lista os codecs e formatos de arquivo com suporte para exportação.


<table border="1">
<tr><th>Formato de arquivo</th><th>Codec de vídeo</th><th>Codec de áudio</th></tr>
<tr><td>MP4 (*.mp4)<br/><br/>(incluindo contêineres de múltiplas taxas de bits MP4) </td><td>H.264 (Perfis Alto, Principal e Linha de base)</td><td>AAC-LC, HE-AAC v1, HE-AAC v2 </td></tr>
<tr><td>MPEG2-TS </td><td>H.264 (Perfis Alto, Principal e Linha de base)</td><td>AAC-LC, HE-AAC v1, HE-AAC v2 </td></tr>
</table>

##Consulte também

[Codificando conteúdo sob demanda com os Serviços de Mídia do Azure](media-services-encode-asset.md)

<!---HONumber=July15_HO3-->