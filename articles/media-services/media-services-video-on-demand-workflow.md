<properties 
	pageTitle="Fornecendo Mídia sob Demanda com os Serviços de Mídia do Azure" 
	description="Este tópico aborda cenários comuns do fornecimento de mídia sob demanda com os Serviços de Mídia do Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
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


#Fornecendo Mídia sob Demanda com os Serviços de Mídia do Azure

##Visão geral

Este tópico descreve as etapas de um fluxo de trabalho vídeo sob demanda dos serviços de mídia do Azure (AMS) típico. Cada etapa vincula para tópicos relevantes. Para tarefas que podem ser obtidas com o uso de tecnologias diferentes, existem botões que vinculam a tecnologia de sua escolha (por exemplo, .NET ou REST).

Observe que você pode integrar os serviços de mídia com suas ferramentas e processos existentes. Por exemplo, codifique conteúdo local e, em seguida, carregue-o para os Serviços de Mídia para transcodificá-lo em vários formatos e entregá-lo por meio da CDN do Azure ou de terceiros.

O diagrama a seguir mostra as partes principais da plataforma de serviços de mídia que estão envolvidas no vídeo no fluxo de trabalho sob demanda.![Fluxo de trabalho VoD][vod-overview]

##<a id="vod_scenarios"></a>Cenários comuns: fornecimento de mídia sob demanda

###Proteja o conteúdo no armazenamento e forneça mídia de streaming sem proteção (não criptografada)

1. Carregar um arquivo mezzanine de alta qualidade em um ativo.
	
	É recomendável aplicar a opção de criptografia de armazenamento a seu ativo para proteger o conteúdo durante o carregamento e enquanto ele estiver em repouso no armazenamento. 
1. Codificar para MP4 definir a taxa de bits adaptável. 

	É recomendável aplicar a opção de criptografia de armazenamento ao ativo de saída para proteger o conteúdo em repouso.
	
1. Configure a política de entrega de ativos (usada pelo empacotamento dinâmico).
	
	Se seu ativo tiver o armazenamento criptografado, você **deverá** configurar a política de entrega de ativos.

1. Publicar o ativo criando um localizador OnDemand.

	Verifique se você tem pelo menos uma unidade reservada de streaming no ponto de extremidade de streaming do qual você deseja transmitir conteúdo.

1. Fluxo de conteúdo publicado.

###Proteger o conteúdo no armazenamento, fornecer mídia de streaming criptografada dinamicamente  

Para poder usar criptografia dinâmica, primeiro é necessário obter pelo menos uma unidade reservada de streaming no ponto de extremidade de streaming do qual você deseja transmitir conteúdo criptografado.

1. Carregue um arquivo mezanino de alta qualidade em um ativo. Aplique a opção de criptografia de armazenamento ao ativo.
1. Codifique para conjunto MP4 de taxa de bits adaptável. Aplique a opção de criptografia de armazenamento ao ativo de saída.
1. Crie uma chave de conteúdo de criptografia para o ativo que você quer que seja criptografado dinamicamente durante a reprodução.
2. Configure a política de autorização de chave de conteúdo.
1. Configure a política de entrega de ativos (usada pelo empacotamento dinâmico e criptografia dinâmica).
1. Publique o ativo, criando um localizador OnDemand.
1. Fluxo de conteúdo publicado. 

###Conteúdo do índice

1. Carregar um arquivo mezzanine de alta qualidade em um Ativo.
1. Conteúdo do índice.

	O trabalho de indexação gera arquivos que podem ser usados como CC (legenda oculta) na reprodução de vídeo. Ele também gera arquivos que o habilitam a fazer pesquisa em vídeo e saltar para o local exato do vídeo.

1. Consumir conteúdo indexado.


###Entregar o download progressivo 

1. Carregar um arquivo mezzanine de alta qualidade em um ativo.
1. Codificar para conjunto de MP4 com taxa de bits adaptável ou para um único MP4.
1. Publicar o ativo criando um localizador OnDemand ou SAS

	Se estiver usando o localizador OnDemand, verifique se você tem pelo menos uma unidade reservada de streaming no ponto de extremidade de streaming do qual você planeja baixar conteúdo de forma progressiva.

	Se você estiver usando o localizador de SAS, o conteúdo será baixado do armazenamento de blob do Azure. Nesse caso, não é necessário ter unidades reservadas de streaming.
  
1. Download progressivo de conteúdo.

Este artigo contém links para tópicos que mostram como configurar seu ambiente de desenvolvimento e executar as tarefas mencionadas acima.


##Conceitos

Para obter conceitos relacionados ao fornecimento de conteúdo sob demanda, veja [Conceitos dos Serviços de Mídia](media-services-concepts.md) (players.md).

##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
 

<!---HONumber=Nov15_HO3-->