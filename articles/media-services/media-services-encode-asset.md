<properties 
	pageTitle="Visão Geral e Comparação de Codificadores de Mídia sob Demanda do Azure" 
	description="Este tópico fornece uma visão geral e oferece uma comparação dos codificadores de Mídia sob Demanda do Azure." 
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
	ms.date="09/07/2015"  
	ms.author="juliako"/>

#Visão Geral e Comparação de Codificadores de Mídia sob Demanda do Azure

##Visão Geral de Codificação

Os codificadores compactam mídia digital usando codecs. Normalmente, os codificadores têm várias configurações que permitem especificar propriedades da mídia gerada, por exemplo, os codecs usados, o formato do arquivo, a resolução e taxa de bits. Formatos de arquivo são contêineres que armazenam o vídeo compactado, bem como informações sobre quais codecs foram usados para compactar o vídeo.

Codecs têm dois componentes: um para compactar arquivos de mídia digital para transmissão e outro para descompactar arquivos de mídia digital para reprodução. Há codecs de áudio para compactar e descompactar áudio e codecs de vídeo para compactar e descompactar vídeo. Os codecs podem usar compactação com ou sem perdas. Codecs sem perdas preservam todas as informações quando ocorre a compressão. Quando o arquivo é descompactado, o resultado é um arquivo idêntico ao da mídia de entrada, o que torna os codecs sem perdas adequados para arquivamento e armazenamento. Codecs com perdas perdem algumas informações durante a codificação e produzem arquivos menores (que o original) à custa da qualidade do vídeo e servem para streaming pela Internet.

É importante compreender a diferença entre codecs e formatos de arquivo. Codecs são o software que implementa os algoritmos de compactação/descompactação. Já os formatos de arquivo são contêineres que armazenam o vídeo compactado. Para obter mais informações, consulte [Codificação versus Empacotamento](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/).

Os Serviços de Mídia fornecem empacotamento dinâmico, que permite a você distribuir o conteúdo de taxa de bits adaptável MP4 ou Smooth Streaming codificado em formatos de streaming suportados pelo Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) sem a necessidade de empacotar novamente nesses formatos de fluxo contínuo.

Para aproveitar os benefícios do [empacotamento dinâmico](media-services-dynamic-packaging-overview.md), você precisa fazer o seguinte:

- Codificar seu arquivo mezanino (fonte) em um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos Smooth Streaming de taxa de bits adaptável (as etapas de codificação são demonstradas mais tarde neste tutorial).
- Obter pelo menos uma unidade de streaming sob demanda para o ponto de extremidade de streaming por meio do qual você planeja fornecer seu conteúdo. Para saber mais, consulte [Como dimensionar unidades reservadas para streaming sob demanda](media-services-manage-origins.md#scale_streaming_endpoints/).

Os Serviços de Mídia são compatíveis com os seguintes codificadores sob demanda descritos neste artigo:

- **Media Encoder Standard**
- **Codificador de Mídia do Azure** 
- **Fluxo de trabalho do Media Encoder Premium**

Este artigo fornece uma breve visão geral dos codificadores de mídia sob demanda e fornece links para artigos que oferecem informações mais detalhadas. O tópico também fornece uma comparação entre os codificadores.

Observe que, por padrão, cada conta dos Serviços de Mídia pode ter uma tarefa de codificação ativa por vez. Você pode reservar unidades de codificação que permitem ter várias tarefas de codificação em execução simultaneamente, uma para cada unidade reservada de codificação que você comprar. Para obter informações, consulte [Dimensionamento das unidades de codificação](media-services-portal-encoding-units.md).

##Media Encoder Standard

###Visão geral

É recomendável usar o Codificador de Mídia Padrão. No entanto, ele atualmente não está exposto no portal do Azure.

Em comparação com o Codificador de Mídia do Azure, este codificador suporta mais formatos de entrada e saída e codecs. Outros benefícios incluem:

- Mais tolerante em relação a como o arquivo de entrada foi criado
- Tem uma melhor qualidade de codec H.264 que a do Codificador de Mídia do Azure
- Baseia-se em um pipeline mais flexível e mais recente
- É mais robusto/flexível

###Como usar

[Como codificar com o Codificador de Mídia Padrão](media-services-dotnet-encode-with-media-encoder-standard.md)

###Formatos

[Formatos e codecs](media-services-media-encoder-standard-formats.md)

###Predefinições

O Codificador de Mídia Padrão é configurado usando um dos codificadores predefinidos descritos [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###Metadados de entrada e saída

Os metadados de entrada dos codificadores estão descritos [aqui](http://msdn.microsoft.com/library/azure/dn783120.aspx) (igual ao Codificador de Mídia do Azure).

Os metadados de saída dos codificadores estão descritos [aqui](http://msdn.microsoft.com/library/azure/dn783217.aspx) (igual ao Codificador de Mídia do Azure).

###Miniatura

Não é suportado no momento.

###Sobreposições de áudio e/ou vídeo

Não é suportado no momento.

###Consulte também

[O blog Serviços de Mídia](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##Codificador de Mídia do Azure

###Visão geral

O Codificador de Mídia do Azure é um dos codificadores suportados pelo Serviços de Mídia. A partir de julho de 2015, é recomendável usar os [Codificadores de Mídia Padrão](media-services-encode-asset.md#media_encoder_standard).

###Como usar

[Como codificar com o Codificador de Mídia do Azure](media-services-dotnet-encode-asset.md)

###Formatos

[Formatos e codecs](media-services-azure-media-encoder-formats.md)

###Predefinições

O Codificador de Mídia do Azure é configurado usando uma das predefinições de codificador descritas [aqui](https://msdn.microsoft.com/library/azure/dn619392.aspx). Você também pode obter os arquivos de predefinição atuais do Codificador de Mídia do Azure [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder).

###Metadados de entrada e saída

Os metadados de entrada dos codificadores estão descritos [aqui](http://msdn.microsoft.com/library/azure/dn783120.aspx).

Os metadados de saída dos codificadores estão descritos [aqui](http://msdn.microsoft.com/library/azure/dn783217.aspx).

###Miniatura

[Criando uma miniatura](https://msdn.microsoft.com/library/azure/Dn673581.aspx)

###Sobreposições de áudio e/ou vídeo

[Criando Sobreposições](media-services-azure-media-customize-ame-presets.md#creating-overlays).

###Convenção de nomenclatura

[Como modificar os nomes de arquivo de saída](media-services-azure-media-customize-ame-presets.md#controlling-azure-media-encoder-output-file-names)

###Consulte também

[Codificando sua mídia com Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md)

##Fluxo de trabalho do Media Encoder Premium
	
O fluxo de trabalho do Media Encoder Premium é configurado usando fluxos de trabalho complexos. Os arquivos de fluxo de trabalho podem ser criados e atualizados usando a ferramenta [Designer de Fluxo de Trabalho](media-services-workflow-designer.md).

Para obter mais informações, consulte:

- [Apresentando a codificação Premium nos Serviços de Mídia do Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Como usar a codificação Premium nos Serviços de Mídia do Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)


##<a id="compare_encoders"></a>Comparar os codificadores

###<a id="billing"></a>Medidor de cobrança usado por cada codificador

Nome do processador de mídia|Preços aplicáveis|Observações
---|---|---
**Media Encoder Standard** |CODIFICADOR|As Tarefas de Codificação serão cobradas de acordo com o tamanho do Ativo de saída, em GB, na taxa especificada [aqui][1], na coluna CODIFICADOR.
**Codificador de Mídia do Azure** |CODIFICADOR|As Tarefas de Codificação serão cobradas de acordo com o tamanho do Ativo de saída, em GB, na taxa especificada [aqui][1], na coluna CODIFICADOR.
**Fluxo de trabalho do Media Encoder Premium** |CODIFICADOR PREMIUM|As Tarefas de Codificação serão cobradas de acordo com o tamanho do Ativo de saída, em GB, na taxa especificada [aqui][1], na coluna CODIFICADOR PREMIUM.


Esta seção compara os recursos de codificação do **Codificador de Mídia Padrão**, do **Codificador de Mídia do Azure** e do **Fluxo de trabalho do Codificador de Mídia Premium**.


###Formatos de arquivo/contêiner de entrada

Formatos de arquivo/contêiner de entrada|Media Encoder Standard|Codificador de Mídia do Azure|Fluxo de trabalho do Media Encoder Premium
---|---|---|---
Adobe® Flash® F4V |Sim|Não |Sim
MXF/SMPTE 377M |Sim|Limitado|Sim
GXF |Sim|Não |Sim
Fluxos de transporte de MPEG-2 |Sim|Sim |Sim
Fluxos de programa MPEG-2 |Sim|Sim |Sim
MPEG-4/MP4 |Sim|Sim |Sim
Windows Media/ASF |Sim|Sim |Sim
AVI (8 bits/10 bits descompactado)|Sim|Sim |Sim
3GPP/3GPP2 |Sim|Sim |Não
Formato de arquivo do Smooth Streaming (PIFF 1.3)|Sim|Sim|Não
[Gravação (DVR-MS) de vídeo Digital da Microsoft](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Sim|Não|Não
Matroska/WebM |Sim|Não|Não

###Codecs de vídeo de entrada

Codecs de vídeo de entrada|Media Encoder Standard|Codificador de Mídia do Azure|Fluxo de trabalho do Media Encoder Premium
---|---|---|---
AVC de 8 bits/10 bits até 4:2:2, incluindo AVCIntra |8 bits 4:2:0 e 4:2:2|Apenas 8 bits 4:2:0|Sim
DNxHD ávido (em MXF) |Sim|Não|Sim
DVCPro/DVCProHD (em MXF) |Sim|Não|Sim
JPEG2000 |Sim|Não|Sim
MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10)|Até perfil 422|Até perfil 422|Sim
MPEG-1 |Sim|Sim|Sim
Windows Media Video/VC-1 |Sim|Sim|Sim
Canopus HQ/HQX |Não|Sim|Não
MPEG-4, parte 2 |Sim|Não|Não
[Theora](https://en.wikipedia.org/wiki/Theora) |Sim|Não|Não

###Codecs de áudio de entrada

Codecs de áudio de entrada|Media Encoder Standard|Codificador de Mídia do Azure|Fluxo de trabalho do Media Encoder Premium
---|---|---|---
AES (SMPTE 331M e 302M, AES3-2003) |Não|Não|Sim
Dolby® E |Não|Não|Sim
Dolby® Digital (AC3) |Não|Sim|Sim
Dolby® Digital Plus (E-AC3) |Não|Não|Sim
AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)|Sim|Sim|Sim
MPEG Layer 2|Sim|Sim|Sim
MP3 (MPEG-1 Audio Layer 3)|Sim|Sim|Sim
Áudio do Windows Media|Sim|Sim|Sim
WAV/PCM|Sim|Sim|Sim
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Sim|Não|Não
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Sim|Não|Não
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Sim|Não|Não


###Formatos de contêiner/arquivo de saída

Formatos de contêiner/arquivo de saída|Media Encoder Standard|Codificador de Mídia do Azure|Fluxo de trabalho do Media Encoder Premium
---|---|---|---
Adobe® Flash® F4V|Não|Não|Sim
MXF (OP1a, XDCAM e AS02)|Não|Não|Sim
DPP (incluindo AS11)|Não|Não|Sim
GXF|Não|Não|Sim
MPEG-4/MP4|Sim|Sim|Sim
MPEG-TS|Sim|Não|Sim
Windows Media/ASF|Não|Sim|Sim
AVI (8 bits/10 bits descompactado)|Não|Não|Sim
Formato de arquivo do Smooth Streaming (PIFF 1.3)|Não|Sim|Sim

###Codecs de vídeo de saída

Codecs de vídeo de saída|Media Encoder Standard|Codificador de Mídia do Azure|Fluxo de trabalho do Media Encoder Premium
---|---|---|---
AVC (H. 264; 8 bits; até perfil, nível elevado 5.2; 4K Ultra HD; Intra AVC)|Somente 8 bits 4:2:0|Apenas 8 bits 4:2:0 até 1080p|Sim
DNxHD ávido (em MXF)|Não|Não|Sim
DVCPro/DVCProHD (em MXF)|Não|Não|Sim
MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10)|Não|Não|Sim
MPEG-1|Não|Não|Sim
Windows Media Video/VC-1|Não|Sim|Sim
Criação de miniaturas JPEG|Não|Sim|Sim

###Codecs de áudio de saída

Codecs de áudio de saída|Media Encoder Standard|Codificador de Mídia do Azure|Fluxo de trabalho do Media Encoder Premium
---|---|---|---
AES (SMPTE 331M e 302M, AES3-2003)|Não|Não|Sim
Dolby® Digital (AC3)|Não|Sim|Sim
Dolby ® Digital Plus (E-AC3) até 7.1|Não|Até 5.1|Sim
AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)|Sim|Sim|Sim
MPEG Layer 2|Não|Não|Sim
MP3 (MPEG-1 Audio Layer 3)|Não|Não|Sim
Áudio do Windows Media|Não|Sim|Sim


##Roteiros de aprendizagem dos Serviços de Mídia

Você pode exibir os roteiros de aprendizagem do AMS aqui:

- [Fluxo de trabalho do streaming ao vivo do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Fluxo de trabalho do streaming sob demanda do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##Artigos relacionados

- [Cotas e limitações](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=Sept15_HO2-->