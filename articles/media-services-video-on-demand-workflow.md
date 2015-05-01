<properties 
	pageTitle="Fornecendo mídia sob demanda com os Serviços de Mídia do Azure" 
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
	ms.date="03/10/2015" 
	ms.author="juliako"/>


#Fornecendo mídia sob demanda com os Serviços de Mídia do Azure

##Visão geral

Este tópico descreve as etapas de um fluxo de trabalho vídeo sob demanda dos serviços de mídia do Azure (AMS) típico. Cada etapa vincula para tópicos relevantes. Para tarefas que podem ser obtidas com o uso de tecnologias diferentes, existem botões que vinculam a tecnologia de sua escolha (por exemplo, .NET ou REST).   

Observe que você pode integrar os serviços de mídia com suas ferramentas e processos existentes. Por exemplo, codifique conteúdo local e, em seguida, carregue-o para os Serviços de Mídia para transcodificá-lo em vários formatos e entregá-lo por meio da CDN do Azure ou de terceiros. 

O diagrama a seguir mostra as partes principais da plataforma de serviços de mídia que estão envolvidas no vídeo no fluxo de trabalho sob demanda.
![Fluxo de trabalho VoD][vod-overview]

##<a id="vod_scenarios"></a>Cenários comuns: fornecimento de mídia sob demanda. 

###Proteja o conteúdo no armazenamento e forneça mídia de streaming sem proteção (não criptografada)

1. Carregar um arquivo mezzanine de alta qualidade em um ativo.
	
	É recomendável aplicar a opção de criptografia de armazenamento a seu ativo para proteger o conteúdo durante o carregamento e enquanto ele estiver em repouso no armazenamento. 
1. Codificar para MP4 definir a taxa de bits adaptável. 

	É recomendável aplicar a opção de criptografia de armazenamento ao ativo de saída para proteger o conteúdo em repouso.
	
1. Configure a política de entrega de ativos (usada pelo empacotamento dinâmico). 
	
	Se seu ativo tiver o armazenamento criptografado, você **deverá** configurar a política de entrega de ativos. 

1. Publique o ativo criando um localizador OnDemand.

	Verifique se você tem pelo menos uma unidade reservada de streaming no ponto de extremidade de streaming do qual você deseja transmitir conteúdo.

1. Fluxo de conteúdo publicado.

###Proteger o conteúdo no armazenamento, fornecer mídia de streaming criptografada dinamicamente  

Para poder usar criptografia dinâmica, primeiro é necessário obter pelo menos uma unidade reservada de streaming no ponto de extremidade de streaming do qual você deseja transmitir conteúdo criptografado.

1. Carregue um arquivo mezzanine de alta qualidade em um ativo. Aplique a opção de criptografia de armazenamento ao ativo.
1. Codifique para MP4 definir a taxa de bits adaptável. Aplique a opção de criptografia de armazenamento ao ativo de saída.
1. Crie uma chave de criptografia de conteúdo para o ativo que você deseja que seja criptografado dinamicamente durante a reprodução.
2. Configurar a política de autorização de chave de conteúdo
1. Configure a política de entrega de ativos (usada pelo empacotamento dinâmico e pela criptografia dinâmica).
1. Publique o ativo criando um localizador OnDemand.
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

Para conceitos relacionados ao fornecimento de conteúdo sob demanda, consulte [Conceitos dos Serviços de Mídia](media-services-concepts.md).

##Criar uma conta de serviços de mídia

Use o **Portal de Gerenciamento do Azure** para [criar conta de serviços de mídia do Azure](media-services-create-account.md). 

##Configurando o ambiente de desenvolvimento  

Escolha **.NET** ou **API REST** para seu ambiente de desenvolvimento.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

##Conectando por meio de programação  

Escolha **.NET** ou **API REST** para conectar-se aos serviços de mídia do Azure por meio de programação.

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]


##Configurando pontos de extremidade de streaming

Para obter uma visão geral sobre pontos de extremidade de streaming e obter informações sobre como gerenciá-los, consulte [Como gerenciar pontos de extremidade de streaming em uma conta dos Serviços de Mídia](media-services-manage-origins.md).

##Carregando de mídia 

Carregue os arquivos usando **Portal de Gerenciamento do Azure**, **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-upload-files](../includes/media-services-selector-upload-files.md)]

##Criando trabalhos\tarefas

Um trabalho é uma entidade que contém metadados sobre um conjunto de tarefas (por exemplo, codificação ou indexação). Cada tarefa executa uma operação atômica nos ativos de entrada. Por exemplo, sobre como criar trabalhos de codificação, consulte:

Para obter uma visão geral, consulte [Trabalhar com trabalhos de Serviços de Mídia do Azure](media-services-jobs.md).

Obtenha um processador de mídia apropriado para sua tarefa com **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../includes/media-services-selector-get-media-processor.md)]

Os exemplos a seguir criam trabalhos de codificação com o **Portal de Gerenciamento do Azure**, o **.NET** ou a **API REST**.

[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

##Indexação

[AZURE.INCLUDE [media-services-selector-index-content](../includes/media-services-selector-index-content.md)]

##Codificação 

**Visão geral**: [Codificando conteúdo sob demanda com os Serviços de Mídia do Azure](media-services-encode-asset.md).

Codificação avançada com o **Fluxo de Trabalho do Media Encoder Premium** usando **.NET**. 

[AZURE.INCLUDE [media-services-selector-advanced-encoding](../includes/media-services-selector-advanced-encoding.md)]

Codificar com **Azure Media Encoder** usando **Portal de gerenciamento**, **.NET** ou **API REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

##Monitorando o progresso do trabalho

Monitore o progresso do trabalho usando o **Portal de Gerenciamento do Azure**, **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-job-progress](../includes/media-services-selector-job-progress.md)]

##Criando uma chave de conteúdo

Crie uma chave de conteúdo com a qual você deseja criptografar seu ativo usando o **.NET** ou a **API REST**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../includes/media-services-selector-create-contentkey.md)]

##Configurando a política de autorização de chave de conteúdo 

Configurar a proteção de conteúdo e a política de autorização de chaves usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

##Configurando a política de fornecimento de ativos

Configurar a política de fornecimento de ativos usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

##Publicando e fornecendo ativos

**Visão geral**: [Visão geral do fornecimento de conteúdo](media-services-deliver-content-overview.md)


Publicar ativos (ao criar localizadores) usando o **Portal de Gerenciamento do Azure** ou **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]

##Habilitando a CDN do Azure

Os Serviços de Mídia dão suporte à integração com a CDN do Azure. Para obter informações sobre como habilitar a CDN do Azure, consulte [Como gerenciar pontos de extremidade de streaming em uma conta dos Serviços de Mídia](media-services-manage-origins.md#enable_cdn).

##Dimensionamento de uma conta de serviços de mídia

Você pode dimensionar os **Serviços de Mídia** especificando o número de **Unidades Reservadas para Streaming** e **Unidades Reservadas para Codificação** com as quais você deseja provisionar sua conta. 

Você também pode dimensionar sua conta dos Serviços de Mídia adicionando contas de armazenamento a ela. Cada conta de armazenamento é limitada a 500 TB. Para expandir o armazenamento além das limitações padrão, você pode optar por anexar diversas contas de armazenamento a uma única conta de serviços de mídia.

[Esse](media-services-how-to-scale.md) tópico fornece link para tópicos relevantes.

##Reprodução de conteúdo com jogadores existentes

Para obter mais informações, consulte [executar seu conteúdo com jogadores existentes](media-services-playback-content-with-existing-players.md).


[vod-overview]: ./media/media-services-overview/media-services-video-on-demand.png


<!--HONumber=52-->