---
title: Usando o módulo de serviços - mapas do Azure | Microsoft Docs
description: Saiba como usar o módulo de serviços de mapas do Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 335e114fc6a4afa83a6b82509148cf258a9e8347
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58501145"
---
# <a name="using-the-azure-maps-services-module"></a>Usando o módulo de serviços de mapas do Azure

SDK da Web de mapas do Azure fornece um módulo de serviços é uma biblioteca auxiliar que torna mais fácil de usar os serviços REST de mapas do Azure em aplicativos web ou Node. js usando JavaScript ou TypeScript.

## <a name="using-the-services-module-in-a-web-page"></a>Usando o módulo de serviços em uma página da web

1. Criar um novo arquivo HTML.
2. Carregar o módulo de serviços de mapas do Azure. Isso pode ser feito usando uma de duas opções:

     a. Usar a versão CDN globalmente hospedada do módulo de serviços de mapas do Azure, adicionando uma referência de script para o <head> elemento do arquivo:
    
    ```html
    <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2"></script>
    ```
    
    b. Como alternativa, carregar o código-fonte do SDK do Azure mapeia Web localmente usando o [rest do azure-maps](https://www.npmjs.com/package/azure-maps-rest) NPM do pacote e hospedá-lo com seu aplicativo. Esse pacote também inclui definições de TypeScript.
    
    > npm install azure-mapas-rest
    
    Em seguida, adicione um script fizer referência para o `<head>` elemento do arquivo:
    
    ```html
    <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
    ```

3. Para inicializar um ponto de extremidade do cliente de URL do serviço, você deve primeiro criar um pipeline de autenticação. Use sua própria chave de conta de mapas do Azure ou as credenciais do Azure Active Directory (AAD) para autenticar o cliente do serviço de pesquisa. Neste exemplo, o cliente de URL do serviço de pesquisa será criado. Se estiver usando uma chave de assinatura para autenticação:

    ```javascript
    //Get an Azure Maps key at https://azure.com/maps
    var subscriptionKey = '<Your Azure Maps Key>';
    
    //Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);
    
    //Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```
    
    Se estiver usando o AAD (Azure Active Directory) para autenticação:

    ```javascript
    // Enter your Azure Actiuve Directory client ID.
    var clientId = "<Your Azure Active Directory Client Id>";
    
    // Use TokenCredential with OAuth token (AAD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);
    
    // Create a repeating timeout that will renew the AAD token.
    // This timeout must be cleared once the TokenCredential object is no longer needed.
    // If the timeout is not cleared the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
        try {
            console.log("Renewing token");
            var token = await getAadToken();
            tokenCredential.token = token;
            tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
        } catch (error) {
            console.log("Caught error when renewing token");
            clearTimeout(tokenRenewalTimer);
            throw error;
        }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));
    
    // Use tokenCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
        retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
        //Use the logged in auth context to get a token.
        return new Promise((resolve, reject) => {
            //The resource should always be https://atlas.microsoft.com/.
            const resource = "https://atlas.microsoft.com/";
            authContext.acquireToken(resource, (error, token) => {
                if (error) {
                    reject(error);
                } else {
                    resolve(token);
                }
            });
        })
    }

    function getExpiration(jwtToken) {
        //Decode the JWT token to get the expiration timestamp.
        const json = atob(jwtToken.split(".")[1]);
        const decode = JSON.parse(json);

        //Return the milliseconds until the token needs renewed.
        //Reduce the time until renew by 5 minutes to avoid using an expired token.
        //The exp property is the timestamp of the expiration in seconds.
        const renewSkew = 300000;
        return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Para obter mais informações, consulte [autenticação com o Azure mapas](azure-maps-authentication.md).

4. O código a seguir usa o cliente de URL do serviço de pesquisa criado recentemente para geocodificar um endereço, "1 Microsoft Way, Redmond, WA" usando o `searchAddress` de função e exibir os resultados como uma tabela no corpo da página. 

    ```javascript
    //Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa').then(response => {
      var html = [];
      
      //Display the total results.
      html.push('Total results: ', response.summary.numResults, '<br/><br/>');
     
      //Create a table of the results.
      html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');
      
      for(var i=0;i<response.results.length;i++){
        html.push('<tr><td>', (i+1), '.</td><td>', 
                  response.results[i].address.freeformAddress, 
                  '</td><td>', 
                  response.results[i].position.lat,
                  '</td><td>', 
                  response.results[i].position.lon,
                  '</td></tr>');
      }
      
      html.push('</table>');
      
      //Add the result HTML to the body of the page.
      document.body.innerHTML = html.join('');
    });
    ```

    Aqui está o exemplo de código totalmente em execução:

<iframe height="500" style="width: 100%;" scrolling="no" title="Usando o módulo de serviços" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>usando o módulo de serviços</a> por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [MapsURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter mais exemplos de código que usam o módulo de serviços:

> [!div class="nextstepaction"]
> [Mostrar resultados da pesquisa no mapa](./map-search-location.md)

> [!div class="nextstepaction"]
> [Obter informações de uma coordenada](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Mostrar trajetos de A para B](./map-route.md)