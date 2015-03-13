<properties 
	pageTitle="Como criptografar ativos em Serviços de Mídia - Azure" 
	description="Saiba como usar a Proteção do Microsoft PlayReady para criptografar um ativo nos Serviços de Mídia. Os exemplos de código são escritos em C# e usam a SDK dos Serviços de Mídia para .NET. Os exemplos de código são escritos em C# e usam a SDK dos Serviços de Mídia para .NET." 
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


# Como: Criptografar dinamicamente um ativo com PlayReady ou AES-128

Este artigo faz parte das séries do [vídeo de serviços de mídia no fluxo de trabalho sob demanda](../media-services-video-on-demand-workflow) and [Media Services Live Streaming workflow](../media-services-live-streaming-workflow) .
  
## Visão geral

Os serviços de mídia do Microsoft Azure permitem distribuir o conteúdo criptografado (dinamicamente) com criptografia AES (padrão avançado) (usando chaves de criptografia de 128 bits) e PlayReady DRM. Os serviços de mídia também fornecem um serviço de distribuição de chaves e licenças do PlayReady aos clientes autorizados. Para entregar conteúdo protegido, você precisa configurar a política de autorização de chave de conte. protegido.

## Configurar

Para obter informações sobre como configurar a política de autorização de chave de conteúdo 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)] 

Configurar políticas de entrega de ativos
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]
 
<!--HONumber=45--> 
