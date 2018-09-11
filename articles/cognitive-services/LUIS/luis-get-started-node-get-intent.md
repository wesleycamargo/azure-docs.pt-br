---
title: Analisar o texto de linguagem natural em Reconhecimento Vocal (LUIS) usando Node.js – Serviços Cognitivos – Serviços Cognitivos do Azure | Microsoft Docs
description: Neste início rápido, use um aplicativo LUIS público disponível para determinar a intenção do usuário do texto de conversa. Usando Node.js, envie a intenção do usuário como texto para o ponto de extremidade de previsão de HTTP do aplicativo público. No ponto de extremidade, LUIS aplica o modelo do aplicativo público para analisar o texto de idioma natural quanto ao significado, determinando a intenção geral e extraindo dados relevantes para o domínio do assunto do aplicativo.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: d7067041ae8e413675de3c95ca4ba0c1b987f47a
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "43768535"
---
# <a name="quickstart-analyze-text-using-nodejs"></a>Início Rápido: analisar texto usando Node.js

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Linguagem de programação [Node.js](https://nodejs.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)
* ID do aplicativo público: df67dcdb-c37d-46af-88e1-8b97951ca1c2


> [!NOTE] 
> A solução Node.js completa está disponível nos [**exemplos de LUIS** no repositório Github](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/analyze-text/node).

## <a name="get-luis-key"></a>Obter chave LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analisar o texto com navegador

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-nodejs"></a>Analisar texto com Node.js

Você pode usar o Node.js para acessar os mesmos resultados que você viu na janela do navegador na etapa anterior.

1. Copie o trecho de código a seguir:

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/quickstarts/analyze-text/node/call-endpoint.js)]

2. Crie o arquivo `.env` com o seguinte texto ou defina essas variáveis no ambiente do sistema:

    ```CMD
    LUIS_APP_ID=df67dcdb-c37d-46af-88e1-8b97951ca1c2
    LUIS_ENDPOINT_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ```

3. Defina a variável de ambiente `LUIS_ENDPOINT_KEY` para sua chave.

4. Instale as dependências executando o seguinte comando na linha de comando: `npm install`.

5. Execute o código com `npm start`. Ele exibe os mesmos valores que você viu anteriormente na janela do navegador.

## <a name="luis-keys"></a>Chaves de LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Exclua o arquivo Node.js.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Adicionar declarações](luis-get-started-node-add-utterance.md)