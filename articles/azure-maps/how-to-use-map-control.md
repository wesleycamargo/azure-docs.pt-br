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
ms.openlocfilehash: 9e3a442a3d6c420c548979327c193628efbee5aa
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549211"
---
# <a name="use-the-azure-maps-map-control"></a>Use o controle de mapa de mapas do Azure

A biblioteca Javascript do lado do cliente do Controle de Mapeamento permite renderizar mapas e funcionalidade inserida nos Mapas do Azure em seu aplicativo Web ou móvel.

## <a name="create-a-new-map-in-a-web-page"></a>Criar um novo mapa em uma página da web

Você pode inserir um mapa em uma página da Web usando a biblioteca Javascript do lado do cliente do Controle de Mapeamento.

1. Criar um novo arquivo HTML.

2. Carregar no SDK Web do Azure Mapas. Isso pode ser feito usando uma de duas opções:

     a. Use a versão do CDN hospedada globalmente do SDK Web do Azure Mapas adicionando os pontos de extremidade da URL à folha de estilos e às referências de script no elemento `<head>` do arquivo:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. Como alternativa, carregue o código-fonte do SDK Web do Azure Mapas localmente usando o pacote NPM [azure-maps-control](https://www.npmjs.com/package/azure-maps-control) e hospede-o com seu aplicativo. Esse pacote também inclui definições de TypeScript.

    > npm install azure-maps-control

    Em seguida, adicione as referências à folha de estilos do Azure Mapas e referências de origem de script ao elemento `<head>` do arquivo:

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css">
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

3. Para renderizar o mapa de modo que ele preencha a página inteira, adicione o seguinte elemento `<style>` ao elemento `<head>`.

    ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
    ```

4. No corpo da página, adicione um elemento `<div>` e dê a ele um `id` de **myMap**.

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. Para inicializar o Controle de Mapeamento, defina uma nova seção no corpo html e crie um script. Use sua própria chave de conta do Azure Mapas ou as credenciais do AAD (Azure Active Directory) para autenticar o mapa usando as [opções de autenticação](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). Se você precisar criar uma conta ou encontrar sua chave, consulte [Como gerenciar sua conta e chaves do Azure Maps](how-to-manage-account-keys.md). A opção **linguagem** especifica a linguagem a ser usada nos rótulos e nos controles de mapa. Para obter mais informações sobre linguagens com suporte, confira [Linguagens com suporte](supported-languages.md). Se estiver usando uma chave de assinatura para autenticação.

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

    Se estiver usando o AAD (Azure Active Directory) para autenticação:

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: 'msft.ccsctp.net'
            }
        });
    </script>
    ```

    Para obter mais informações, consulte [autenticação com o Azure mapas](azure-maps-authentication.md) para obter mais detalhes.

6. Como opção, você poderá considerar útil adicionar os seguintes elementos de marcação meta ao cabeçalho da sua página:

    ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

7. Juntando as peças seu arquivo HTML deve ser algo parecido com o código a seguir:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. Abra o arquivo no seu navegador da Web e exiba o mapa renderizado. Deve se parecer com o código a seguir:

    <iframe height="700" style="width: 100%;" scrolling="no" title="Como usar o controle de mapeamento" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">Consulte a caneta <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>como usar o controle de mapa</a> por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba como criar e interagir com um mapa:

> [!div class="nextstepaction"]
> [Criar um mapa](map-create.md)

Saiba como definir o estilo de um mapa:

> [!div class="nextstepaction"]
> [Escolher um estilo de mapa](choose-map-style.md)
