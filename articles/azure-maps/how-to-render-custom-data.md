---
title: Como renderizar dados personalizados no mapa de varredura no Azure Mapas | Microsoft Docs
description: Renderize dados personalizados no mapa de varredura no Azure Mapas.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9b3c0f7b1ff56cb269f6852be8fd2affeca8b8f1
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56119003"
---
# <a name="render-custom-data-on-raster-map"></a>Renderizar dados personalizados no mapa de varredura

Este artigo mostra como usar o [serviço de imagem estática](https://docs.microsoft.com/rest/api/maps/render/getmapimage) com a funcionalidade de composição de imagem para permitir sobreposições na parte superior de um mapa de varredura. A composição da imagem inclui a capacidade de recuperar um bloco de varredura com dados adicionais como sobreposições de geometria, rótulos e pinos personalizados. Para renderizar geometria, rótulos e pinos personalizados, usaremos o aplicativo Postman. Abra o aplicativo Postman, clique em Novo | Criar Novo, selecione e nomeie uma coleção ou pasta para salvá-la e, em seguida, clique em Salvar.

Usaremos [APIs de Serviço de Dados](https://docs.microsoft.com/rest/api/maps/data) do Azure Mapas para armazenar e renderizar sobreposições. 


## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta dos Mapas do Azure 

Para seguir as etapas deste guia, primeiro é necessário consultar [gerenciar conta e chaves](how-to-manage-account-keys.md) para criar e gerenciar a assinatura da conta com tipo de preço S1.

## <a name="render-pushpins-with-labels-and-custom-image"></a>Renderizar pinos com rótulos e imagem personalizada

> [!Note]
> Esse exemplo requer uma conta do Azure Mapas com tipo de preço S0 ou S1. 

A SKU S0 da conta do Azure Mapas dá suporte apenas a uma única instância do parâmetro `pins` permitindo que os usuários renderizem até 5 pinos especificados na solicitação de URL com imagem personalizada.

Para renderizar pinos com rótulos e imagens personalizadas, siga as etapas abaixo:

1. Abra o aplicativo Postman e clique em novo | Criar novo e selecione Solicitar. Digite um nome da Solicitação para pinos de renderização, selecione uma coleção ou pasta para salvá-la e clique em Salvar.
    
    ![Carregar cercas geográficas usando Postman](./media/tutorial-geofence/postman-new.png)

2. Selecione o método HTTP GET na guia do construtor e insira a seguinte URL para fazer uma solicitação GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    A seguir, a imagem de resposta que você recebe.

    ![renderizar pinos personalizados com rótulos](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Obter dados do armazenamento de dados do Azure Mapas

> [!Note]
> Esse exemplo requer uma conta do Azure Mapas com tipo de preço S1.

As informações de localização de marcadores e caminho também podem ser obtidas por meio da [API de Upload de Dados](https://docs.microsoft.com/rest/api/maps/mapdata/upload). Siga as etapas abaixo para carregar o caminho e os dados dos marcadores.

1. No aplicativo Postman, abra uma nova guia na mesma coleção criada acima. Selecione o método HTTP POST na guia do construtor e insira a seguinte URL para fazer uma solicitação POST:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Clique em Parâmetros e, em seguida, insira os seguintes pares chave-valor a serem usados para a URL de solicitação POST. Substitua o valor subscription-key pela chave de assinatura do Azure Mapas.
    
    ![Postman de parâmetros de chave-valor](./media/how-to-render-custom-data/postman-key-vals.png)

3. Clique em **Corpo** e, em seguida, selecione o formato de entrada bruto e escolha JSON como o formato de entrada na lista suspensa. Forneça o seguinte JSON como dados a serem carregados:
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. Clique em enviar e revise o cabeçalho de resposta. O cabeçalho de localização contém o URI para acessar ou baixar os dados para uso futuro. Também contém um `udId` exclusivo para os dados carregados.   

  ```HTTP
  https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
  ```



5. Use o valor de `udid` recebido da API de Upload de Dados para renderizar recursos no mapa. Para fazer isso, abra uma nova guia na mesma coleção criada acima. Selecione o método HTTP GET na guia do construtor e insira a seguinte URL para fazer uma solicitação GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. A imagem de resposta deverá ser semelhante à seguinte:

    ![renderizar dados carregados](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-polygon-with-color-and-opacity"></a>Renderizar polígono com cor e opacidade

> [!Note]
> Esse exemplo requer uma conta do Azure Mapas com tipo de preço S1.

É possível modificar a aparência de um polígono, usando modificadores de estilo com o [parâmetro de caminho](https://docs.microsoft.com/rest-staging/api/maps/render/getmapimage#uri-parameters).

1. No aplicativo Postman, abra uma nova guia na mesma coleção criada acima. Selecione o método HTTP GET na guia do construtor e insira a seguinte URL para fazer uma solicitação GET para renderizar um polígono com cor e opacidade:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

A imagem de resposta deverá ser semelhante à seguinte:

![renderizar polígono opaco](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-polygon-with-circle-and-push-pins-with-custom-labels"></a>Renderizar polígono com círculo e pinos com rótulos personalizados

> [!Note]
> Esse exemplo requer uma conta do Azure Mapas com tipo de preço S1.

É possível criar pinos e rótulos maiores ou menores, usando o modificador de estilo de escala 'sc'. Esse é um valor maior que zero. Um valor de 1 é a escala padrão. Valores maiores que 1 tornarão os marcadores maiores e valores menores que 1 os tornarão menores. Para obter mais informações sobre modificadores de estilo, consulte [parâmetros de caminho de serviço de imagem estática](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

Siga as etapas abaixo para renderizar um polígono com um círculo e pinos com rótulos personalizados:

1. No aplicativo Postman, abra uma nova guia na mesma coleção criada acima. Selecione o método HTTP GET na guia do construtor e insira a seguinte URL para fazer uma solicitação GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

A imagem de resposta deverá ser semelhante à seguinte:

![renderizar círculo com marcadores personalizados](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>Próximas etapas

* Explore a documentação [Obter API de Imagem de Mapa do Azure Mapas](https://docs.microsoft.com/rest/api/maps/search).
* Para saber mais sobre as funcionalidades do Serviço de Dados do Azure Mapas, consulte [Documentação de serviço](https://docs.microsoft.com/rest/api/maps/data).