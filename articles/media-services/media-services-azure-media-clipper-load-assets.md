---
title: Carregar ativos no Azure Media Clipper | Microsoft Docs
description: Etapas para carregar ativos no Azure Media Clipper
services: media-services
keywords: "clipe; subclipe; codificação; mídia"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6062e6ae22971055e186b5d1068ba78c900a7a4c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="loading-assets-into-azure-media-clipper"></a>Carregando ativos no Azure Media Clipper
Os ativos podem ser carregados no Azure Media Clipper por dois métodos:
1. Passando estaticamente uma biblioteca de ativos
2. Gerando uma lista de ativos dinamicamente por meio da API

## <a name="loading-static-asset-library"></a>Carregando uma biblioteca estática de ativos
Nesse caso, você passa um conjunto estático de ativos para o Clipper. Cada ativo inclui uma ID de ativo/filtro do AMS, um nome e uma URL de streaming publicada. Se aplicável, um token de autenticação de proteção de conteúdo ou uma matriz de URLs da miniatura pode ser passada. Se forem passadas, as miniaturas serão populadas na interface. Em cenários em que a biblioteca de ativos é estática e pequena, passe o contrato de ativos de cada ativo na biblioteca.

Para carregar uma biblioteca estática de ativos, use o método **load** para passar uma representação JSON de cada ativo. O exemplo de código a seguir ilustra a representação JSON de um ativo.

```javascript
var assets = 
{
  /* Required: represents the Azure Media Services asset Id when "type" === "asset"; otherwise, represents the dynamic manifest asset filter Id ("type" === "filter")  */
  "id": "my-asset-or-dynamic-manifest-asset-filter-id",

  /* Required: represents the asset name as shown in the Clipper interface */
  "name": "My Asset or Dynamic Manifest Asset Filter Name",

  /* Required: must be one of the following values: "asset" or "filter" */
  /* NOTE: "asset" type represents a rendered asset; "filter" type represents a dynamic manifest asset filter */
  "type": "asset",

  /* Required */
  "source": {

    /* Required: represents the asset streaming locator, the base Smooth Streaming URL */
    "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",

    /* Optional: default value "application/vnd.ms-sstr+xml" */
    "type": "application/vnd.ms-sstr+xml",

    /* Required: If the asset has content protection applied, then you must include an array with the different protection types along with the token to request the license/key; otherwise, provide an empty array */
    "protectionInfo": [{
        "type": "AES",
        "authenticationToken": "Bearer aes-token-placeholder"
      },
      {
        "type": "PlayReady",
        "authenticationToken": "Bearer playready-token-placeholder"
      },
      {
        "type": "Widevine",
        "authenticationToken": "Bearer widevine-token-placeholder"
      },
      {
        "type": "FairPlay",
        "certificateUrl": "//example/path/to/myfairplay.der",
        "authenticationToken": "Bearer fairplay-token-placeholder"
      }
    ]
  },

  /* Optional: array containing thumbnail URLs for the video. */
  /* NOTE: For the thumbnail URLs to work as expected in the Clipper timeline they must be evenly distributed across the video (based on the duration) and in chronological order within the array. */
  "thumbnails": [
    "//example/path/thumbnail_001.jpg",
    "//example/path/thumbnail_002.jpg",
    "//example/path/thumbnail_003.jpg",
    "//example/path/thumbnail_004.jpg",
    "//example/path/thumbnail_005.jpg"
  ]
};
var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.load(assets)
```

> [!NOTE]
> Para que as URLs da miniatura funcionem como esperado na linha do tempo do Clipper, elas devem ser distribuídas uniformemente no vídeo (de acordo com a duração) e em ordem cronológica dentro da matriz. Use o seguinte trecho de predefinição JSON como uma referência de exemplo para a geração de imagens com o processador “Media Encoder Standard”:

```json
{
  "Start": "0",
  "Step": "00:00:05",
  "Range": "100%",
  "Type": "PngImage",
  "PngLayers": [
    {
      "Type": "PngLayer",
      "Width": 48,
      "Height": 26
    }
  ]
}
```

## <a name="loading-dynamic-asset-library"></a>Carregando uma biblioteca dinâmica de ativos
Como alternativa, você pode carregar ativos dinamicamente por meio de um retorno de chamada. Em cenários em que os ativos são gerados dinamicamente ou a biblioteca é grande, você deve fazer o carregamento por meio do retorno de chamada. Para carregar o ativo dinamicamente, é necessário implementar a função de retorno de chamada opcional onLoadAssets. Essa função é passada para o Clipper na inicialização. Os ativos resolvidos devem seguir o mesmo contrato dos ativos carregados estaticamente. O exemplo de código a seguir ilustra a assinatura do método, a entrada esperada e a saída esperada.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, retuns an object containing an array of assets (input contract)
    //          that satisfies the filter parameters, plus optionally the total types of files available:
    // {
    //  total: 100,
    //  assets: [{...}],
    // }
    var onLoadAssets = function (search, skip, take, type) {
        var promise = new Promise(function (resolve, reject) {
            // TODO: perform the back-end AJAX request to get the assets using the filter parameters (search, skip, take).
            var assets = [{
                // asset (input contract)
            }, {
                // asset (input contract)
            }];

            if (/* everything turned out fine */ assets !== null) {
                resolve({
                    total: 100,
                    assets: assets
                });
            }
            else {
                reject(Error("error details"));
            }
        });

        return promise;
    };

    // Create widget instance:
    // - using a root element selector
    // - enabling the Video Assets panel by registering a callback in the 'assetsPanelLoaderCallback' option parameter.
    var widget = new subclipper({
        selector: '#root',

        // Enable the Video Assets panel in the widget to automatically load assets (input contract)
        assetsPanelLoaderCallback: onLoadAssets
    });

    // ...
    // The widget will automatically invoke the 'assetsPanelLoaderCallback' callback with the filter parameters specified by the user 
    // and load assets returned by the Promise into the Video Assets panel.
    // ...
```