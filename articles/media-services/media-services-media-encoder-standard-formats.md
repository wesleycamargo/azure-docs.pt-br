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
	ms.date="09/03/2015"
	ms.author="juliako"/>

#Codecs e formatos padrão do codificador de mídia


Este documento contém uma lista dos formatos de arquivo de importação e importação mais comuns que você pode usar com o Codificador de Mídia padrão.


##Formatos de arquivo/contêiner de entrada

Formatos de arquivo (extensões de arquivo)|Suportado
---|---|---|---
FLV (com codecs H.264 e AAC) (.flv) |Sim 
MXF (.mxf) |Sim 
GXF (.gxf) |Sim 
MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Sim 
Vídeo do Windows Media (WMV)/ASF (.wmv, .asf) |Sim 
AVI (8 bits/10 bits descompactado) (.avi)|Sim 
MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv)|Sim 
[Gravação (DVR-MS) de vídeo Digital da Microsoft](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Sim 
Matroska/WebM (.mkv) |Sim 
WAVE/WAV (.wav) |Sim 
 

##Codecs de vídeo de entrada

Codecs de vídeo de entrada|Suportado
---|---|---|---
AVC de 8 bits/10 bits até 4:2:2, incluindo AVCIntra |8 bits 4:2:0 e 4:2:2 
DNxHD ávido (em MXF) |Sim 
DVCPro/DVCProHD (em MXF) |Sim 
JPEG 2000 |Sim 
MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10)|Até perfil 422 
MPEG-1 |Sim 
VC-1/WMV9 |Sim 
Canopus HQ/HQX |Não 
MPEG-4, parte 2 |Sim 
[Theora](https://en.wikipedia.org/wiki/Theora) |Sim 
YUV420 descompactado, ou mezzanine |Sim


##Codecs de áudio de entrada

Codecs de áudio de entrada|Suportado
---|---|---|---
AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)|Sim 
MPEG Layer 2|Sim 
MP3 (MPEG-1 Audio Layer 3)|Sim 
Áudio do Windows Media|Sim 
WAV/PCM|Sim 
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Sim 
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Sim 
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Sim 
AMR (multitaxa adaptável)|Sim
AES (SMPTE 331M e 302M, AES3-2003) |Não 
Dolby® E |Não 
Dolby® Digital (AC3) |Não 
Dolby® Digital Plus (E-AC3) |Não 


##Formatos e codecs de saída

A tabela a seguir lista os codecs e formatos de arquivo com suporte para exportação.


Formato de arquivo|Codec de vídeo|Codec de áudio
---|---|---
MP4 <br/><br/>(incluindo contêineres de múltiplas taxas de bits MP4) |H.264 (Perfis Alto, Principal e Linha de base)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2-TS |H.264 (Perfis Alto, Principal e Linha de base)|AAC-LC, HE-AAC v1, HE-AAC v2 


##Roteiros de aprendizagem dos Serviços de Mídia

Você pode exibir os roteiros de aprendizagem do AMS aqui:

- [Fluxo de trabalho do streaming ao vivo do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Fluxo de trabalho do streaming sob demanda do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##Consulte também

[Codificando conteúdo sob demanda com os Serviços de Mídia do Azure](media-services-encode-asset.md)

[Como codificar com o Codificador de Mídia Padrão](media-services-dotnet-encode-with-media-encoder-standard.md)

<!---HONumber=Sept15_HO2-->