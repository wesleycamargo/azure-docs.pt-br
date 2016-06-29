<properties 
	pageTitle="Visão Geral e Comparação de Codificadores de Mídia sob Demanda do Azure" 
	description="Este tópico fornece uma visão geral e oferece uma comparação dos codificadores de Mídia sob Demanda do Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="02/25/2016"  
	ms.author="juliako"/>

#Visão Geral e Comparação de Codificadores de Mídia sob Demanda do Azure

##Visão Geral de Codificação

Os Serviços de Mídia do Azure fornecem várias opções para a codificação de mídia na nuvem.

Ao começar a usar os Serviços de Mídia é importante compreender a diferença entre codecs e formatos de arquivo. Codecs são o software que implementa os algoritmos de compactação/descompactação. Já os formatos de arquivo são contêineres que armazenam o vídeo compactado.

Os Serviços de Mídia fornecem empacotamento dinâmico, que permite a você distribuir o conteúdo de taxa de bits adaptável MP4 ou Smooth Streaming codificado em formatos de streaming suportados pelo Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) sem a necessidade de empacotar novamente nesses formatos de fluxo contínuo.

Para aproveitar os benefícios do [empacotamento dinâmico](media-services-dynamic-packaging-overview.md), você precisa fazer o seguinte:

- Codificar seu arquivo mezanino (fonte) em um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos Smooth Streaming de taxa de bits adaptável (as etapas de codificação são demonstradas mais tarde neste tutorial).
- Obter pelo menos uma unidade de streaming sob demanda para o ponto de extremidade de streaming por meio do qual você planeja fornecer seu conteúdo. Para saber mais, consulte [Como dimensionar unidades reservadas para streaming sob demanda](media-services-manage-origins.md#scale_streaming_endpoints/).

Os Serviços de Mídia são compatíveis com os seguintes codificadores sob demanda descritos neste artigo:

- [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
- [Fluxo de trabalho do Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Este artigo fornece uma breve visão geral dos codificadores de mídia sob demanda e fornece links para artigos que oferecem informações mais detalhadas. O tópico também fornece uma comparação entre os codificadores.

Observe que, por padrão, cada conta dos Serviços de Mídia pode ter uma tarefa de codificação ativa por vez. Você pode reservar unidades de codificação que permitem ter várias tarefas de codificação em execução simultaneamente, uma para cada unidade reservada de codificação que você comprar. Para saber mais, consulte [Dimensionamento das unidades de codificação](media-services-portal-encoding-units.md).

##Media Encoder Standard

###Como usar

[Como codificar com o Codificador de Mídia Padrão](media-services-dotnet-encode-with-media-encoder-standard.md)

###Formatos

[Formatos e codecs](media-services-media-encoder-standard-formats.md)

###Predefinições

O Media Encoder Standard é configurado usando um dos codificadores predefinidos descritos [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###Metadados de entrada e saída

Os metadados de entrada dos codificadores estão descritos [aqui](http://msdn.microsoft.com/library/azure/dn783120.aspx).

Os metadados de saída dos codificadores estão descritos [aqui](http://msdn.microsoft.com/library/azure/dn783217.aspx).

###Gerar miniaturas

Para obter informações, veja [Como gerar miniaturas usando o Codificador de Mídia Padrão](media-services-custom-mes-presets-with-dotnet.md#thumbnails).

###Cortar vídeos (recorte)

Para obter informações, veja [Como cortar vídeos usando o Codificador de Mídia Padrão](media-services-custom-mes-presets-with-dotnet.md#trim_video).

###Criar sobreposições

Para obter informações, veja [Como criar sobreposições usando o Codificador de Mídia Padrão](media-services-custom-mes-presets-with-dotnet.md#overlay).

###Consulte também

[O blog Serviços de Mídia](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##Fluxo de trabalho do Media Encoder Premium

###Visão geral

[Apresentando a codificação Premium nos Serviços de Mídia do Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

###Como usar

O fluxo de trabalho do Media Encoder Premium é configurado usando fluxos de trabalho complexos. Os arquivos de fluxo de trabalho podem ser criados e atualizados usando a ferramenta [Designer de Fluxo de Trabalho](media-services-workflow-designer.md).

[Como usar a codificação Premium nos Serviços de Mídia do Azure](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

###Problemas conhecidos

Se o vídeo de entrada não contiver a legendagem oculta, o ativo de saída ainda conterá um arquivo TTML vazio.


##<a id="compare_encoders"></a>Comparar os codificadores

###<a id="billing"></a>Medidor de cobrança usado por cada codificador

Nome do processador de mídia|Preços aplicáveis|Observações
---|---|---
**Media Encoder Standard** |CODIFICADOR|As Tarefas de Codificação serão cobradas de acordo com o tamanho do Ativo de saída, em GB, na taxa especificada [aqui][1], na coluna CODIFICADOR.
**Fluxo de trabalho do Media Encoder Premium** |CODIFICADOR PREMIUM|As Tarefas de Codificação serão cobradas de acordo com o tamanho do Ativo de saída, em GB, na taxa especificada [aqui][1], na coluna CODIFICADOR PREMIUM.


Esta seção compara as funcionalidades de codificação do **Codificador de Mídia do Azure** e do **Fluxo de Trabalho Premium de Codificação de Mídia**.


###Formatos de arquivo/contêiner de entrada

Formatos de arquivo/contêiner de entrada|Media Encoder Standard|Fluxo de trabalho do Media Encoder Premium
---|---|---
Adobe® Flash® F4V |Sim|Sim
MXF/SMPTE 377M |Sim|Sim
GXF |Sim|Sim
Fluxos de transporte de MPEG-2 |Sim|Sim
Fluxos de programa MPEG-2 |Sim|Sim
MPEG-4/MP4 |Sim|Sim
Windows Media/ASF |Sim|Sim
AVI (8 bits/10 bits descompactado)|Sim|Sim
3GPP/3GPP2 |Sim|Não
Formato de arquivo do Smooth Streaming (PIFF 1.3)|Sim|Não
[Gravação (DVR-MS) de vídeo Digital da Microsoft](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Sim|Não
Matroska/WebM |Sim|Não
QuickTime (.mov) |Sim|Não

###Codecs de vídeo de entrada

Codecs de vídeo de entrada|Media Encoder Standard|Fluxo de trabalho do Media Encoder Premium
---|---|---
AVC de 8 bits/10 bits até 4:2:2, incluindo AVCIntra |8 bits 4:2:0 e 4:2:2|Sim
DNxHD ávido (em MXF) |Sim|Sim
DVCPro/DVCProHD (em MXF) |Sim|Sim
JPEG2000 |Sim|Sim
MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10)|Até perfil 422|Sim
MPEG-1 |Sim|Sim
Windows Media Video/VC-1 |Sim|Sim
Canopus HQ/HQX |Não|Não
MPEG-4, parte 2 |Sim|Não
[Theora](https://en.wikipedia.org/wiki/Theora) |Sim|Não
Apple ProRes 422 |Sim|Não
Apple ProRes 422 LT |Sim|Não
Apple ProRes 422 HQ |Sim|Não
Apple ProRes Proxy|Sim|Não
Apple ProRes 4444 |Sim|Não
Apple ProRes 4444 XQ |Sim|Não

###Codecs de áudio de entrada

Codecs de áudio de entrada|Media Encoder Standard|Fluxo de trabalho do Media Encoder Premium
---|---|---
AES (SMPTE 331M e 302M, AES3-2003) |Não|Sim
Dolby® E |Não|Sim
Dolby® Digital (AC3) |Não|Sim
Dolby® Digital Plus (E-AC3) |Não|Sim
AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)|Sim|Sim
MPEG Layer 2|Sim|Sim
MP3 (MPEG-1 Audio Layer 3)|Sim|Sim
Áudio do Windows Media|Sim|Sim
WAV/PCM|Sim|Sim
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Sim|Não
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format)) |Sim|Não
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Sim|Não


###Formatos de contêiner/arquivo de saída

Formatos de contêiner/arquivo de saída|Media Encoder Standard|Fluxo de trabalho do Media Encoder Premium
---|---|---
Adobe® Flash® F4V|Não|Sim
MXF (OP1a, XDCAM e AS02)|Não|Sim
DPP (incluindo AS11)|Não|Sim
GXF|Não|Sim
MPEG-4/MP4|Sim|Sim
MPEG-TS|Sim|Sim
Windows Media/ASF|Não|Sim
AVI (8 bits/10 bits descompactado)|Não|Sim
Formato de arquivo do Smooth Streaming (PIFF 1.3)|Não|Sim

###Codecs de vídeo de saída

Codecs de vídeo de saída|Media Encoder Standard|Fluxo de trabalho do Media Encoder Premium
---|---|---
AVC (H. 264; 8 bits; até perfil, nível elevado 5.2; 4K Ultra HD; Intra AVC)|Somente 8 bits 4:2:0|Sim
DNxHD ávido (em MXF)|Não|Sim
DVCPro/DVCProHD (em MXF)|Não|Sim
MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10)|Não|Sim
MPEG-1|Não|Sim
Windows Media Video/VC-1|Não|Sim
Criação de miniaturas JPEG|Não|Sim

###Codecs de áudio de saída

Codecs de áudio de saída|Media Encoder Standard|Fluxo de trabalho do Media Encoder Premium
---|---|---
AES (SMPTE 331M e 302M, AES3-2003)|Não|Sim
Dolby® Digital (AC3)|Não|Sim
Dolby ® Digital Plus (E-AC3) até 7.1|Não|Sim
AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)|Sim|Sim
MPEG Layer 2|Não|Sim
MP3 (MPEG-1 Audio Layer 3)|Não|Sim
Áudio do Windows Media|Não|Sim


##Códigos do Erro  

A tabela a seguir lista os códigos de erro que podem ser retornados caso um erro tenha sido encontrado durante a execução de tarefas de codificação. Para obter detalhes do erro em seu código do .NET, use a classe [ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx). Para obter detalhes do erro em seu código REST, use a API REST [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx).

ErrorDetail.Code|Causas possíveis para erro
-----|-----------------------
Desconhecido| Erro desconhecido ao executar a tarefa
ErrorDownloadingInputAssetMalformedContent|Categoria de erros que abrange erros ao baixar um ativo de entrada, como nomes de arquivo inválidos, arquivo com comprimento zero, formatos incorretos e assim por diante.
ErrorDownloadingInputAssetServiceFailure|Categoria de erros que aborda problemas no lado do serviço - por exemplo, erros de rede ou armazenamento durante o download.
ErrorParsingConfiguration|Categoria de erros em que a tarefa <see cref="MediaTask.PrivateData"/> (configuração) não é válida, por exemplo, a configuração não é uma predefinição de sistema válida ou contém XML inválido.
ErrorExecutingTaskMalformedContent|Categoria de erros durante a execução da tarefa em que os problemas de conteúdo nos arquivos de mídia de entrada causam falha.
ErrorExecutingTaskUnsupportedFormat|Categoria de erros em que o processador de mídia não pode processar os arquivos fornecidos - não há suporte para o formato de mídia ou ele não coincide com a Configuração. Por exemplo, tentando produzir uma saída somente de áudio com base em um ativo que tenha apenas vídeo
ErrorProcessingTask|Categoria de outros erros que o processador de mídia encontra durante o processamento da tarefa e que não estão relacionados ao conteúdo.
ErrorUploadingOutputAsset|Categoria de erros ao carregar o ativo de saída
ErrorCancelingTask|Categoria de erros para cobrir falhas durante a tentativa de cancelar a Tarefa
TransientError|Categoria de erros para abordar problemas transitórios (por exemplo, problemas de rede temporários com o Armazenamento do Azure)


Para obter ajuda da equipe dos **Serviços de Mídia**, abra um [tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).



##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Artigos relacionados

- [Executar tarefas avançadas de codificação ao personalizar predefinições do Codificador de Mídia Padrão](media-services-custom-mes-presets-with-dotnet.md)
- [Cotas e limitações](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=AcomDC_0615_2016-->