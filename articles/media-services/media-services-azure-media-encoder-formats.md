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
	ms.date="06/29/2015" 
	ms.author="juliako"/>

#Codecs e formatos do Codificador de Mídia do Azure

Os codificadores compactam mídia digital usando codecs. Normalmente, os codificadores têm várias configurações que permitem especificar propriedades da mídia gerada, por exemplo, os codecs usados, o formato do arquivo, a resolução e taxa de bits. Formatos de arquivo são contêineres que armazenam o vídeo compactado, bem como informações sobre quais codecs foram usados para compactar o vídeo.

Codecs têm dois componentes: um para compactar arquivos de mídia digital para transmissão e outro para descompactar arquivos de mídia digital para reprodução. Há codecs de áudio para compactar e descompactar áudio e codecs de vídeo para compactar e descompactar vídeo. Os codecs podem usar compactação com ou sem perdas. Codecs sem perdas preservam todas as informações quando ocorre a compressão. Quando o arquivo é descompactado, o resultado é um arquivo idêntico ao da mídia de entrada, o que torna os codecs sem perdas adequados para arquivamento e armazenamento. Codecs com perdas perdem algumas informações durante a codificação e produzem arquivos menores (que o original) à custa da qualidade do vídeo e servem para streaming pela Internet. Os dois principais codecs usados pelo Codificador de Mídia do Azure para codificação são H.264 e VC-1. Outros codecs podem estar disponíveis em nosso ecossistema de parceiros de codificadores.

É importante compreender a diferença entre codecs e formatos de arquivo. Codecs são o software que implementa os algoritmos de compactação/descompactação. Já os formatos de arquivo são contêineres que armazenam o vídeo compactado. Para obter mais informações, consulte [Codificação versus Empacotamento](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/).

Este documento contém uma lista dos formatos de arquivo de importação e importação mais comuns que você pode usar com o Codificador de Mídia do Azure.


[Formatos de Importação do Codificador de Mídia do Azure](#import_formats)

[Formatos de Exportação do Codificador de Mídia do Azure](#export_formats)


##<a id="import_formats"></a>Formatos de Importação do Codificador de Mídia do Azure 

A seção a seguir lista os codecs e formatos de arquivo com suporte para importação.

###Codecs de vídeo

- H.264 (Perfis Linha de base, Principal e Alto)
- MPEG-1 (incluindo MPEG-PS)
- MPEG-2 (Perfis Simples e Principal e perfil 4:2:2)
- MPEG-4 v2 (Perfil Visual Simples e Perfil Simples Avançado)
- VC-1 (Perfis Simples, Principal e Avançado)
- Vídeo do Windows Media (Perfis Simples, Principal e Avançado)
- DV (DVC, DVHD, DVSD, DVSL)
- Grass Valley HQ/HQX
 
###Codecs de áudio

- AC-3 (áudio Dolby Digital)
- AAC (AAC-LC, HE-AAC v1 com núcleo AAC-LC e HE-AAC v2 com núcleo AAC-LC)
- MP3
- Áudio do Windows Media 9 (Windows Media Audio Standard, Windows Media Audio Professional e Windows Media Audio Lossless)

###Formatos de arquivo de vídeo
 
Formato de arquivo|Extensões de arquivo
---|---
3GPP, 3GPP2|.3gp, .3g2, .3gp2
Formato ASF|.asf
AVCHD (Codificação de vídeo avançada de alta definição) [fluxo de transporte de MPEG-2]|.mts, .m2ts
Formato AVI|.avi
Câmera de vídeo digital MPEG-2 (MOD)|.mod
Arquivo TS (fluxo de transporte) de DVD|.ts
Arquivo VOB (objeto de vídeo) de DVD|.vob
Arquivo de codec do Expression Encoder Screen Capture|.xesc
MP4|.mp4
Fluxo de sistema MPEG-1|.mpeg, .mpg
Arquivo de vídeo MPEG-2|.m2v
Formato de arquivo do Smooth Streaming (PIFF 1.3)|.ismv
Vídeo do Windows Media (WMV)|.wmv


Há suporte para alguns formatos descompactados. Para saber mais, consulte [Formatos de vídeo descompactado compatíveis](#uncompressed)

###Formatos de arquivo de áudio

Formato de arquivo|Extensões de arquivo
---|---
Áudio AC-3 (Dolby Digital)|.ac3
Formato AIFF|.aiff
Broadcast Wave Format|.bwf
MP3 (MPEG-1 Audio Layer 3)|.mp3
Áudio MP4|.m4A
Audiolivro MPEG-4|.m4b
Arquivo WAVE|.wav
Áudio do Windows Media|.wma

###Formatos de arquivo de imagem

Formato de arquivo|Extensões de arquivo
---|---
Bitmap|.bmp
GIF, GIF animado|.gif
JPEG|.jpeg, .jpg
PNG|.png
TIFF|.tif
WPF Canvas XAML|.xaml


##<a id="export_formats"></a>Formatos de exportação do Codificador de Mídia do Azure

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

<!---HONumber=August15_HO6-->