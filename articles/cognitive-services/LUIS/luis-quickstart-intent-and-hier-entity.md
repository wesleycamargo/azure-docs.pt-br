---
title: 'Tutorial 5: Relações de pai/filho – entidade hierárquica do LUIS para dados aprendidos contextualmente'
titleSuffix: Azure Cognitive Services
description: Localize partes relacionadas de dados com base no contexto. Por exemplo, os locais de origem e de destino de uma mudança física de um edifício e escritório para outro estão relacionados.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: d3b8d0597f0732a4a3cfab79125a885b2d141c9f
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424690"
---
# <a name="tutorial-5-extract-contextually-related-data"></a>Tutorial 5: Extrair dados relacionados contextualmente
Neste tutorial, localize partes relacionadas de dados com base no contexto. Por exemplo, os locais de origem e de destino de uma mudança física de um edifício e escritório para outro estão relacionados. Para gerar uma ordem de trabalho, ambas as partes de dados podem ser necessárias e elas estão relacionadas umas às outras.  

Esse aplicativo determina para onde o funcionário deve ser movido, do local de origem (prédio e sala) para o local de destino (prédio e sala). Ele usa a entidade hierárquica para determinar os locais dentro do enunciado. A finalidade da entidade **hierárquica** é encontrar dados relacionados dentro do enunciado com base no contexto. 

A entidade hierárquica é uma boa opção para esse tipo de dado porque as duas partes dos dados:

* São entidades simples.
* Estão relacionadas entre si no contexto do enunciado.
* Usam escolhas de palavras específicas para indicar cada local. Exemplos dessas palavras: de/para, deixando/indo, saindo/entrando.
* Os locais estão frequentemente no mesmo enunciado. 
* Precisam ser agrupados e processados pelo aplicativo cliente como uma unidade de informações.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Usar o aplicativo do tutorial existente
> * Adicionar intenção 
> * Adicionar entidade hierárquica local com os filhos de origem e destino
> * Treinar
> * Publicar
> * Obter intenções e entidades do ponto de extremidade

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usar o aplicativo existente
Continue com o aplicativo criado no último tutorial, denominado **HumanResources**. 

Se você não tiver o aplicativo HumanResources do tutorial anterior, use as seguintes etapas:

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-list-HumanResources.json).

2. Importe o JSON em um novo aplicativo.

3. Na seção **Gerenciar**, na guia **Versões**, clone a versão e nomeie-a como `hier`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL. 

## <a name="remove-prebuilt-number-entity-from-app"></a>Remover entidade número predefinida do aplicativo
Para ver a expressão inteira e marcar os filhos hierárquicos, remova temporariamente a entidade de número predefinida.

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selecione **Entidades** no menu à esquerda.

3. Marque a caixa de seleção à esquerda da entidade de número na lista. Selecione **Excluir**. 

## <a name="add-utterances-to-moveemployee-intent"></a>Adicionar enunciados à intenção MoveEmployee

1. Selecione **Intents** no menu esquerdo.

2. Selecione **MoveEmployee** na lista de intenções.

3. Adicione os seguintes enunciados de exemplo:

    |Exemplo de enunciados|
    |--|
    |Mover John W. Smith **para** a-2345|
    |Direcionar Jones Jill **para** b-3499|
    |Organizar a movimentação de x23456 **de** hh-2345 **para** e-0234|
    |Começar a documentação para definir x12345 **deixando** a-3459 **em direção a** f-34567|
    |Deslocar 425-555-0000 **de** g-2323 **para** hh-2345|

    [ ![Captura de tela do LUIS com novos enunciados na intenção MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

    No tutorial de [entidade de lista](luis-quickstart-intent-and-list-entity.md), um funcionário é designado por nome, endereço de email, ramal do telefone, número de telefone celular ou número do seguro social. Esses números de funcionário são usados nos enunciados. Os enunciados do exemplo anterior incluem formas de anotar os locais de origem e destino, marcados em negrito. Alguns enunciados têm somente destinos propositalmente. Isso ajuda o LUIS a compreender como esses locais são colocados no enunciado quando a origem não é especificada.     

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="create-a-location-entity"></a>Criar uma entidade de local
LUIS precisa entender o que é um local rotulando a origem e o destino nos enunciados. Se você precisar ver a expressão no modo de exibição de token (bruto), selecione a alternância na barra acima dos enunciados rotulada **Modo de Exibição de Entidades**. Depois de alternar a opção, o controle é rotulado **exibição Tokens**.

Considere o enunciado a seguir:

```JSON
mv Jill Jones from a-2349 to b-1298
```

O enunciado tem dois locais especificados, `a-2349` e `b-1298`. Suponha que a letra corresponda a um nome de prédio e o número indique a sala dentro desse prédio. Faz sentido que são ambos sejam agrupados como filhos de uma entidade hierárquica, `Locations`, pois ambas as partes de dados precisam ser extraídas do enunciado para concluir a solicitação no aplicativo cliente e elas estão relacionadas umas às outras. 
 
Se apenas um filho (origem ou destino) de uma entidade hierárquica estiver presente, ele ainda assim é extraído. Não é necessário que se tenha todos filhos para que seja extraído um ou alguns. 

1. No enunciado, `Displace 425-555-0000 away from g-2323 toward hh-2345`, selecione a palavra `g-2323`. Um menu suspenso é exibido com uma caixa de texto na parte superior. Insira o nome da entidade `Locations` na caixa de texto, selecione **Criar nova entidade** no menu suspenso. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png "Captura de tela da criação de nova entidade na página de intenção")](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png#lightbox)

2. Na janela pop-up, selecione o tipo de entidade **Hierárquica** com `Origin` e `Destination` como as entidades filho. Selecione **Concluído**.

    ![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Captura de tela do diálogo pop-up de criação de entidade para a nova entidade de Local")

3. O rótulo de `g-2323` está marcado como `Locations` porque o LUIS não sabe se o termo foi a origem, o destino ou nenhum deles. Selecione `g-2323`; em seguida, selecione **Locais**, siga o menu à direita e selecione `Origin`.

    [![](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png "Captura de tela do diálogo pop-up de rotulação de entidade para alterar o filho da entidade Locais")](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png#lightbox)

5. Rotule os outros locais em todos os outros enunciados selecionando o prédio e a sala no enunciado, selecionando Locais e seguindo o menu à direita para selecionar `Origin` ou `Destination`. Quando todos os locais estiverem rotulados, os enunciados no **Modo de Exibição de Tokens** começará a mostrar um padrão. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png "Captura de tela da entidade Locais rotulada nos enunciados")](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png#lightbox)

## <a name="add-prebuilt-number-entity-to-app"></a>Adicionar entidade de número predefinida ao aplicativo
Adicione a entidade numérica predefinida novamente ao aplicativo.

1. Selecione **Entidades** no menu de navegação à esquerda.

2. Selecione o botão **Adicionar entidade predefinida**.

3. Selecione **número** na lista de entidades predefinidas, e selecione **Concluído**.

    ![Captura de tela do número selecionado no diálogo de entidades predefinidas](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicar o aplicativo para obter a URL do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Consultar o ponto de extremidade com um enunciado diferente

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


2. Vá até o final da URL na barra de endereços e insira `Please relocation jill-jones@mycompany.com from x-2345 to g-23456`. O último parâmetro de querystring é `q`, o enunciado **consulta**. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, ele é um bom teste e deve retornar a intenção `MoveEmployee` com a entidade hierárquica extraída.

    ```JSON
    {
      "query": "Please relocation jill-jones@mycompany.com from x-2345 to g-23456",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9966052
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9966052
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0325253047
        },
        {
          "intent": "FindForm",
          "score": 0.006137873
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00462633232
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00415637763
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00382325822
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00249120337
        },
        {
          "intent": "None",
          "score": 0.00130756292
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00119622645
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.26910036E-05
        }
      ],
      "entities": [
        {
          "entity": "jill - jones @ mycompany . com",
          "type": "Employee",
          "startIndex": 18,
          "endIndex": 41,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "x - 2345",
          "type": "Locations::Origin",
          "startIndex": 48,
          "endIndex": 53,
          "score": 0.8520272
        },
        {
          "entity": "g - 23456",
          "type": "Locations::Destination",
          "startIndex": 58,
          "endIndex": 64,
          "score": 0.974032
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 49,
          "endIndex": 53,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "-23456",
          "type": "builtin.number",
          "startIndex": 59,
          "endIndex": 64,
          "resolution": {
            "value": "-23456"
          }
        }
      ]
    }
    ```
    
    A intenção correta é prevista e a matriz de entidades tem os valores de origem e de destino na propriedade **entity** correspondente.
    

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>Uma expressão regular poderia ser usada para cada local?
Sim, crie a entidade de expressão regular com funções de origem e destino e use-a em um padrão.

Os locais neste exemplo, como `a-1234`, seguem um formato específico de uma ou duas letras com um traço e, em seguida, uma série de 4 ou 5 números. Esses dados podem ser descritos como uma entidade de expressão regular com uma função para cada local. As funções estão disponíveis apenas para padrões. Você pode criar padrões com base nesses enunciados, criar uma expressão regular para o formato de local e adicioná-la aos padrões. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="hierarchical-entities-versus-roles"></a>Funções versus entidades hierárquicas

Para mais informações, consulte [Funções versus entidades hierárquicas](luis-concept-roles.md#roles-versus-hierarchical-entities).

## <a name="next-steps"></a>Próximas etapas
Este tutorial criou uma nova intenção e adicionou enunciados de exemplo para os dados aprendidos contextualmente dos locais de origem e de destino. Depois que o aplicativo estiver treinado e publicado, o aplicativo cliente poderá usar essas informações para criar um tíquete de mudança com as informações relevantes.

> [!div class="nextstepaction"] 
> [Saiba como adicionar uma entidade de composição](luis-tutorial-composite-entity.md) 
