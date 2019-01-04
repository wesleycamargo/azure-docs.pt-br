---
title: Entidade composta
titleSuffix: Azure Cognitive Services
description: Adicione uma entidade composta para agrupar dados extraídos de vários tipos em uma única entidade contida. Agrupando os dados, o aplicativo cliente poderá extrair com facilidade dados relacionados em diferentes tipos de dados.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: b5923d5cd4a704dda76e33ee6a2b76cfd903219d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079204"
---
# <a name="tutorial-6-group-and-extract-related-data"></a>Tutorial 6: Agrupar e extrair dados relacionados
Neste tutorial, adicione uma entidade composta para agrupar dados extraídos de vários tipos em uma única entidade contida. Agrupando os dados, o aplicativo cliente poderá extrair com facilidade dados relacionados em diferentes tipos de dados.

A finalidade da entidade composta é agrupar entidades relacionadas em uma entidade de categoria pai. As informações existem como entidades separadas antes da criação de uma composição. É semelhante à entidade hierárquica, mas pode conter diferentes tipos de entidades. 

A entidade composta é uma boa opção para esse tipo de dados, porque os dados:

* Estão relacionados uns aos outros. 
* Usam uma variedade de tipos de entidade.
* Precisam ser agrupadas e processados pelo aplicativo cliente como uma unidade de informações.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Usar o aplicativo do tutorial existente
> * Adicionar entidade composta 
> * Treinar
> * Publicar
> * Obter intenções e entidades do ponto de extremidade

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usar o aplicativo existente
Continue com o aplicativo criado no último tutorial, denominado **HumanResources**. 

Se você não tiver o aplicativo HumanResources do tutorial anterior, use as seguintes etapas:

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-hier-HumanResources.json).

2. Importe o JSON em um novo aplicativo.

3. Na seção **Gerenciar**, na guia **Versões**, clone a versão e nomeie-a como `composite`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL.


## <a name="composite-entity"></a>Entidade composta
Crie uma entidade composta, quando as entidades separadas puderem ser logicamente agrupadas e esse agrupamento lógico for útil para o aplicativo cliente. 

Neste aplicativo, o nome do funcionário é definido na entidade de lista **Funcionário** e inclui sinônimos de nome, endereço de email, ramal de telefone da empresa, número de celular e EUA. ID de imposto federal. 

A intenção **MoveEmployee** tem exemplos de enunciados para solicitar que um funcionário seja transferido de um prédio e escritório para outro. A criação de nomes é alfabética: "A", "B", etc. Escritórios são numéricos: "1234", "13245". 

Exemplo de enunciados na intenção de **MoveEmployee** incluem:

|Exemplo de enunciados|
|--|
|Transferir João W . Silva para a-2345|
|desloca x12345 para h-1234 amanhã|
 
A solicitação de transferência deve incluir o funcionário (usando qualquer sinônimo) e a localização final do prédio e do escritório. A solicitação também pode incluir o escritório de origem, bem como uma data em que a transferência deverá acontecer. 

Os dados extraídos do ponto de extremidade devem conter essas informações e retorná-las em uma entidade composta `RequestEmployeeMove`:

```json
"compositeEntities": [
  {
    "parentType": "RequestEmployeeMove",
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
]
```

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Na página **Intenções**, selecione **MoveEmployee**. 

3. Selecione o ícone de lupa na barra de ferramentas para filtrar a lista de enunciados. 

    [![Captura de tela do LUIS na intenção 'MoveEmployee' com o botão de lupa realçado](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "Captura de tela do LUIS na intenção 'MoveEmployee' com o botão de lupa realçado")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. Insira `tomorrow` na caixa de texto do filtro para localizar o enunciado `shift x12345 to h-1234 tomorrow`.

    [![Captura de tela do LUIS na intenção 'MoveEmployee' com o filtro 'tomorrow' realçado](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "Captura de tela do LUIS na intenção 'MoveEmployee' com o filtro 'tomorrow' realçado")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    Outro método é filtrar a entidade por datetimeV2, selecionando **Filtros de entidades** e, em seguida, selecionando **datetimeV2** na lista. 

5. Selecione a primeira entidade, `Employee` e, em seguida, selecione **Encapsular em entidade composta** na lista do menu pop-up. 

    [![Captura de tela do LUIS na intenção 'MoveEmployee' selecionando a primeira entidade na composição realçada](media/luis-tutorial-composite-entity/hr-create-entity-1.png "Captura de tela do LUIS na intenção 'MoveEmployee' selecionando a primeira entidade na composição realçada")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. Em seguida, selecione imediatamente a última entidade, `datetimeV2` no enunciado. Uma barra verde é desenhada sob as palavras selecionadas, indicando uma entidade composta. No menu pop-up, insira o nome composto `RequestEmployeeMove` e, em seguida, selecione enter. 

    [![Captura de tela do LUIS na intenção 'MoveEmployee' selecionando a última entidade na composição e criando a entidade realçada](media/luis-tutorial-composite-entity/hr-create-entity-2.png "Captura de tela do LUIS na intenção 'MoveEmployee' selecionando a última entidade na composição e criando a entidade realçada")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. Em **Que tipo de entidade você quer criar?**, quase todos os campos necessários estão na lista. Apenas o local de origem está ausente. Selecione **Adicionar uma entidade filho**, selecione **Locations::Origin** na lista de entidades existentes, em seguida, selecione **Concluído**. 

    Observe que a entidade predefinida, número, foi adicionada à entidade composta. Se você pudesse fazer uma entidade predefinida ser exibida entre os tokens de início e fim de uma entidade composta, ela deveria conter essas entidades predefinidas. Se as entidades predefinidas não forem incluídas, a entidade composta não será prevista corretamente, mas, sim, cada elemento individual.

    ![Captura de tela do LUIS na intenção 'MoveEmployee' adicionando outra entidade na janela pop-up](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. Selecione a lupa na barra de ferramentas para remover o filtro. 

9. Remova a palavra `tomorrow` do filtro para poder ver todos os enunciados de exemplo novamente. 

## <a name="label-example-utterances-with-composite-entity"></a>Declarações de exemplo de rótulo com entidade composta


1. Em cada enunciado de exemplo, selecione a entidade mais à esquerda que deveria estar na composição. Em seguida, selecione **Encapsular em entidade composta**.

    [![Captura de tela do LUIS na intenção 'MoveEmployee' selecionando a primeira entidade na composição realçada](media/luis-tutorial-composite-entity/hr-label-entity-1.png "Captura de tela do LUIS na intenção 'MoveEmployee' selecionando a primeira entidade na composição realçada")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. Selecione a última palavra na entidade composta e, em seguida, selecione **RequestEmployeeMove** no menu pop-up. 

    [![Captura de tela do LUIS na intenção 'MoveEmployee' selecionando a última entidade na composição realçada](media/luis-tutorial-composite-entity/hr-label-entity-2.png "Captura de tela do LUIS na intenção 'MoveEmployee' selecionando a última entidade na composição realçada")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. Verifique se todos os enunciados na intenção estão rotulados com a entidade composta. 

    [![Captura de tela do LUIS em 'MoveEmployee' com todas as declarações rotuladas](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "Captura de tela do LUIS em 'MoveEmployee' com todas as declarações rotuladas")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train"></a>Treinar

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicar

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obter a intenção e as entidades do ponto de extremidade 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá até o final da URL no endereço e insira `Move Jill Jones from a-1234 to z-2345 on March 3 2 p.m.`. O último parâmetro querystring é `q`, a consulta da declaração. 

    Esse teste é para verificar se a composição é extraída corretamente, e um teste poderá incluir um enunciado de exemplo existente ou um novo enunciado. Um bom teste é incluir todas as entidades filho na entidade composta.

    ```json
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
          "type": "RequestEmployeeMove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "RequestEmployeeMove",
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
      ]
    }
    ```

  Esse enunciado retorna uma matriz de entidades compostas. Cada entidade recebe um tipo e valor. Para conseguir maior precisão para cada entidade filho, use a combinação de tipo e valor do item da matriz composta para localizar o item correspondente na matriz de entidades.  

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Este tutorial criou uma entidade composta para encapsular as entidades existentes. Isso permite que o aplicativo cliente encontre um grupo de dados relacionados em diferentes tipos de dados para continuar a conversa. Um aplicativo cliente para este aplicativo de Recursos Humanos poderia perguntar qual dia e hora a transferência precisa começar e terminar. Ele também poderia pedir outra logística da transferência, como um telefone físico. 

> [!div class="nextstepaction"] 
> [Saiba mais sobre como adicionar uma entidade simples com uma lista de frases](luis-quickstart-primary-and-secondary-data.md)  
