---
title: Entidades predefinidas em LUIS | Microsoft Docs
description: Este artigo contém listas de entidades predefinidas incluídas em LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/15/2018
ms.author: diberry
ms.openlocfilehash: 95ff173a843e7ad18ec396a1130b2e2b4035f656
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248230"
---
# <a name="prebuilt-entities"></a>Entidades predefinidas

O LUIS incluem um conjunto de entidades predefinidas para reconhecer tipos comuns de informações, como datas, horas, números, medidas e moeda. O suporte a entidade predefinida varia conforme a cultura do seu aplicativo de LUIS. Para obter uma lista completa de entidades predefinidas compatíveis com LUIS, incluindo suporte por cultura, veja a [referência de entidade predefinida](./luis-reference-prebuilt-entities.md).

> [!NOTE]
> **builtin.datetime** foi preterido. Ele foi substituído por [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), que fornece o reconhecimento de intervalos de data e hora, bem como reconhecimento aprimorado de datas e horas ambíguas.

## <a name="add-a-prebuilt-entity"></a>Adicionar uma entidade predefinida

1. Abra seu aplicativo clicando no nome na página **Meus Aplicativos** e, em seguida, clicando em **Entidades** no lado esquerdo. 
2. Na página **Entidades**, clique em **Gerenciar entidades predefinidas**.

    ![Página de Entidades – Gerenciar entidades predefinidas](./media/luis-use-prebuilt-entity/add-prebuilt-entity-button.png)
3. Na caixa de diálogo **Adicionar entidades predefinidas**, clique na entidade predefinida que você deseja adicionar (por exemplo, "datetimeV2"). Em seguida, clique em **Salvar**.

    ![Adicionar caixa de diálogo de entidade predefinida](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

## <a name="use-a-prebuilt-number-entity"></a>Usar uma entidade de número predefinida
Quando uma entidade predefinida é incluída em seu aplicativo, suas previsões são incluídas em seu aplicativo publicado. O comportamento das entidades predefinidas é previamente treinado e **não pode** ser modificado. Siga estas etapas para ver como uma entidade predefinida funciona:

1. Adicione uma entidade de **número** ao seu aplicativo e, em seguida, [Treine](luis-interactive-test.md) e [Publique](luis-how-to-publish-app.md) o aplicativo.
2. Clique na URL do ponto de extremidade na página **Publicar Aplicativo** para abrir o ponto de extremidade LUIS em um navegador da Web. 
3. Acrescente um enunciado à URL que contém uma expressão numérica. Por exemplo, você pode digitar em `buy two plane ticktets` e ver se o LUIS identifica `two` como uma entidade `builtin.number` e identifica `2` como seu valor no campo `resolution`. O campo `resolution` ajuda você a resolver os números e as datas para uma forma canônica mais fácil para seu aplicativo cliente usar. 

    ![enunciado no navegador contendo uma entidade de número](./media/luis-use-prebuilt-entity/browser-query.png)

O LUIS pode reconhecer de maneira inteligente números que não estão na forma não padrão. Experimentar diferentes expressões numéricas em seus enunciados e veja o que o LUIS retorna.

O exemplo a seguir mostra uma resposta JSON de LUIS, que inclui a resolução do valor de 24, para o enunciado "duas dúzias".

```json
{
  "query": "order two dozen tickets for group travel",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.905443209
  },
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "value": "24"
      }
    }
  ]
}
```
## <a name="use-a-prebuilt-datetimev2-entity"></a>Use uma entidade datetimeV2 predefinida
A entidade predefinida **datetimeV2** reconhece datas, horas, intervalos de datas e durações de tempo. Siga estas etapas para ver como a entidade predefinida `datetimeV2` funciona:

1. Adicione uma entidade **datetimeV2** ao seu aplicativo e, em seguida, [Treine](luis-interactive-test.md) e [publique](luis-how-to-publish-app.md) o aplicativo.
2. Clique na URL do ponto de extremidade na página **Publicar Aplicativo** para abrir o ponto de extremidade LUIS em um navegador da Web. 
3. Acrescente um enunciado à URL que contém um intervalo de datas. Por exemplo, você pode digitar em `book a flight tomorrow` e ver se o LUIS identifica `tomorrow` como uma entidade `builtin.datetimeV2.date` e identifica a data de amanhã como seu valor no campo `resolution`. 

O exemplo a seguir mostra como poderia ser a resposta JSON do LUIS se a data de hoje fosse de 31 de outubro de 2017.

```json
{
  "query": "book a flight tomorrow",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9063408
  },
  "entities": [
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 14,
      "endIndex": 21,
      "resolution": {
        "values": [
          {
            "timex": "2017-11-01",
            "type": "date",
            "value": "2017-11-01"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Referência de entidade predefinida](./luis-reference-prebuilt-entities.md)
