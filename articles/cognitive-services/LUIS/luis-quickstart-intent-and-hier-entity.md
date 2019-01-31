---
title: Entidade hierárquica
titleSuffix: Azure Cognitive Services
description: Localize partes relacionadas de dados com base no contexto. Por exemplo, os locais de origem e de destino de uma mudança física de um edifício e escritório para outro estão relacionados.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: dc8b9831b89aade7ca5dfd3e1f53b9dccc15e66b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217026"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Tutorial: Extrair dados relacionados ao contexto de um enunciado

Neste tutorial, localize partes relacionadas de dados com base no contexto. Por exemplo, os locais de origem e destino para uma transferência de uma cidade para outra. As duas partes de dados podem ser necessárias e estão relacionadas entre si.  

**Neste tutorial, você aprenderá a:**

> [!div class="checklist"]
> * Criar novo aplicativo
> * Adicionar intenção 
> * Adicionar entidade hierárquica local com os filhos de origem e destino
> * Treinar
> * Publicar
> * Obter intenções e entidades do ponto de extremidade

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="hierarchical-data"></a>Dados hierárquicos

Este aplicativo determina para onde um funcionário deve ser movido, da cidade de origem para a cidade de destino. Ele usa a entidade hierárquica para determinar os locais dentro do enunciado. 

A entidade hierárquica é uma boa opção para esse tipo de dados porque as duas partes de dados, locais filho:

* São entidades simples.
* Estão relacionadas entre si no contexto do enunciado.
* usam uma escolha de palavras específica para indicar cada entidade. Exemplos dessas palavras: de/para, deixando/indo, saindo/entrando.
* ambos os filhos estão frequentemente no mesmo enunciado. 
* Precisam ser agrupados e processados pelo aplicativo cliente como uma unidade de informações.

## <a name="create-a-new-app"></a>Criar um novo aplicativo

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Criar uma intenção para mover os funcionários entre as cidades

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecione **Criar nova intenção**. 

1. Insira `MoveEmployeeToCity` na caixa de diálogo pop-up, depois selecione **Concluído**. 

    ![Captura de tela do diálogo Criar nova intenção com](./media/luis-quickstart-intent-and-hier-entity/create-new-intent-move-employee-to-city.png)

1. Adicione enunciados de exemplo para a intenção.

    |Exemplo de enunciados|
    |--|
    |mover Guilherme Sarmento saindo de Seattle indo para Dallas|
    |transferir Samuel Ferreira de Seattle para Cairo|
    |Remover Jorge Andrade de Tampa, trazendo-o para Atlanta |
    |mover Brenda Fernandes para Tulsa de Dallas|
    |mover Samuel Ferreira saindo de Cairo indo para Tampa|
    |Mudar Marina Azevedo para Oakland de Redmond|
    |Nicolau Mendes de São Francisco para Redmond|
    |Transferir Valério Pereira de São Diego para Bellevue |
    |remover Fabio Pena de Kansas City e movê-lo para Chicago|

    [ ![Captura de tela do LUIS com novos enunciados na intenção MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

## <a name="create-a-location-entity"></a>Criar uma entidade de local
LUIS precisa entender o que é um local rotulando a origem e o destino nos enunciados. Se você precisar ver a expressão no modo de exibição de token (bruto), selecione a alternância na barra acima dos enunciados rotulada **Modo de Exibição de Entidades**. Depois de alternar a opção, o controle é rotulado **exibição Tokens**.

Considere o enunciado a seguir:

```json
move John W. Smith leaving Seattle headed to Dallas
```

O enunciado tem dois locais especificados, `Seattle` e `Dallas`. Ambos são agrupados como filhos de uma entidade hierárquica, `Location`, pois ambas as partes de dados precisam ser extraídas do enunciado para concluir a solicitação no aplicativo cliente e elas estão relacionadas entre si. 
 
Se apenas um filho (origem ou destino) de uma entidade hierárquica estiver presente, ele ainda assim é extraído. Não é necessário que se tenha todos filhos para que seja extraído um ou alguns. 

1. No enunciado, `move John W. Smith leaving Seattle headed to Dallas`, selecione a palavra `Seattle`. Um menu suspenso é exibido com uma caixa de texto na parte superior. Insira o nome da entidade `Location` na caixa de texto, selecione **Criar nova entidade** no menu suspenso. 

    [![Captura de tela da criação da nova entidade na página de intenção](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png "Captura de tela da criação da nova entidade na página de intenção")](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png#lightbox)

1. Na janela pop-up, selecione o tipo de entidade **Hierárquica** com `Origin` e `Destination` como as entidades filho. Selecione **Concluído**.

    ![Captura de tela da caixa de diálogo pop-up de criação de entidade para a nova entidade Location](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Captura de tela da caixa de diálogo pop-up de criação de entidade para a nova entidade Location")

1. O rótulo de `Seattle` está marcado como `Location` porque o LUIS não sabe se o termo foi a origem, o destino ou nenhum deles. Selecione `Seattle`, escolha **Localização**, em seguida, siga o menu à direita e selecione `Origin`.

    [![Captura de tela da caixa de diálogo pop-up de rotulação de entidade para alterar localizações da entidade child](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png "Captura de tela da caixa de diálogo pop-up de rotulação de entidade para alterar localizações da entidade child")](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png#lightbox)

1. Rotule os outros locais em todos os outros enunciados. Quando todos os locais estiverem marcados, os enunciados começarão a ficar como um padrão. 

    [![Captura de tela da entidade Locais rotulada em declarações](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png "Captura de tela da entidade Locais rotulada em declarações")](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png#lightbox)

    O sublinhado vermelho indica que o LUIS não tem certeza quanto à entidade. O treinamento resolve isso. 

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar enunciados de exemplo à intenção None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Treinar o aplicativo para que as alterações à intenção possam ser testadas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicar o aplicativo para que o modelo em que foi feito o treinamento possa ser consultado por meio do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter a previsão de entidade e de intenção do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Vá até o final da URL na barra de endereços e insira `Please move Carl Chamerlin from Tampa to Portland`. O último parâmetro de querystring é `q`, o enunciado **consulta**. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, ele é um bom teste e deve retornar a intenção `MoveEmployee` com a entidade hierárquica extraída.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.979823351
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.979823351
        },
        {
          "intent": "None",
          "score": 0.0156363435
        }
      ],
      "entities": [
        {
          "entity": "portland",
          "type": "Location::Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "tampa",
          "type": "Location::Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    A intenção correta é prevista e a matriz de entidades tem valores de origem e de destino em propriedades de **entidades** correspondentes.
    
## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* Informações conceituais de [entidade hierárquica](luis-concept-entity-types.md)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)
* [Funções versus entidades hierárquicas](luis-concept-roles.md#roles-versus-hierarchical-entities)
* [Melhorar previsões com padrões](luis-concept-patterns.md)

## <a name="next-steps"></a>Próximas etapas

Este tutorial criou uma intenção e adicionou enunciados de exemplo para os dados obtidos de acordo com o contexto de locais de origem e destino. Depois que o aplicativo estiver treinado e publicado, o aplicativo cliente poderá usar essas informações para criar um tíquete de mudança com as informações relevantes.

> [!div class="nextstepaction"] 
> [Saiba como adicionar uma entidade de composição](luis-tutorial-composite-entity.md) 
