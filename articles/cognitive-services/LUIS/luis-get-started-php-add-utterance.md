---
title: Tutorial para aprender a adicionar enunciados a um aplicativo LUIS usando PHP | Microsoft Docs
description: Neste tutorial, você aprenderá a chamar um aplicativo LUIS usando PHP.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 59150b7ed6782c28f243041be2ed6aa17e69cc01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263773"
---
# <a name="tutorial-add-utterances-to-app-using-php"></a>Tutorial: Adicionar enunciados ao aplicativo usando PHP 
Neste tutorial, escreva um programa para adicionar um enunciado para uma intenção usando as APIs de criação em PHP.

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um projeto de console do Visual Studio 
> * Adicionar o método para chamar a API LUIS para adicionar o enunciado e treinar o aplicativo
> * Adicionar arquivo JSON com enunciados de exemplo com a intenção de reservar um voo
> * Executar o console e ver o status de treinamento para enunciados

Para obter mais informações, consulte a documentação técnica para [adicionar o enunciado de exemplo para APIs de intenção](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) e [status de treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Para este artigo, você precisa de uma conta gratuita de [LUIS][LUIS] para criar o seu aplicativo LUIS.

## <a name="prerequisites"></a>pré-requisitos

* [**PHP**](http://php.net/) mais recente.
* Certifique-se de que o openssl está disponível como uma dependência para PHP.  
* Sua **[chave de criação](luis-concept-keys.md#authoring-key)** LUIS. Você pode encontrar essa chave em Configurações de conta no site do [LUIS](luis-reference-regions.md).
* A [**ID do seu aplicativo**](./luis-get-started-create-app.md) LUIS existente. A ID do aplicativo é mostrada no painel do aplicativo. O aplicativo LUIS com as entidades e intenções usadas no arquivo `utterances.json` devem existir antes da execução do código em `add-utterances.php`. O código neste artigo não cria as entidades e intenções. Ele apenas adiciona as enunciados para entidades e intenções existentes. 
* O **ID da versão** no aplicativo que recebe os enunciados. A ID padrão é “0.1”
* Criar um novo arquivo chamado projeto `add-utterances.php` no VSCode.

> [!NOTE] 
> O arquivo `add-utterances.cs` completo incluindo um arquivo `utterances.json` de exemplo está disponível nos [**exemplos de LUIS** do repositório Github](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/php/).


## <a name="write-the-php-code"></a>Escreva o código PHP

Adicione as dependências ao arquivo.

   [!code-php[PHP and LUIS Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=10-29 "PHP and LUIS Dependencies")]

Adicione a solicitação GET usada para o status de treinamento.

   [!code-php[SendGet](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=31-50 "SendGet")]

Adicione a solicitação POST usada para criar enunciados ou iniciar o treinamento. 

   [!code-php[SendPost](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=52-72 "SendPost")]

Adicione a função `AddUtterances`.

   [!code-php[AddUtterances method](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=74-79 "AddUtterances method")]


Adicione a função `Train`. 

   [!code-php[Train](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=81-88 "Train")]

Adicione a função `Status`.

   [!code-php[Status](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=90-94 "Status")]

Para gerenciar os argumentos de linha de comando, adicione o bloco de código principal.

   [!code-php[Main code](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=96-120 "Main code")]

## <a name="specify-utterances-to-add"></a>Especifique os enunciados a serem adicionados
Crie e edite o arquivo `utterances.json` para especificar a **matriz de enunciados** que você deseja adicionar ao aplicativo LUIS. A intenção e as entidades já **devem** estar no aplicativo LUIS.

> [!NOTE]
> O aplicativo LUIS com as entidades e intenções usadas no arquivo `utterances.json` devem existir antes da execução do código em `add-utterances.php`. O código neste artigo não cria as entidades e intenções. Ele apenas adiciona as enunciados para entidades e intenções existentes.

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

Execute o aplicativo a partir de uma linha de comando com PHP.

Chamar `add-utterances.php` apenas com utterance.json como um argumento adiciona, mas não treina LUIS nos novos enunciados.
````
> php add-utterances.php ./utterances.json
````

O JSON a seguir é retornado da chamada à API para adicionar enunciados. O campo `response` é neste formato para enunciados que foram adicionados. O `hasError` é false, que indica que o enunciado foi adicionado.  

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
Chame `add-utterance.php` com o argumento `-train` para enviar uma solicitação para treinar. 

````
> php add-utterances.php ./utterances.json -train
````

> [!NOTE]
> Enunciados duplicados não serão adicionados novamente, mas não causam um erro. O `response` contém a ID do enunciado original.

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
Chame o aplicativo com o argumento `-status` para verificar o status de treinamento e exibir detalhes do status.

````
> php add-utterances.php -status
````

```
Requested training status.
[
   {
      "modelId": "eb2f117c-e10a-463e-90ea-1a0176660acc",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c1bdfbfc-e110-402e-b0cc-2af4112289fb",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "863023ec-2c96-4d68-9c44-34c1cbde8bc9",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "82702162-73ba-4ae9-a6f6-517b5244c555",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "37121f4c-4853-467f-a9f3-6dfc8cad2763",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "de421482-753e-42f5-a765-ad0a60f50d69",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "80f58a45-86f2-4e18-be3d-b60a2c88312e",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c9eb9772-3b18-4d5f-a1e6-e0c31f91b390",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "2afec2ff-7c01-4423-bb0e-e5f6935afae8",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "95a81c87-0d7b-4251-8e07-f28d180886a1",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   }
]
```

## <a name="clean-up-resources"></a>Limpar recursos
Depois de concluir o tutorial, remova o Visual Studio e o aplicativo de console se não precisar mais deles. 

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Criar um aplicativo LUIS programaticamente](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website