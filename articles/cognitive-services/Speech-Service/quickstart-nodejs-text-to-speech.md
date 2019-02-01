---
title: 'Início Rápido: Converter texto em fala, Node.js - Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você aprenderá como converter texto em fala usando Node.js e API REST de Conversão de Texto em Fala. O texto de exemplo incluído neste guia é estruturado como Linguagem de marcação de síntese de Fala (SSML). Isso permite que você escolha a voz e o idioma da resposta de Fala.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 808466e9d2546472a4f86dd0d7eacd48e75cb94a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224472"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Início Rápido: Converter texto em fala usando Node.js

Neste início rápido, você aprenderá como converter texto em fala usando Node.js e API REST de conversão de texto em Fala. O corpo da solicitação neste guia é estruturado como [Linguagem de marcação de síntese de Fala (SSML)](speech-synthesis-markup.md), que permite escolher a voz e o idioma da resposta.

Este início rápido requer uma [Conta de Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso do Serviço de Fala. Se não tiver uma conta, você poderá usar a [avaliação gratuita](get-started.md) para obter uma chave de assinatura.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Node 8.12.x ou posterior](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou seu editor de texto favorito
* Uma chave de assinatura do Azure para o Serviço de Fala. [Obtenha uma gratuitamente!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Criar um projeto e exigir dependências

Crie um novo projeto Node.js usando seu editor ou IDE favorito. Em seguida, copie esse trecho de código para seu projeto em um arquivo denominado `tts.js`.

```javascript
// Requires request for HTTP requests
const request = require('request');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> Se você nunca usou esses módulos, você precisará instalá-los antes de executar o programa. Para instalar esses pacotes, execute: `npm install request readline-sync`.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Defina a chave de assinatura e crie um prompt para o TTS

Nas próximas seções, você criará funções para manipular a autorização, chamar a API de conversão de texto em fala e validar a resposta. Vamos começar, adicionando uma chave de assinatura e criando um prompt para entrada de texto.

```javascript
/*
 * These lines will attempt to read your subscription key from an environment
 * variable. If you prefer to hardcode the subscription key for ease of use,
 * replace process.env.SUBSCRIPTION_KEY with your subscription key as a string.  
 */
const subscriptionKey = process.env.SUBSCRIPTION_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};

// Prompts the user to input text.
let text = readline.question('What would you like to convert to speech? ');
```

## <a name="get-an-access-token"></a>Obter um token de acesso

A API REST de conversão de texto em fala requer um token de acesso para autenticação. Para obter um token de acesso, é necessária uma troca. Essa amostra troca sua chave de assinatura do Serviço de Fala por um token de acesso usando o endpoint `issueToken`.

Essa função usa dois argumentos, a chave de assinatura de Serviços de Fala e uma função de retorno de chamada. Depois que a função obter um token de acesso, ela passará o valor para a função de retorno de chamada. Na próxima seção, criaremos a função para chamar a API de conversão de texto em fala e salvar a resposta de fala sintetizada.

Este exemplo pressupõe que a sua assinatura do serviço de Fala esteja na região Oeste dos EUA. Se você estiver usando uma região diferente, atualize o valor para `uri`. Para uma lista completa, consulte [Regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copie este código em seu projeto:

```javascript
function textToSpeech(subscriptionKey, saveAudio) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    };
    // This function retrieve the access token and is passed as callback
    // to request below.
    function getToken(error, response, body) {
        console.log("Getting your token...\n")
        if (!error && response.statusCode == 200) {
            //This is the callback to our saveAudio function.
            // It takes a single argument, which is the returned accessToken.
            saveAudio(body)
        }
        else {
          throw new Error(error);
        }
    }
    request(options, getToken)
}
```

> [!NOTE]
> Para obter mais informações sobre autenticação, consulte [Autenticar com um token de acesso](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Faça uma solicitação e salve a resposta

Aqui, você criará a solicitação à API de conversão de texto em fala e salvará a resposta de fala. Este exemplo supõe que você esteja usando o endpoint do West US. Se o seu recurso estiver registrado em uma região diferente, atualize o `uri`. Para obter mais informações, consulte [regiões do Serviço de Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Em seguida, você precisa adicionar cabeçalhos obrigatórios para a solicitação. Certifique-se de atualizar `User-Agent` com o nome do seu recurso (localizado no portal do Azure) e defina `X-Microsoft-OutputFormat` como sua saída de áudio preferida. Para obter uma lista completa dos formatos de saída, consulte [Saídas de áudio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-outputs).

Em seguida, construa o corpo da solicitação usando a Linguagem de marcação de síntese de Fala (SSML). Esta amostra define a estrutura e usa a entrada `text` criada anteriormente.

>[!NOTE]
> Este exemplo usa a fonte de voz `JessaRUS`. Para obter uma lista completa das vozes / idiomas fornecidos pela Microsoft, consulte [Suporte ao idioma](language-support.md).
> Se você estiver interessado em criar uma voz exclusiva e reconhecível para sua marca, consulte [Criando fontes de voz personalizadas](how-to-customize-voice-font.md).

Finalmente, você fará uma solicitação ao serviço. Se a solicitação for obtida com êxito e um código de status 200 for retornado, a resposta de fala será gravada como `sample.wav`.

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function saveAudio(accessToken) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
      .att('version', '1.0')
      .att('xml:lang', 'en-us')
      .ele('voice')
      .att('xml:lang', 'en-us')
      .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
      .txt(text)
      .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: body
    };
    // This function makes the request to convert speech to text.
    // The speech is returned as the response.
    function convertText(error, response, body){
      if (!error && response.statusCode == 200) {
        console.log("Converting text-to-speech. Please hold...\n")
      }
      else {
        throw new Error(error);
      }
      console.log("Your file is ready.\n")
    }
    // Pipe the response to file.
    request(options, convertText).pipe(fs.createWriteStream('sample.wav'));
}
```

## <a name="put-it-all-together"></a>Colocar tudo isso junto

Você está quase lá. A última etapa é chamar a função `textToSpeech`.

```javascript
// Start the sample app.
textToSpeech(subscriptionKey, saveAudio);
```

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

É isso aí, você está pronto para executar seu aplicativo de amostra de texto para fala. Na linha de comando (ou sessão de terminal), navegue até o diretório do seu projeto e execute:

```console
node tts.js
```

Quando solicitado, digite o que você deseja converter de conversão de texto em fala. Se bem sucedido, o arquivo de fala está localizado na pasta do seu projeto. Reproduzi-lo usando o player de mídia favorito.

## <a name="clean-up-resources"></a>Limpar recursos

Remova todas as informações confidenciais do código-fonte do seu aplicativo de exemplo, como as chaves de assinatura.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar exemplos do Node.js no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Consulte também

* [Referência de API de texto em fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)
* [Criar fontes de voz personalizada](how-to-customize-voice-font.md)
* [Grave amostras de voz para criar uma voz personalizada](record-custom-voice-samples.md)
