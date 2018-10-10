---
title: 'Início Rápido: Reconhecer fala em JavaScript em um navegador usando o SDK de Fala dos Serviços Cognitivos'
titleSuffix: Microsoft Cognitive Services
description: Saiba como reconhecer fala em JavaScript em um navegador usando o SDK de Fala dos Serviços Cognitivos
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 09/24/2018
ms.author: fmegen
ms.openlocfilehash: b925a382a32f320c5d6906bc3fa23f046d260cb0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997430"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-cognitive-services-speech-sdk"></a>Início Rápido: Reconhecer fala em JavaScript em um navegador usando o SDK de Fala dos Serviços Cognitivos

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprenderá como criar um site usando a associação JavaScript do SDK de Fala de Serviços Cognitivos para transcrever conversão de fala em texto.
O aplicativo é baseado no SDK de fala de Serviços Cognitivos da Microsoft ([Baixar versão 1.0.0](https://aka.ms/csspeech/jsbrowserpackage)).

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de assinatura para o serviço de fala. Veja [Experimente o serviço de fala gratuitamente](get-started.md).
* Um PC ou Mac, com um microfone funcionando.
* Um editor de texto.
* Uma versão atual do Chrome ou Microsoft Edge.
* Opcionalmente, um servidor Web que dá suporte à hospedagem de scripts PHP.

## <a name="create-a-new-website-folder"></a>Criar uma nova pasta Site

Crie uma nova pasta vazia. Caso deseje hospedar o exemplo em um servidor Web, certifique-se de que ele possa acessar a pasta.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Descompactar o SDK de Fala para JavaScript nessa pasta

Baixe o SDK de Fala como um [pacote .zip](https://aka.ms/csspeech/jsbrowserpackage) e descompacte-o na pasta recém-criada. Isso deve resultar na descompactação de dois arquivos, por exemplo, `microsoft.cognitiveservices.speech.sdk.bundle.js` e `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
O último arquivo é opcional e usado para ajudar na depuração no código do SDK se necessário.

## <a name="create-an-indexhtml-page"></a>Criar uma página index.html

Crie um novo arquivo na pasta, chamada `index.html`, e abra este arquivo com um editor de texto.

1. Crie o esqueleto do HTML a seguir:

  ```html
  <html>
  <head>
      <title>Microsoft Cognitive Service Speech SDK JavaScript Quickstart</title>
  </head>
  <body>
    <!-- UI code goes here -->

    <!-- SDK reference goes here -->

    <!-- Optional authorization token request goes here -->

    <!-- Sample code goes here -->
  </body>
  </html>
  ```

1. Adicione o seguinte código da interface do usuário ao seu arquivo, embaixo do primeiro comentário:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. Adicionar uma referência ao SDK de Fala

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. Conecte manipuladores para o botão de reconhecimento, resultado de reconhecimento e campos relacionados à assinatura definidos pelo código da interface do usuário:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>Criar a origem do token (opcional)

Caso deseje hospedar a página da Web em um servidor Web, é possível fornecer opcionalmente uma origem do token para seu aplicativo de demonstração.
Dessa forma, sua chave de assinatura nunca sairá do seu servidor, permitindo que seus usuários usem funcionalidades de fala sem inserir nenhum código de autorização por si próprios.

1. Crie um arquivo chamado `token.php`. Neste exemplo, vamos supor seu servidor Web seja compatível com a linguagem de script PHP. Insira o seguinte código:

  [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. Edite o arquivo `index.html` e adicione o seguinte código a ele:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> Os tokens de autorização só têm um tempo de vida limitado.
> Este exemplo simplificado não mostra como atualizar os tokens de autorização automaticamente. Como um usuário, é possível recarregar manualmente a página ou pressionar F5 para atualizar.

## <a name="build-and-run-the-sample-locally"></a>Criar e executar o exemplo localmente

Para iniciar o aplicativo, clique duas vezes no arquivo index.html ou abra index.html com seu navegador da Web favorito. Ele apresentará uma GUI simples que permite a você inserir sua chave de assinatura e [região](regions.md) e disparar um reconhecimento usando o microfone.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Criar e executar o exemplo por meio de um servidor Web

Para iniciar seu aplicativo, abra seu navegador da Web favorito e aponte-o para a URL pública na qual a pasta está hospedada, insira sua [região](regions.md) e dispare um reconhecimento usando o microfone. Se configurado, ele adquirirá um token da origem do token.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure esse exemplo na pasta `quickstart/js-browser`.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Obtenha nossas amostras](speech-sdk.md#get-the-samples)
