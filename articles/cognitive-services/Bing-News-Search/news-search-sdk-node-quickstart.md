---
title: 'Início Rápido: Executar uma pesquisa de notícias – SDK de Pesquisa de Notícias do Bing para Node.js'
titleSuffix: Azure Cognitive Services
description: Use este início rápido para pesquisar notícias usando o SDK de Pesquisa de Notícias do Bing para Node.js e processar a resposta.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: adfdfd56c4ff678f3a0b87cd75dad50b5c5f0c01
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55204024"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>Início Rápido: Executar uma pesquisa de notícias com o SDK de Pesquisa de Notícias do Bing para Node.js

Use este início rápido para começar a pesquisar notícias com o SDK de Pesquisa de Notícias do Bing para Node.js. Embora a Pesquisa de Notícias do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço aos seus aplicativos. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/)

Para configurar um aplicativo de console usando o SDK de Pesquisa de Notícias do Bing:
1. Execute `npm install ms-rest-azure` no seu ambiente de desenvolvimento.
2. Execute `npm install azure-cognitiveservices-newssearch` no seu ambiente de desenvolvimento.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Crie uma instância de `CognitiveServicesCredentials`. Crie variáveis para a chave de assinatura e um termo de pesquisa.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. crie uma instância do cliente:
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Enviar uma consulta de pesquisa

3. Use o cliente para pesquisar com um texto de consulta, nesse caso, "Olimpíadas de Inverno":
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

O código imprime `result.value` itens para o console sem analisar qualquer texto. Os resultados por categoria, se houver, incluirão:

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
[Criar um aplicativo Web de página única](tutorial-bing-news-search-single-page-app.md)
