---
title: Tutorial para aprender a adicionar enunciados a um aplicativo LUIS usando Java | Microsoft Docs
description: Neste tutorial, você aprenderá a chamar um aplicativo LUIS usando Java.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 5b9c7b90ca96b23fbabfeb1e1d06e4124e65a0dc
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266032"
---
# <a name="tutorial-add-utterances-to-app-using-java"></a>Tutorial: Adicionar enunciados ao aplicativo usando Java 
Neste tutorial, escreva um programa para adicionar o enunciado para uma intenção usando as APIs de criação em Java.

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um projeto de console do Visual Studio 
> * Adicionar o método para chamar a API LUIS para adicionar o enunciado e treinar o aplicativo
> * Adicionar arquivo JSON com enunciados de exemplo com a intenção de reservar um voo
> * Executar o console e ver o status de treinamento para enunciados

Para obter mais informações, consulte a documentação técnica para [adicionar o enunciado de exemplo para APIs de intenção](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) e [status de treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Para este artigo, você precisa de uma conta gratuita de [LUIS][LUIS] para criar o seu aplicativo LUIS.

## <a name="prerequisites"></a>pré-requisitos

* Oracle [**Java/JDK**](http://www.oracle.com/technetwork/java/javase/downloads/index.html) mais recente.
* [Biblioteca de JSON de GSON do Google](https://github.com/google/gson).
* Sua **[chave de criação](luis-concept-keys.md#authoring-key)** LUIS. Você pode encontrar essa chave em Configurações de conta no site do [LUIS](luis-reference-regions.md).
* A [**ID do seu aplicativo**](./luis-get-started-create-app.md) LUIS existente. A ID do aplicativo é mostrada no painel do aplicativo. O aplicativo LUIS com as entidades e intenções usadas no arquivo `utterances.json` devem existir antes da execução do código em `AddUtterances.java`. O código neste artigo não criará as entidades e intenções. Ele apenas adicionará os enunciados para entidades e intenções existentes. 
* O **ID da versão** no aplicativo que recebe os enunciados. A ID padrão é “0.1”
* Crie um novo arquivo de texto chamado `AddUtterances.java`.

> [!NOTE] 
> O arquivo `add-utterances.cs` completo incluindo um arquivo `utterances.json` de exemplo está disponível nos [**exemplos de LUIS** do repositório Github](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/authoring-api-samples/java).


## <a name="write-the-java-code"></a>Escrever o código de Java

Adicione as dependências Java ao arquivo.

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=31-34 "Java Dependencies")]

Crie a classe `AddUtterances`

```Java
public class AddUtterances {
    // Insert code here
}
```

Essa classe contém todos os trechos de código seguintes.

Adicione as constantes de LUIS à classe. Copie o código a seguir e insira a sua chave de criação, a ID do aplicativo e a ID da versão.

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=41-53 "LUIS-based IDs")]

Adicione o método para chamar a API de LUIS. 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=55-178 "HTTP request to LUIS")]

Adicione o método para a resposta HTTP da API de LUIS.

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=180-226 "HTTP response from LUIS")]


Adicione a manipulação de exceção. 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=228-256 "Exception Handling")]

Adicione a manipulação de saída/impressão.

   [!code-java[Add output handling](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=258-267 "Add configuration information for adding utterance")]

Adicione a função principal.

   [!code-java[Add main function](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=269-345 "Add main function")]


## <a name="specify-utterances-to-add"></a>Especifique os enunciados a serem adicionados
Crie e edite o arquivo `utterances.json` para especificar a **matriz de enunciados** que você deseja adicionar ao aplicativo LUIS. A intenção e as entidades já **devem** estar no aplicativo LUIS.

> [!NOTE]
> O aplicativo LUIS com as entidades e intenções usadas no arquivo `utterances.json` devem existir antes da execução do código em `AddUtterances.java`. O código neste artigo não criará as entidades e intenções. Ele apenas adicionará os enunciados para entidades e intenções existentes.

O campo `text` contém o texto do enunciado. O campo `intentName` deve corresponder ao nome de uma intenção no aplicativo LUIS. O campo `entityLabels` é obrigatório. Se você não quiser rotular nenhuma entidade, forneça uma lista vazia, conforme mostrado no exemplo a seguir.

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

Compile AddUtterance com as dependências

```
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

Chamar `AddUtterance` sem argumentos adiciona os enunciados de LUIS ao aplicativo sem treiná-lo.
````
> java -classpath .;gson-2.8.2.jar AddUtterances
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
Chame `add-utterance` com o argumento `-train` para enviar uma solicitação para treinar. 

````
> java -classpath .;gson-2.8.2.jar AddUtterances -train
````

> [!NOTE]
> Enunciados duplicados não serão adicionados novamente, mas não causam um erro. O `response` contém a ID do enunciado original.

Quando você chama o aplicativo com o argumento `-train`, ele cria um arquivo `training-results.json` indicando que a solicitação para treinar o aplicativo LUIS foi enfileirada com êxito. 

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
Chame o aplicativo com o argumento `-status` para verificar o status de treinamento e gravar os detalhes do status em um arquivo.

````
> java -classpath .;gson-2.8.2.jar AddUtterances -status
````

## <a name="clean-up-resources"></a>Limpar recursos
Depois de concluir o tutorial, remova o Visual Studio e o aplicativo de console se não precisar mais deles. 

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Criar um aplicativo LUIS programaticamente](luis-tutorial-node-import-utterances-csv.md) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website