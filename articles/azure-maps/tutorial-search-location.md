---
title: Pesquisar com Mapas do Azure | Microsoft Docs
description: Pesquisar ponto de interesse próximo usando os Mapas do Azure
author: walsehgal
ms.author: v-musehg
ms.date: 12/14/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 1396260ef703ce22f8e0309bd2c8df691d0af86e
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120454"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Pesquisar pontos de interesse próximos usando os Mapas do Azure

Este tutorial mostra como configurar uma conta dos Mapas do Azure e depois usar as APIs de Mapas para procurar um ponto de interesse. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma conta dos Mapas do Azure
> * Obter a chave primária de sua conta dos Mapas
> * Criar uma nova página da Web usando a API de controle de mapeamento
> * Usar o serviço de pesquisa dos Mapas para localizar um ponto de interesse próximo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Criar uma conta dos Mapas do Azure

Crie uma nova conta dos Mapas seguindo as etapas abaixo:

1. No canto superior esquerdo do [portal do Azure](https://portal.azure.com), clique em **Criar um recurso**.
2. No campo *Pesquisar no Marketplace*, digite **Mapas**.
3. Nos *Resultados*, selecione **Mapas**. Clique no botão **Criar** que aparece abaixo do mapa.
4. Na página **Criar Conta dos Mapas**, insira os seguintes valores:
    * O *Nome* da sua nova conta.
    * A *Assinatura* que você deseja usar para a conta.
    * O nome do *Grupo de recursos* para a conta. Você pode optar por *Criar novo* ou *Usar existente* em relação ao grupo de recursos.
    * Selecione a *Localização do grupo de recursos*.
    * Leia a *Política de Privacidade* e de *Licença* e marque a caixa de seleção para aceitar os termos.
    * Selecione o botão **Criar**.

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
   <html>
   <head>
      <title>Map Search</title>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

      <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
      <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

      <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
      <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>

      <script>      
         var map, datasource, client, popup;

         function GetMap(){
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

3. Adicione o seguinte código JavaScript à função `GetMap` do arquivo HTML. Substitua a cadeia de caracteres **\<Sua Chave do Azure Mapas\>** pela chave primária que você copiou da conta dos Mapas.

   ```JavaScript
   //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
   atlas.setSubscriptionKey('<Your Azure Maps Key>');

   //Initialize a map instance.
   map = new atlas.Map('myMap');
   ```

   Este segmento inicializa a API de Controle de Mapeamento da sua chave de conta do Azure Mapas. **atlas** é o namespace que contém a API e os componentes visuais relacionados. **atlas.Map** fornece o controle para um mapa visual e interativo na Web. 

4. Salve suas alterações no arquivo e abra a página HTML em um navegador. Esse é o mapa mais básico que você pode fazer chamando **atlas.map** e usando sua chave de conta.

   ![Exibir o mapa](./media/tutorial-search-location/basic-map.png)

5. Na função `GetMap`, depois de inicializar o mapa, adicione o código JavaScript a seguir. 

   ```JavaScript
   //Wait until the map resources have fully loaded.
   map.events.add('load', function () {

      //Create a data source and add it to the map.
      datasource = new atlas.source.DataSource();
      map.sources.add(datasource);

      //Add a layer for rendering point data.
      var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
         iconOptions: {
            iconImage: 'pin-round-darkblue',
            anchor: 'center',
            allowOverlap: true
         }
      });
      map.layers.add(resultLayer);

      //Create a popup but leave it closed so we can update it and display it later.
      popup = new atlas.Popup();

      //Add a mouse over event to the result layer and display a popup when this event fires.
      map.events.add('mouseover', resultLayer, showPopup);
   });
   ```

   Um evento de carregamento é adicionado ao mapa, que será disparado quando os recursos de mapa forem totalmente carregados. No manipulador de eventos de carregamento do mapa, uma fonte de dados é criada para armazenar os dados de resultado. Uma camada de símbolo é criada e anexada à fonte de dados. Essa camada especifica como os dados de resultado na fonte de dados devem ser renderizados; neste caso, com um ícone de pino redondo azul escuro centralizado sobre as coordenadas do resultado e que permite a sobreposição de outros ícones. 

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Adicionar recursos de pesquisa

Esta seção mostra como usar a API dos Mapas do Azure para encontrar um ponto de interesse em seu mapa. Trata-se de uma API RESTful projetada para desenvolvedores para pesquisa de endereços, pontos de interesse e outras informações geográficas. O serviço de Pesquisa atribui informações de latitude e longitude a um endereço especificado. O **Módulo de Serviço** explicado a seguir pode ser usado para procurar um local usando a API de Pesquisa de Mapas.

### <a name="service-module"></a>Módulo de serviço

1. No manipulador de eventos de carregamento do mapa, instancie o cliente do serviço adicionando o código JavaScript a seguir.

    ```JavaScript
    //Create an instance of the services client.
     client = new atlas.service.Client(atlas.getSubscriptionKey());
    ```

2. Adicione o bloco de script a seguir para criar a consulta de pesquisa. Ele usa o Serviço de Pesquisa Difusa, que é uma API de pesquisa básica do Serviço de Pesquisa. O Serviço de Pesquisa Difusa trata a maioria das entradas difusas, como endereços, locais e POI (pontos de interesse). Esse código pesquisa postos de gasolina próximos, dentro do raio especificado. A resposta, em seguida, é analisada em formato GeoJSON e adicionada à fonte de dados, o que automaticamente resulta na renderização de dados no mapa por meio da camada de símbolos. A última parte do script define a exibição de câmera dos mapas usando a caixa delimitadora dos resultados com a propriedade [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do mapa. Um preenchimento é adicionado para compensar as dimensões de pixel dos ícones de símbolo, já que a caixa delimitadora é calculada com base nas coordenadas. 
 
   ```JavaScript
   //Execute a POI search query then add the results to the map.
    client.search.getSearchPOI('gasoline station', {
        lat: 47.6292,
        lon: -122.2337,
        radius: 100000
    }).then(response => {
        //Parse the response into GeoJSON so that the map can understand.
        var geojsonResponse = new atlas.service.geojson.GeoJsonSearchResponse(response);
        var results = geojsonResponse.getGeoJsonResults();

        //Add the results to the data source so they can be rendered. 
        datasource.add(results);

        // Set the camera bounds
        map.setCamera({
            bounds: results.bbox,
            padding: 50
        });
    });
   ```
 
3. Salve o arquivo **MapSearch.html** e atualize seu navegador. Agora, você verá que o mapa está centralizado em Seattle e alfinetes azuis marcam os locais de postos de gasolina na região.

   ![Exibir o mapa com os resultados da pesquisa](./media/tutorial-search-location/pins-map.png)

4. Você pode ver os dados brutos que o mapa estiver renderizando ao digitar o seguinte HTTPRequest em seu navegador. Substitua \<Sua Chave da Conta do Azure Mapas\> pela chave primária.

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<Your Azure Maps Key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

Neste momento, a página MapSearch pode exibir os locais de pontos de interesse que são retornados de uma consulta de pesquisa difusa. Vamos adicionar alguns recursos interativos e mais informações sobre os locais.

## <a name="add-interactive-data"></a>Adicionar dados interativos

O mapa que fizemos até agora apenas analisa os dados de longitude/latitude para os resultados da pesquisa. Se você examinar o JSON bruto retornado pelo serviço de Pesquisa de Mapas, no entanto, verá que ele contém informações adicionais sobre cada posto de gasolina, incluindo o nome e endereço. Você pode incorporar esses dados ao mapa com caixas pop-up interativas.

1. Adicione as linhas de código a seguir no manipulador de eventos de carregamento do mapa após o código para consultar o serviço de pesquisa difusa. Isso criará uma instância de pop-up e adicionará um evento de passar o mouse à camada de símbolos.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```
    
    A API **atlas.Popup** fornece uma janela de informações ancorada na posição requerida no mapa. 
      
2. Na marca *script*, após a função `GetMap`, adicione o código a seguir para mostrar as informações do resultado no pop-up depois de passar o mouse. 

   ```JavaScript
   function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occurred on.
        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = ['<div style="padding:5px"><div><b>', p.poi.name,
            '</b></div><div>', p.address.freeformAddress,
            '</div><div>', position[1], ', ', position[0], '</div></div>'];

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html.join(''),
            position: position
        });

        //Open the popup.
        popup.open(map);
   }
   ```

2. Salve o arquivo e atualize seu navegador. Agora, o mapa no navegador mostra pop-ups de informações quando você passa o mouse sobre qualquer marcação de pesquisa.

    ![Controle de mapa do Azure e Serviço de Pesquisa](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma conta dos Mapas do Azure
> * Obter a chave primária de sua conta
> * Criar nova página da Web usando a API de Controle de mapa
> * Usar o Serviço de Pesquisa para localizar um ponto de interesse próximo

Você pode acessar o exemplo de código para este tutorial aqui:

> [Pesquisa de local com o Azure Mapas](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

[Confira o exemplo dinâmico aqui](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

O seguinte tutorial demonstra como exibir uma rota entre dois locais.

> [!div class="nextstepaction"]
> [Rotear para um destino](./tutorial-route-location.md)
