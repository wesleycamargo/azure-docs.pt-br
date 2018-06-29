---
title: Tutorial para aprender a adicionar enunciados a um aplicativo LUIS usando C# | Microsoft Docs
description: Neste tutorial, você aprenderá a chamar um aplicativo LUIS usando C#.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: v-geberr
ms.openlocfilehash: d9b3ca46cc635d961edadf3e3555f9656b6b5a1d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264236"
---
# <a name="tutorial-add-utterances-to-a-luis-app-using-c"></a>Tutorial: Adicionar enunciados a um aplicativo LUIS usando C# 
Neste tutorial, escreva um programa para adicionar um enunciado para uma intenção usando as APIs de criação no C#.

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um projeto de console do Visual Studio 
> * Adicionar o método para chamar a API LUIS para adicionar o enunciado e treinar o aplicativo
> * Adicionar arquivo JSON com enunciados de exemplo com a intenção de reservar um voo
> * Executar o console e ver o status de treinamento para enunciados

Para obter mais informações, consulte a documentação técnica para [adicionar o enunciado de exemplo para APIs de intenção](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) e [status de treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Para este artigo, você precisa de uma conta gratuita de [LUIS][LUIS] para criar o seu aplicativo LUIS.

## <a name="prerequisites"></a>pré-requisitos

* [**Edição mais recente do Visual Studio Community**](https://www.visualstudio.com/downloads/). 
* Sua **[chave de criação](luis-concept-keys.md#authoring-key)** LUIS. Você pode encontrar essa chave em Configurações de conta no site do [LUIS](luis-reference-regions.md).
* A [**ID do seu aplicativo**](./luis-get-started-create-app.md) LUIS existente. A ID do aplicativo é mostrada no painel do aplicativo. O aplicativo LUIS com as entidades e intenções usadas no arquivo `utterances.json` devem existir antes da execução do código. O código neste artigo não cria as entidades e intenções. Ele adiciona os enunciados para entidades e intenções existentes. 
* O **ID da versão** no aplicativo que recebe os enunciados. A ID padrão é “0.1”
* Criar um novo arquivo chamado projeto `add-utterances.cs` no VSCode.

> [!NOTE] 
> A solução C# completa incluindo um arquivo `utterances.json` de exemplo está disponível nos [**exemplos de LUIS** do repositório Github](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/csharp/).

## <a name="create-the-project-in-visual-studio"></a>Criar um projeto no Visual Studio

No Visual Studio, crie um novo aplicativo de **Console de área de trabalho clássica do Windows** usando o .Net Framework. 

![Tipo de projeto do Visual Studio](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

## <a name="add-the-systemweb-dependency"></a>Adicionar a dependência do System.Web

No projeto do Visual Studio, o projeto precisa do **System. Web**. No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** e selecione **Adicionar Referência**.

![Adicione a referência System.web](./media/luis-quickstart-cs-add-utterance/system.web.png)

## <a name="write-the-c-code"></a>Escreva o código C#
O arquivo **Program.cs** deve seguir a seguinte estrutura:

```CSharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}

```

Adicione as dependências System.IO e System.Net.Http.

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=1-6 "Add the dependencies")]


Adicione as IDs de LUIS e cadeias de caracteres para a classe do **Programa**.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=12-30&dedent=8 "Add the LUIS IDs and strings")]

Adicione o método JsonPrettyPrint.

   [!code-csharp[Add the JsonPrettyPrint method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=32-92 "Add the JsonPrettyPrint method")]

Adicione a solicitação GET usada para criar enunciados ou iniciar o treinamento. 

   [!code-csharp[SendGet](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=93-103 "SendGet")]


Adicione a solicitação POST usada para criar enunciados ou iniciar o treinamento. 

   [!code-csharp[SendPost](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=104-115 "SendPost")]

Adicione a função `AddUtterances`.

   [!code-csharp[AddUtterances method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=116-125 "AddUtterances method")]


Adicione a função `Train`. 

   [!code-csharp[Train](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=126-136 "Train")]

Adicione a função `Status`.

   [!code-csharp[Status](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=137-143 "Status")]

Para gerenciar os argumentos, adicione o código principal.

   [!code-csharp[Main code](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=144-172 "Main code")]

## <a name="specify-utterances-to-add"></a>Especifique os enunciados a serem adicionados
Crie e edite o arquivo `utterances.json` para especificar a **matriz de enunciados** que você deseja adicionar ao aplicativo LUIS. A intenção e as entidades já **devem** estar no aplicativo LUIS.

> [!NOTE]
> O aplicativo LUIS com as entidades e intenções usadas no arquivo `utterances.json` devem existir antes da execução do código em `add-utterances.cs`. O código neste artigo não cria as entidades e intenções. Ele apenas adiciona as enunciados para entidades e intenções existentes.

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

## <a name="mark-the-json-file-as-content"></a>Marcar o arquivo JSON como conteúdo
No Gerenciador de Soluções, clique com o botão direito do mouse no `utterances.json` e selecione **Propriedades**. Nas janelas de propriedades, marque a **Ação de compilação** de `Content`e **Copiar para diretório de saída** de `Copy Always`.  

![Marcar o arquivo JSON como conteúdo](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="add-an-utterance-from-the-command-line"></a>Adicionar um enunciado a partir da linha de comando

Compile e execute o aplicativo a partir de uma linha de comando com C# a partir do diretório /bin/Debug. Verifique se o arquivo utterances.json também está nesse diretório.

Chamar utterances.cs adicionar apenas com utterance.json como um argumento adiciona, mas não treina LUIS nos novos enunciados.
````
ConsoleApp\bin\Debug> ConsoleApp1.exe utterances.json
````

A linha de comando exibe os resultados da chamada da API para adicionar enunciados. O campo `response` é neste formato para enunciados que foram adicionados. O `hasError` é false, que indica que o enunciado foi adicionado.  

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
Chamar uma API para adicionar enunciado com o argumento `-train` para enviar uma solicitação para treinar. 

````
ConsoleApp\bin\Debug> ConsoleApp1.exe -train utterances.json
````

> [!NOTE]
> Enunciados duplicados não serão adicionados novamente, mas não causam um erro. O `response` contém a ID do enunciado original.

O JSON a seguir mostra o resultado de uma solicitação bem-sucedida para treinar:

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
ConsoleApp\bin\Debug> ConsoleApp1.exe -status
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
