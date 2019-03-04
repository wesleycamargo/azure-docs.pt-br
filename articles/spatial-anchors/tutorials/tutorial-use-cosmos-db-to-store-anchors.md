---
title: Tutorial – compartilhamento entre sessões e dispositivos com Âncoras Espaciais do Azure e um back-end do Azure Cosmos DB | Microsoft Docs
description: Neste tutorial, você aprenderá a compartilhar os identificadores de Âncora Espacial do Azure entre os dispositivos no Unity com um serviço de back-end e o Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: f0cd42fc37727099ed95a1c6fc2d427b7862412e
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753422"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>Tutorial: Compartilhamento entre sessões e dispositivos com Âncoras Espaciais do Azure e um back-end do Azure Cosmos DB

Este tutorial mostrará como usar [Âncoras Espaciais do Azure](../overview.md) para:

1. Criar âncoras em uma sessão e, em seguida, localizá-las em outra sessão no mesmo dispositivo ou em outro. Por exemplo, em outro dia.
2. Crie âncoras que possam ser localizadas por vários dispositivos no mesmo lugar ao mesmo tempo.

![Persistência](./media/persistence.gif)

As [Âncoras Espaciais do Azure](../overview.md) são um serviço de desenvolvedor multiplataforma para você criar experiências de realidade misturada usando objetos que persistem sua localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo que pode ser implantado em dois ou mais dispositivos. Âncoras Espaciais do Azure criado por uma instância compartilhará seus identificadores com outros usando o Cosmos DB.

Você aprenderá a:

> [!div class="checklist"]
> * Implante um aplicativo Web do ASP.NET Core no Azure que possa ser usado para compartilhar âncoras, armazenando-as no Cosmos DB.
> * Configure a cena AzureSpatialAnchorsLocalSharedDemo dentro do exemplo do Unity de nossos inícios rápidos para aproveitar o aplicativo Web de Âncoras de Compartilhamento.
> * Implante e execute um ou mais dispositivos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Vale a pena notar que, embora você vá usar o Unity e o Azure Cosmos DB neste tutorial, é apenas para mostrar um exemplo de como compartilhar identificadores de Âncora Espacial do Azure em outros dispositivos. Você poderá usar outras linguagens e tecnologias de back-end para alcançar a mesma meta. Além disso, o aplicativo Web do ASP.NET Core usado neste Tutorial tem uma dependência do SDK 2.2 do .NET Core. Ele é executado sem problemas em Aplicativos Web do Azure normais (para Windows), mas atualmente não funciona em Aplicativos Web do Azure para Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Anote essa `Connection String`, pois ela será usada posteriormente.

## <a name="deploy-your-sharing-anchors-service"></a>Implantar seu Serviço de Compartilhamento de Âncoras

Abra o Visual Studio e abra o projeto na pasta `Sharing\SharingServiceSample`.

### <a name="configure-the-service-so-that-it-uses-your-cosmos-db"></a>Configurar o serviço para que ele use o Cosmos DB

No **Gerenciador de Soluções**, abra `SharingService\Startup.cs`.

Localize a linha `#define INMEMORY_DEMO` na parte superior do arquivo e comente-a. Salve o arquivo.

No **Gerenciador de Soluções**, abra `SharingService\appsettings.json`.

Localize a propriedade `StorageConnectionString` e defina o valor para que seja a `Connection String` que você anotou na [etapa criar uma conta de banco de dados](#create-a-database-account). Salve o arquivo.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou o Azure Cosmos DB para compartilhar identificadores de âncora entre dispositivos. Para saber mais sobre a biblioteca de Âncoras Espaciais do Azure, veja nosso guia sobre como criar e localizar âncoras.

> [!div class="nextstepaction"]
> [Criar e localizar as âncoras usando Âncoras Espaciais do Azure](../create-locate-anchors-overview.md)
