<properties
	pageTitle="Visão geral do empacotamento dinâmico | Microsoft Azure"
	description="O tópico apresenta uma visão geral do empacotamento dinâmico."
	authors="Juliako"
	manager="erikre"
	editor=""
	services="media-services"
	documentationCenter=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016" 
	ms.author="juliako"/>


#Empacotamento dinâmico

##Visão geral

Os Serviços de Mídia do Microsoft Azure podem ser usados para fornecer vários formatos de arquivos de mídia de origem, formatos de streaming de mídia e formatos de proteção de conteúdo para uma variedade de tecnologias de cliente (por exemplo, iOS, XBOX, Silverlight, Windows 8). Esses clientes entendem protocolos diferentes, por exemplo: o iOS requer um formato HTTP Live Streaming (HLS) V4, enquanto Silverlight e Xbox requerem Smooth Streaming. Se você tiver um conjunto de arquivos MP4 (mídia base ISO 14496-12) de taxa de bits adaptável (múltiplas taxas de bits), ou um conjunto de arquivos Smooth Streaming de taxa de bits adaptável que você deseja fornecer a clientes que entendam MPEG DASH, HLS ou Smooth Streaming, você deve tirar proveito do empacotamento dinâmico dos Serviços de Mídia.

Com o empacotamento dinâmico, tudo o que você precisa é criar um ativo que contenha um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos de Smooth Streaming de taxa de bits adaptável. Em seguida, com base no formato especificado na solicitação de fragmento ou manifesto, o servidor de Streaming OnDemand garantirá que você receba o fluxo no protocolo escolhido por você. Como resultado você só precisa armazenar e pagar pelos arquivos em um único formato de armazenamento, e os Serviços de Mídia vão criar e fornecer a resposta apropriada com base nas solicitações de um cliente.

O diagrama a seguir mostra a codificação tradicional e o fluxo de trabalho de empacotamento estático.

![Codificação estática](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

O diagrama a seguir mostra o fluxo de trabalho de empacotamento dinâmico.

![Codificação dinâmica](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]Para tirar proveito do empacotamento dinâmico, você precisa obter primeiro pelo menos uma unidade de streaming OnDemand para o ponto de extremidade de streaming por meio do qual você planeja fornecer seu conteúdo. Para obter mais informações, consulte [Como dimensionar os Serviços de Mídia](media-services-portal-manage-streaming-endpoints.md).

##Cenário comum

1. Carrega um arquivo de entrada (chamado de arquivo de mezanino). Por exemplo, H.264, MP4 ou WMV (para obter a lista de formatos com suporte, consulte [Formatos com suporte do Codificador de Mídia Padrão](media-services-media-encoder-standard-formats.md)).

1. Codifique o arquivo de mezanino para conjuntos de taxa de bits adaptável MP4 H.264.

1. Publique o ativo que contém a taxa de bits adaptável MP4 definida ao criar o localizador OnDemand.

1. Crie as URLs de streaming para acessar e transmitir seu conteúdo.


##Preparação de ativos para streaming dinâmico

Para preparar o ativo de streaming dinâmico você tem duas opções:

1. [Carregue um arquivo mestre](media-services-dotnet-upload-files.md).
2. [Use o Codificador de Mídia Padrão para produzir conjuntos de taxa de bits adaptáveis MP4 H.264](media-services-dotnet-encode-with-media-encoder-standard.md).
3. [Transmita seu conteúdo](media-services-deliver-content-overview.md).

-OU-

1. Carregue os arquivos MP4 pré-codificados.

>[AZURE.NOTE] Essa opção não é recomendada.

2. [Valide seus arquivos pré-codificados](media-services-static-packaging.md#validating-adaptive-bitrate-mp4s-encoded-with-external-encoders).
3. [Transmita seu conteúdo](media-services-deliver-content-overview.md).


##<a id="unsupported_formats"></a>Formatos sem suporte pelo empacotamento dinâmico

Os formatos de arquivo de origem a seguir não têm suporte pelo empacotamento dinâmico.

- Arquivos mp4 Dolby digital.
- Arquivos suaves Dolby digital.

##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0921_2016-->