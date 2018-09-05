---
title: Pesquisar com Mapas do Azure | Microsoft Docs
description: Pesquisar ponto de interesse próximo usando os Mapas do Azure
author: dsk-2015
ms.author: dkshir
ms.date: 08/23/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e30d84c70f786a5bea25073c70a29b63c9a00ae9
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42917655"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Pesquisar pontos de interesse próximos usando os Mapas do Azure

Este tutorial mostra como configurar uma conta dos Mapas do Azure e depois usar as APIs de Mapas para procurar um ponto de interesse. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma conta dos Mapas do Azure
> * Obter a chave primária de sua conta dos Mapas
> * Criar uma nova página da Web usando a API de controle de mapeamento
> * Usar o serviço de pesquisa dos Mapas para localizar um ponto de interesse próximo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure
Faça logon no [Portal do Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Criar uma conta dos Mapas do Azure

Crie uma nova conta dos Mapas seguindo as etapas abaixo:

1. No canto superior esquerdo do [portal do Azure](https://portal.azure.com), clique em **Criar um recurso**.
2. No campo *Pesquisar no Marketplace*, digite **Mapas**.
3. Nos *Resultados*, selecione **Mapas**. Clique no botão **Criar** que aparece abaixo do mapa. 
4. Na página **Criar Conta dos Mapas**, insira os seguintes valores:
    - O *Nome* da sua nova conta. 
    - A *Assinatura* que você deseja usar para a conta.
    - O nome do *Grupo de recursos* para a conta. Você pode optar por *Criar novo* ou *Usar existente* em relação ao grupo de recursos.
    - Selecione a *Localização do grupo de recursos*.
    - Leia a *Política de Privacidade* e de *Licença* e marque a caixa de seleção para aceitar os termos. 
    - Selecione o botão **Criar**.
   
    ![Criar a conta dos Mapas no portal](./media/tutorial-search-location/create-account.png)


<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Obter a chave primária de sua conta

Depois de criar com êxito sua conta dos Mapas, recupere a chave que permite consultar as APIs dos Mapas.

1. Abra a sua conta dos Mapas no portal.
2. Na seção de configurações, selecione **Chaves**.
3. Copie a **Chave Primária** para sua área de transferência. Salve-a localmente para usar depois neste tutorial. 

    ![Obter a Chave Primária no portal](./media/tutorial-search-location/get-key.png)


<a id="createmap"></a>

## <a name="create-a-new-map"></a>Criar um novo mapa 
A API de Controle de Mapeamento é uma biblioteca cliente conveniente que permite integrar facilmente os Mapas do Azure ao seu aplicativo Web. Ela oculta a complexidade das chamadas de serviço básicas REST e aumenta a produtividade com componentes estilizáveis e personalizáveis. As etapas a seguir mostra como criar uma página HTML estática inserida com a API do Controle de Mapeamento. 

1. Em seu computador local, crie um novo arquivo e nomeie-o como **MapSearch.html**. 
2. Adicione os seguintes componentes HTML ao arquivo:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" /> 
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script> 
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.min.js?api-version=1"></script> 

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
    Observe que o cabeçalho HTML inclui os arquivos de recurso CSS e JavaScript hospedados pela biblioteca de Controle de mapa do Azure. Observe o segmento *script* adicionado ao *corpo* do arquivo HTML. Esse segmento conterá o código JavaScript embutido para acessar as APIs dos Mapas do Azure.
 
3. Adicione o seguinte código JavaScript ao bloco de *script* do arquivo HTML. Substitua a cadeia de caracteres **\<sua chave de conta\>** pela chave primária que você copiou da sua conta dos Mapas.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    Este segmento inicia a API de Controle de Mapeamento para a chave da sua conta dos Mapas do Azure. **Atlas** é o namespace que contém a API e os componentes visuais relacionados. **Atlas.Map** fornece o controle para um mapa visual e interativo na Web. 
    
4. Salve suas alterações no arquivo e abra a página HTML em um navegador. Esse é o mapa mais básico que você pode fazer chamando **atlas.map** e fornecendo sua chave de conta. 

   ![Exibir o mapa](./media/tutorial-search-location/basic-map.png)


<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Adicionar recursos de pesquisa

Esta seção mostra como usar a API dos Mapas do Azure para encontrar um ponto de interesse em seu mapa. É uma API RESTful projetada para desenvolvedores para a procura de endereços, pontos de interesse e outras informações geográficas. O serviço de Pesquisa atribui informações de latitude e longitude a um endereço especificado. O **Módulo de Serviço** explicado a seguir pode ser usado para procurar um local usando a API de Pesquisa de Mapas.

### <a name="service-module"></a>Módulo de serviço

1. Adicione uma nova camada ao mapa para exibir os resultados da pesquisa. Adicione o seguinte código Javascript ao bloco de script após o código que inicializa o mapa. 
    
    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    map.addPins([], {
        name: searchLayerName,
        cluster: false,
        icon: "pin-round-darkblue"
    });
    ```

2. Para instanciar o serviço do cliente, adicione o seguinte código Javascript ao bloco de script após o código que inicializa o mapa.

    ```JavaScript
    var client = new atlas.service.Client(subscriptionKey);
    ```

3. Adicione o seguinte bloco de script para criar a consulta. Ele usa o Serviço de Pesquisa Difusa, que é uma API de pesquisa básica do Serviço de Pesquisa. O Serviço de Pesquisa Difusa manipula a maioria das entradas difusas como qualquer combinação de endereço e tokens do ponto de interesse (POI). Ele pesquisa por postos de gasolina próximos, dentro do raio especificado. A resposta é analisada em formato GeoJSON e convertida em recursos de ponto, que são adicionados ao mapa como pinos. A última parte do script adiciona limites de câmera para o mapa por meio do uso da propriedade do mapa [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest).

    ```JavaScript
    client.search.getSearchFuzzy("gasoline station", {
     lat: 47.6292,
     lon: -122.2337,
     radius: 100000
    }).then(response => {
       // Parse the response into GeoJSON 
       var geojsonResponse = new atlas.service.geojson.GeoJsonSearchResponse(response); 
 
       // Create the point features that will be added to the map as pins 
       var searchPins = geojsonResponse.getGeoJsonResults().features.map(poiResult => { 
           var poiPosition = [poiResult.properties.position.lon, poiResult.properties.position.lat]; 
           return new atlas.data.Feature(new atlas.data.Point(poiPosition), { 
                name: poiResult.properties.poi.name, 
                address: poiResult.properties.address.freeformAddress, 
                position: poiPosition[1] + ", " + poiPosition[0] 
           }); 
       }); 
 
       // Add pins to the map for each POI 
       map.addPins(searchPins, { 
           name: searchLayerName 
       }); 
 
       // Set the camera bounds 
       map.setCameraBounds({ 
           bounds: geojsonResponse.getGeoJsonResults().bbox, 
           padding: 50 
       ); 
    }); 
    ```
4. Salve o arquivo **MapSearch.html** e atualize seu navegador. Agora você verá que o mapa está centralizado em Seattle e pins azuis marcam os locais de postos de gasolina na região. 

   ![Exibir o mapa com os resultados da pesquisa](./media/tutorial-search-location/pins-map.png)

5. Você pode ver os dados brutos que o mapa estiver renderizando ao digitar o seguinte HTTPRequest em seu navegador. Substitua \<sua chave de conta\> por sua chave primária. 

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<your account key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

Neste momento, a página MapSearch pode exibir os locais de pontos de interesse que são retornados de uma consulta de pesquisa difusa. Vamos adicionar alguns recursos interativos e mais informações sobre os locais. 

## <a name="add-interactive-data"></a>Adicionar dados interativos

O mapa que fizemos até agora apenas analisa os dados de latitude/longitude para os resultados da pesquisa. Se você examinar o JSON bruto retornado pelo serviço de Pesquisa de Mapas, no entanto, verá que ele contém informações adicionais sobre cada posto de gasolina, incluindo o nome e endereço. Você pode incorporar esses dados ao mapa com caixas pop-up interativas. 

1. Adicione as linhas a seguir o bloco de *script* para criar pop-ups para os pontos de interesse retornados pelo Serviço de Pesquisa:

    ```JavaScript
    // Add a popup to the map which will display some basic information about a search result on hover over a pin
    var popup = new atlas.Popup();
    map.addEventListener("mouseover", searchLayerName, (e) => {
        var popupContentElement = document.createElement("div");
        popupContentElement.style.padding = "5px";

        var popupNameElement = document.createElement("div");
        popupNameElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupNameElement);

        var popupAddressElement = document.createElement("div");
        popupAddressElement.innerText = e.features[0].properties.address;
        popupContentElement.appendChild(popupAddressElement);

        var popupPositionElement = document.createElement("div");
        popupPositionElement.innerText = e.features[0].properties.position;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    A API **atlas.Popup** fornece uma janela de informações ancorada na posição requerida no mapa. Este trecho de código define o conteúdo e a posição para a pop-up, além de adicionar um ouvinte de eventos para o controle de `map`, aguardando o _mouse_ passar sobre a pop-up. 

4. Salve o arquivo e atualize seu navegador. Agora, o mapa no navegador mostra pop-ups de informações quando você passa o mouse sobre qualquer marcação de pesquisa. 

    ![Controle de mapa do Azure e Serviço de Pesquisa](./media/tutorial-search-location/popup-map.png)


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma conta dos Mapas do Azure
> * Obter a chave primária de sua conta
> * Criar nova página da Web usando a API de Controle de mapa
> * Usar o Serviço de Pesquisa para localizar um ponto de interesse próximo

O seguinte tutorial demonstra como exibir uma rota entre dois locais. 

> [!div class="nextstepaction"]
> [Rotear para um destino](./tutorial-route-location.md)
