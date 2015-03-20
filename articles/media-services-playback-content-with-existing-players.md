<properties 
	pageTitle="Reprodução de conteúdo" 
	description="Este tópico descreve como reproduzir o conteúdo." 
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
	ms.date="02/16/2015" 
	ms.author="juliako"/>


#Reprodução de conteúdo

Os serviços de mídia do Microsoft Azure suporta muitos formatos populares de streaming, como Smooth Streaming, HTTP Live Streaming e MPEG-Dash. Este tópico aponta para players existentes que você pode usar para testar os fluxos.  

##Reprodução de seu conteúdo com players existentes

Este tópico mostra os jogadores existentes com os quais você pode reproduzir o conteúdo.

>[AZURE.NOTE]Para reproduzir conteúdo dinamicamente empacotado ou criptografado dinamicamente, certifique-se de obter pelo menos uma unidade de transmissão para o ponto de extremidade de streaming por meio do qual você planeja distribuir seu conteúdo. Para obter informações sobre dimensionamento de unidades de streaming, consulte: [Como dimensionar unidades de streaming](../media-services-manage-origins#scale_streaming_endpoints).


###Player de conteúdo do Portal de Gerenciamento dos Serviços de Mídia do Azure

O **Portal de Gerenciamento** fornece um player de conteúdo que você pode usar para testar o vídeo.

Clique no vídeo desejado (Verifique se ele foi [publicado](../media-services-manage-content#publish)) e clique no botão **Reproduzir** na parte inferior do portal. 
 
Algumas considerações se aplicam:

- O **PLAYER DE CONTEÚDO DOS SERVIÇOS DE MÍDIA** é reproduzido por meio do ponto de extremidade de streaming padrão. Se você deseja reproduzir por meio de um ponto de extremidade de streaming não padrão, use outro reprodutor. Por exemplo, o [Reprodutor dos Serviços de Mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
 

![AMSPlayer][AMSPlayer]

###Player dos Serviços de Mídia do Azure

Use o [Player dos Serviços de Mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para reproduzir seu conteúdo.


##Desenvolvendo players de vídeo

Para obter informações sobre como desenvolver seus próprios players, consulte [Desenvolvendo players de vídeo](../media-services-develop-video-players)
 
[AMSPlayer]: ./media/media-services-players/media-services-portal-player.png
<!--HONumber=47-->
