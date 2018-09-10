---
title: Tutorial para aprender a adicionar enunciados a um aplicativo LUIS usando Python | Microsoft Docs
description: Neste tutorial, você aprenderá a chamar um aplicativo LUIS usando Python.
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 3e1ca2ea874b6b39ac8a1b1eaa94fe9ff1894ea3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264294"
---
# <a name="tutorial-add-utterances-to-app-using-python"></a>Tutorial: Adicionar enunciados ao aplicativo usando Python
Neste tutorial, escreva um programa para adicionar um enunciado para uma intenção usando as APIs de criação em Python.

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um projeto de console do Visual Studio 
> * Adicionar o método para chamar a API LUIS para adicionar o enunciado e treinar o aplicativo
> * Adicionar arquivo JSON com enunciados de exemplo com a intenção de reservar um voo
> * Executar o console e ver o status de treinamento para enunciados

Para obter mais informações, consulte a documentação técnica para o [adicionar o enunciado de exemplo a intenção](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45), [treinar](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45), e [status treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) APIs.

Para este artigo, você precisa de uma conta gratuita de [LUIS][LUIS] para criar o seu aplicativo LUIS.

## <a name="prerequisites"></a>pré-requisitos

* [Python 3.6](https://www.python.org/downloads/) ou posterior.
* **[Recomendado]** [Visual Studio Code](https://code.visualstudio.com/) para IntelliSense e depuração.
* Sua **[chave de criação](luis-concept-keys.md#authoring-key)** LUIS. Você pode encontrar essa chave em Configurações de conta no site do [LUIS](luis-reference-regions.md).
* A [**ID do seu aplicativo**](./luis-get-started-create-app.md) LUIS existente. A ID do aplicativo é mostrada no painel do aplicativo. O aplicativo LUIS com as entidades e intenções usadas no arquivo `utterances.json` devem existir antes da execução do código em `add-utterances.js`. O código neste artigo não cria as entidades e intenções. Ele apenas adiciona as enunciados para entidades e intenções existentes. 
* O **ID da versão** no aplicativo que recebe os enunciados. A ID padrão é “0.1”
* Criar um novo arquivo chamado projeto `add-utterances-3-6.py` no VSCode.

> [!NOTE] 
> O arquivo `add-utterances-3-6.py` completo está disponível nos [**exemplos de LUIS** no repositório Github](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/authoring-api-samples/python).


## <a name="write-the-python-code"></a>Escrever o código Python

1. Copie os trechos de código a seguir:

   [!code-python[Console app code that adds an utterance Python 3.6](~/samples-luis/documentation-samples/authoring-api-samples/python/add-utterances-3-6.py)]

## <a name="specify-utterances-to-add"></a>Especifique os enunciados a serem adicionados
Crie e edite o arquivo `utterances.json` para especificar a **matriz de enunciados** que você deseja adicionar ao aplicativo LUIS. A intenção e as entidades já **devem** estar no aplicativo LUIS.

> [!NOTE]
> O aplicativo LUIS com as entidades e intenções usadas no arquivo `utterances.json` devem existir antes da execução do código em `add-utterances.js`. O código neste artigo não cria as entidades e intenções. Ele apenas adiciona as enunciados para entidades e intenções existentes.

O campo `text` contém o texto do enunciado. O campo `intentName` deve corresponder ao nome de uma intenção no aplicativo LUIS. O campo `entityLabels` é obrigatório. Se você não quiser rotular nenhuma entidade, forneça uma lista vazia, conforme mostrado no exemplo a seguir:

Se a lista de entityLabels não estiver vazia, o `startCharIndex` e `endCharIndex` precisarão marcar a entidade referida no campo `entityName`. Ambos os índices são contagens de zeros, o que significa que 6 no exemplo superior refere-se ao "S" de Seattle e não ao espaço antes do S maiúsculo.

```json
[
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
    },
    {
        "text": "book a flight",
        "intentName": "BookFlight",
        "entityLabels": []
    }
]
```

## <a name="add-an-utterance-from-the-command-line"></a>Adicionar um enunciado a partir da linha de comando

Execute o aplicativo a partir de uma linha de comando com o Python 3.6.

Chamar a API para adicionar enunciados sem argumentos adiciona um enunciado ao aplicativo sem treiná-lo.

````
> python add-utterances-3-6.py
````

Este exemplo cria um arquivo com o `results.json` que contém os resultados da chamada à API para adicionar enunciados. O campo `response` é neste formato para enunciados que foram adicionados. O `hasError` é false, que indica que o enunciado foi adicionado.  

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

## <a name="add-an-utterance-and-train-from-the-command-line"></a>Adicionar um enunciado e treinar a partir da linha de comando
Chame a API para adicionar enunciados com o argumento `-train` para enviar uma solicitação para treinar e, posteriormente, solicitar o status do treinamento. O status será enfileirado imediatamente após o início do treinamento. OS detalhes do status são gravados em um arquivo.

````
> python add-utterances-3-6.py -train
````

> [!NOTE]
> Enunciados duplicados não serão adicionados novamente, mas não causam um erro. O `response` contém a ID do enunciado original.

Quando você chama o exemplo com o argumento `-train`, ele cria um arquivo `training-results.json` indicando que a solicitação para treinar o aplicativo LUIS foi enfileirada com êxito. 

O exemplo a seguir mostra o resultado de uma solicitação bem-sucedida para treinar:
```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```

Depois que a solicitação para treinar é enfileirada, pode demorar algum tempo para o treinamento ser concluído.

## <a name="get-training-status-from-the-command-line"></a>Obter status de treinamento da linha de comando
Chame o exemplo com o argumento `-status` para verificar o status de treinamento e gravar os detalhes do status em um arquivo.

````
> python add-utterances-3-6.py -status
````
## <a name="clean-up-resources"></a>Limpar recursos
Depois de concluir o tutorial, remova o Visual Studio e o aplicativo de console se não precisar mais deles. 

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Criar um aplicativo LUIS programaticamente](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website

