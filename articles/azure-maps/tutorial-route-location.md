---
title: Encontrar rota com os Mapas do Azure | Microsoft Docs
description: Rotear para um ponto de interesse usando os Mapas do Azure
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: fc5dfafec303a439d8a1092771fd2247ab305172
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601336"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Rotear para um ponto de interesse usando os Mapas do Azure

Este tutorial mostra como usar sua conta dos Mapas do Azure e o SDK do Serviço de Roteiros para encontrar a rota para seu ponto de interesse. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma nova página da Web usando a API de controle de mapeamento
> * Definir coordenadas de endereço
> * Serviço de Roteiros de Consulta para obter o trajeto até o ponto de interesse

## <a name="prerequisites"></a>pré-requisitos

Antes de prosseguir, siga as etapas no tutorial anterior para [criar sua conta dos Mapas do Azure](./tutorial-search-location.md#createaccount) e [obter a chave de assinatura da conta](./tutorial-search-location.md#getkey). 

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Criar um novo mapa 
As etapas a seguir mostra como criar uma página HTML estática inserida com a API do Controle de Mapeamento. 

1. Em seu computador local, crie um novo arquivo e nomeie-o como **MapRoute.html**. 
2. Adicione os seguintes componentes HTML ao arquivo:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>

    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    O cabeçalho HTML insere os locais de recurso para arquivos CSS e JavaScript da biblioteca dos Mapas do Azure. O segmento *script* no corpo do arquivo HTML conterá o código JavaScript para acessar as APIs dos Mapas do Azure.

3. Adicione o seguinte código JavaScript ao bloco de *script* do arquivo HTML. Substitua a cadeia de caracteres **\<sua chave de conta\>** pela chave primária que você copiou da sua conta dos Mapas.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    O **atlas.Map** fornece o controle de um mapa Web visual e interativo, e é um componente da API de Controle de Mapeamento do Azure.

4. Salve o arquivo e abra-o em seu navegador. Agora você tem um mapa básico que você pode desenvolver ainda mais. 

   ![Exibir mapa básico](./media/tutorial-route-location/basic-map.png)

## <a name="set-start-and-end-points"></a>Definir pontos de início e fim

Neste tutorial, defina o ponto inicial como Microsoft e o ponto de destino como um posto de gasolina em Seattle. 

1. No mesmo bloco *script* do arquivo **MapRoute.html**, adicione o seguinte código JavaScript para criar os pontos inicial e final para a rota:

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Microsoft",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.3352, 47.61397]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Contoso Oil & Gas",
        icon: "pin-blue"
    });
    ```
    Esse código cria dois [objetos GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) para representar os pontos de partida e chegada da rota. 

2. Adicione o código JavaScript a seguir para adicionar os pinos dos pontos de partida e chegada da rota:

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 50
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    O **map.setCameraBounds** ajusta a janela do mapa de acordo com as coordenadas dos pontos de partida e final. A API do **map.addPins** adiciona os pontos ao controle de mapa como componentes visuais.

7. Salve o arquivo **MapRoute.html** e atualize seu navegador. Agora o mapa está centralizado em Seattle e você pode ver o pin azul redondo marcando o ponto de início e o pin azul marcando o ponto final.

   ![Exibir o mapa com os pontos inicial e final marcados](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Obter direções

Esta seção mostra como usar a API do serviço de roteiros dos Mapas para encontrar várias rotas a partir de um determinado ponto de partida até um destino. O serviço de roteiros fornece APIs para planejar as rotas *mais rápida*, *mais curta*, *econômica* ou *emocionante* entre dois locais. Também permite planejar rotas futuras usando o amplo banco de dados de histórico de tráfego do Azure e prevendo durações de rotas para cada dia e hora. Para saber mais, confira [Obter direções de rota](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).


1. Primeiro, adicione uma nova camada no mapa para exibir o caminho da rota, ou *linestring*. Adicione o seguinte código JavaScript ao bloco *script*:

    ```JavaScript
    // Initialize the linestring layer for routes on the map
    var routeLinesLayerName = "routes";
    map.addLinestrings([], {
        name: routeLinesLayerName,
        color: "#2272B9",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });
    ```

2. Crie um [XMLHttpRequest](https://xhr.spec.whatwg.org/) e adicione um manipulador de eventos para analisar a resposta JSON enviada pelo serviço de roteiros dos Mapas. Esse código constrói uma matriz de coordenadas para os segmentos de linha da rota e, em seguida, adiciona o conjunto de coordenadas à camada de linestrings do mapa. 

    ```JavaScript
    // Perform a request to the route service and draw the resulting route on the map
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(xhttp.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], { name: routeLinesLayerName });
        }
    };
    ```

3. Adicione o código a seguir para compilar a consulta e envie o XMLHttpRequest para o serviço de roteiros dos Mapas:

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "api-version=1.0";
    url += "&subscription-key=" + MapsAccountKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```

3. Salve o arquivo **MapRoute.html** e atualize seu navegador da Web. Para uma conexão bem-sucedida com APIs dos Mapas, você deverá ver um mapa semelhante ao seguinte. 

    ![Controle de Mapeamento e Serviço de Roteiros do Azure](./media/tutorial-route-location/map-route.png)


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma nova página da Web usando a API de controle de mapeamento
> * Definir coordenadas de endereço
> * Consultar o serviço de roteiros para obter o trajeto até o ponto de interesse

O tutorial a seguir demonstra como criar uma consulta de rota com restrições como modo de viagem ou tipo de carga e, em seguida, exiba várias rotas no mesmo mapa. 

> [!div class="nextstepaction"]
> [Localizar rotas para diferentes modos de viagem](./tutorial-prioritized-routes.md)
