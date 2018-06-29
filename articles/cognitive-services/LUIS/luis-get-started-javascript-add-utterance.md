---
title: Tutorial para aprender a adicionar enunciados a um aplicativo LUIS usando JavaScript | Microsoft Docs
description: Neste tutorial, você aprenderá a chamar um aplicativo LUIS usando JavaScript.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: v-geberr
ms.openlocfilehash: b6d021dcfdddb5449aa989c6aa06d7faf326befb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265452"
---
# <a name="tutorial-add-utterances-to-app-using-javascript"></a>Tutorial: Adicionar enunciados ao aplicativo usando JavaScript
Neste tutorial, escreva um programa para adicionar um enunciado para uma intenção usando as APIs de criação em Javascript.

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um projeto de console do Visual Studio 
> * Adicionar o método para chamar a API LUIS para adicionar o enunciado e treinar o aplicativo
> * Adicionar arquivo JSON com enunciados de exemplo com a intenção de reservar um voo
> * Executar o console e ver o status de treinamento para enunciados

Para obter mais informações, consulte a documentação técnica para [adicionar o enunciado de exemplo para APIs de intenção](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) e [status de treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Para este artigo, você precisa de uma conta gratuita de [LUIS][LUIS] para criar o seu aplicativo LUIS.

## <a name="prerequisites"></a>pré-requisitos
* Sua [**chave de criação**](luis-concept-keys.md#authoring-key) LUIS. 
* A **ID do seu aplicativo** e **ID de versão** do LUIS existente. 
* Um novo arquivo chamado projeto `add-utterances.html` no VSCode.

> [!NOTE] 
> O arquivo `add-utterances.html` completo está disponível nos [**exemplos de LUIS** no repositório Github](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/javascript/add-utterance.html).


## <a name="write-the-code"></a>Escreva o código
Crie `add-utterances.html` e adicione o código a seguir:

   [!code-javascript[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/javascript/add-utterance.html "Java Dependencies")]

## <a name="view-in-browser"></a>Exibir no navegador
1. Abra o arquivo em um navegador.

2. Adicione sua ID de criação de LUIS, a ID do aplicativo LUIS e altere a versão, se não for `0.1`

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
Depois de concluir o tutorial, remova o Visual Studio e o aplicativo de console se não precisar mais deles. 

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Integrar LUIS com um bot](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website