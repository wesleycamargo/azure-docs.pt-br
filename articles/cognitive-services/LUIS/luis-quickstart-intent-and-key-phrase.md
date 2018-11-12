---
title: 'Tutorial 8: Extração de frases-chave no LUIS'
titleSuffix: Azure Cognitive Services
description: Use a entidade pré-criada keyPhrase para extrair o assunto dos enunciados. Não é necessário rotular nenhum enunciado com as entidades pré-criadas. A entidade é detectada automaticamente.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: d6feb24b1e59aee70204d8438f1a4c51f71d1835
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281805"
---
# <a name="tutorial-8-extract-key-phrases-of-utterance"></a>Tutorial 8: Extrair frases-chave do enunciado
Neste tutorial, use a entidade keyPhrase pré-criada para extrair o assunto principal dos enunciados. Não é necessário rotular nenhum enunciado com as entidades pré-criadas. A entidade é detectada automaticamente.

Os enunciados a seguir mostram exemplos de frases-chave:

|Enunciado|Valores da entidade KeyPhrase|
|--|--|
|Há um novo plano de saúde com uma franquia menor oferecida para o próximo ano?|"franquia menor"<br>"novo plano de saúde"<br>"ano"|
|Terapia visual possui cobertura no plano de médicos de franquia alta?|"plano de saúde de franquia alta"<br>"terapia visual"|

O aplicativo cliente pode usar esses valores, junto com outras entidades extraídas, para decidir a próxima etapa na conversa.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Usar o aplicativo do tutorial existente
> * Adicionar entidade KeyPhrase 
> * Treinar
> * Publicar
> * Obter intenções e entidades do ponto de extremidade

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usar o aplicativo existente

Continue com o aplicativo criado no último tutorial, denominado **HumanResources**. 

Se você não tiver o aplicativo HumanResources do tutorial anterior, use as seguintes etapas:

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-simple-HumanResources.json).

2. Importe o JSON em um novo aplicativo.

3. Na seção **Gerenciar**, na guia **Versões**, clone a versão e nomeie-a como `keyphrase`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL.

## <a name="add-keyphrase-entity"></a>Adicionar entidade KeyPhrase 
Adicione a entidade predefinida keyPhrase para extrair o assunto dos enunciados.

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selecione **Entidades** no menu à esquerda.

3. Selecione **Adicionar entidade predefinida**.

4. Na caixa de diálogo pop-up, selecione **keyPhrase**, em seguida, selecione **Concluído**. 

    [ ![Caixa de diálogo pop-up de Lista de entidades](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

5. Selecione **Intenções** no menu à esquerda e selecione a intenção **Utilities.Confirm**. A entidade keyPhrase é rotulada em diversos enunciados. 

    [ ![Captura de tela da intenção Utilities.Confirm com keyPhrases rotuladas no enunciado](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train"></a>Treinar

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicar

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obter a intenção e as entidades do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá até o final da URL no endereço e insira `does form hrf-123456 cover the new dental benefits and medical plan`. O último parâmetro de querystring é `q`, o enunciado **consulta**. 
    
    ```JSON
    {
      "query": "does form hrf-123456 cover the new dental benefits and medical plan",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9300641
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9300641
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0359598845
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0141798034
        },
        {
          "intent": "MoveEmployee",
          "score": 0.0112197418
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00507669244
        },
        {
          "intent": "None",
          "score": 0.00238501839
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00202810857
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00102957746
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0008688423
        },
        {
          "intent": "Utilities.Confirm",
          "score": 3.557994E-05
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",git 
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "new dental benefits",
          "type": "builtin.keyPhrase",
          "startIndex": 31,
          "endIndex": 49
        },
        {
          "entity": "medical plan",
          "type": "builtin.keyPhrase",
          "startIndex": 55,
          "endIndex": 66
        },
        {
          "entity": "hrf",
          "type": "builtin.keyPhrase",
          "startIndex": 10,
          "endIndex": 12
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        }
      ]
    }
    ```

    Ao procurar um formulário, o usuário forneceu mais informações do que era necessário para localizar o formulário. As informações adicionais são retornadas como **builtin.keyPhrase**. O aplicativo cliente pode usar essas informações adicionais para uma pergunta de acompanhamento, como “Você deseja conversar com um representante dos Recursos Humanos sobre novos benefícios de plano odontológico” ou forneça um menu com mais opções, incluindo “Obter mais informações sobre novos benefícios de plano odontológico ou plano de saúde”.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Este tutorial adicionou a entidade keyPhrase pré-criada, fornecendo rapidamente frases-chave em enunciados sem a necessidade de rotular enunciados. 

> [!div class="nextstepaction"]
> [Adicionar a análise de sentimento ao aplicativo](luis-quickstart-intent-and-sentiment-analysis.md)