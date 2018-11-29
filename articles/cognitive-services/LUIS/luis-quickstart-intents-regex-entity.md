---
title: 'Tutorial 3: dados correspondidos de expressão regular – extrair dados bem formatados'
titleSuffix: Azure Cognitive Services
description: Extrair dados formatados consistentemente de um enunciado usando a entidade de Expressão Regular.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 5e8345deb629d293d9673819893181e652d5dbb9
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52423408"
---
# <a name="tutorial-3-extract-well-formatted-data"></a>Tutorial 3: extrair dados bem formatados
Neste tutorial, modifique o aplicativo de recursos humanos para extrair dados formatados consistentemente de um enunciado usando a entidade de **Expressão Regular**.

A finalidade de uma entidade é extrair dados importantes contidos no enunciado. O uso da entidade de expressão regular pelo aplicativo é para obter números formatados de formulário de RH (recursos humanos) de um enunciado. Embora a intenção do enunciado sempre seja determinada com aprendizado de máquina, esse tipo de entidade específico não é de aprendizado de máquina. 

**Enunciados de exemplo incluem:**

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
Uma expressão regular é uma boa escolha para esse tipo de dados quando:

* os dados são bem formatados.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Usar o aplicativo do tutorial existente
> * Adicionar intenção FindForm
> * Adicionar entidade de expressão regular 
> * Treinar
> * Publicar
> * Obter intenções e entidades do ponto de extremidade

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usar o aplicativo existente
Continue com o aplicativo criado no último tutorial, denominado **HumanResources**. 

Se você não tiver o aplicativo HumanResources do tutorial anterior, use as seguintes etapas:

1. Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-prebuilts-HumanResources.json).

2. Importe o JSON em um novo aplicativo.

3. Na seção **Gerenciar**, na guia **Versões**, clone a versão e nomeie-a como `regex`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL. 

## <a name="findform-intent"></a>Intenção FindForm

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selecione **Criar nova intenção**. 

3. Insira `FindForm` na caixa de diálogo pop-up, depois selecione **Concluído**. 

    ![Captura de tela da caixa de diálogo Criar nova tentativa com utilitários na caixa de pesquisa](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

4. Adicione enunciados de exemplo para a intenção.

    |Exemplo de enunciados|
    |--|
    |Qual é a URL para hrf-123456?|
    |Onde está a hrf-345678?|
    |Quando hrf-456098 foi atualizada?|
    |John Smith atualizou a hrf-234639 na semana passada?|
    |Quantas versões da hrf-345123 existem?|
    |Quem deve autorizar o formulário hrf-123456?|
    |Quantas pessoas precisam se desconectar da hrf-345678?|
    |Data da hrf-234123?|
    |Autor da hrf-546234?|
    |Título da hrf-456234?|

    [ ![Captura de tela da página Intenção com novos enunciados realçados](./media/luis-quickstart-intents-regex-entity/findform-intent.png) ](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    O aplicativo tem o número da entidade predefinida adicionado do tutorial anterior, portanto, cada número de formulário está marcado. Isso pode ser suficiente para seu aplicativo cliente, mas o número não será rotulado com o tipo de número. Criar uma nova entidade com um nome apropriado permite que o aplicativo cliente processe a entidade adequadamente ao ser retornado do LUIS.

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="regular-expression-entity"></a>Entidade de expressão regular 
A entidade de expressão regular para corresponder ao número de formulário é `hrf-[0-9]{6}`. Essa expressão regular corresponde aos caracteres literais `hrf-`, mas ignora variantes de caixa e cultura. Ela corresponde aos dígitos 0-9 de exatamente 6 dígitos.

Representa o HRF para `human resources form`.

O LUIS cria tokens do enunciado quando é adicionada a uma intenção. A geração de tokens desses enunciados adiciona espaços antes e depois do hífen `Where is HRF - 123456?` A expressão regular é aplicada ao enunciado em formato bruto antes que sejam criados tokens para ele. Por ser aplicada ao formulário _bruto_, a expressão regular não precisa lidar com limites de palavras. 

Crie uma entidade de expressão regular para informar ao LUIS o que é um formato HRF-número nas etapas a seguir:

1. Selecione **Entidades** no painel esquerdo.

2. Selecione o botão **Criar nova entidade** na página Entidades. 

3. Na caixa de diálogo pop-up, insira o novo nome de entidade `HRF-number`, selecione **RegEx** como o tipo de entidade, insira `hrf-[0-9]{6}` como o **Regex** e, depois, selecione **Concluído**.

    ![Captura de tela com caixa de diálogo pop-up configurando as propriedades da entidade](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. Selecione **Intenções** no menu à esquerda, em seguida, a intenção **FindForm** para ver a expressão regular rotulada nos enunciados. 

    [![Captura de tela do enunciado Rótulo com um padrão existente de entidade e regex](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Como a entidade não é uma entidade de aprendizado de máquina, o rótulo é aplicado aos enunciados e exibido no site LUIS assim que ela for criada.

## <a name="train"></a>Treinar

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicar

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obter a intenção e as entidades do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá até o final da URL no endereço e insira `When were HRF-123456 and hrf-234567 published in the last year?`. O último parâmetro de querystring é `q`, o enunciado **consulta**. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, ele é um bom teste e deve retornar a intenção `FindForm` com os números de formulário de `HRF-123456` e `hrf-234567`.

    ```JSON
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9993477
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9993477
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0206110049
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00533067342
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.004215215
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00209096959
        },
        {
          "intent": "None",
          "score": 0.0017655947
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00109490135
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.0005704638
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.000525338168
        }
      ],
      "entities": [
        {
          "entity": "last year",
          "type": "builtin.datetimeV2.daterange",
          "startIndex": 53,
          "endIndex": 61,
          "resolution": {
            "values": [
              {
                "timex": "2017",
                "type": "daterange",
                "start": "2017-01-01",
                "end": "2018-01-01"
              }
            ]
          }
        },
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        },
        {
          "entity": "-234567",
          "type": "builtin.number",
          "startIndex": 28,
          "endIndex": 34,
          "resolution": {
            "value": "-234567"
          }
        }
      ]
    }
    ```

    Os números no enunciado são retornados duas vezes: uma vez como a nova entidade `hrf-number` e uma vez como uma entidade predefinida `number`. Um enunciado pode ter mais de uma entidade e mais de uma entidade do mesmo tipo, como mostra o exemplo. Usando uma entidade de expressão regular, o LUIS extrai dados nomeados, o que é mais programaticamente útil para o aplicativo cliente recebendo a resposta JSON.


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas
Este tutorial criou uma nova intenção, adicionou exemplos de enunciados e criou uma entidade de expressão regular para extrair dados bem formatados de enunciados. Após o treinamento e a publicação do aplicativo, uma consulta para o ponto de extremidade identificou a intenção e retornou os dados extraídos.

> [!div class="nextstepaction"]
> [Saiba mais sobre a entidade de lista](luis-quickstart-intent-and-list-entity.md)

