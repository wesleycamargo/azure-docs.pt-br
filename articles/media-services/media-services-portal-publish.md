<properties
	pageTitle=" Publicar conteúdo com o portal do Azure | Microsoft Azure"
	description="Este tutorial orienta você pelas etapas de publicar o conteúdo com o portal do Azure."
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/29/2016"
	ms.author="juliako"/>

# Publicar conteúdo com o portal do Azure

> [AZURE.SELECTOR]
- [Portal](media-services-portal-publish.md)
- [.NET](media-services-deliver-streaming-content.md)
- [REST](media-services-rest-deliver-streaming-content.md)

## Visão geral

> [AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

Para fornecer a seus usuários uma URL que pode ser usada para transmitir ou baixar seu conteúdo, primeiro você precisa "publicar" o ativo criando um localizador. Os localizadores fornecem acesso aos arquivos contidos no ativo. Os Serviços de Mídia oferecem suporte a dois tipos de localizadores:

- Localizadores de transmissão (OnDemandOrigin), usados para a transmissão adaptável (por exemplo, para transmitir MPEG DASH, HLS ou Smooth Streaming). Para criar um localizador de transmissão, seu ativo deve conter um arquivo .ism.
- Localizadores progressivos (SAS), usados para a entrega de vídeo por meio do download progressivo.


Uma URL de streaming tem o formato a seguir e você pode usá-la para reproduzir ativos de Smooth Streaming.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para criar uma URL de streaming de HLS, anexe (format=m3u8-aapl) à URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para criar uma URL de streaming MPEG DASH, anexe (format=mpd-time-csf) à URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Uma URL SAS tem o seguinte formato.

	{blob container name}/{asset name}/{file name}/{SAS signature}

Para obter mais informações, consulte [Visão geral sobre fornecimento de conteúdo](media-services-deliver-content-overview.md).

>[AZURE.NOTE] Se você usou o portal para criar localizadores antes de março de 2015, foram criados localizadores com uma data de validade de dois anos.

Para atualizar uma data de validade em um localizador, use as APIs [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Observe que, quando você atualiza a data de validade de um localizador SAS, a URL é alterada.

### Para usar o portal para publicar um ativo

Para usar o portal para publicar um ativo, faça o seguinte:

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
1. Selecione **Configurações** > **Ativos**.
1. Selecione o ativo que você deseja publicar.
1. Clique no botão **Publicar**.
1. Selecione o tipo de localizador.
2. Pressione **Adicionar**.

	![Publicar](./media/media-services-portal-vod-get-started/media-services-publish1.png)

A URL será adicionada à lista de **URLs Publicadas**.

## Reproduzir conteúdo do portal

O portal do Azure fornece um player de conteúdo que você pode usar para testar o vídeo.

Clique no vídeo desejado e clique no botão **Reproduzir**.

![Publicar](./media/media-services-portal-vod-get-started/media-services-play.png)

Algumas considerações se aplicam:

- Verifique se que o vídeo foi publicado.
- Esse **Player de Mídia*** reproduz do ponto de extremidade de streaming padrão. Se você quiser reproduzir a partir de um ponto de extremidade de streaming não padrão, clique para copiar a URL e use outra reprodução. Por exemplo, o [Player dos Serviços de Mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
- O ponto de extremidade de streaming do qual você estiver transmitindo deverá estar em execução.
- Para transmitir de um ponto de extremidade de streaming, você deve adicionar pelo menos uma unidade de streaming. Para obter mais informações, consulte [este](media-services-portal-scale-streaming-endpoints.md) tópico.

##Próximas etapas

Examine os roteiros de aprendizagem dos Serviços de Mídia.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0831_2016-->