<properties 
	pageTitle="Fluxo de trabalho de vídeo sob demanda dos serviços de mídia" 
	description="Este tópico descreve as etapas de um fluxo de trabalho de vídeo sob demanda dos serviços de mídia típico." 
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
	ms.date="02/15/2015" 
	ms.author="juliako"/>


#Fluxo de trabalho de vídeo sob demanda dos serviços de mídia

##Visão geral

Este tópico descreve as etapas de um fluxo de trabalho vídeo sob demanda dos serviços de mídia do Azure (AMS) típico. Cada etapa vincula para tópicos relevantes. Para tarefas que podem ser obtidas com o uso de tecnologias diferentes, existem botões que vinculam a tecnologia de sua escolha (por exemplo, .NET ou REST).   

Observe que você pode integrar os serviços de mídia com suas ferramentas e processos existentes. Por exemplo, codificar conteúdo no local e, em seguida, carregá-lo para os Serviços de Mídia para transcodificá-lo em vários formatos e entregá-lo por meio do CDN do Azure ou de terceiros. 

O diagrama a seguir mostra as partes principais da plataforma de serviços de mídia que estão envolvidas no vídeo no fluxo de trabalho sob demanda.
![VoD workflow][vod-overview]

Para obter mais informações, consulte [Visão Geral dos Serviços de Mídia](../media-services-overview).

##Criar uma conta de serviços de mídia

Use o **Portal de Gerenciamento do Azure** para [criar conta de serviços de mídia do Azure](../media-services-create-account/). 

##Configurando pontos de extremidade de streaming

[trabalho em andamento]

##Configurando o ambiente de desenvolvimento  

Escolha **.NET** ou **API REST** para seu ambiente de desenvolvimento.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

##Conectando por meio de programação  

Escolha **.NET** ou **API REST** para conectar-se aos serviços de mídia do Azure por meio de programação.

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

##Carregando de mídia 

Carregue os arquivos usando **Portal de Gerenciamento do Azure**, **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-upload-files](../includes/media-services-selector-upload-files.md)]

##Processamento de mídia: codificação, indexação e monitoramento de trabalhos

###Obtendo o processador de mídia

Obtenha o processador de mídia com **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../includes/media-services-selector-get-media-processor.md)]

###Criando trabalhos 

Um trabalho é uma entidade que contém metadados sobre um conjunto de tarefas (por exemplo, codificação ou indexação). Cada tarefa executa uma operação atômica nos ativos de entrada. Por exemplo, sobre como criar trabalhos de codificação, consulte:

[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

###Monitorando o progresso do trabalho

Monitore o progresso do trabalho usando o **Portal de Gerenciamento do Azure**, **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-job-progress](../includes/media-services-selector-job-progress.md)]

###Indexação

[AZURE.INCLUDE [media-services-selector-index-content](../includes/media-services-selector-index-content.md)]

###Codificação 

Consulte [Codificando com os Serviços de Mídia do Azure](../media-services-encode-asset).

##Configurar a Política de Autorização de Chave de Conteúdo 

Configurar a proteção de conteúdo e a política de autorização de chaves usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

##Configurando a política de fornecimento de ativos

Configurar a política de fornecimento de ativos usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

##Publicando ativos

Publicar ativos (ao criar localizadores) usando o **Portal de Gerenciamento do Azure** ou **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]

##Dimensionamento de uma conta de serviços de mídia

Você pode dimensionar os **Serviços de Mídia** especificando o número de **Unidades Reservadas para Streaming** e **Unidades Reservadas para Codificação** com as quais você deseja provisionar sua conta. 

Você também pode dimensionar sua conta dos Serviços de Mídia adicionando contas de armazenamento a ela. Cada conta de armazenamento é limitada a 500 TB. Para expandir o armazenamento além das limitações padrão, você pode optar por anexar diversas contas de armazenamento a uma única conta de serviços de mídia.

[Esse](../media-services-how-to-scale) tópico fornece link para tópicos relevantes.


##Reprodução de conteúdo

Para obter mais informações, consulte [executar seu conteúdo com jogadores existentes](../media-services-playback-content).

[vod-overview]: ./media/media-services-overview/media-services-video-on-demand.png
<!--HONumber=47-->
