---
title: Adicionar intenções e entidades predefinidas para extrair dados comuns no Reconhecimento vocal – Azure | Microsoft Docs
description: Saiba como usar intenções e entidades predefinidas para extrair diferentes tipos de dados de entidade.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: diberry
ms.openlocfilehash: 0ec6f002b35b1224118b62accda1f69e7be22fb8
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358515"
---
# <a name="tutorial-2-add-prebuilt-intents-and-entities"></a>Tutorial: 2. Adicionar entidades e intenções predefinidas
Adicione intenções e entidades predefinidas ao aplicativo de tutorial de Recursos Humanos para adquirir rapidamente previsão de intenção e extração de dados. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
* Adicionar intenções predefinidas 
* Adicionar entidades predefinidas datetimeV2 e number
* Treinar e publicar
* Consultar o LUIS e receber resposta de previsão

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Antes de começar
Caso não tenha o aplicativo de [recursos humanos](luis-quickstart-intents-only.md) do tutorial anterior, [importe](luis-how-to-start-new-app.md#import-new-app) o JSON em um novo aplicativo no site do [LUIS](luis-reference-regions.md#luis-website) do repositório Github de [exemplos do LUIS](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json).

Caso queira manter o aplicativo de recursos humanos original, clone a versão na página [Configurações](luis-how-to-manage-versions.md#clone-a-version) e nomeie-a como `prebuilts`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. 

## <a name="add-prebuilt-intents"></a>Adicionar intenções predefinidas
O LUIS oferece várias intenções predefinidas para ajudar com intenções do comuns do usuário.  

1. Verifique se o seu aplicativo está na seção **Criar** do LUIS. Você pode alterar essa seção selecionando **Compilar** na barra de menus da parte superior direita. 

2. Selecione **Adicionar intenção de domínio predefinida**. 

    [ ![Captura de tela da página Intenções com o botão Adicionar intenção de domínio predefinida realçado](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

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

    [ ![Captura de tela da lista de intenções com Entidades realçado na navegação esquerda](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. Selecione o botão **Gerenciar entidades predefinidas**.

    [ ![Captura de tela da lista Entidades com a opção Gerenciar entidades predefinidas realçada](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. Selecione **number** e **datetimeV2** na lista de entidades predefinidas e selecione **Concluído**.

    ![Captura de tela do número selecionado no diálogo de entidades predefinidas](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>Treinar e publicar o aplicativo

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-app-to-endpoint"></a>Publicar o aplicativo para o ponto de extremidade

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-endpoint-with-an-utterance"></a>Consultar o ponto de extremidade com uma declaração

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá até o final da URL no endereço e insira `I want to cancel on March 3`. O último parâmetro da cadeia de caracteres de consulta é `q`, a declaração **query**. 

    O resultado previu a intenção Utilities.Cancel e extraiu a data de 3 de março e o número 3. 

    ```
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

    Há dois valores para 3 de março, porque o enunciado diz se 3 de março está no passado ou no futuro. É responsabilidade do aplicativo de chamada do LUIS fazer uma suposição ou pedir esclarecimentos, se for necessário. 

    Ao adicionar rápida e facilmente intenções e entidades predefinidas, o aplicativo cliente pode adicionar gerenciamento de conversa e extrair tipos de dados comuns. 

## <a name="clean-up-resources"></a>Limpar recursos

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como adicionar uma entidade de expressão regular](luis-quickstart-intents-regex-entity.md)

