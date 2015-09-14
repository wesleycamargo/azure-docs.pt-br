<properties 
	pageTitle="Codecs e formatos do Codificador de Mídia do Azure"
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
	ms.date="08/30/2015"
	ms.author="juliako"/>

#Codecs e formatos do Codificador de Mídia do Azure

Este documento contém uma lista dos formatos e codecs de arquivo de entrada e saída mais comuns que você pode usar com o Codificador de Mídia do Azure.


##Formatos de arquivo de entrada de vídeo (contêineres)
 
Formato de arquivo (extensões de arquivo)|Suportado
---|---
3GPP, 3GPP2 (.3gp, .3g2, .3gp2) |Sim
Formato ASF (.asf) |Sim
AVCHD (Codificação de vídeo avançada de alta definição) [fluxo de transporte de MPEG-2] (.mts, .m2ts) |Sim
Formato AVI (.avi) |Sim
Câmera de vídeo digital MPEG-2 (MOD) (.mod) |Sim
Arquivo TS (fluxo de transporte) de DVD (.ts) |Sim
Arquivo VOB (objeto de vídeo) de DVD (.vob) |Sim
Arquivo de codec do Expression Encoder Screen Capture (.xesc) |Sim
MP4 (.mp4) |Sim
Fluxo de sistema MPEG-1 (.mpeg, .mpg) |Sim
Arquivo de vídeo MPEG-2 (.m2v) |Sim
Formato de arquivo do Smooth Streaming (PIFF 1.3) (.ismv) |Sim
Vídeo do Windows Media (WMV) (.wmv) |Sim
Adobe® Flash® F4V |Não		
MXF/SMPTE 377M |Limitado 
GXF |Não		 
[Gravação (DVR-MS) de vídeo Digital da Microsoft](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Não
Matroska/WebM |Não

Há suporte para alguns formatos descompactados. Para saber mais, consulte [Formatos de vídeo descompactado compatíveis](#uncompressed)

##Formatos de arquivo de áudio de entrada

Formato de arquivo (extensões de arquivo)|Suportado
---|---
Áudio AC-3 (Dolby Digital) (.ac3)|Sim
Formato AIFF (.aiff)|Sim
Broadcast Wave Format (.bwf)|Sim
MP3 (MPEG-1 Audio Layer 3) (.mp3)|Sim
Áudio MP4 (.m4A)|Sim
Audiolivro MPEG-4 (.m4b)|Sim
Arquivo WAVE (.wav)|Sim
Áudio do Windows Media (.wma)|Sim


##Codecs de vídeo de entrada

Codecs de vídeo de entrada|Suportado
---|--- 
H.264 (Perfis Linha de base, Principal e Alto) |Sim
AVC de 8 bits/10 bits até 4:2:2, incluindo AVCIntra |Apenas 8 bits 4:2:0
DNxHD ávido (em MXF) |Não
DVCPro/DVCProHD (em MXF) |Não
JPEG2000 |Não
MPEG-2 (Perfis Simples e Principal e perfil 4:2:2) |Até perfil 4:2:2
MPEG-1 (incluindo MPEG-PS) |Sim
Windows Media Video/VC-1 |Sim
Canopus HQ/HQX |Sim
MPEG-4 v2 (Perfil Visual Simples e Perfil Simples Avançado) |Sim
[Theora](https://en.wikipedia.org/wiki/Theora) |Não
VC-1 (Perfis Simples, Principal e Avançado) |Sim
Vídeo do Windows Media (Perfis Simples, Principal e Avançado) |Sim
DV (DVC, DVHD, DVSD, DVSL) |Sim
Grass Valley HQ/HQX |Sim
 

##Codecs de áudio de entrada

Codecs de áudio de entrada|Suportado
---|---
AES (SMPTE 331M e 302M, AES3-2003) |Não
Dolby® E |Não
Dolby® Digital (AC3) |Sim
Dolby® Digital Plus (E-AC3) |Não
AAC (AAC-LC, HE-AAC v1 com núcleo AAC-LC e HE-AAC v2 com núcleo AAC-LC; até 5.1)|Sim
MPEG Layer 2|Sim|Sim|Sim
MP3 (MPEG-1 Audio Layer 3)|Sim
Áudio do Windows Media 9 (Windows Media Audio Standard, Windows Media Audio Professional e Windows Media Audio Lossless) |Sim
WAV/PCM|Sim
[FLAC](https://en.wikipedia.org/wiki/FLAC)|Não
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Não
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)|Não


##Formatos de arquivo de imagem de entrada

Formato de arquivo (extensões de arquivo) | Suportado
---|---
Bitmap (.bpm) | Sim
GIF, GIF animado (.gif)| Sim
.jpeg, .jpg (.jpeg, .jpg)| Sim
PNG (. png)| Sim
TIFF (. tif)| Sim
WPF Canvas XAML (.xaml)| Sim


##Formatos e codecs de saída

A tabela a seguir lista os codecs e formatos de arquivo com suporte para exportação.

Formato de arquivo|Codec de vídeo|Codec de áudio
---|---|---
Windows Media (* .wmv; * .wma)|VC-1 (Perfis Avançado, Principal e Simples)|Windows Media Audio Standard, Windows Media Audio Professional, Windows Media Audio Voice, Windows Media Audio Lossless
MP4 (* .mp4)|H.264 (Perfis Alto, Principal e Linha de base)|AAC-LC, HE-AAC v1, HE-AAC v2, Dolby Digital Plus
Formato de arquivo do Smooth Streaming (PIFF 1.1) (* .ismv; * .isma)|VC-1 (Perfil Avançado)<p>H.264 (Perfis Alto, Principal e Linha de base) |Windows Media Audio Standard, Windows Media Audio Professional<p><p>AAC-LC, HE-AAC v1, HE-AAC v2

Para obter os outros codecs e filtros com suporte nos Serviços de Mídia, consulte [Filtros do Windows DirectShow](https://msdn.microsoft.com/library/windows/desktop/dd375464.aspx).

##<a id="uncompressed"></a>Formatos de vídeo descompactado com suporte 

Os Serviços de Mídia do Azure dão suporte à importação de dados de vídeos descompactados.

Esta é uma lista parcial de formatos descompactados com suporte.

Formato de vídeo descompactado|Descrição
---|---
Dados descompactados em formato YVU9 padrão|Um formato YUV planar. Uma amostra Y a cada pixel, uma amostra U e V a cada quatro pixels horizontais em cada linha; e uma amostra Y em cada linha vertical, uma amostra U e V a cada quarta linha vertical. 9 bits por pixel.
Dados em formato YUV 411|Uma amostra Y a cada pixel, uma amostra U e V a cada quatro pixels horizontais em cada linha; cada linha vertical de amostra. A ordem de bytes (menor primeiro) é U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, em que o sufixo 0 é o pixel mais à esquerda e os números crescentes são pixels que aumentam da esquerda para a direita. Cada bloco de 12 bytes tem 8 pixels da imagem.
Dados em formato Y41P|Uma amostra Y a cada pixel, uma amostra U e V a cada quatro pixels horizontais em cada linha; cada linha vertical de amostra. A ordem de bytes (menor primeiro) é U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, em que o sufixo 0 é o pixel mais à esquerda e os números crescentes são pixels que aumentam da esquerda para a direita. Cada bloco de 12 bytes tem 8 pixels da imagem.
Dados em formato YUY2|O mesmo que UYVY, mas com ordem de pixels diferente. A ordem de bytes (menor primeiro) é Y0, U0, Y1, V0, Y2, U2, Y3, V2, Y4, U4, Y5, V4, onde o sufixo 0 é o pixel mais à esquerda e os números crescentes são pixels que aumentam da esquerda para a direita. Cada bloco de 4 bytes tem 2 pixels da imagem.
Dados em formato YVYU|Um formato YUV empacotado. O mesmo que UYVY, mas com ordem de pixels diferente. A ordem de bytes (menor primeiro) é Y0, V0, Y1, U0, Y2, V2, Y3, U2, Y4, V4, Y5, U4, em que o sufixo 0 é o pixel mais à esquerda e os números crescentes são pixels que aumentam da esquerda para a direita. Cada bloco de 4 bytes tem 2 pixels da imagem.
Dados em formato UYVY|Um formato YUV empacotado. Uma amostra Y a cada pixel, uma amostra U e V a cada dois pixels horizontais em cada linha; cada linha vertical de amostra. Mais popular dos vários formatos YUV 4:2:2. A ordem de bytes (menor primeiro) é U0, Y0, V0, Y1, U2, Y2, V2, Y3, U4, Y4, V4, Y5, em que o sufixo 0 é o pixel mais à esquerda e os números crescentes são pixels que aumentam da esquerda para a direita. Cada bloco de 4 bytes tem 2 pixels da imagem.
Dados em formato YUV 211|Um formato YUV empacotado. Uma amostra Y a cada dois pixels, uma amostra U e V a cada quatro pixels horizontais em cada linha; cada linha vertical de amostra. A ordem de bytes (menor primeiro) é Y0, U0, Y2, V0, Y4, U4, Y6, V4, Y8, U8, Y10, V8, em que o sufixo 0 é o pixel mais à esquerda e os números crescentes são pixels que aumentam da esquerda para a direita. Cada bloco de 4 bytes tem 4 pixels da imagem.
Formato Cirrus Logic Jr YUV 411|Formato Cirrus Logic Jr YUV 411 com menos de 8 bits por amostra Y, U e V. Uma amostra Y a cada pixel, uma amostra U e V a cada quatro pixels horizontais em cada linha; cada linha vertical de amostra.
Formato YVU9 produzido em Indeo|Formato YVU9 produzido em Indeo com informações adicionais sobre as diferenças do último quadro. 9,5 bits por pixel, mas relatado como 9.

<!---HONumber=September15_HO1-->