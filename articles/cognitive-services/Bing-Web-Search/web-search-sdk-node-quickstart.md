---
title: 'Início Rápido: usar o SDK de Pesquisa na Web do Bing para Node.js'
titleSuffix: Azure Cognitive Services
description: O SDK de Pesquisa na Web do Bing torna fácil integrar a Pesquisa na Web do Bing ao seu aplicativo Node.js. Neste início rápido, você aprenderá a criar uma instância de um cliente, enviar uma solicitação e imprimir a resposta.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 08/16/2018
ms.author: erhopf
ms.openlocfilehash: b27c11a69c7d16f38c2448d380b611940078c501
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121885"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-nodejs"></a>Início Rápido: usar o SDK de Pesquisa na Web do Bing para Node.js

O SDK de Pesquisa na Web do Bing torna fácil integrar a Pesquisa na Web do Bing ao seu aplicativo Node.js. Neste início rápido, você aprenderá a criar uma instância de um cliente, enviar uma solicitação e imprimir a resposta.

Deseja ver o código agora mesmo? Os [exemplos de SDK de Pesquisa na Web do Bing para Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) estão disponíveis no GitHub.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>Pré-requisitos

Aqui estão alguns itens de que você poderá precisar antes de executar este início rápido:

* [Node.js 6](https://nodejs.org/en/download/) ou posterior
* Uma chave de assinatura  

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Vamos começar configurando o ambiente de desenvolvimento para nosso projeto em Node.js.

1. Crie um novo diretório para seu projeto:

    ```console
    mkdir YOUR_PROJECT
    ```

2. Criar um novo arquivo de pacote:

    ```console
    cd YOUR_PROJECT
    npm init
    ```

3. Agora, vamos instalar alguns módulos do Azure e adicioná-los ao `package.json`:

    ```console
    npm install --save azure-cognitiveservices-websearch
    npm install --save ms-rest-azure
    ```

## <a name="create-a-project-and-declare-required-modules"></a>Criar um projeto e declarar os módulos necessários

No mesmo diretório que seu `package.json`, crie um novo projeto do Node.js usando seu IDE ou editor favorito. Por exemplo: `sample.js`.

Em seguida, copie esse código para seu projeto. Ele carrega os módulos instalados na seção anterior.

```javascript
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
```

## <a name="instantiate-the-client"></a>Instancie o cliente

Esse código cria uma instância de um cliente e usando o módulo `azure-cognitiveservices-websearch`. Insira uma chave de assinatura válida para sua conta do Azure antes de continuar.

```javascript
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```

## <a name="make-a-request-and-print-the-results"></a>Fazer uma solicitação e imprimir os resultados

Use o cliente para enviar uma consulta de pesquisa para Pesquisa na Web do Bing. Se a resposta incluir os resultados para qualquer um dos itens na matriz `properties`, o `result.value` será impresso no console.

```javascript
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})
```

## <a name="run-the-program"></a>Execute o programa

A etapa final é executar o programa!

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar com este projeto, remova sua chave de assinatura do código do programa.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exemplos de SDK do Node.js de Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

## <a name="see-also"></a>Consulte também

* [Referência do SDK do Node do Azure](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-websearch/)
