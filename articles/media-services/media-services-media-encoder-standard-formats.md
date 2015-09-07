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
	ms.date="08/11/2015"
	ms.author="juliako"/>

#Codecs e formatos padrão do codificador de mídia


Este documento contém uma lista dos formatos de arquivo de importação e importação mais comuns que você pode usar com o Codificador de Mídia padrão.


[Formatos de Importação do Codificador de Mídia ](#import_formats)

[Formatos de Exportação do Codificador de Mídia](#export_formats)


##<a id="import_formats"></a>Formatos de Importação do Media Encoder Standard 

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

Formato de arquivo|Extensões de arquivo
---|---
FLV (com H.264 e codecs AAC) |.flv
MP4/ISMV|* .ismv
MPEG2-PS, MPEG2-TS, 3GP|.ts, .ps, .3gp
MXF|.mxf
WMV/ASF|.mwv, .asf
DVR-MS|.dvr-ms 
AVI|.avi
Matroska|.mkv
GXF|.gxf
WAVE/WAV |.wav


##<a id="export_formats"></a>Formatos de Exportação do Media Encoder Standard

A tabela a seguir lista os codecs e formatos de arquivo com suporte para exportação.


Formato de arquivo|Codec de vídeo|Codec de áudio
---|---|---
MP4 (* .mp4)<br/><br/>(incluindo contêineres de múltiplas taxas de bits MP4) |H.264 (Perfis Alto, Principal e Linha de base)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2-TS |H.264 (Perfis Alto, Principal e Linha de base)|AAC-LC, HE-AAC v1, HE-AAC v2 

##Consulte também

[Codificando conteúdo sob demanda com os Serviços de Mídia do Azure](media-services-encode-asset.md)

<!---HONumber=August15_HO9-->