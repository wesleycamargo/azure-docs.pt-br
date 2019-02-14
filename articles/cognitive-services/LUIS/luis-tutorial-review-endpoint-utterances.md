---
title: Examinando declarações de ponto de extremidade
titleSuffix: Azure Cognitive Services
description: Melhore as previsões de aplicativo verificando ou corrigindo os enunciados recebidos pelo ponto de extremidade HTTP do LUIS sobre os quais o LUIS não tem certeza. Alguns enunciados podem ser verificados quanto à intenção e outros quanto à entidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 88ac9f07fb8791ca3d64123663b0380a56220cdd
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55865940"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Tutorial: Corrigir previsões incertas examinando os enunciados de ponto de extremidade
Neste tutorial, melhore as previsões de aplicativo verificando ou corrigindo os enunciados recebidos pelo ponto de extremidade HTTP do LUIS sobre os quais o LUIS não tem certeza. Alguns enunciados podem precisar ser verificados quanto à intenção e outros quanto à entidade. Você deve analisar os enunciados de ponto de extremidade como uma parte regular da sua manutenção agendada do LUIS. 

Esse processo de revisão é outra maneira do LUIS aprender sobre o domínio do seu aplicativo. O LUIS selecionou os enunciados que aparecem na lista de análise. Esta lista é:

* Específica para o aplicativo.
* Destina-se a melhorar a precisão da previsão do aplicativo. 
* Deve ser revisada regularmente. 

Ao revisar os enunciados de ponto de extremidade, você verifica ou corrige a intenção prevista do enunciado. Você também pode rotular entidades personalizadas que não foram previstas ou foram previstas incorretamente. 

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importar o aplicativo de exemplo
> * Examinar declarações de ponto de extremidade
> * Atualizar lista de frases
> * Treinar o aplicativo
> * Publicar aplicativo
> * Consulte ponto de extremidade do aplicativo para ver a resposta JSON do LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importar o aplicativo de exemplo

Continue com o aplicativo criado no último tutorial, denominado **HumanResources**. 

Use as seguintes etapas:

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json).

2. Importe o JSON em um novo aplicativo.

3. Na seção **Gerenciar**, na guia **Versões**, clone a versão e nomeie-a como `review`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL.

    Se você usar este tutorial como um aplicativo novo e importado, você também precisa treinar, publicar e, em seguida, adicionar os enunciados para o ponto de extremidade com um [script](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js) ou a partir do ponto de extremidade em um navegador. Os enunciados a serem adicionados são:

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

    Se você tiver todas as versões do aplicativo, por meio da série de tutoriais, você poderá se surpreender ao ver que a lista **Examinar enunciados de ponto de extremidade** não muda com base na versão. Há um único pool de enunciados para analisar, independentemente de qual versão está ativamente editando ou qual versão do aplicativo foi publicada no ponto de extremidade. 

## <a name="review-endpoint-utterances"></a>Examinar declarações de ponto de extremidade

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selecione **Examinar enunciados de ponto de extremidade** na barra de navegação à esquerda. A lista é filtrada para a intenção **ApplyForJob**. 

    [ ![Captura de tela do botão Examinar enunciados de ponto de extremidade na barra de navegação à esquerda](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png#lightbox)

3. Alterne o **modo de exibição de Entidades** para ver as entidades rotuladas. 
    
    [ ![Captura de tela de Examinar enunciados de ponto de extremidade com o modo de exibição de Entidades destacado](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png#lightbox)

    |Enunciado|Intenção correta|Entidades ausentes|
    |:--|:--|:--|
    |Estou procurando por um trabalho de processamento de linguagem natural|GetJobInfo|Trabalho - “Processamento de linguagem natural”|

    Esse enunciado não está na intenção correta e tem uma pontuação inferior a 50%. A intenção **ApplyForJob** tem 21 declarações em comparação com os sete enunciados em **GetJobInformation**. Além de alinhar corretamente o enunciado de ponto de extremidade, mais enunciados devem ser adicionados à intenção **GetJobInformation**. Isso será deixado como um exercício para você concluir por conta própria. Cada intenção, exceto a intenção **None**, deve ter aproximadamente o mesmo número de enunciados de exemplo. A intenção **None** deve conter 10% do total de enunciados no aplicativo. 

4. Para a intenção `I'm looking for a job with Natual Language Processing`, selecione a intenção correta **GetJobInformation** na coluna **Alinhar intenção**. 

    [ ![Captura de tela de Examinar enunciados de ponto de extremidade alinhando o enunciado com a intenção](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

5. No mesmo enunciado, a entidade para `Natural Language Processing` é a keyPhrase. Essa deve ser uma entidade **Trabalho**. Selecione `Natural Language Processing`, em seguida, selecione a entidade **Trabalho** na lista.

    [ ![Captura de tela de Examinar enunciados de ponto de extremidade rotulando a entidade no enunciado](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

6. Na mesma linha, selecione a marca de seleção dentro de um círculo na coluna **Adicionar à intenção alinhada**. 

    [ ![Captura de tela de Finalizando o alinhamento do enunciado na intenção](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    Essa ação move o enunciado de **Examinar os enunciados de ponto de extremidade** para a intenção **GetJobInformation**. O enunciado de ponto de extremidade agora é um enunciado de exemplo para essa intenção. 

7. Examine o restante dos enunciados nessa intenção, rotulando os enunciados e corrigindo a **Intenção alinhada**, se estas estiverem incorretas.

8. Quando todos os enunciados estiverem corretos, marque a caixa de seleção em cada linha, em seguida **Adicionar selecionada** para alinhar os enunciados corretamente. 

    [ ![Captura de tela de Finalizando os enunciados restantes para a intenção alinhada](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

9. A lista não deve ter mais esses enunciados. Se aparecerem mais enunciados, continue a trabalhar na lista, corrigindo as intenções e rotulando quaisquer entidades ausentes, até que ela fique vazia. 

10. Selecione a intenção seguinte na lista de Filtros e continue a corrigir os enunciados e a rotular as entidades. Lembre-se que a última etapa de cada intenção é selecionar **Adicionar à intenção alinhada** na linha do enunciado ou marcar a caixa para cada enunciado e selecionar **Adicionar selecionada** acima da tabela.

    Continue até que todas as intenções e entidades na lista de filtro tenham uma lista vazia. Esse é um aplicativo muito pequeno. O processo de revisão leva apenas alguns minutos. 

## <a name="update-phrase-list"></a>Atualizar lista de frases
Mantenha a lista de frases atualizada com os nomes de quaisquer nomes de trabalho descobertos recentemente. 

1. Selecione as **listas de Frases** no painel de navegação esquerdo.

2. Selecione a lista de frases de **Trabalhos**.

3. Adicione `Natural Language Processing` como um valor e, em seguida, selecione **Salvar**. 

## <a name="train"></a>Treinar

LUIS não sabe sobre as alterações até que seja treinado. 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicar

Se você importou esse aplicativo, você precisa selecionar **Análise de sentimento**.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obter a intenção e as entidades do ponto de extremidade

Tente um enunciado próximo ao enunciado corrigido. 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá até o final da URL no endereço e insira `Are there any natural language processing jobs in my department right now?`. O último parâmetro de querystring é `q`, o enunciado **consulta**. 

  ```json
  {
    "query": "are there any natural language processing jobs in my department right now?",
    "topScoringIntent": {
      "intent": "GetJobInformation",
      "score": 0.9247605
    },
    "intents": [
      {
        "intent": "GetJobInformation",
        "score": 0.9247605
      },
      {
        "intent": "ApplyForJob",
        "score": 0.129989788
      },
      {
        "intent": "FindForm",
        "score": 0.006438211
      },
      {
        "intent": "EmployeeFeedback",
        "score": 0.00408575451
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00194211153
      },
      {
        "intent": "None",
        "score": 0.00166400627
      },
      {
        "intent": "Utilities.Help",
        "score": 0.00118593348
      },
      {
        "intent": "MoveEmployee",
        "score": 0.0007885918
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.0006373631
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0005980781
      },
      {
        "intent": "Utilities.Confirm",
        "score": 3.719905E-05
      }
    ],
    "entities": [
      {
        "entity": "right now",
        "type": "builtin.datetimeV2.datetime",
        "startIndex": 64,
        "endIndex": 72,
        "resolution": {
          "values": [
            {
              "timex": "PRESENT_REF",
              "type": "datetime",
              "value": "2018-07-05 15:23:18"
            }
          ]
        }
      },
      {
        "entity": "natural language processing",
        "type": "Job",
        "startIndex": 14,
        "endIndex": 40,
        "score": 0.9869922
      },
      {
        "entity": "natural language processing jobs",
        "type": "builtin.keyPhrase",
        "startIndex": 14,
        "endIndex": 45
      },
      {
        "entity": "department",
        "type": "builtin.keyPhrase",
        "startIndex": 53,
        "endIndex": 62
      }
    ],
    "sentimentAnalysis": {
      "label": "positive",
      "score": 0.8251864
    }
  }
  }
  ```

  A intenção correta foi prevista com uma pontuação alta e a entidade **Trabalho** foi detectada como `natural language processing`. 

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>A revisão pode ser substituída pela adição de mais enunciados? 
Você deve estar imaginando por que não adicionar mais exemplos de enunciado. Qual é o objetivo de examinar os enunciados de ponto de extremidade? Em um aplicativo do mundo real LUIS, os enunciados de ponto de extremidade são de usuários com um arranjo e escolha de palavras que você ainda não usou. Se você tivesse usado o mesmo arranjo e escolha de palavras, a previsão original teria tido um percentual mais alto. 

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Por que a intenção mais bem pontuada está na lista de enunciados? 
Alguns enunciados de ponto de extremidade terão uma pontuação de previsão alta na lista de análise. Ainda assim é necessário examinar esses enunciados. Eles estão na lista porque a intenção com a segunda pontuação mais alta tinha uma pontuação muito próxima da intenção com a maior pontuação. Você deseja cerca de 15% de diferença entre as duas primeiras intenções.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você analisou enunciados enviados no ponto de extremidade sobre os quais o LUIS não tinha certeza. Depois que esses enunciados forem verificados e movidos para as intenções corretas como enunciados de exemplo, o LUIS melhorará a precisão da previsão.

> [!div class="nextstepaction"]
> [Saiba como usar padrões](luis-tutorial-pattern.md)
