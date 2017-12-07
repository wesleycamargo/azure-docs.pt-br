---
title: "Várias rotas com os Serviços do Azure Baseados na Localização | Microsoft Docs"
description: "Encontre rotas para diferentes modos de viagem usando os Serviços do Azure Baseados na Localização"
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
ms.openlocfilehash: 3631bab8e5cb505689e92d2862c6863bcd56404d
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2017
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-location-based-services"></a>Encontre rotas para diferentes modos de viagem usando os Serviços do Azure Baseados na Localização

Este tutorial mostra como usar sua conta dos Serviços do Azure Baseados na Localização e o SDK do Serviço de Roteiros para encontrar a rota para seu ponto de interesse, priorizado por seu modo de viagem. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar sua consulta do Serviço de Roteiros
> * Renderizar rotas priorizadas por modo de viagem

## <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, verifique se você [criou sua conta dos Serviços do Azure Baseados na Localização](./tutorial-search-location.md#createaccount) e [obteve a chave de assinatura da conta](./tutorial-search-location.md#getkey). Você também pode observar como usar o Controle de mapa e as APIs de Serviço de Pesquisa, conforme discutido no tutorial [Pesquisar ponto de interesse próximo usando os Serviços do Azure Baseados na Localização](./tutorial-search-location.md), bem como saber mais sobre o uso básico das APIs do Serviço de Roteiros, conforme discutido no tutorial [Rota para um ponto de interesse usando os Serviços do Azure Baseados na Localização](./tutorial-route-location.md).


<a id="queryroutes"></a>

## <a name="configure-your-route-service-query"></a>Configurar sua consulta do Serviço de Roteiros

Use as etapas a seguir para criar uma página HTML estática inserida com a API do Controle de mapa dos Serviços Baseados na Localização. 

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
    Observe que o cabeçalho HTML insere os locais de recurso para arquivos CSS e JavaScript da biblioteca dos Serviços do Azure Baseados na Localização. Observe também o segmento de *script* adicionado ao corpo do HTML, que deve conter o código JavaScript embutido para acessar a API de controle de mapa do Azure.
3. Adicione o seguinte código JavaScript ao bloco de *script* do arquivo HTML. Substitua o espaço reservado *<insert-key>* pela chave primária da conta dos Serviços Baseados em Localização.

    ```HTML
            // Instantiate map to the div with id "map"
            var subscriptionKey = "<insert-key>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey
            });

    ```
    O **atlas.Map** fornece o controle de um mapa Web visual e interativo, e é um componente da API de Controle do Mapa do Azure.

4. Adicione o seguinte código JavaScript para o bloco de *script* para adicionar a exibição do fluxo de tráfego para o mapa:

    ```HTML
            // Add Traffic Flow to the Map
            map.setTraffic({
                flow: "relative"
            });
    ```
    Esse código define o fluxo de tráfego para `relative`, que é a velocidade do caminho relativo ao fluxo contínuo. Você também pode defini-lo na velocidade `absolute` do caminho, ou `relative-delay`, o qual exibe a velocidade relativa em que difere do fluxo contínuo. 

5. Adicione o código JavaScript a seguir para criar os marcadores para os pontos de partida e final da rota:

    ```HTML
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
    Esse código cria dois [objetos GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) para representar os pontos de partida e final da rota. 

6. Adicione o código JavaScript a seguir para adicionar camadas de *linestrings* ao Controle de Mapa para exibir rotas baseadas no modo de transporte, por exemplo _carro_ e _caminhão_.

    ```HTML
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

7. Adicione o código JavaScript a seguir para adicionar os pontos de partida e final da rota:

    ```HTML
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
    A API do **map.setCameraBounds** ajusta a janela do mapa de acordo com as coordenadas dos pontos de partida e final. A API do **map.addPins** adiciona os pontos ao controle de mapa como componentes visuais.

8. Salve o arquivo **MapTruckRoute.html** em seu computador. 

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Renderizar rotas priorizadas por modo de viagem

Esta seção mostra como usar a API do Serviço de Roteiros dos Serviços do Azure Baseados na Localização para encontrar várias rotas a partir de um determinado ponto de partida até um destino com base no seu modo de transporte. O Serviço de Roteiros fornece APIs para planejar a rota mais rápida, mais curta ou econômica entre dois locais, considerando as condições de tráfego em tempo real. Também permite planejar rotas futuras usando o amplo banco de dados de histórico de tráfego do Azure e prevendo durações de rotas para cada dia e hora. 

1. Abra o arquivo **MapTruckRoute.html** criado na seção anterior e adicione o seguinte código JavaScript ao bloco de *script* para obter a rota para um caminhão usando o Serviço de Roteiros.

    ```HTML
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
            truckRouteUrl += "&subscription-key=" + subscriptionKey;
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
    
    O trecho de código também envia a consulta ao Serviço de Roteiros para obter a rota entre o ponto de partida e final especificados da chave de assinatura da conta. Os parâmetros opcionais a seguir são usados para indicar a rota de um caminhão pesado: - O parâmetro `travelMode=truck` especifica o modo de viagem como *caminhão*. Outros modos de viagem com suporte são *táxi*, *ônibus*, *van*, *motocicleta* e o *carro*  padrão. 
        - Os parâmetros `vehicleWidth`, `vehicleHeight` e `vehicleLength` especificam as dimensões do veículo em metros e só são considerados se o modo de viagem for *caminhão*. 
        - O `vehicleLoadType` classifica a carga como perigosa e restrita em algumas estradas. Isso também só é considerado para o modo *caminhão* atualmente. 

2. Adicione o seguinte código JavaScript para obter a rota para um carro usando o Serviço de Roteiros:

    ```HTML
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
            carRouteUrl += "&subscription-key=" + subscriptionKey;
            carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
                destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

            xhttpCar.open("GET", carRouteUrl, true);
            xhttpCar.send();
    ```
    Esse trecho de código cria outra [XMLHttpRequest](https://xhr.spec.whatwg.org/) e adiciona um manipulador de eventos para analisar a resposta de entrada. Para uma resposta bem-sucedida, ele cria uma matriz de coordenadas para a rota retornada e adiciona a camada `carRouteLayerName` do mapa. 
    
    O trecho de código também envia a consulta ao Serviço de Roteiros para obter a rota entre o ponto de partida e final especificados da chave de assinatura da conta. Como nenhum outro parâmetro é usado, a rota para o modo de viagem padrão *carro* é retornada. 

3. Salve o arquivo **MapTruckRoute.html** localmente, em seguida, abra-o em um navegador da Web de sua escolha e observe o resultado. Para uma conexão bem-sucedida com APIs de Serviços Baseados em Localização, você deve ver um mapa semelhante ao seguinte. 

    ![Rotas priorizadas com o Serviço de Roteiros do Azure](./media/tutorial-prioritized-routes/lbs-prioritized-routes.png)

    Observe que a rota de caminhão está em azul, enquanto a rota de carro está em roxo.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Configurar sua consulta do Serviço de Roteiros
> * Renderizar rotas priorizadas por modo de viagem

Prossiga para os artigos **Conceitos** e **Como fazer** para saber mais sobre o SDK dos Serviços do Azure Baseados na Localização em detalhes. 
