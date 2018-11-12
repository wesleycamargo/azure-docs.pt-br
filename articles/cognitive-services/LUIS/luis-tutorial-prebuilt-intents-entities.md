---
title: 'Tutorial 2: Intenções e entidades pré-criadas – usar enunciados comuns pré-criados – extrair dados comuns no LUIS'
titleSuffix: Azure Cognitive Services
description: Adicione intenções e entidades predefinidas ao aplicativo de tutorial de Recursos Humanos para adquirir rapidamente previsão de intenção e extração de dados. Não é necessário rotular nenhum enunciado com as entidades pré-criadas. A entidade é detectada automaticamente.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: d73284ecf40b2dfcd4954c1ddee82bf92a8931b9
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281788"
---
# <a name="tutorial-2-identify-common-intents-and-entities"></a>Tutorial 2: Identificar intenções e entidades comuns
Neste tutorial, modifique o aplicativo de recursos humanos. Adicione intenções e entidades predefinidas ao aplicativo de tutorial de Recursos Humanos para adquirir rapidamente previsão de intenção e extração de dados. Não é necessário rotular os enunciados com as entidades pré-criadas, porque a entidade é detectada automaticamente.

Os modelos pré-criados de domínios no assunto e dados tipos comuns ajudam você a criar seu modelo rapidamente e também fornecem um exemplo da aparência de um modelo. 

**Neste tutorial, você aprenderá a:**

> [!div class="checklist"]
> * Usar o aplicativo do tutorial existente
> * Adicionar intenções predefinidas 
> * Adicionar entidades predefinidas 
> * Treinar 
> * Publicar 
> * Obter intenções e entidades do ponto de extremidade

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usar o aplicativo existente
Continue com o aplicativo criado no último tutorial, denominado **HumanResources**. 

Se você não tiver o aplicativo HumanResources do tutorial anterior, use as seguintes etapas:

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-intent-only-HumanResources.json).

2. Importe o JSON em um novo aplicativo.

3. Na seção **Gerenciar**, na guia **Versões**, clone a versão e nomeie-a como `prebuilts`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL. 

## <a name="add-prebuilt-intents"></a>Adicionar intenções predefinidas
O LUIS oferece várias intenções predefinidas para ajudar com intenções do comuns do usuário.  

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selecione **Adicionar intenção pré-criada**. 

3. Pesquise `Utilities`. 

    [ ![Captura de tela da caixa de diálogo de intenções predefinidas com Utilitários na caixa de pesquisa](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Selecione as seguintes intenções e selecione **Concluído**: 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>Adicionar entidades predefinidas
O LUIS fornece várias entidades predefinidas para extração de dados comuns. 

1. Selecione **Entidades** no menu de navegação à esquerda.

2. Selecione o botão **Gerenciar entidade pré-criada**.

3. Selecione **number** e **datetimeV2** na lista de entidades predefinidas e selecione **Concluído**.

    ![Captura de tela do número selecionado no diálogo de entidades predefinidas](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train"></a>Treinar

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicar

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obter a intenção e as entidades do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Acesse o fim da URL na barra de endereço do navegador e insira `I want to cancel on March 3`. O último parâmetro da cadeia de consulta é `q`, a declaração **query**. 

    ```JSON
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

    O resultado previu a intenção Utilities.Cancel e extraiu a data de 3 de março e o número 3. 

    Há dois valores para 3 de março, porque o enunciado diz se 3 de março está no passado ou no futuro. É responsabilidade do aplicativo cliente fazer uma suposição ou pedir esclarecimentos, caso necessário. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Adicionando entidades e intenções pré-criadas, o aplicativo cliente pode determinar as intenções comuns do usuário e extrair os tipos de dados comuns. 

> [!div class="nextstepaction"]
> [Saiba como adicionar uma entidade de expressão regular](luis-quickstart-intents-regex-entity.md)

