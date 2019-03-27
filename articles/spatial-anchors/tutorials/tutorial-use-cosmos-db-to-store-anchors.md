---
title: Tutorial – compartilhamento entre sessões e dispositivos com Âncoras Espaciais do Azure e um back-end do Azure Cosmos DB | Microsoft Docs
description: Neste tutorial, você aprenderá a compartilhar os identificadores de Âncoras Espaciais do Azure entre os dispositivos Android/iOS no Unity com um serviço de back-end e Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0e7011b9778221869940b137a2b87239f2d8db9b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286390"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>Tutorial: Compartilhamento entre sessões e dispositivos com Âncoras Espaciais do Azure e um back-end do Azure Cosmos DB

Neste tutorial, você aprenderá a usar as [Âncoras Espaciais do Azure](../overview.md) para:

- Criar âncoras durante em uma sessão e, em seguida, localizá-las em outra sessão no mesmo dispositivo ou em outro. Por exemplo, a segunda sessão pode ser em outro dia.
- Crie âncoras que possam ser localizadas por vários dispositivos no mesmo lugar e ao mesmo tempo.

![Persistência de objetos que ilustram GIF](./media/persistence.gif)

As [Âncoras Espaciais do Azure](../overview.md) são um serviço de desenvolvedor multiplataforma em que você pode criar experiências de realidade misturada usando objetos que persistem sua localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo que pode ser implantado em dois ou mais dispositivos. Âncoras espaciais do Azure criadas por uma instância compartilhará seus identificadores com outros usando o Azure Cosmos DB.

Você aprenderá a:

> [!div class="checklist"]
> * Implante um aplicativo Web do ASP.NET Core no Azure que possa ser usado para compartilhar âncoras, armazenando-as no Azure Cosmos DB.
> * Configure a cena AzureSpatialAnchorsLocalSharedDemo dentro do exemplo do Unity dos inícios rápidos para aproveitar o aplicativo Web de Âncoras de Compartilhamento.
> * Implante um aplicativo ou mais dispositivos e execute-os.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Vale a pena lembrar que, embora você vá usar o Unity e o Azure Cosmos DB neste tutorial, será apenas um exemplo de como compartilhar identificadores de Âncoras Espaciais do Azure em outros dispositivos. Você poderá usar outras linguagens e tecnologias de back-end para alcançar a mesma meta. Além disso, o aplicativo Web do ASP.NET Core usado neste tutorial tem uma dependência do SDK 2.2 do .NET Core. Ele é executado bem em aplicativos Web para Windows, mas atualmente ele não será executado nos aplicativos Web para Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Copie o `Connection String` porque você precisará.

## <a name="open-the-sample-project-in-unity"></a>Abrir o projeto de exemplo no Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>Implante o serviço de compartilhamento de âncoras

Abra o Visual Studio e abra o projeto na pasta `Sharing\SharingServiceSample`.

### <a name="configure-the-service-to-use-your-azure-cosmos-db-database"></a>Configure o serviço para usar seu banco de dados do Azure Cosmos DB

No **Gerenciador de Soluções**, abra `SharingService\Startup.cs`.

Localize `#define INMEMORY_DEMO` na parte superior do arquivo e comente-a. Salve o arquivo.

No **Gerenciador de Soluções**, abra `SharingService\appsettings.json`.

Localize a propriedade `StorageConnectionString` e defina o valor para que seja o mesmo `Connection String` que você copiou na [etapa criar uma conta de banco de dados](#create-a-database-account). Salve o arquivo.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou o Azure Cosmos DB para compartilhar identificadores de âncora entre dispositivos. Para saber mais sobre a biblioteca de Âncoras Espaciais do Azure, veja nosso guia sobre como criar e localizar âncoras.

> [!div class="nextstepaction"]
> [Criar e localizar as âncoras usando Âncoras Espaciais do Azure](../create-locate-anchors-overview.md)
