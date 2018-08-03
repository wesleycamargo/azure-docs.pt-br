---
title: Tutorial sobre como criar uma entidade composta para extrair dados complexos - Azure | Microsoft Docs
description: Saiba como criar uma entidade composta no aplicativo LUIS para extrair diferentes tipos de dados de entidade.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 07/30/2018
ms.author: diberry
ms.openlocfilehash: 9c84afc231ff4b086e76f50702870e30da7add6e
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364895"
---
# <a name="tutorial-6-add-composite-entity"></a>Tutorial: 6. Adicionar entidade Composta 
Neste tutorial, adicione uma entidade composta para agrupar os dados extraídos em uma entidade contida.

Neste tutorial, você aprenderá como:

<!-- green checkmark -->
> [!div class="checklist"]
> * Reconhecer entidades compostas 
> * Adicionar entidade composta para extrair dados
> * Treinar e publicar o aplicativo
> * Consulte ponto de extremidade do aplicativo para ver a resposta JSON do LUIS

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Antes de começar
Caso não tenha o aplicativo de recursos humanos do tutorial da [entidade hierárquica](luis-quickstart-intent-and-hier-entity.md), [importe](luis-how-to-start-new-app.md#import-new-app) o JSON em um aplicativo novo no site do [LUIS](luis-reference-regions.md#luis-website). O aplicativo a ser importado pode ser encontrado no repositório Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json).

Caso queira manter o aplicativo de recursos humanos original, clone a versão na página [Configurações](luis-how-to-manage-versions.md#clone-a-version) e nomeie-a como `composite`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original.  

## <a name="composite-entity-is-a-logical-grouping"></a>A entidade composta é um agrupamento lógico 
A finalidade da entidade composta é agrupar entidades relacionadas em uma entidade de categoria pai. As informações existem como entidades separadas antes da criação de uma composição. É semelhante à entidade hierárquica, mas pode conter mais tipos de entidades. 

 Crie uma entidade composta, quando as entidades separadas puderem ser logicamente agrupadas e esse agrupamento lógico for útil para o aplicativo cliente. 

Neste aplicativo, o nome do funcionário é definido na entidade de lista **Funcionário** e inclui sinônimos de nome, endereço de email, ramal de telefone da empresa, número de celular e EUA. ID de imposto federal. 

A intenção **MoveEmployee** tem exemplos de enunciados para solicitar que um funcionário seja transferido de um prédio e escritório para outro. Nomes de edifícios são alfabéticos: "A", "B", etc., enquanto os escritórios são numéricos: "1234", "13245". 

Exemplo de enunciados na intenção de **MoveEmployee** incluem:

|Exemplo de enunciados|
|--|
|Transferir João W . Silva para a-2345|
|desloca x12345 para h-1234 amanhã|
 
A solicitação de transferência deve incluir pelo menos o funcionário (usando qualquer sinônimo) e a localização final do prédio e do escritório. A solicitação também pode incluir o escritório de origem, bem como uma data em que a transferência deverá acontecer. 

Os dados extraídos do ponto de extremidade devem conter essas informações e retorná-los em uma entidade composta `RequestEmployeeMove`. 

## <a name="create-composite-entity"></a>Criar entidade composta
1. Verifique se o seu aplicativo de recursos humanos está na seção **Compilar** do LUIS. Você pode alterar essa seção selecionando **Compilar** na barra de menus da parte superior direita. 

2. Na página **Intenções**, selecione **MoveEmployee**. 

3. Selecione o ícone de lupa na barra de ferramentas para filtrar a lista de enunciados. 

    [![](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "Captura de tela de LUIS na intenção 'MoveEmployee' com o botão de lupa realçado")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. Insira `tomorrow` na caixa de texto do filtro para localizar o enunciado `shift x12345 to h-1234 tomorrow`.

    [![](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "Captura de tela de LUIS na intenção 'MoveEmployee' com filtro de 'amanhã' realçado")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    Outro método é filtrar a entidade por datetimeV2, selecionando **Filtros de entidades** e, em seguida, selecionando **datetimeV2** na lista. 

5. Selecione a primeira entidade, `Employee` e, em seguida, selecione **Encapsular em entidade composta** na lista do menu pop-up. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-1.png "Captura de tela do LUIS na intenção 'MoveEmployee' selecionando a primeira entidade na composição realçada")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. Em seguida, selecione imediatamente a última entidade, `datetimeV2` no enunciado. Uma barra verde é desenhada sob as palavras selecionadas, indicando uma entidade composta. No menu pop-up, insira o nome composto `RequestEmployeeMove` e, em seguida, selecione **Criar nova composição** no menu pop-up. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-2.png "Captura de tela do LUIS na intenção 'MoveEmployee' selecionando a última entidade na composição e criando a entidade realçada")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. Em **Que tipo de entidade você quer criar?**, quase todos os campos necessários estão na lista. Apenas o local de origem está ausente. Selecione **Adicionar uma entidade filho**, selecione **Locations::Origin** na lista de entidades existentes, em seguida, selecione **Concluído**. 

    ![Captura de tela do LUIS na intenção 'MoveEmployee' adicionando outra entidade na janela pop-up](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. Selecione a lupa na barra de ferramentas para remover o filtro. 

## <a name="label-example-utterances-with-composite-entity"></a>Declarações de exemplo de rótulo com entidade composta
1. Em cada enunciado de exemplo, selecione a entidade mais à esquerda que deveria estar na composição. Em seguida, selecione **Encapsular em entidade composta**.

    [![](media/luis-tutorial-composite-entity/hr-label-entity-1.png "Captura de tela do LUIS na intenção 'MoveEmployee' selecionando a primeira entidade na composição realçada")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. Selecione a última palavra na entidade composta e, em seguida, selecione **RequestEmployeeMove** no menu pop-up. 

    [![](media/luis-tutorial-composite-entity/hr-label-entity-2.png "Captura de tela do LUIS na intenção 'MoveEmployee' selecionando a última entidade na composição realçada")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. Verifique se todos os enunciados na intenção estão rotulados com a entidade composta. 

    [![](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "Captura de tela do LUIS em 'MoveEmployee' com todos os enunciados realçados")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS
O LUIS não reconhecerá a nova entidade composta até que o aplicativo seja treinado. 

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicar o aplicativo para obter a URL do ponto de extremidade

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint"></a>Consultar o ponto de extremidade 

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá até o final da URL no endereço e insira `Move Jill Jones from a-1234 to z-2345 on March 3 2 p.m.`. O último parâmetro querystring é `q`, a consulta da declaração. 

    Esse teste é para verificar se a composição é extraída corretamente, e um teste poderá incluir um enunciado de exemplo existente ou um novo enunciado. Um bom teste é incluir todas as entidades filho na entidade composta.

    ```JSON
    {
      "query": "Move Jill Jones from a-1234 to z-2345 on March 3  2 p.m",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9959525
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9959525
        },
        {
          "intent": "GetJobInformation",
          "score": 0.009858314
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00728598563
        },
        {
          "intent": "FindForm",
          "score": 0.0058053555
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.005371796
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00266987388
        },
        {
          "intent": "None",
          "score": 0.00123299169
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00116407464
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00102653319
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0006628214
        }
      ],
      "entities": [
        {
          "entity": "march 3 2 p.m",
          "type": "builtin.datetimeV2.datetime",
          "startIndex": 41,
          "endIndex": 54,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2018-03-03 14:00:00"
              },
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2019-03-03 14:00:00"
              }
            ]
          }
        },
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 5,
          "endIndex": 14,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "z - 2345",
          "type": "Locations::Destination",
          "startIndex": 31,
          "endIndex": 36,
          "score": 0.9690751
        },
        {
          "entity": "a - 1234",
          "type": "Locations::Origin",
          "startIndex": 21,
          "endIndex": 26,
          "score": 0.9713137
        },
        {
          "entity": "-1234",
          "type": "builtin.number",
          "startIndex": 22,
          "endIndex": 26,
          "resolution": {
            "value": "-1234"
          }
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 32,
          "endIndex": 36,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 47,
          "endIndex": 47,
          "resolution": {
            "value": "3"
          }
        },
        {
          "entity": "2",
          "type": "builtin.number",
          "startIndex": 50,
          "endIndex": 50,
          "resolution": {
            "value": "2"
          }
        },
        {
          "entity": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "type": "requestemployeemove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "requestemployeemove",
          "value": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "children": [
            {
              "type": "builtin.datetimeV2.datetime",
              "value": "march 3 2 p.m"
            },
            {
              "type": "Locations::Destination",
              "value": "z - 2345"
            },
            {
              "type": "Employee",
              "value": "jill jones"
            },
            {
              "type": "Locations::Origin",
              "value": "a - 1234"
            }
          ]
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
    }
    ```

  Esse enunciado retorna uma matriz de entidades compostas. Cada entidade recebe um tipo e valor. Para conseguir maior precisão para cada entidade filho, use a combinação de tipo e valor do item da matriz composta para localizar o item correspondente na matriz de entidades.  

## <a name="what-has-this-luis-app-accomplished"></a>O que esse aplicativo de LUIS realizou?
Esse aplicativo identificou uma intenção de consulta de linguagem natural e retornou os dados extraídos como um grupo nomeado. 

O chatbot agora tem informações suficientes para determinar a ação primária e os detalhes relacionados no enunciado. 

## <a name="where-is-this-luis-data-used"></a>Onde esses dados do LUIS são usados? 
O LUIS é feito com essa solicitação. O aplicativo de chamada, como um chatbot, pode levar o resultado de topScoringIntent e os dados da entidade para realizar a próxima etapa. O LUIS não realiza esse trabalho de programação para o bot ou para o aplicativo de chamada. O LUIS só determina qual é a intenção do usuário. 

## <a name="clean-up-resources"></a>Limpar recursos

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Saiba mais sobre como adicionar uma entidade simples com uma lista de frases](luis-quickstart-primary-and-secondary-data.md)  