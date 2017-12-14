---
title: "Encontrar a rota com os Serviços do Azure Baseados na Localização | Microsoft Docs"
description: "Rotear para um ponto de interesse usando os Serviços do Azure Baseados na Localização"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: f2be9ca98330866ac8b6fb12efd56efdc711eedf
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="route-to-a-point-of-interest-using-azure-location-based-services"></a>Rotear para um ponto de interesse usando os Serviços do Azure Baseados na Localização

Este tutorial mostra como usar sua conta dos Serviços do Azure Baseados na Localização e o SDK do Serviço de Roteiros para encontrar a rota para seu ponto de interesse. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Obter coordenadas de endereço
> * Serviço de Roteiros de Consulta para obter o trajeto até o ponto de interesse

## <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, verifique se você [criou sua conta dos Serviços do Azure Baseados na Localização](./tutorial-search-location.md#createaccount) e [obteve a chave de assinatura da conta](./tutorial-search-location.md#getkey). Você também pode observar como usar o Controle de Mapeamento e as APIs do Serviço de Pesquisa, conforme discutido no tutorial [Pesquisar ponto de interesse mais próximo usando os Serviços do Azure Baseados na Localização](./tutorial-search-location.md).


<a id="getcoordinates"></a>

## <a name="get-address-coordinates"></a>Obter coordenadas de endereço

Use as etapas a seguir para criar uma página HTML estática inserida com a API do Controle de Mapeamento dos Serviços Baseados na Localização. 

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
    Observe como o cabeçalho HTML insere os locais de recurso para arquivos CSS e JavaScript da biblioteca dos Serviços do Azure Baseados na Localização. Observe também o segmento *script* no corpo do arquivo HTML, que contém o código JavaScript para acessar as APIs do Serviço Baseado na Localização do Azure.

3. Adicione o seguinte código JavaScript ao bloco de *script* do arquivo HTML. Substitua o espaço reservado *<insert-key>* pela chave primária da conta dos Serviços Baseados na Localização.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var subscriptionKey = "<insert-key>";
    var map = new atlas.Map("map", {
        "subscription-key": subscriptionKey
    });
    ```
    O **atlas.Map** fornece o controle de um mapa Web visual e interativo, e é um componente da API de Controle de Mapeamento do Azure.

4. Adicione o seguinte código JavaScript ao bloco *script*. Isso adiciona uma camada de *linestrings* ao Controle de Mapeamento para mostrar a rota:

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

5. Adicione o seguinte código JavaScript para criar os pontos inicial e final para a rota:

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
    Esse código cria dois [objetos GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) para representar os pontos de partida e chegada da rota. O ponto de extremidade é a combinação de latitude/longitude para um dos *postos de gasolina* pesquisados no tutorial anterior, [Pesquisar ponto de interesse mais próximo usando os Serviços do Azure Baseados na Localização](./tutorial-search-location.md).

6. Adicione o código JavaScript a seguir para adicionar os pinos dos pontos de partida e chegada da rota:

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
    A API do **map.setCameraBounds** ajusta a janela do mapa de acordo com as coordenadas dos pontos de partida e final. A API do **map.addPins** adiciona os pontos ao controle de mapa como componentes visuais.

7. Salve o arquivo **MapRoute.html** em seu computador. 

<a id="getroute"></a>

## <a name="query-route-service-for-directions-to-point-of-interest"></a>Serviço de Roteiros de Consulta para obter o trajeto até o ponto de interesse

Esta seção mostra como usar a API do Serviço de Roteiros dos Serviços do Azure Baseados na Localização para encontrar várias rotas a partir de um determinado ponto de partida até um destino. O Serviço de Roteiros fornece APIs para planejar a rota mais rápida, mais curta ou econômica entre dois locais, considerando as condições de tráfego em tempo real. Também permite planejar rotas futuras usando o amplo banco de dados de histórico de tráfego do Azure e prevendo durações de rotas para cada dia e hora. 

1. Abra o arquivo **MapRoute.html** criado na seção anterior e adicione o seguinte código JavaScript ao bloco de *script* para ilustrar o Serviço de Roteiros.

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
    Esse trecho de código cria uma [XMLHttpRequest](https://xhr.spec.whatwg.org/) e adiciona um manipulador de eventos para analisar a resposta de entrada. Para uma resposta bem-sucedida, ela constrói uma matriz de coordenadas de segmentos de linha da primeira rota retornada. Ele então adiciona esse conjunto de coordenadas dessa rota para a camada *linestrings* do mapa.

2. Adicione o seguinte código para o bloco de *script* para enviar o XMLHttpRequest ao Serviço de Roteiros dos Serviços do Azure Baseados na Localização:

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "&api-version=1.0";
    url += "&subscription-key=" + subscriptionKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```
    A solicitação acima mostra os parâmetros necessários, que são a chave de assinatura da sua conta e as coordenadas para os pontos de partida e de chegada, na ordem fornecida. 

3. Salve o arquivo **MapRoute.html** localmente, em seguida, abra-o em um navegador da Web de sua escolha e observe o resultado. Para uma conexão bem-sucedida com APIs de Serviços Baseados na Localização, você deverá ver um mapa semelhante ao seguinte. 

    ![Controle de Mapeamento e Serviço de Roteiros do Azure](./media/tutorial-route-location/lbs-map-route.png)


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Obter coordenadas de endereço
> * Serviço de Roteiros de Consulta para obter o trajeto até o ponto de interesse

Vá para o tutorial [Localizar rotas de modos diferentes de trajeto usando os Serviços do Azure Baseados na Localização](./tutorial-prioritized-routes.md) para aprender a usar os Serviços do Azure Baseados na Localização para priorizar as rotas até o ponto de interesse, com base no meio de transporte. 
