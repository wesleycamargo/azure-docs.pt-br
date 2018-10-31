---
title: Como usar o Controle de Mapeamento dos Mapas do Azure | Microsoft Docs
description: Saiba como usar a biblioteca Javascript do lado do cliente do Controle de Mapeamento dos Mapas do Azure.
author: dsk-2015
ms.author: dkshir
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 850f9b28c112c11fd98a8abc81a1811cd26d81cc
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166010"
---
# <a name="use-the-azure-maps-map-control"></a>Use o controle de mapa de mapas do Azure

A biblioteca Javascript do lado do cliente do Controle de Mapeamento permite renderizar mapas e funcionalidade inserida nos Mapas do Azure em seu aplicativo Web ou móvel.

## <a name="create-a-new-map-in-a-web-page"></a>Criar um novo mapa em uma página da web

Você pode inserir um mapa em uma página da Web usando a biblioteca Javascript do lado do cliente do Controle de Mapeamento.

1. Crie um novo arquivo e nomeie- **Mapsearch**.

2. Adicione as referências de origem de scirpt e de folha de estilos dos Mapas do Azure ao elemento `<head>` do arquivo:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
    ```

3. Para renderizar um novo mapa em seu navegador, adicione uma referência **#map** no elemento `<style>`:

    ```html
    <style>
        #map {
            width: 100%;
            height: 100%;
        }
    </style>
    ```

4. Para inicializar o Controle de Mapeamento, defina uma nova seção no corpo html e crie um script. Use sua própria chave de conta dos Mapas do Azure no script. Se você precisar criar uma conta ou encontrar sua chave, consulte [Como gerenciar sua conta e chaves do Azure Maps](how-to-manage-account-keys.md). O método **setLanguage** especifica o idioma a ser usado para rótulos de mapa e controles. Para obter mais informações sobre linguagens com suporte, confira [Linguagens com suporte](https://docs.microsoft.com/azure/azure-maps/supported-languages).

    ```html
    <div id="map">
        <script>
            atlas.setSubscriptionKey("<_your account key_>");
            atlas.setLanguage("en");
            var map = new atlas.Map("map", {
                center: [-122.33263,47.59093],
                zoom: 12
            });
        </script>
    </div>
    ```

5. Abra o arquivo no seu navegador da Web e exiba o mapa renderizado.

## <a name="next-steps"></a>Próximas etapas

Saiba como criar um mapa usando um exemplo completo:

> [!div class="nextstepaction"]
> [Criar um mapa](map-create.md)

Saiba como definir o estilo de um mapa:

> [!div class="nextstepaction"]
> [Escolher um estilo de mapa](choose-map-style.md)
