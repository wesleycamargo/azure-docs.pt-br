<properties 
	pageTitle="Reprodução de conteúdo" 
	description="Este tópico lista os players existentes que você pode usar para reproduzir conteúdo." 
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
	ms.date="04/08/2015" 
	ms.author="juliako"/>


# Reprodução de seu conteúdo com players existentes

Os serviços de mídia do Microsoft Azure suporta muitos formatos populares de streaming, como Smooth Streaming, HTTP Live Streaming e MPEG-Dash. Este tópico aponta para players existentes que você pode usar para testar os fluxos.  

>[AZURE.NOTE]Para reproduzir conteúdo dinamicamente empacotado ou criptografado dinamicamente, certifique-se de obter pelo menos uma unidade de transmissão para o ponto de extremidade de streaming por meio do qual você planeja distribuir seu conteúdo. Para obter informações sobre dimensionamento de unidades de streaming, consulte: [Como dimensionar unidades de streaming](media-services-manage-origins.md#scale_streaming_endpoints).

### Player de conteúdo do Portal de Gerenciamento dos Serviços de Mídia do Azure

O **Portal de Gerenciamento** fornece um player de conteúdo que você pode usar para testar o vídeo.

Clique no vídeo desejado (verifique se ele foi [publicado](media-services-manage-content.md#publish)) e clique no botão **Reproduzir** na parte inferior do portal. 
 
Algumas considerações se aplicam:

- O **PLAYER DE CONTEÚDO DOS SERVIÇOS DE MÍDIA** é reproduzido por meio do ponto de extremidade de streaming padrão. Se você deseja reproduzir por meio de um ponto de extremidade de streaming não padrão, use outro reprodutor. Por exemplo, o [Reprodutor dos Serviços de Mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
 

![AMSPlayer][AMSPlayer]

### Player dos Serviços de Mídia do Azure

Use o [Player dos Serviços de Mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para reproduzir conteúdo (protegido ou não) em qualquer um dos seguintes formatos:

- Smooth Streaming
- MPEG DASH
- HLS
- MP4 progressivo


### Flash Player

#### Criptografado com AES com token 

[http://aestoken.azurewebsites.net]("http://aestoken.azurewebsites.net)

### Players Silverlight

#### Monitoramento

[http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)

#### PlayReady com token

[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### Players DASH

[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

### Outros

Para testar as URLs de HLS, você também pode usar:

- **Safari** em um dispositivo iOS ou
- **Player 3ivx HLS** no Windows.

## Desenvolvendo players de vídeo

Para obter informações sobre como desenvolver seus próprios players, consulte [Desenvolvendo players de vídeo](media-services-develop-video-players.md)
 
[AMSPlayer]: ./media/media-services-players/media-services-portal-player.png

<!--HONumber=52-->