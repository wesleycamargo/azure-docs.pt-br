---
title: Tutorial – Compartilhamento entre as sessões e dispositivos com Âncoras Espaciais do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá como compartilhar identificadores de Âncora Espacial do Azure entre dispositivos Android/iOS no Unity com um serviço back-end.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7d9fe58b7db60513eed81aae628ebd7ca754a53a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901297"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors"></a>Tutorial: Compartilhando entre sessões e dispositivos com Âncoras Espaciais do Azure

Este tutorial mostrará como usar [Âncoras Espaciais do Azure](../overview.md) para:

1. Criar âncoras em uma sessão e, em seguida, localizá-las em outra sessão no mesmo dispositivo ou em outro. Por exemplo, em outro dia.
2. Crie âncoras que possam ser localizadas por vários dispositivos no mesmo lugar ao mesmo tempo.

![Persistência](./media/persistence.gif)

As Âncoras Espaciais do Azure são um serviço de desenvolvedor multiplataforma para você criar experiências de realidade misturada usando objetos que persistem sua localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo que pode ser implantado em dois ou mais dispositivos. Âncoras Espaciais do Azure criadas por uma instância podem ser compartilhadas com outras.

Você aprenderá a:

> [!div class="checklist"]
> * Implante um aplicativo Web do ASP.NET Core no Azure que possa ser usado para compartilhar âncoras, armazenando-as na memória por um tempo.
> * Configure a cena AzureSpatialAnchorsLocalSharedDemo dentro do exemplo do Unity de nossos inícios rápidos para aproveitar o aplicativo Web de Âncoras de Compartilhamento.
> * Implante e execute um ou mais dispositivos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Vale a pena notar que, embora você vá usar o Unity e o Aplicativo Web ASP.NET Core neste tutorial, é apenas para mostrar um exemplo de como compartilhar identificadores de Âncora Espacial do Azure em outros dispositivos. Você pode usar outras linguagens e tecnologias de back-end para atingir o mesmo objetivo. Além disso, o aplicativo Web do ASP.NET Core usado neste Tutorial tem uma dependência do SDK 2.2 do .NET Core. Ele é executado sem problemas em Aplicativos Web do Azure normais (para Windows), mas atualmente não funciona em Aplicativos Web do Azure para Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Abrir o projeto de exemplo no Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Implantar seu Serviço de Compartilhamento de Âncoras

Abra o Visual Studio e abra o projeto na pasta `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="open-the-sample-project-in-unity"></a>Abrir o projeto de exemplo no Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou um aplicativo Web do ASP.NET Core no Azure e, em seguida, configurou e implantou um aplicativo Unity. Você criou Âncoras Espaciais com o aplicativo e as compartilhou com outros dispositivos por meio de seu aplicativo Web do ASP.NET Core.

Para saber mais sobre como melhorar o Aplicativo Web do ASP.NET Core, de modo que ele use o Azure Cosmos DB para armazenar as Âncoras Espaciais compartilhadas, continue no próximo tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Usar o Azure Cosmos DB para Armazenar Âncoras](./tutorial-use-cosmos-db-to-store-anchors.md)
