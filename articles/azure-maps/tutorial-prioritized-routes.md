---
title: Várias rotas com os Mapas do Azure | Microsoft Docs
description: Encontre rotas para diferentes modos de viagem usando os Mapas do Azure
author: walsehgal
ms.author: v-musehg
ms.date: 10/29/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 67b68489f2e06b9149f842f293a769fa7f688be0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412696"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Encontre rotas para diferentes modos de viagem usando os Mapas do Azure

Este tutorial mostra como usar sua conta dos Mapas do Azure e o serviço de roteiro para encontrar a rota para seu ponto de interesse, priorizado por seu modo de viagem. Você exibe duas rotas diferentes em seu mapa, uma para carros e outra para caminhões que podem ter restrições de rota devido a altura, peso ou carga perigosa. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma nova página da Web usando a API de controle de mapeamento
> * Visualizar o fluxo de tráfego em seu mapa
> * Criar consultas de rota que declarem o modo de viagem
> * Exibir várias rotas em seu mapa

## <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, siga as etapas no primeiro tutorial para [criar sua conta dos Mapas do Azure](./tutorial-search-location.md#createaccount) e [obter a chave de assinatura da conta](./tutorial-search-location.md#getkey).

## <a name="create-a-new-map"></a>Criar um novo mapa

As etapas a seguir mostra como criar uma página HTML estática inserida com a API do Controle de Mapeamento.

1. Em seu computador local, crie um novo arquivo e nomeie-o como **MapTruckRoute.html**.
2. Adicione os seguintes componentes HTML ao arquivo:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Multiple routes by mode of travel</title>
        
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                position: relative;
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```
    
    Observe que o cabeçalho HTML inclui os arquivos de recurso CSS e JavaScript hospedados pela biblioteca de Controle de mapa do Azure. Observe o evento `onload` no corpo da página que chamará a função `GetMap` quando o corpo da página for carregado. Essa função conterá o código JavaScript embutido para acessar as APIs do Azure Mapas.

3. Adicione o código JavaScript a seguir à função `GetMap`. Substitua a cadeia de caracteres **\<Sua Chave do Azure Mapas\>** pela chave primária que você copiou da conta dos Mapas.

    ```JavaScript
    //Add your Azure Maps subscription key to the map SDK. 
    atlas.setSubscriptionKey('<Your Azure Maps Key>');

    //Initialize a map instance.
    map = new atlas.Map('myMap');
    ```

    O **atlas.Map** fornece o controle de um mapa Web visual e interativo, e é um componente da API de Controle de Mapeamento do Azure.

4. Salve o arquivo e abra-o em seu navegador. Agora você tem um mapa básico que você pode desenvolver ainda mais.

   ![Exibir mapa básico](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualizar o fluxo de tráfego

1. Adicione a exibição do fluxo de tráfego ao mapa. O `map.events.add` faz com que todas as funções de mapas adicionadas ao mapa sejam carregadas após o carregamento total do mapa.

    ```JavaScript
    map.events.add("load", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```
    
     Um evento de carregamento é adicionado ao mapa, que será disparado quando os recursos de mapa forem totalmente carregados. No manipulador de eventos de carregamento do mapa, a configuração do fluxo de tráfego no mapa é definida como `relative`, que é a velocidade da estrada em relação ao fluxo livre. Você também pode defini-lo na velocidade `absolute` do caminho, ou `relative-delay`, o qual exibe a velocidade relativa em que difere do fluxo contínuo.

2. Salve o arquivo **MapTruckRoute.html** e atualize a página em seu navegador. Você deve ver as ruas de Los Angeles com seus dados de tráfego atual.

   ![Exibir o mapa de trânsito](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definir como a rota será renderizada

Neste tutorial, duas rotas serão calculadas e renderizadas no mapa. Uma rota usando estradas acessíveis a carros e outra usando as acessíveis a caminhões. Quando renderizadas, exibiremos um ícone de símbolo para o início e o fim da rota e linhas de cores diferentes para cada caminho de rota.

1. Na função GetMap, depois de inicializar o mapa, adicione o código JavaScript a seguir.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round',
            filter: ['==', '$type', 'LineString']
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
            },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['==', '$type', 'Point']
        }));
    });
    ```
    
    Um evento de carregamento é adicionado ao mapa, que será disparado quando os recursos de mapa forem totalmente carregados. No manipulador de eventos de carregamento do mapa, uma fonte de dados é criada para armazenar as linhas de rota e os pontos de partida e de chegada. Uma camada de linhas é criada e anexada à fonte de dados para definir como a linha de rota será renderizada. Expressões são usadas para recuperar a largura da linha e a cor das propriedades no recurso de linha de rota. Um filtro é adicionado para fazer com que essa camada renderize apenas dados LineString GeoJSON. Quando você adiciona a camada ao mapa, um segundo parâmetro com o valor de `'labels'` é passado especificando que essa camada deve ser renderizada abaixo dos rótulos do mapa. Isso fará com que a linha de rota não cubra os rótulos de estrada. Uma camada de símbolo é criada e anexada à fonte de dados. Essa camada especifica como os pontos de partida e chegada serão renderizados; neste caso, expressões foram adicionadas para recuperar as informações de rótulo de texto e de imagem de ícone das propriedades em cada objeto de ponto. 
    
2. Neste tutorial, defina o ponto inicial como uma empresa fictícia em Seattle chamada Fabrikam e o ponto de destino como um escritório da Microsoft. No manipulador de eventos de carregamento do mapa, adicione o código a seguir.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-blue'
    });
    ```
    
    Esse código cria dois [objetos GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) para representar os pontos de partida e chegada da rota. As propriedades `title` e `icon` são adicionadas a cada ponto.

3. Em seguida, adicione o código JavaScript abaixo para adicionar os pinos dos pontos de partida e chegada ao mapa:

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```
    
    Os pontos de partida e chegada são adicionados à fonte de dados. A caixa delimitadora dos pontos de partida e chegada é calculada com a função `atlas.data.BoundingBox.fromData`. Essa caixa delimitadora é usada para definir o modo de exibição das câmeras do mapa nos pontos de partida e chegada usando a função `map.setCamera`. Um preenchimento é adicionado para compensar as dimensões de pixel dos ícones de símbolo.

4. Salve o arquivo e atualize seu navegador para ver os pinos exibidos em seu mapa. Agora o mapa está centralizado em Seattle e você pode ver o pin azul redondo marcando o ponto de início e o pin azul marcando o ponto final.

   ![Exibir o mapa com os pontos inicial e final](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Renderizar rotas priorizadas por modo de viagem

Esta seção mostra como usar a API do serviço de roteiros dos Mapas para encontrar várias rotas a partir de um determinado ponto de partida até um destino com base no seu modo de transporte. O serviço de roteiros fornece APIs para planejar a rota *mais rápida*, *mais curta*, *econômica* ou *emocionante* entre dois locais, considerando as condições de trânsito em tempo real. Também permite planejar rotas futuras usando o amplo banco de dados de histórico de tráfego do Azure e prevendo durações de rotas para cada dia e hora. Para saber mais, confira [Trajeto GetRoute](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Todos os blocos de código a seguir devem ser adicionados **dentro do eventListener de carregamento do mapa** para garantir que eles sejam carregados após o carregamento total do mapa.

1. Instancie o cliente do serviço adicionando o código Javascript a seguir ao manipulador de eventos de carregamento do mapa.

    ```JavaScript
    //If the service client hasn't been created, create an instance of it.
    if (!client) {
        client = new atlas.service.Client(atlas.getSubscriptionKey());
    }
    ```
    
2. Adicione o seguinte bloco de código para construir uma cadeia de consulta de rota.

    ```JavaScript
    //Create the route request with the query being the start and end point in the format 'startLongitude,startLatitude:endLongitude,endLatitude'.
    var routeQuery = startPoint.geometry.coordinates[1] +
        ',' +
        startPoint.geometry.coordinates[0] +
        ':' +
        endPoint.geometry.coordinates[1] +
        ',' +
        endPoint.geometry.coordinates[0];
    ```

3. Adicione o seguinte código JavaScript para solicitar a rota de um caminhão transportando carga de risco classificada como USHazmatClass2 e exibir os resultados:

    ```JavaScript
    //Execute the truck route query then add the route to the map.
    client.route.getRouteDirections(routeQuery, {
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```
    Esse snippet de código consulta o serviço de roteamento do Azure Mapas por meio do método [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) e analisa a resposta no formato GeoJSON usando [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest). Em seguida, ele cria uma matriz de coordenadas para a rota retornada e a adiciona à fonte de dados, mas também adiciona um índice de 0 para fazer com que ela seja processada antes de qualquer outra linha na fonte de dados. Isso é feito porque o cálculo da rota de caminhão geralmente será mais lento do que o cálculo de rota de carro e, se a linha da rota de caminhão for adicionada à fonte de dados após a rota de carro, ela será renderizada por cima. Duas propriedades são adicionadas à linha de rota de caminhão: uma cor de traço que é um belo tom de azul e uma largura de traço de 9 pixels. 

4. Adicione o seguinte código JavaScript para solicitar a rota para um carro e exibir os resultados:

    ```JavaScript
    //Execute the car route query then add the route to the map.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source.
        datasource.add(routeLine);
    });
    ```
    Este snippet de código usa a mesma consulta de rota de caminhão para um carro. Ele consulta o serviço de roteamento do Azure Mapas por meio do método [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) e, então, analisa a resposta no formato GeoJSON usando [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest). Em seguida, ele cria uma matriz de coordenadas para a rota retornada e a adiciona à fonte de dados. Duas propriedades são adicionadas à linha de rota de carro: uma cor de traço que é um tom de roxo e uma largura de traço de 5 pixels. 

5. Salve o arquivo **MapTruckRoute.html** e atualize seu navegador para observar o resultado. Para uma conexão bem-sucedida com APIs dos Mapas, você deverá ver um mapa semelhante ao seguinte.

    ![Rotas priorizadas com o Serviço de Roteiros do Azure](./media/tutorial-prioritized-routes/prioritized-routes.png)

    A rota de caminhão é azul e mais grossa, enquanto a rota de carro é roxa e mais fina. A rota de carro vai passa pelo Lago Washington pela estrada I-90, que passa por túneis em áreas residenciais, restringindo a passagem de carga de lixo tóxico. A rota de caminhão, que especifica um tipo de carga USHazmatClass2, é instruída corretamente a usar uma estrada diferente.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma nova página da Web usando a API de controle de mapeamento
> * Visualizar o fluxo de tráfego em seu mapa
> * Criar consultas de rota que declarem o modo de viagem
> * Exibir várias rotas em seu mapa

Você pode acessar o exemplo de código para este tutorial aqui:

> [Várias rotas com o Azure Mapas](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

[Consulte o exemplo dinâmico aqui](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

Para saber mais sobre a cobertura e as funcionalidades do Azure Mapas:

> [!div class="nextstepaction"]
> [Níveis de zoom e grade lado a lado](zoom-levels-and-tile-grid.md)

Para ver mais exemplos de código e uma experiência interativa de codificação:

> [!div class="nextstepaction"]
> [Como usar o Controle de Mapeamento](how-to-use-map-control.md)
