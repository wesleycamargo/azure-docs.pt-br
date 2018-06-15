---
title: Várias rotas com os Mapas do Azure | Microsoft Docs
description: Encontre rotas para diferentes modos de viagem usando os Mapas do Azure
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ea68c70befde1922fc30d06386a2ae326460c722
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601404"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Encontre rotas para diferentes modos de viagem usando os Mapas do Azure

Este tutorial mostra como usar sua conta dos Mapas do Azure e o serviço de roteiro para encontrar a rota para seu ponto de interesse, priorizado por seu modo de viagem. Você exibe duas rotas diferentes em seu mapa, uma para carros e outra para caminhões que podem ter restrições de rota devido a altura, peso ou carga perigosa. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma nova página da Web usando a API de controle de mapeamento
> * Visualizar o fluxo de tráfego em seu mapa
> * Criar consultas de rota que declarem o modo de viagem
> * Exibir várias rotas em seu mapa

## <a name="prerequisites"></a>pré-requisitos

Antes de prosseguir, siga as etapas no primeiro tutorial para [criar sua conta dos Mapas do Azure](./tutorial-search-location.md#createaccount) e [obter a chave de assinatura da conta](./tutorial-search-location.md#getkey). 


## <a name="create-a-new-map"></a>Criar um novo mapa 
As etapas a seguir mostra como criar uma página HTML estática inserida com a API do Controle de Mapeamento. 

1. Em seu computador local, crie um novo arquivo e nomeie-o como **MapTruckRoute.html**. 
2. Adicione os seguintes componentes HTML ao arquivo:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
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
    O cabeçalho HTML insere os locais de recurso para arquivos CSS e JavaScript da biblioteca dos Mapas do Azure. O segmento *script* no corpo do HTML conterá o código JavaScript embutido para o mapa.
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

   ![Exibir mapa básico](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualizar o fluxo de tráfego

1. Se você não informar ao mapa onde ele deve se concentrar, você verá a exibição do mundo todo. Para poder exibir os dados de tráfego, defina um ponto central e um nível de zoom em seu mapa. Substitua o código que declara um `new atlas.Map` com o seguinte código JavaScript: 
    
    ```JavaScript
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey,
        center: [-118.2437,34.0522],
        zoom: 12
    });
    ```

    Esse código define o ponto central para o mapa e declara um nível de zoom para que você possa se concentrar em uma área particular por padrão. 

1. Adicione a exibição do fluxo de tráfego para o mapa:

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    Esse código define o fluxo de tráfego para `relative`, que é a velocidade do caminho relativo ao fluxo contínuo. Você também pode defini-lo na velocidade `absolute` do caminho, ou `relative-delay`, o qual exibe a velocidade relativa em que difere do fluxo contínuo. 

2. Salve o arquivo **MapTruckRoute.html** e atualize a página em seu navegador. Você deve ver as ruas de Los Angeles com seus dados de tráfego atual.

   ![Exibir o mapa de trânsito](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="set-start-and-end-points"></a>Definir pontos de início e fim

Neste tutorial, defina o ponto inicial como uma empresa fictícia em Seattle chamada Fabrikam e o ponto de destino como um escritório da Microsoft. 

1. Adicione o código JavaScript a seguir para criar os marcadores para os pontos de partida e final da rota:

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Fabrikam, Inc.",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Microsoft",
        icon: "pin-blue"
    });
    ```
    Esse código cria dois [objetos GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) para representar os pontos de partida e chegada da rota. 

2. Adicione o código JavaScript a seguir para adicionar os pontos de partida e final da rota:

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 100
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    A chamada **map.setCameraBounds** ajusta a janela do mapa de acordo com as coordenadas dos pontos de partida e final. A API do **map.addPins** adiciona os pontos ao controle de mapa como componentes visuais.

3. Salve o arquivo e atualize seu navegador para ver os pinos exibidos em seu mapa. Apesar de você ter declarado seu mapa com um ponto central em Los Angeles, o **map.setCameraBounds** moveu o modo de exibição para exibir os pontos inicial e final. 

   ![Exibir o mapa com os pontos inicial e final](./media/tutorial-prioritized-routes/pins-map.png)


<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Renderizar rotas priorizadas por modo de viagem

Esta seção mostra como usar a API do serviço de roteiros dos Mapas para encontrar várias rotas a partir de um determinado ponto de partida até um destino com base no seu modo de transporte. O serviço de roteiros fornece APIs para planejar a rota *mais rápida*, *mais curta*, *econômica* ou *emocionante* entre dois locais, considerando as condições de trânsito em tempo real. Também permite planejar rotas futuras usando o amplo banco de dados de histórico de tráfego do Azure e prevendo durações de rotas para cada dia e hora. Para saber mais, confira [Obter direções de rota](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).


1. Primeiro, adicione uma nova camada no mapa para exibir o caminho da rota, ou *linestring*. Neste tutorial, há duas rotas diferentes, **rota por carro** e **rota por caminhão**, cada um com seu próprio estilo. Adicione o seguinte código JavaScript ao bloco *script*:

    ```JavaScript
    // Place route layers on the map
    var carRouteLayerName = "car-route";
    map.addLinestrings([], {
        name: carRouteLayerName,
        color: "#B76DAB",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });

    var truckRouteLayerName = "truck-route";
    map.addLinestrings([], {
        name: truckRouteLayerName,
        color: "#2272B9",
        width: 9,
        cap: "round",
        join: "round",
        before: carRouteLayerName
    });
    ```

2. Adicione o seguinte código JavaScript ao bloco *script*, para solicitar a rota para um caminhão e exibir os resultados do mapa:

    ```JavaScript
    // Perform a request to the route service and draw the resulting truck route on the map
    var xhttpTruck = new XMLHttpRequest();
    xhttpTruck.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: truckRouteLayerName
            });
        }
    };

    var truckRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    truckRouteUrl += "&api-version=1.0";
    truckRouteUrl += "&subscription-key=" + MapsAccountKey;
    truckRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    truckRouteUrl += "&travelMode=truck";
    truckRouteUrl += "&vehicleWidth=2";
    truckRouteUrl += "&vehicleHeight=2";
    truckRouteUrl += "&vehicleLength=5";
    truckRouteUrl += "&vehicleLoadType=USHazmatClass2";

    xhttpTruck.open("GET", truckRouteUrl, true);
    xhttpTruck.send();
    ```
    Esse trecho de código cria uma [XMLHttpRequest](https://xhr.spec.whatwg.org/) e adiciona um manipulador de eventos para analisar a resposta de entrada. Para uma resposta bem-sucedida, ele cria uma matriz de coordenadas para a rota retornada e adiciona a camada `truckRouteLayerName` do mapa. 
    
    Este trecho de código também cria a consulta para o serviço de roteiro de Mapas usando sua chave de conta. A consulta inclui as coordenadas do ponto inicial, as coordenadas do ponto final e parâmetros opcionais para indicar que a rota é para um caminhão pesado.

2. Adicione o seguinte código JavaScript para solicitar a rota para um carro e exibir os resultados:

    ```JavaScript
    // Perform a request to the route service and draw the resulting car route on the map
    var xhttpCar = new XMLHttpRequest();
    xhttpCar.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: carRouteLayerName
            });
        }
    };

    var carRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    carRouteUrl += "&api-version=1.0";
    carRouteUrl += "&subscription-key=" + MapsAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    Esse trecho de código cria outra [XMLHttpRequest](https://xhr.spec.whatwg.org/) e adiciona um manipulador de eventos para analisar a resposta de entrada. Para uma resposta bem-sucedida, ele cria uma matriz de coordenadas para a rota retornada e adiciona-a à camada `carRouteLayerName` do mapa. 
    
    Este trecho de código também cria a consulta para o serviço de roteiro de Mapas usando sua chave de conta. A consulta inclui as coordenadas de ponto inicial e coordenadas de ponto final. Como nenhum parâmetro adicional é fornecido, o serviço de rota tem como padrão de modo de viagem o *carro*. 

3. Salve o arquivo **MapTruckRoute.html** e atualize seu navegador para observar o resultado. Para uma conexão bem-sucedida com APIs dos Mapas, você deverá ver um mapa semelhante ao seguinte. 

    ![Rotas priorizadas com o Serviço de Roteiros do Azure](./media/tutorial-prioritized-routes/prioritized-routes.png)

    A rota de caminhão é azul e mais grossa, enquanto a rota de carro é roxa e mais fina. A rota de carro vai passa pelo Lago Washington pela estrada I-90, que passa por túneis em áreas residenciais, restringindo a passagem de carga de lixo tóxico. A rota de caminhão, que especifica um tipo de carga USHazmatClass2, é instruída corretamente a usar uma estrada diferente. 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma nova página da Web usando a API de controle de mapeamento
> * Visualizar o fluxo de tráfego em seu mapa
> * Criar consultas de rota que declarem o modo de viagem
> * Exibir várias rotas em seu mapa

Para saber mais sobre a cobertura e os recursos dos Mapas do Azure, consulte [Níveis de zoom e grade lado a lado](zoom-levels-and-tile-grid.md) e os outros artigos de Conceitos. 

Para obter mais exemplos de código e uma experiência interativa de codificação, consulte [Como usar o controle de mapa](how-to-use-map-control.md) e outros guias de instruções. 
