<properties 
	pageTitle="Codecs e formatos do Codificador de Mídia do Azure" 
	description="Este tópico fornece uma visão geral dos codecs e formatos do Codificador de Mídia do Azure" 
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
	ms.date="02/27/2015" 
	ms.author="juliako"/>

#Codecs e formatos do Codificador de Mídia do Azure

Os codificadores compactam mídia digital usando codecs. Normalmente, os codificadores têm várias configurações que permitem especificar propriedades da mídia gerada, por exemplo, os codecs usados, o formato do arquivo, a resolução e taxa de bits. Formatos de arquivo são contêineres que armazenam o vídeo compactado, bem como informações sobre quais codecs foram usados para compactar o vídeo. 

Codecs têm dois componentes: um para compactar arquivos de mídia digital para transmissão e outro para descompactar arquivos de mídia digital para reprodução. Há codecs de áudio para compactar e descompactar áudio e codecs de vídeo para compactar e descompactar vídeo. Os codecs podem usar compactação com ou sem perdas. Codecs sem perdas preservam todas as informações quando ocorre a compressão. Quando o arquivo é descompactado, o resultado é um arquivo idêntico ao da mídia de entrada, o que torna os codecs sem perdas adequados para arquivamento e armazenamento. Codecs com perdas perdem algumas informações durante a codificação e produzem arquivos menores (que o original) à custa da qualidade do vídeo e servem para streaming pela Internet. Os dois principais codecs usados pelo Codificador de Mídia do Azure para codificação são H.264 e VC-1. Outros codecs podem estar disponíveis em nosso ecossistema de parceiros de codificadores.

É importante compreender a diferença entre codecs e formatos de arquivo. Codecs são o software que implementa os algoritmos de compactação/descompactação. Já os formatos de arquivo são contêineres que armazenam o vídeo compactado. Para obter mais informações, consulte [Codificação versus empacotamento](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/).

Este documento contém uma lista dos formatos de arquivo de importação e importação mais comuns que você pode usar com o Codificador de Mídia do Azure.


[Formatos de importação do Codificador de Mídia](#import_formats)

[Formatos de exportação do Codificador de Mídia](#export_formats)


##<a id="import_formats"></a>Formatos de importação do Codificador de Mídia 

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
 
<table border="1">
<tr><th>Formato de arquivo</th><th>Extensões de arquivo</th></tr>
<tr><td>3GPP, 3GPP2</td><td>.3gp, .3g2, .3gp2</td></tr>
<tr><td>Formato ASF</td><td>.asf</td></tr>
<tr><td>AVCHD (Codificação de vídeo avançada de alta definição) [fluxo de transporte de MPEG-2]</td><td>.mts, .m2ts</td></tr>
<tr><td>Formato AVI</td><td>.avi</td></tr>
<tr><td>Câmera de vídeo digital MPEG-2 (MOD)</td><td>.mod</td></tr>
<tr><td>Arquivo TS (fluxo de transporte) de DVD</td><td>.ts</td></tr>
<tr><td>Arquivo VOB (objeto de vídeo) de DVD</td><td>.vob</td></tr>
<tr><td>Arquivo de codec do Expression Encoder Screen Capture</td><td>.xesc</td></tr>
<tr><td>MP4</td><td>.mp4</td></tr>
<tr><td>Fluxo de sistema MPEG-1</td><td>.mpeg, .mpg</td></tr>
<tr><td>Arquivo de vídeo MPEG-2</td><td>.m2v</td></tr>
<tr><td>Formato de arquivo do Smooth Streaming (PIFF 1.3)</td><td>.ismv</td></tr>
<tr><td>Vídeo do Windows Media (WMV)</td><td>.wmv</td></tr>
</table>

Há suporte para alguns formatos descompactados. Para obter mais informações, consulte [Formatos de vídeo descompactado compatíveis](#uncompressed)

###Formatos de arquivo de áudio

<table border="1">
<tr><th>Formato de arquivo</th><th>Extensões de arquivo</th></tr>
<tr><td>Áudio AC-3 (Dolby Digital)</td><td>.ac3</td></tr>
<tr><td>Formato AIFF</td><td>.aiff</td></tr>
<tr><td>Broadcast Wave Format</td><td>.bwf</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>.mp3</td></tr>
<tr><td>Áudio MP4</td><td>.m4A</td></tr>
<tr><td>Audiolivro MPEG-4</td><td>.m4b</td></tr>
<tr><td>Arquivo WAVE</td><td>.wav</td></tr>
<tr><td>Áudio do Windows Media</td><td>.wma</td></tr>   
</table>

###Formatos de arquivo de imagem

<table border="1">
<tr><th>Formato de arquivo</th><th>Extensões de arquivo</th></tr>
<tr><td>Bitmap</td><td>.bmp</td></tr>
<tr><td>GIF, GIF animado</td><td>.gif</td></tr>
<tr><td>JPEG</td><td>.jpeg, .jpg</td></tr>
<tr><td>PNG</td><td>.png</td></tr>
<tr><td>TIFF</td><td>.tif</td></tr>
<tr><td>WPF Canvas XAML</td><td>.xaml</td></tr>
</table>


##<a id="export_formats"></a>Formatos de exportação do Codificador de Mídia

A tabela a seguir lista os codecs e formatos de arquivo com suporte para exportação.


<table border="1">
<tr><th>Formato de arquivo</th><th>Codec de vídeo</th><th>Codec de áudio</th></tr>
<tr><td>Windows Media (*.wmv; *.wma)</td><td>VC-1 (Perfis Avançado, Principal e Simples)</td><td>Windows Media Audio Standard, Windows Media Audio Professional, Windows Media Audio Voice, Windows Media Audio Lossless</td></tr>
<tr><td>MP4 (*.mp4)</td><td>H.264 (Perfis Alto, Principal e Linha de base)</td><td>AAC-LC, HE-AAC v1, HE-AAC v2, Dolby Digital Plus</td></tr>
<tr><td>Formato de arquivo do Smooth Streaming (PIFF 1.1) (*.ismv; *.isma)</td><td>VC-1 (Perfil Avançado)<br/><br/>
H.264 (Perfis Alto, Principal e Linha de base)</td><td>Windows Media Audio Standard, Windows Media Audio Professional<br/><br/>
AAC-LC, HE-AAC v1, HE-AAC v2</td></tr>
</table>

Para obter codecs com suporte adicionais e filtros nos Serviços de Mídia, consulte [Filtros do Windows DirectShow](https://msdn.microsoft.com/library/windows/desktop/dd375464.aspx).

##<a id="uncompressed"></a>Formatos de vídeo descompactado compatíveis 

Os Serviços de Mídia do Azure dão suporte à importação de dados de vídeos descompactados.

Esta é uma lista parcial de formatos descompactados com suporte.

<table border="1">
<tr><th>Formato de vídeo descompactado</th><th>Descrição</th></tr>
<tr><td>Dados descompactados em formato YVU9 padrão</td><td>Um formato YUV planar. Uma amostra Y a cada pixel, uma amostra U e V a cada quatro pixels horizontais em cada linha; e uma amostra Y em cada linha vertical, uma amostra U e V a cada quarta linha vertical. 9 bits por pixel.</td></tr>
<tr><td>Dados em formato YUV 411</td><td>Uma amostra Y a cada pixel, uma amostra U e V a cada quatro pixels horizontais em cada linha; cada linha vertical de amostra. A ordem de bytes (menor primeiro) é U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, em que o sufixo 0 é o pixel mais à esquerda e os números crescentes são pixels que aumentam da esquerda para a direita. Cada bloco de 12 bytes tem 8 pixels da imagem.</td></tr>
<tr><td>Dados em formato Y41P</td><td>Uma amostra Y a cada pixel, uma amostra U e V a cada quatro pixels horizontais em cada linha; cada linha vertical de amostra. A ordem de bytes (menor primeiro) é U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, em que o sufixo 0 é o pixel mais à esquerda e os números crescentes são pixels que aumentam da esquerda para a direita. Cada bloco de 12 bytes tem 8 pixels da imagem.</td></tr>
<tr><td>Dados em formato YUY2</td><td>O mesmo que UYVY, mas com ordem de pixels diferente. A ordem de bytes (menor primeiro) é Y0, U0, Y1, V0, Y2, U2, Y3, V2, Y4, U4, Y5, V4, onde o sufixo 0 é o pixel mais à esquerda e os números crescentes são pixels que aumentam da esquerda para a direita. Cada bloco de 4 bytes tem 2 pixels da imagem.</td></tr>
<tr><td>Dados em formato YVYU</td><td>Um formato YUV empacotado. O mesmo que UYVY, mas com ordem de pixels diferente. A ordem de bytes (menor primeiro) é Y0, V0, Y1, U0, Y2, V2, Y3, U2, Y4, V4, Y5, U4, em que o sufixo 0 é o pixel mais à esquerda e os números crescentes são pixels que aumentam da esquerda para a direita. Cada bloco de 4 bytes tem 2 pixels da imagem.</td></tr>
<tr><td>Dados em formato UYVY</td><td>Um formato YUV empacotado. Uma amostra Y a cada pixel, uma amostra U e V a cada dois pixels horizontais em cada linha; cada linha vertical de amostra. Mais popular dos vários formatos YUV 4:2:2. A ordem de bytes (menor primeiro) é U0, Y0, V0, Y1, U2, Y2, V2, Y3, U4, Y4, V4, Y5, em que o sufixo 0 é o pixel mais à esquerda e os números crescentes são pixels que aumentam da esquerda para a direita. Cada bloco de 4 bytes tem 2 pixels da imagem.</td></tr>
<tr><td>Dados em formato YUV 211</td><td>Um formato YUV empacotado. Uma amostra Y a cada dois pixels, uma amostra U e V a cada quatro pixels horizontais em cada linha; cada linha vertical de amostra. A ordem de bytes (menor primeiro) é Y0, U0, Y2, V0, Y4, U4, Y6, V4, Y8, U8, Y10, V8, em que o sufixo 0 é o pixel mais à esquerda e os números crescentes são pixels que aumentam da esquerda para a direita. Cada bloco de 4 bytes tem 4 pixels da imagem.</td></tr>
<tr><td>Formato Cirrus Logic Jr YUV 411</td><td>Formato Cirrus Logic Jr YUV 411 com menos de 8 bits por amostra Y, U e V. Uma amostra Y a cada pixel, uma amostra U e V a cada quatro pixels horizontais em cada linha; cada linha vertical de amostra.</td></tr>
<tr><td>Formato YVU9 produzido em Indeo</td><td>Formato YVU9 produzido em Indeo com informações adicionais sobre as diferenças do último quadro. 9,5 bits por pixel, mas relatado como 9.</td></tr>
</table>

<!--HONumber=47-->
