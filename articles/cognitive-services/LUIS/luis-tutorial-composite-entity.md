---
title: Criar uma entidade composta para extrair dados complexos – Azure | Microsoft Docs
description: Saiba como criar uma entidade composta no aplicativo LUIS para extrair diferentes tipos de dados de entidade.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 03/28/2018
ms.author: v-geberr
ms.openlocfilehash: cb581ee60dea2b0810332933455a03a8b68e16ea
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264378"
---
# <a name="use-composite-entity-to-extract-complex-data"></a>Use a entidade composta para extrair dados complexos
Este aplicativo simples tem duas [intenções](luis-concept-intent.md) e várias entidades. Sua finalidade é reservar voos como "1 passagem de Seattle para Cairo na sexta-feira" e retornar todas as especificações da reserva como uma parte única dos dados. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
* Adicionar entidades predefinidas datetimeV2 e number
* Criar uma entidade composta
* Consultar o LUIS e receber dados de entidade composta

## <a name="before-you-begin"></a>Antes de começar
* Seu aplicativo LUIS do **[início rápido hierárquico](luis-tutorial-composite-entity.md)**. 

> [!Tip]
> Se você ainda não tem uma assinatura, é possível se registrar em uma [conta gratuita](https://azure.microsoft.com/free/).

## <a name="composite-entity-is-a-logical-grouping"></a>A entidade composta é um agrupamento lógico 
A finalidade da entidade é localizar e categorizar partes do texto no enunciado. Uma entidade [composta](luis-concept-entity-types.md) é composta de outros tipos de entidade aprendidos de contexto. Para esse aplicativo de viagem que faz reservas de voos, há várias partes de informações somo datas, locais e número de assentos. 

As informações existem como entidades separadas antes da criação de uma composição. Crie uma entidade composta quando as entidades separadas puderem ser logicamente agrupadas e esse agrupamento lógico for útil para o chatbot ou outro aplicativo que consome o LUIS. 

Os exemplos de enunciados simples dos usuários incluem:

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Reserve a seat from New York to Paris on the first of April
```
 
A entidade composta corresponde à contagem de assentos, local da origem, local do destino e data. 

## <a name="what-luis-does"></a>O que o LUIS faz
Quando a intenção e as entidades do enunciado são identificadas, [extraídas](luis-concept-data-extraction.md#list-entity-data) e retornadas em JSON do [ponto de extremidade](https://aka.ms/luis-endpoint-apis), o LUIS conclui sua ação. O aplicativo de chamada ou chatbot usa essa resposta JSON e atende a solicitação, conforme a maneira como o aplicativo ou chatbot foram projetados. 

## <a name="add-prebuilt-entities-number-and-datetimev2"></a>Adicionar entidades predefinidas number e datetimeV2
1. Selecione o aplicativo `MyTravelApp` da lista de aplicativos no site do [LUIS][LUIS].

2. Quando o aplicativo abrir, selecione o link de navegação esquerdo **Entidades**.

    ![Botão Selecionar entidades](./media/luis-tutorial-composite-entity/intents-page-select-entities.png)    

3. Selecione **Gerenciar entidades predefinidas**.

    ![Botão Selecionar entidades](./media/luis-tutorial-composite-entity/manage-prebuilt-entities-button.png)

4. Na caixa pop-up, selecione **number** e **datetimeV2**.

    ![Botão Selecionar entidades](./media/luis-tutorial-composite-entity/prebuilt-entity-ddl.png)

5. Para que as novas entidades sejam extraídas, selecione **Treinar** na barra de navegação superior.

    ![Selecione o botão treinar](./media/luis-tutorial-composite-entity/train.png)

## <a name="use-existing-intent-to-create-composite-entity"></a>Usar intenção existente para criar a entidade composta
1. Selecione **Intenções** no painel de navegação esquerdo. 

    ![Selecione a página Intenções](./media/luis-tutorial-composite-entity/intents-from-entities-page.png)

2. Selecione `BookFlight` na lista **Intenções**.  

    ![Selecione a intenção BookFlight da lista](./media/luis-tutorial-composite-entity/intent-page-with-prebuilt-entities-labeled.png)

    As entidades predefinidas number e datetimeV2 são rotuladas nas declarações.

3. Para a declaração `book 2 flights from seattle to cairo next monday`, selecione a entidade `number` azul e **Encapsular na entidade composta** da lista. Uma linha verde, sob as palavras, segue o cursor à medida que ele se move para a direita, indicando uma entidade composta. Em seguida, passe para a direita para selecionar a última entidade predefinida `datetimeV2`, insira `FlightReservation` na caixa de texto da janela pop-up e selecione **Criar nova composição**. 

    ![Criar entidade composta na página de intenções](./media/luis-tutorial-composite-entity/create-new-composite.png)

4. Uma caixa de diálogo pop-up que permite que você verifique se os filhos de entidade composta. Selecione **Concluído**.

    ![Criar entidade composta na página de intenções](./media/luis-tutorial-composite-entity/validate-composite-entity.png)

## <a name="wrap-the-entities-in-the-composite-entity"></a>Encapsular as entidades na entidade composta
Depois que a entidade composta for criada, rotule as declarações restantes na entidade composta. Para encapsular uma frase como uma entidade composta, é necessário selecionar a palavra mais à esquerda e selecionar **Encapsular na entidade composta** na lista exibida e, em seguida, selecionar a palavra mais à direita e selecionar a entidade composta nomeada `FlightReservation`. Esta é uma etapa rápida e suave de seleções, divididas nas seguintes etapas:

1. Na declaração `schedule 4 seats from paris to london for april 1`, selecione o 4 como a entidade predefinida de número.

    ![Selecione a palavra mais à esquerda](./media/luis-tutorial-composite-entity/wrap-composite-step-1.png)

2. Selecione **Encapsular na entidade composta** da lista exibida.

    ![Selecione o encapsulamento da lista](./media/luis-tutorial-composite-entity/wrap-composite-step-2.png)

3. Selecione a palavra mais à direita. Uma linha verde é exibida embaixo da frase, indicando uma entidade composta.

    ![Selecione a palavra mais à direita](./media/luis-tutorial-composite-entity/wrap-composite-step-3.png)

4. Selecione o nome composto `FlightReservation` na lista exibida.

    ![Selecione a entidade composta nomeada](./media/luis-tutorial-composite-entity/wrap-composite-step-4.png)

    Para a última declaração, encapsule `London` e `tomorrow` na entidade composta, usando as mesmas instruções. 

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS
O LUIS não fica ciente das alterações nas intenções e entidades (o modelo) até que seja treinado. 

1. No canto superior direito do site do LUIS, selecione o botão **Train** (Treinar).

    ![Treinar o aplicativo](./media/luis-tutorial-composite-entity/train-button.png)

2. O treinamento é concluído quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

    ![Treinamento bem-sucedido](./media/luis-tutorial-composite-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicar o aplicativo para obter a URL do ponto de extremidade
Para obter uma previsão do LUIS em um chatbot ou outro aplicativo, você precisa publicar o aplicativo. 

1. No canto superior direito do site do LUIS, selecione o botão **Publish** (Publicar). 

2. Selecione o slot de produção e o botão **Publicar**.

    ![publicar aplicativo](./media/luis-tutorial-composite-entity/publish-to-production.png)

3. A publicação estará concluída quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Consultar o ponto de extremidade com um enunciado diferente
1. Na página **Publicar**, selecione o link do **ponto de extremidade** na parte inferior da página. Essa ação abre outra janela do navegador com a URL de ponto de extremidade na barra de endereços. 

    ![Selecionar a URL de ponto de extremidade](./media/luis-tutorial-composite-entity/publish-select-endpoint.png)

2. Vá até o final da URL no endereço e insira `reserve 3 seats from London to Cairo on Sunday`. O último parâmetro querystring é `q`, a consulta da declaração. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, ele é um bom teste e deve retornar a intenção `BookFlight` com a entidade hierárquica extraída.

```
{
  "query": "reserve 3 seats from London to Cairo on Sunday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.999999046
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.999999046
    },
    {
      "intent": "None",
      "score": 0.227036044
    }
  ],
  "entities": [
    {
      "entity": "sunday",
      "type": "builtin.datetimeV2.date",
      "startIndex": 40,
      "endIndex": 45,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-03-25"
          },
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-04-01"
          }
        ]
      }
    },
    {
      "entity": "3 seats from london to cairo on sunday",
      "type": "flightreservation",
      "startIndex": 8,
      "endIndex": 45,
      "score": 0.6892485
    },
    {
      "entity": "cairo",
      "type": "Location::Destination",
      "startIndex": 31,
      "endIndex": 35,
      "score": 0.557570755
    },
    {
      "entity": "london",
      "type": "Location::Origin",
      "startIndex": 21,
      "endIndex": 26,
      "score": 0.8933808
    },
    {
      "entity": "3",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "3"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "flightreservation",
      "value": "3 seats from london to cairo on sunday",
      "children": [
        {
          "type": "builtin.datetimeV2.date",
          "value": "sunday"
        },
        {
          "type": "Location::Destination",
          "value": "cairo"
        },
        {
          "type": "builtin.number",
          "value": "3"
        },
        {
          "type": "Location::Origin",
          "value": "london"
        }
      ]
    }
  ]
}
```

Essa declaração retorna uma matriz de entidades compostas incluindo o objeto **flightreservation** com os dados extraídos.  

## <a name="what-has-this-luis-app-accomplished"></a>O que esse aplicativo de LUIS realizou?
Esse aplicativo, com apenas duas intenções e uma entidade composta, identificou uma intenção de consulta de linguagem natural e retornou os dados extraídos. 

Agora seu chatbot tem informações suficientes para determinar a ação primária, `BookFlight`, e as informações de reserva encontradas na declaração. 

## <a name="where-is-this-luis-data-used"></a>Onde esses dados do LUIS são usados? 
O LUIS é feito com essa solicitação. O aplicativo de chamada, como um chatbot, pode levar o resultado de topScoringIntent e os dados da entidade para realizar a próxima etapa. O LUIS não realiza esse trabalho de programação para o bot ou para o aplicativo de chamada. O LUIS só determina qual é a intenção do usuário. 

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre entidades](luis-concept-entity-types.md). 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
