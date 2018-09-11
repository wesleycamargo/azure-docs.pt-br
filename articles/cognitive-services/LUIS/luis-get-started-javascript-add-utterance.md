---
title: 'Início Rápido: aprendendo a adicionar enunciados a um aplicativo LUIS usando JavaScript – Serviços Cognitivos do Azure | Microsoft Docs'
description: Neste início rápido, você aprenderá a chamar um aplicativo LUIS usando JavaScript.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: ffc19d12c1d3fbb24c514ac87f298d1a52d23eb8
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43768562"
---
# <a name="quickstart-change-model-using-javascript"></a>Início Rápido: Alterar o modelo usando JavaScript

[!include[Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!include[Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [Visual Studio Code](https://code.visualstudio.com/).

[!include[Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Exemplo de arquivo JSON de enunciados

[!include[Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]


## <a name="create-quickstart-code"></a>Criar código de início rápido

Crie `add-utterances.html` e adicione o código a seguir:

   [!code-html[Html code](~/samples-luis/documentation-samples/quickstarts/change-model/javascript/add-utterance.html "Javascript code")]

## <a name="run-code"></a>Executar código

1. Abra o arquivo em um navegador.

2. Adicione sua ID de criação do LUIS, sua ID de aplicativo do LUIS.

3. Modificar a **matriz de enunciados** para adicionar ao seu aplicativo. Eles são armazenados na variável utteranceJSON. Altere esses valores para suas próprias necessidades de domínio e enunciado. 

    ```json
    // example batch utterances
    var utteranceJSON = [
        {
            "text": "go to Seattle",
            "intentName": "BookFlight",
            "entityLabels": [
                {
                    "entityName": "Location::LocationTo",
                    "startCharIndex": 6,
                    "endCharIndex": 12
                }
            ]
        }
    ,
        {
            "text": "book a flight",
            "intentName": "BookFlight",
            "entityLabels": []
        }
    ];
    ```

4. Selecione o botão `Upload utterance`. Os resultados de LUIS são exibidos abaixo dos botões.

5. Selecione o botão `Train model` para treinar seu aplicativo com esses novos enunciados.

6. Selecione o botão `Train Status` para ver o status de treinamento. 

    ![Add-utterances.html](./media/luis-quickstart-javascript-add-utterance/add-utterance.png)

## <a name="clean-up-resources"></a>Limpar recursos
Quando você terminar com o início rápido, remova todos os arquivos criados nesse início rápido. 

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Integrar LUIS com um bot](luis-csharp-tutorial-build-bot-framework-sample.md)
