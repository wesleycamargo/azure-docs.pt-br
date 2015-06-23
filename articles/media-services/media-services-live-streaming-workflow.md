<properties 
	pageTitle="Fornecendo mídia sob demanda com os Serviços de Mídia do Azure" 
	description="Este tópico descreve as etapas de um fluxo de trabalho da transmissão ao vivo dos serviços de mídia típico." 
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

Este tópico descreve as etapas de um fluxo de trabalho da transmissão ao vivo dos serviços de mídia do Azure (AMS) típico. Cada etapa vincula para tópicos relevantes. Para tarefas que podem ser obtidas com o uso de tecnologias diferentes, existem botões que vinculam a tecnologia de sua escolha (por exemplo, .NET ou REST).   

Observe que você pode integrar os serviços de mídia com suas ferramentas e processos existentes. Por exemplo, codificar conteúdo no local e, em seguida, carregá-lo para os Serviços de Mídia para transcodificá-lo em vários formatos e entregá-lo por meio do CDN do Azure ou de terceiros. 

O diagrama a seguir mostra as partes principais da plataforma de serviços de mídia que estão envolvidas no fluxo de trabalho de trabalho de transmissão ao vivo.

![Fluxo de trabalho ao vivo][live-overview]

Este tópico descreve os conceitos relacionados à transmissão ao vivo e fornece links para tópicos que demonstram como realizar tarefas de transmissão ao vivo.

##Conceitos

Para conceitos relacionados à transmissão ao vivo, consulte [Conceitos dos Serviços de Mídia](media-services-concepts.md).

##Criar uma conta de serviços de mídia

Use o **Portal de Gerenciamento do Azure** para [Criar uma conta de Serviços de Mídia do Azure](media-services-create-account.md).

##Configurando pontos de extremidade de streaming

Para obter uma visão geral sobre pontos de extremidade de streaming e obter informações sobre como gerenciá-los, consulte [Como gerenciar pontos de extremidade de streaming em uma conta dos Serviços de Mídia](media-services-manage-origins.md)

##Configurando o ambiente de desenvolvimento  

Escolha **.NET** ou **API REST** para seu ambiente de desenvolvimento.

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

##Conectando por meio de programação  

Escolha **.NET** ou **API REST** para conectar-se aos serviços de mídia do Azure por meio de programação.

[AZURE.INCLUDE [media-services-selector-connect](../../includes/media-services-selector-connect.md)]


##Usando codificadores ao vivo locais para gerar um fluxo com múltiplas taxas de bits para um canal

##Trabalhando com transcodificadores ao vivo de terceiros

Para obter mais informações, consulte [Usando 3ª parte dos codificadores ao vivo com os serviços de mídia do Azure](https://msdn.microsoft.com/library/azure/dn783464.aspx).

##Gerenciar canais, programas, ativos

**Visão geral**: [Visão geral sobre gerenciamento de canais e programas](media-services-manage-channels-overview.md).

Escolha **Portal**, **.NET**, **API REST** para ver exemplos.

[AZURE.INCLUDE [media-services-selector-manage-channels](../../includes/media-services-selector-manage-channels.md)]

##Configurando a política de fornecimento de ativos

Configurar a política de fornecimento de ativos usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

##Criando uma chave de conteúdo

Crie uma chave de conteúdo com a qual você deseja criptografar seu ativo usando o **.NET** ou a **API REST**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

##Configurando a política de autorização de chave de conteúdo 

Configurar a proteção de conteúdo e a política de autorização de chaves usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


##Publicando e fornecendo ativos

**Visão geral**: [Visão geral do fornecimento de conteúdo](media-services-deliver-content-overview.md)

Publicar ativos (ao criar localizadores) usando o **Portal de Gerenciamento do Azure** ou **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


##Habilitando a CDN do Azure

Os Serviços de Mídia dão suporte à integração com a CDN do Azure. Para obter informações sobre como habilitar a CDN do Azure, consulte [Como gerenciar pontos de extremidade de streaming em uma conta dos Serviços de Mídia](media-services-manage-origins.md#enable_cdn).

##Dimensionamento de uma conta de serviços de mídia

Você pode dimensionar os **serviços de mídia** especificando o número de **unidades reservadas de Streaming**, você gostaria que sua conta fosse provisionada. 

Para obter informações sobre dimensionamento de unidades de streaming, consulte: [Como dimensionar unidades de streaming](media-services-manage-origins.md#scale_streaming_endpoints.md).




[live-overview]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png


<!--HONumber=52--> 