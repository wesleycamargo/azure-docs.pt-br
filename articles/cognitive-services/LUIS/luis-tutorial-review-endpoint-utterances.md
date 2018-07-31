---
title: Tutorial para examinar enunciados de ponto de extremidade no Serviço Inteligente de Reconhecimento Vocal (LUIS) - Azure | Microsoft Docs
description: Neste tutorial, aprenda a analisar os enunciados de ponto de extremidade do domínio de recursos humanos (RH) no LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 07/03/2018
ms.author: diberry
ms.openlocfilehash: 1f1e3310e0d02983aaecc3f87ba9c116d65b751b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237206"
---
# <a name="tutorial-review-endpoint-utterances"></a>Tutorial: Examinar enunciados de ponto de extremidade
Neste tutorial, melhore previsões de aplicativo verificando ou corrigindo os enunciados recebidos pelo ponto de extremidade HTTP do LUIS. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Entender como examinar os enunciados de ponto de extremidade 
> * Usar o aplicativo LUIS para o domínio dos recursos humanos (RH) 
> * Examinar declarações de ponto de extremidade
> * Treinar e publicar o aplicativo
> * Consulte ponto de extremidade do aplicativo para ver a resposta JSON do LUIS

Para este artigo, você precisa de uma conta gratuita do [LUIS](luis-reference-regions.md#luis-website) para criar seu aplicativo LUIS.

## <a name="before-you-begin"></a>Antes de começar
Se você não tiver o aplicativo de recursos humanos do tutorial sobre [sentimento](luis-quickstart-intent-and-sentiment-analysis.md), importe o aplicativo do repositório do Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-sentiment-HumanResources.json). Se você usar este tutorial como um aplicativo novo e importado, você também precisa treinar, publicar e, em seguida, adicionar os enunciados para o ponto de extremidade com um [script](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js) ou a partir do ponto de extremidade em um navegador. Os enunciados a serem adicionados são:

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

Caso queira manter o aplicativo de recursos humanos original, clone a versão na página [Configurações](luis-how-to-manage-versions.md#clone-a-version) e nomeie-a como `review`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. 

Se você tiver todas as versões do aplicativo, por meio da série de tutoriais, você poderá se surpreender ao ver que a lista **Examinar enunciados de ponto de extremidade** não muda com base na versão. Há um único conjunto de enunciados para revisar, independentemente de qual versão a expressão está ativamente editando ou qual versão do aplicativo foi publicado no ponto de extremidade. 

## <a name="purpose-of-reviewing-endpoint-utterances"></a>Objetivo de examinar os enunciados de ponto de extremidade
Esse processo de revisão é outra maneira do LUIS aprender sobre o domínio do seu aplicativo. LUIS selecionou os enunciados na lista de revisão. Esta lista é:

* Específica para o aplicativo.
* Destina-se a melhorar a precisão da previsão do aplicativo. 
* Deve ser revisada regularmente. 

Ao revisar os enunciados de ponto de extremidade, você verifica ou corrige a intenção prevista do enunciado. Você também pode rotular entidades personalizadas que não foram previstas. 

## <a name="review-endpoint-utterances"></a>Examinar declarações de ponto de extremidade

1. Verifique se o seu aplicativo de recursos humanos está na seção **Compilar** do LUIS. Você pode alterar essa seção selecionando **Compilar** na barra de menus da parte superior direita. 

    [ ![Captura de tela do aplicativo LUIS com Compilar realçado na barra de navegação superior direita](./media/luis-tutorial-review-endpoint-utterances/first-image.png)](./media/luis-tutorial-review-endpoint-utterances/first-image.png#lightbox)

1. Selecione **Examinar enunciados de ponto de extremidade** na barra de navegação à esquerda. A lista é filtrada para a intenção **ApplyForJob**. 

    [ ![Captura de tela do botão Examinar enunciados de ponto de extremidade na barra de navegação à esquerda](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png)](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png#lightbox)

2. Alterne o **modo de exibição de Entidades** para ver as entidades rotuladas. 
    
    [ ![Captura de tela de Examinar enunciados de ponto de extremidade com o modo de exibição de Entidades destacado](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png)](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png#lightbox)

    |Enunciado|Intenção correta|Entidades ausentes|
    |:--|:--|:--|
    |Estou procurando por um trabalho de processamento de linguagem natural|GetJobInfo|Trabalho - “Processamento de linguagem natural”|

    Esse enunciado não está na intenção correta e tem uma pontuação inferior a 50%. A intenção **ApplyForJob** tem 21 declarações em comparação com os sete enunciados em **GetJobInformation**. Além de alinhar corretamente o enunciado de ponto de extremidade, mais enunciados devem ser adicionados à intenção **GetJobInformation**. Isso será deixado como um exercício para você concluir por conta própria. Cada intenção, exceto a intenção **None**, deve ter aproximadamente o mesmo número de enunciados de exemplo. A intenção **None** deve conter 10% do total de enunciados no aplicativo. 

    Quando você estiver no **Modo de exibição de Tokens**, você pode passar o mouse sobre qualquer texto azul do enunciado para ver o nome da entidade prevista. 

3. Para a intenção `I'm looking for a job with Natual Language Processing`, selecione a intenção correta **GetJobInformation** na coluna **Alinhar intenção**. 

    [ ![Captura de tela de Examinar enunciados de ponto de extremidade alinhando o enunciado com a intenção](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

4. No mesmo enunciado, a entidade para `Natural Language Processing` é a keyPhrase. Essa deve ser uma entidade **Trabalho**. Selecione `Natural Language Processing`, em seguida, selecione a entidade **Trabalho** na lista.

    [ ![Captura de tela de Examinar enunciados de ponto de extremidade rotulando a entidade no enunciado](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

5. Na mesma linha, selecione a marca de seleção dentro de um círculo na coluna **Adicionar à intenção alinhada**. 

    [ ![Captura de tela de Finalizando o alinhamento do enunciado na intenção](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    Essa ação move o enunciado de **Examinar os enunciados de ponto de extremidade** para a intenção **GetJobInformation**. O enunciado de ponto de extremidade agora é um enunciado de exemplo para essa intenção. 

6. Examine o restante dos enunciados nessa intenção, rotulando os enunciados e corrigindo a **Intenção alinhada**, se estas estiverem incorretas.

7. Quando todos os enunciados estiverem corretos, marque a caixa de seleção em cada linha, em seguida **Adicionar selecionada** para alinhar os enunciados corretamente. 

    [ ![Captura de tela de Finalizando os enunciados restantes para a intenção alinhada](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

8. A lista não deve ter mais esses enunciados. Se aparecerem mais enunciados, continue a trabalhar na lista, corrigindo as intenções e rotulando quaisquer entidades ausentes, até que ela fique vazia. Selecione a intenção seguinte na lista de Filtros e continue a corrigir os enunciados e a rotular as entidades. Lembre-se que a última etapa de cada intenção é selecionar **Adicionar à intenção alinhada** na linha do enunciado ou marcar a caixa para cada enunciado e selecionar **Adicionar selecionada** acima da tabela. 

    Esse é um aplicativo muito pequeno. O processo de revisão leva apenas alguns minutos.

## <a name="add-new-job-name-to-phrase-list"></a>Adicionar um novo nome de trabalho à lista de frases
Mantenha a lista de frases atualizada com os nomes de quaisquer nomes de trabalho descobertos recentemente. 

1. Selecione as **listas de Frases** no painel de navegação esquerdo.

2. Selecione a lista de frases de **Trabalhos**.

3. Adicione `Natural Language Processing` como um valor e, em seguida, selecione **Salvar**. 

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS
LUIS não sabe sobre as alterações até que seja treinado. 

1. No canto superior direito do site do LUIS, selecione o botão **Train** (Treinar).

2. O treinamento é concluído quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicar o aplicativo para obter a URL do ponto de extremidade
Para obter o modelo atualizado do aplicativo LUIS em um chatbot ou outro aplicativo, você precisa publicar o aplicativo. 

1. No canto superior direito do site do LUIS, selecione o botão **Publish** (Publicar). 

2. Se você importou esse aplicativo, você precisa selecionar **Análise de sentimento**. 

3. Selecione o Slot de produção e o botão **Publicar**.

4. A publicação é concluída quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

## <a name="query-the-endpoint-with-an-utterance"></a>Consultar o ponto de extremidade com um enunciado
Tente um enunciado próximo ao enunciado corrigido. 

1. Na página **Publicar**, selecione o link do **ponto de extremidade** na parte inferior da página. Essa ação abre outra janela do navegador com a URL de ponto de extremidade na barra de endereços. 

2. Vá até o final da URL no endereço e insira `Are there any natural language processing jobs in my department right now?`. O último parâmetro de querystring é `q`, o enunciado **consulta**. 

```JSON
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
Alguns enunciados de ponto de extremidade terão uma porcentagem alta na lista de revisão. Ainda assim é necessário examinar esses enunciados. Eles estão na lista porque a intenção com a segunda pontuação mais alta tinha uma pontuação muito próxima da intenção com a maior pontuação. 

## <a name="what-has-this-tutorial-accomplished"></a>O que esse tutorial conseguiu fazer?
A precisão da previsão do aplicativo aumentou ao revisar os enunciados do ponto de extremidade. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua o aplicativo LUIS. Selecione **Meus aplicativos** no menu superior esquerdo. Selecione as reticências **...** à direita do nome do aplicativo na lista de aplicativos e selecione **Excluir**. Na caixa de diálogo pop-up **Excluir aplicativo?**, selecione **OK**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como usar padrões](luis-tutorial-pattern.md)
