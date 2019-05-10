---
title: Criando filtros com o SDK do .NET dos Serviços de Mídia do Azure
description: Este tópico descreve como criar filtros para que seu cliente possa usá-los na transmissão de seções específicas de um fluxo. Os Serviços de Mídia criam manifestos dinâmicos para atingir esse streaming seletivo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: 8c786f46308848c6b9182453510744942a8eb9e8
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472427"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Criar filtros com o SDK do .NET dos Serviços de Mídia

Ao entregar seu conteúdo aos clientes (streaming de eventos ao vivo ou Video por Demanda), seu cliente pode precisar de mais flexibilidade do que o descrito no arquivo de manifesto do ativo padrão. Os Serviços de Mídia do Azure permitem definir filtros de conta e filtros de recursos para o seu conteúdo. Para obter mais informações, consulte [Filtros e manifestos dinâmicos](filters-dynamic-manifest-overview.md).

Este tópico mostra como usar o SDK do .NET dos Serviços de Mídia para definir um filtro para um ativo Video por Demanda e criar [Filtros de Conta](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) e [Filtros de Ativos](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

## <a name="prerequisites"></a>Pré-requisitos 

- Analise [Filtros e manifestos dinâmicos](filters-dynamic-manifest-overview.md).
- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md). Lembre-se de lembrar o nome do grupo de recursos e o nome da conta do Media Services. 
- Obtenha as informações necessárias para [acessar as APIs](access-api-cli-how-to.md)
- Analise [Carregar, codificar e transmitir usando os Serviços de Mídia do Azure](stream-files-tutorial-with-api.md) para ver como [começar a usar o .NET SDK](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>Definir um filtro  

No .NET, você configura as seleções de trilha com as classes [ FilterTrackSelection ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) e [ FilterTrackPropertyCondition ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet). 

O código a seguir define um filtro que inclui todas as faixas de áudio que são EC-3 e todas as faixas de vídeo com taxa de bits de 0 a 1000000.

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>Crie filtros de conta

O código a seguir mostra como usar o .NET para criar um filtro de conta que inclua todas as seleções de trilha [definidas acima](#define-a-filter). 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Crie filtros de ativos

O código a seguir mostra como usar o .NET para criar um filtro de ativos que inclui todas as seleções de trilha [definidas acima](#define-a-filter). 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Associar filtros de localizador de Streaming

Você pode especificar uma lista de filtros de ativo ou conta, que se aplica a localizador de Streaming. O [empacotador dinâmico (ponto de extremidade de Streaming)](dynamic-packaging-overview.md) se aplica a esta lista de filtros junto com aqueles seu cliente especifica a URL. Essa combinação gera uma [manifesto dinâmico](filters-dynamic-manifest-overview.md), que se baseia nos filtros na URL + filtros que você especificar no localizador de Streaming. É recomendável que você use esse recurso se você deseja aplicar filtros, mas não quiser expor os nomes de filtro na URL.

O seguinte C# código mostra como criar um localizador de Streaming e especifique `StreamingLocator.Filters`. Isso é uma propriedade opcional que usa um `IList<string>` de nomes de filtros.

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>Stream usando filtros

Depois que você definir filtros, seus clientes poderão usá-los na URL de streaming. Os filtros podem ser aplicados a protocolos de streaming de taxa de bits adaptável: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela a seguir mostra alguns exemplos de URLs com filtros:

|Protocol|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Próximas etapas

[Vídeos do Stream](stream-files-tutorial-with-api.md) 


