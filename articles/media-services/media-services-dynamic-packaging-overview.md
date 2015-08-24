<properties 
	pageTitle="Visão geral do empacotamento dinâmico" 
	description="O tópico apresenta uma visão geral do empacotamento dinâmico." 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015"  
	ms.author="juliako"/>


#Empacotamento dinâmico 

##Visão geral

Os Serviços de Mídia do Microsoft Azure podem ser usados para fornecer vários formatos de arquivos de mídia de origem, formatos de streaming de mídia e formatos de proteção de conteúdo para uma variedade de tecnologias de cliente (por exemplo, iOS, XBOX, Silverlight, Windows 8). Esses clientes entendem protocolos diferentes, por exemplo: o iOS requer um formato HTTP Live Streaming (HLS) V4, enquanto Silverlight e Xbox requerem Smooth Streaming. Se você tiver um conjunto de arquivos MP4 (mídia base ISO 14496-12) de taxa de bits adaptável (múltiplas taxas de bits), ou um conjunto de arquivos Smooth Streaming de taxa de bits adaptável que você deseja fornecer a clientes que entendam MPEG DASH, HLS ou Smooth Streaming, você deve tirar proveito do empacotamento dinâmico dos Serviços de Mídia.

Com o empacotamento dinâmico, tudo o que você precisa é criar um ativo que contenha um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos de Smooth Streaming de taxa de bits adaptável. Em seguida, com base no formato especificado na solicitação de fragmento ou manifesto, o servidor de Streaming OnDemand garantirá que você receba o fluxo no protocolo escolhido por você. Como resultado você só precisa armazenar e pagar pelos arquivos em um único formato de armazenamento, e os Serviços de Mídia vão criar e fornecer a resposta apropriada com base nas solicitações de um cliente.

O diagrama a seguir mostra a codificação tradicional e o fluxo de trabalho de empacotamento estático.

![Codificação estática](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

O diagrama a seguir mostra o fluxo de trabalho de empacotamento dinâmico.

![Codificação dinâmica](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]Para tirar proveito do empacotamento dinâmico, você precisa obter primeiro pelo menos uma unidade de streaming OnDemand para o ponto de extremidade de streaming por meio do qual você planeja fornecer seu conteúdo. Para obter mais informações, consulte [Como dimensionar os Serviços de Mídia](media-services-manage-origins.md#scale_streaming_endpoints).

##Cenário comum

1. Carrega um arquivo de entrada (chamado de arquivo de mezanino). Por exemplo, H.264, MP4 ou WMV (para obter a lista de formatos com suporte, consulte Formatos com suporte pelo codificador dos Serviços de Mídia).
 
1. Codifique o arquivo de mezanino para conjuntos de taxa de bits adaptável MP4 H.264.
 
1. Publique o ativo que contém a taxa de bits adaptável MP4 definida ao criar o localizador OnDemand.
 
1. Crie as URLs de streaming para acessar e transmitir seu conteúdo.
 
>[AZURE.NOTE]Nem todos os formatos de arquivo MP4 têm suporte pelo empacotamento dinâmico; para obter mais informações, consulte [Formatos para empacotamento dinâmico sem suporte](media-services-dynamic-packaging-overview.md#unsupported_formats).

##Preparação de ativos para streaming dinâmico

Para preparar o ativo de streaming dinâmico você tem duas opções:

- Carregue um arquivo mestre e produza conjuntos de taxa de bits adaptável MP4 H.264 usando o Codificador de Mídia do Azure.
- Carregar conjuntos de taxa de bits adaptável existente e validá-los usando o Empacotador de Mídia.

###Carregue um arquivo mestre e produza conjuntos de taxa de bits adaptável MP4 H.264 usando o Codificador de Mídia do Azure

Para obter informações sobre como carregar e codificar ativos, consulte os seguintes artigos:


Carregue seus arquivos usando **Portal de Gerenciamento do Azure**, **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

Codifique-os com o **Codificador de Mídia do Azure** usando o **Portal de Gerenciamento**, o **.NET** ou **API REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]


###Carregar conjuntos de taxa de bits adaptável existente e validá-los usando o Empacotador de Mídia

Normalmente, você desejaria executar essa tarefa se estiver carregando um conjunto de arquivos MP4 com taxa de bits adaptável, que não tenham sido codificados com o Codificador dos Serviços de Mídia. O tópico [Validando MP4s com taxa de bits adaptável codificados com codificadores externos](https://msdn.microsoft.com/library/azure/dn750842.aspx) mostra como realizar essa tarefa.

##Transmissão de seu conteúdo para clientes

Uma vez que você tenha os conjuntos com taxa de bits adaptável, você pode publicar seu ativo criando um localizador sob demanda e compor as URLs de streaming para Smooth Streaming, MPEG DASH, HLS e HDS (apenas para licenciados Adobe PrimeTime/Access).

Para obter informações sobre como criar localizadores e usar o empacotamento dinâmico para transmitir o conteúdo, consulte os tópicos a seguir:

[Visão geral do fornecimento de conteúdo a clientes](media-services-deliver-content-overview.md).

Configure a política de fornecimento de ativos usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Publicar ativos (pela criação de localizadores) usando o **Portal de Gerenciamento do Azure** ou **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


##<a id="unsupported_formats"></a>Formatos sem suporte pelo empacotamento dinâmico

Os formatos de arquivo de origem a seguir não têm suporte pelo empacotamento dinâmico.

- Arquivos mp4 Dolby digital plus.
- Dolby digital mais arquivos smooth. 

<!---HONumber=August15_HO7-->