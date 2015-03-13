<properties 
	pageTitle="Fluxo de trabalho da transmissão ao vivo dos serviços de mídia" 
	description="Este tópico descreve as etapas de um fluxo de trabalho da transmissão ao vivo dos serviços de mídia típico." 
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


# Fluxo de trabalho da transmissão ao vivo dos serviços de mídia

## Visão geral

Este tópico descreve as etapas de um fluxo de trabalho da transmissão ao vivo dos serviços de mídia do Azure (AMS) típico. Cada etapa vincula para tópicos relevantes. Para tarefas que podem ser obtidas com o uso de tecnologias diferentes, existem botões que vinculam a tecnologia de sua escolha (por exemplo, .NET ou REST).   

Observe que você pode integrar os serviços de mídia com suas ferramentas e processos existentes. Por exemplo, codificar conteúdo no local e, em seguida, carregá-lo para os Serviços de Mídia para transcodificá-lo em vários formatos e entregá-lo por meio do CDN do Azure ou de terceiros. 

O diagrama a seguir mostra as partes principais da plataforma de serviços de mídia que estão envolvidas no vídeo no fluxo de trabalho sob demanda.
![Live workflow][live-overview]

## Criar uma conta de serviços de mídia

Use o **Portal de Gerenciamento do Azure** para [criar conta de serviços de mídia do Azure](../media-services-create-account/).

## Configurando o ambiente de desenvolvimento  

Escolha **.NET** ou **API REST** para seu ambiente de desenvolvimento.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

## Conectando por meio de programação  

Escolha **.NET** ou **API REST** para conectar-se aos serviços de mídia do Azure por meio de programação.

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

## Trabalhando com transcodificadores ao vivo

Para obter mais informações, consulte [Usando 3ª parte dos codificadores ao vivo com os serviços de mídia do Azure](https://msdn.microsoft.com/pt-br/library/azure/dn783464.aspx).

## Gerenciar canais, programas, ativos

Para obter mais informações, consulte [Transmissão ao vivo](https://msdn.microsoft.com/pt-br/library/azure/dn783466.aspx).

## Configurar a Política de Autorização de Chave de Conteúdo 

Configurar a política de autorização de chave usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

## Configurando a política de fornecimento de ativos

Configurar a política de fornecimento de ativos usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

## Publicando ativos

Publicar ativos (ao criar localizadores) usando o **Portal de Gerenciamento do Azure** ou **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


## Dimensionamento de uma conta de serviços de mídia

Você pode dimensionar os **serviços de mídia** especificando o número de **unidades reservadas de Streaming**, você gostaria que sua conta fosse provisionada. 

Para obter informações sobre unidades de streaming de escala, consulte: [Como dimensionar unidades de streaming](../media-services-manage-origins#scale_streaming_endpoints).



[live-overview]: ./media/media-services-overview/media-services-live-streaming-current.png
<!--HONumber=45--> 
