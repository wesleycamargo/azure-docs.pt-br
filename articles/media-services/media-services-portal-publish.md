---
title: "  Publicar conteúdo com o Portal do Azure | Microsoft Docscs"
description: "Este tutorial orienta você pelas etapas de publicar o conteúdo com o portal do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: 72b0b7feef583799613b1cb2c18b2020c1401b36


---
# <a name="publish-content-with-the-azure-portal"></a>Publicar conteúdo com o portal do Azure
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>Visão geral
> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Para fornecer a seus usuários uma URL que pode ser usada para transmitir ou baixar seu conteúdo, primeiro você precisa "publicar" o ativo criando um localizador. Os localizadores fornecem acesso aos arquivos contidos no ativo. Os Serviços de Mídia oferecem suporte a dois tipos de localizadores: 

* Localizadores de transmissão (OnDemandOrigin), usados para a transmissão adaptável (por exemplo, para transmitir MPEG DASH, HLS ou Smooth Streaming). Para criar um localizador de transmissão, seu ativo deve conter um arquivo .ism. 
* Localizadores progressivos (SAS), usados para a entrega de vídeo por meio do download progressivo.

Uma URL de streaming tem o formato a seguir e você pode usá-la para reproduzir ativos de Smooth Streaming.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para criar uma URL de streaming de HLS, anexe (format=m3u8-aapl) à URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para criar uma URL de streaming MPEG DASH, anexe (format=mpd-time-csf) à URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Uma URL SAS tem o seguinte formato.

    {blob container name}/{asset name}/{file name}/{SAS signature}

Para obter mais informações, consulte [Visão geral sobre fornecimento de conteúdo](media-services-deliver-content-overview.md).

> [!NOTE]
> Se você usou o portal para criar localizadores antes de março de 2015, foram criados localizadores com uma data de validade de dois anos.  
> 
> 

Para atualizar uma data de validade em um localizador, use as APIs [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Observe que, quando você atualiza a data de validade de um localizador SAS, a URL é alterada.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Para usar o portal para publicar um ativo
Para usar o portal para publicar um ativo, faça o seguinte:

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia do Azure.
2. Selecione **Configurações** > **Ativos**.
3. Selecione o ativo que você deseja publicar.
4. Clique no botão **Publicar** .
5. Selecione o tipo de localizador.
6. Pressione **Adicionar**.
   
    ![Publicar](./media/media-services-portal-vod-get-started/media-services-publish1.png)

A URL será adicionada à lista de **URLs Publicadas**.

## <a name="play-content-from-the-portal"></a>Reproduzir conteúdo do portal
O portal do Azure fornece um player de conteúdo que você pode usar para testar o vídeo.

Clique no vídeo desejado e clique no botão **Reproduzir** .

![Publicar](./media/media-services-portal-vod-get-started/media-services-play.png)

Algumas considerações se aplicam:

* Verifique se que o vídeo foi publicado.
* Esse **Media player** * reproduz do ponto de extremidade de streaming padrão. Se você quiser reproduzir a partir de um ponto de extremidade da transmissão não padrão, clique para copiar a URL e use outra reprodução. Por exemplo, o [Player dos Serviços de Mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
* O ponto de extremidade de streaming do qual você estiver transmitindo deverá estar em execução.  

## <a name="next-steps"></a>Próximas etapas
Examine os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


