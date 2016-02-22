<properties 
	pageTitle="Codecs e formatos de fluxo de trabalho do Media Encoder Premium" 
	description="Este tópico fornece uma visão geral dos codecs e formatos de Formatos de Fluxo de Trabalho do Media Encoder Premium" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2016"  
	ms.author="juliako"/>

#Codecs e formatos de fluxo de trabalho do Media Encoder Premium


>[AZURE.NOTE]Para solucionar dúvidas sobre o codificador premium, envie um email para o mepd em Microsoft.com.
>
>O processador de mídia do Fluxo de trabalho do Media Encoder Premium discutido neste tópico não está disponível na China.

Este documento contém uma lista de formatos de arquivo de entrada e saída e codecs com suporte pela versão de demonstração pública do codificador de **Fluxo de trabalho do Media Encoder Premium**.

[Codecs e formatos de entrada de fluxo de trabalho do Media Encoder Premium](#input_formats)

[Codecs e formatos de saída de fluxo de trabalho do Media Encoder Premium](#output_formats)

**Fluxo de trabalho do Media Encoder Premium** dá suporte a legendas codificadas descritas [nesta](#closed_captioning) seção


##<a id="input_formats"></a>Codecs e formatos de entrada de fluxo de trabalho do Media Encoder Premium

A seção a seguir lista os codecs e formatos de arquivo aos quais esse processador de mídia dá suporte como entrada.

###Formatos de arquivo/contêiner de entrada

- Adobe® Flash® F4V
- MXF/SMPTE 377M
- GXF
- Fluxos de transporte de MPEG-2
- Fluxos de programa MPEG-2
- MPEG-4/MP4
- Windows Media/ASF
- AVI (8 bits/10 bits descompactado)

###Codecs de vídeo de entrada

- AVC de 8 bits/10 bits até 4:2:2, incluindo AVCIntra
- DNxHD ávido (em MXF)
- DVCPro/DVCProHD (em MXF)
- JPEG2000
- MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10)
- MPEG-1
- Windows Media Video/VC-1

###Codecs de áudio de entrada

- AES (SMPTE 331M e 302M, AES3-2003)
- Dolby® E
- Dolby® Digital (AC3)
- AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Áudio do Windows Media
- WAV/PCM
 
##<a id="output_format"></a>Codecs e formatos de saída de fluxo de trabalho do Media Encoder Premium

A seção a seguir lista os codecs e formatos de arquivo com suporte como a saída desse processador de mídia.

###Formatos de contêiner/arquivo de saída

- Adobe® Flash® F4V
- MXF (OP1a, XDCAM e AS02)
- DPP (incluindo AS11)
- GXF
- MPEG-4/MP4
- Windows Media/ASF
- AVI (8 bits/10 bits descompactado)
- Formato de arquivo do Smooth Streaming (PIFF 1.3)
- MPEG-TS 


###Codecs de vídeo de saída

- AVC (H. 264; 8 bits; até perfil, nível elevado 5.2; 4K Ultra HD; Intra AVC)
- DNxHD ávido (em MXF)
- DVCPro/DVCProHD (em MXF)
- MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10)
- MPEG-1
- Windows Media Video/VC-1
- Criação de miniaturas JPEG

###Codecs de áudio de saída

- AES (SMPTE 331M e 302M, AES3-2003)
- Dolby® Digital (AC3)
- Dolby ® Digital Plus (E-AC3) até 7.1
- AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Áudio do Windows Media

##<a id="closed_captioning"></a>Suporte a legenda codificada

Na ingestão, o **fluxo de trabalho do Media Encoder Premium** dá suporte a:

1. Arquivos SCC
1. Arquivos SMPTE-TT
1. CEA-608/CEA-708 – transportados como dados de usuário (mensagens SEI de fluxos elementares H.264, ATSC/53, SCTE20) ou executados como dados complementares em arquivos MXF/GXF
1. Arquivos de legenda STL

Na saída, as seguintes opções estão disponíveis:

1. Tradução de CEA-608 para CEA-708
1. Transmissão CEA-608/CEA-708 (integrada em mensagens SEI para fluxos elementares H.264, ou carregada como dados auxiliares nos arquivos MXF)
1. SCC
1. Texto cronometrado de SMPTE (de origem CEA-608 por SMPTE RP2052, inclusive criação de arquivo DFXP)
1. Arquivo de legenda SRT
1. Fluxos de legenda DVB

Observação: nem todos os formatos de saída acima têm suporte para fornecimento por meio de streaming nos Serviços de Mídia do Azure.

##Problemas conhecidos

Se o vídeo de entrada não contiver a legendagem oculta, o ativo de saída ainda conterá um arquivo TTML vazio.


##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->