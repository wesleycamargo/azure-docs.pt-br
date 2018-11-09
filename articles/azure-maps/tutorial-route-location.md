---
title: Encontrar rota com os Mapas do Azure | Microsoft Docs
description: Rotear para um ponto de interesse usando os Mapas do Azure
author: walsehgal
ms.author: v-musehg
ms.date: 10/22/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b6ce89d141af434d4f40e9079b39e4d7eed114df
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420907"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Rotear para um ponto de interesse usando os Mapas do Azure

Este tutorial mostra como usar sua conta dos Mapas do Azure e o SDK do Serviço de Roteiros para encontrar a rota para seu ponto de interesse. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma nova página da Web usando a API de controle de mapeamento
> * Definir coordenadas de endereço
> * Serviço de Roteiros de Consulta para obter o trajeto até o ponto de interesse

## <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, siga as etapas no tutorial anterior para [criar sua conta dos Mapas do Azure](./tutorial-search-location.md#createaccount) e [obter a chave de assinatura da conta](./tutorial-search-location.md#getkey).

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Criar um novo mapa

As etapas a seguir mostra como criar uma página HTML estática inserida com a API do Controle de Mapeamento.

1. Em seu computador local, crie um novo arquivo e nomeie-o como **MapRoute.html**.
2. Adicione os seguintes componentes HTML ao arquivo:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
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

            #map {
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

    O `atlas.Map` fornece o controle de um mapa Web visual e interativo, além de ser um componente da API de Controle de Mapeamento do Azure.

4. Salve o arquivo e abra-o em seu navegador. Agora você tem um mapa básico que você pode desenvolver ainda mais.

   ![Exibir mapa básico](./media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Definir como a rota será renderizada

Neste tutorial, uma rota simples será renderizada usando um ícone de símbolo para o início e o fim da rota e uma linha para o caminho da rota.

1. Na função GetMap, depois de inicializar o mapa, adicione o código JavaScript a seguir.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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
    
    Um evento de carregamento é adicionado ao mapa, que será disparado quando os recursos de mapa forem totalmente carregados. No manipulador de eventos de carregamento do mapa, uma fonte de dados é criada para armazenar a linha da rota e os pontos de partida e de chegada. Uma camada de linhas é criada e anexada à fonte de dados para definir como a linha de rota será renderizada. A linha da rota será renderizada em uma bela tonalidade de azul com uma largura de 5 pixels e limites e junções arredondadas. Um filtro é adicionado para fazer com que essa camada renderize apenas dados LineString GeoJSON. Quando você adiciona a camada ao mapa, um segundo parâmetro com o valor de `'labels'` é passado especificando que essa camada deve ser renderizada abaixo dos rótulos do mapa. Isso fará com que a linha de rota não cubra os rótulos de estrada. Uma camada de símbolo é criada e anexada à fonte de dados. Essa camada especifica como os pontos de partida e chegada serão renderizados; neste caso, expressões foram adicionadas para recuperar as informações de rótulo de texto e de imagem de ícone das propriedades em cada objeto de ponto. 
    
2. Neste tutorial, defina o ponto de partida como Microsoft e o ponto de chegada como um posto de gasolina em Seattle. No manipulador de eventos de carregamento do mapa, adicione o código a seguir.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: 'Microsoft',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: 'Contoso Oil & Gas',
        icon: 'pin-blue'
    });    
    ```

    Esse código cria dois [objetos de Ponto GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) para representar os pontos de partida e chegada da rota. As propriedades `title` e `icon` são adicionadas a cada ponto.
    
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
    
    Os pontos de partida e chegada são adicionados à fonte de dados. A caixa delimitadora dos pontos de partida e chegada é calculada com a função `atlas.data.BoundingBox.fromData`. Essa caixa delimitadora é usada para definir o modo de exibição das câmeras do mapa nos pontos de partida e chegada usando a função **map.setCamera**. Um preenchimento é adicionado para compensar as dimensões de pixel dos ícones de símbolo.

3. Salve o arquivo **MapRoute.html** e atualize seu navegador. Agora o mapa está centralizado em Seattle e você pode ver o pin azul redondo marcando o ponto de início e o pin azul marcando o ponto final.

   ![Exibir o mapa com os pontos inicial e final marcados](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Obter direções

Esta seção mostra como usar a API do serviço de roteiros dos Mapas para encontrar várias rotas a partir de um determinado ponto de partida até um destino. O serviço de roteiros fornece APIs para planejar as rotas *mais rápida*, *mais curta*, *econômica* ou *emocionante* entre dois locais. Também permite planejar rotas futuras usando o amplo banco de dados de histórico de tráfego do Azure e prevendo durações de rotas para cada dia e hora. Para saber mais, confira [Obter direções de rota](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Todas as funcionalidades a seguir devem ser adicionadas **dentro do eventListener do carregamento de mapa** para assegurar que elas sejam carregadas após o carregamento total do mapa.

1. Instancie o cliente do serviço adicionando o código Javascript a seguir ao manipulador de eventos de carregamento do mapa.

    ```JavaScript
    //If the service client hasn't already been created, create an instance.
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

3. Para obter a rota, adicione o seguinte bloco de código ao script. Ele consulta o serviço de roteamento do Azure Mapas por meio do método [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) e, em seguida, analisa a resposta no formato GeoJSON usando [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes). Ele adiciona a linha da rota em resposta à fonte de dados que automaticamente a renderiza no mapa.

    ```JavaScript
    //Execute the car route query then add the route to the map once a response is received.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Add the route line to the data source.
        datasource.add(geoJsonResponse.getGeoJsonRoutes().features[0]);
    });
    ```

5. Salve o arquivo **MapRoute.html** e atualize seu navegador da Web. Para uma conexão bem-sucedida com APIs dos Mapas, você deverá ver um mapa semelhante ao seguinte.

    ![Controle de Mapeamento e Serviço de Roteiros do Azure](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma nova página da Web usando a API de controle de mapeamento
> * Definir coordenadas de endereço
> * Consultar o serviço de roteiros para obter o trajeto até o ponto de interesse

Você pode acessar o exemplo de código para este tutorial aqui:

> [Encontrar rota com os Azure Mapas](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

[Veja este exemplo dinâmico aqui](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

O tutorial a seguir demonstra como criar uma consulta de rota com restrições como modo de viagem ou tipo de carga e, em seguida, exiba várias rotas no mesmo mapa.

> [!div class="nextstepaction"]
> [Localizar rotas para diferentes modos de viagem](./tutorial-prioritized-routes.md)
