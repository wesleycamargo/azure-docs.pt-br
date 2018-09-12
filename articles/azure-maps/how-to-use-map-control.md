---
title: Como usar o Controle de Mapeamento dos Mapas do Azure | Microsoft Docs
description: Saiba como usar a biblioteca Javascript do lado do cliente do Controle de Mapeamento dos Mapas do Azure.
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 5b8703c218790549a0cf5a319345132a0eca66ce
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2018
ms.locfileid: "44025182"
---
# <a name="how-to-use-the-azure-maps-map-control"></a>Como usar o Controle de Mapeamento dos Mapas do Azure
A biblioteca Javascript do lado do cliente do Controle de Mapeamento permite renderizar mapas e funcionalidade inserida nos Mapas do Azure em seu aplicativo Web ou móvel. 

## <a name="create-a-new-map-in-a-web-page"></a>Criar um novo mapa em uma página da web

Você pode inserir um mapa em uma página da Web usando a biblioteca Javascript do lado do cliente do Controle de Mapeamento.

1. Crie um novo arquivo e nomeie-o como MapSearch.html.

2. Adicione as referências de origem de scirpt e de folha de estilos dos Mapas do Azure ao elemento `<head>` do arquivo:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
    ```
    
3. Para renderizar um novo mapa em seu navegador, adicione uma referência **#map** ao elemento `<style>`.

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. Para inicializar o controle de mapa, defina uma nova seção no corpo html e crie um script. Use sua própria chave de conta dos Mapas do Azure no script. Se você precisar criar uma conta ou encontrar sua chave, consulte [Como gerenciar sua conta e chaves dos Mapas do Azure](how-to-manage-account-keys.md)

    ```html
    <div id="map">
        <script>
            var MapsAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": MapsAccountKey,
                center: [-122.33263,47.59093],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. Abra o arquivo no seu navegador da Web e exiba o mapa renderizado.

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como criar um mapa básico com sua chave de Mapas do Azure. Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos: 

* [Criar um mapa](map-create.md)
* [Escolher um estilo de mapa](choose-map-style.md)
