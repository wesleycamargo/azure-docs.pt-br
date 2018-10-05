---
title: 'Tutorial 9: Análise de sentimento incluindo positivo, negativo e neutro no LUIS'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, crie um aplicativo que demonstra como extrair sentimentos positivos, negativos e neutros dos enunciados. O sentimento é determinado pelo enunciado inteiro.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: ff5a47f977f34535c5ad1fde7e6cac5995e7f7dd
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031451"
---
# <a name="tutorial-9--extract-sentiment-of-overall-utterance"></a>Tutorial 9: Extrair o sentimento do enunciado geral
Neste tutorial, crie um aplicativo que demonstra como extrair sentimentos positivos, negativos e neutros dos enunciados. O sentimento é determinado pelo enunciado inteiro.

A análise de sentimento é a capacidade de determinar se o enunciado de um usuário é positivo, negativo ou neutro. 

Os enunciados a seguir mostram exemplos de sentimentos:

|Sentimento|Pontuação|Enunciado|
|:--|:--|:--|
|positivo|0.91 |John W. Smith fez um ótimo trabalho na apresentação em Paris.|
|positivo|0.84 |jill-jones@mycompany.com fez um fabuloso trabalho na apresentação de vendas de Parker.|

A análise de sentimento é uma configuração de publicação que se aplica a cada enunciado. Não é necessário localizar as palavras que indicam o sentimento no enunciado e rotulá-las, já que a análise de sentimento é aplicada no enunciado como um todo. 

Como se trata de uma configuração de publicação, ela não aparece nas páginas de intenções ou de entidades. Ela aparece no painel [teste interativo](luis-interactive-test.md#view-sentiment-results) ou ao testar a URL de ponto de extremidade. 

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Usar o aplicativo do tutorial existente 
> * Adicionar a análise de sentimento como uma configuração de publicação
> * Treinar
> * Publicar
> * Obter o sentimento do enunciado do ponto de extremidade

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usar o aplicativo existente

Continue com o aplicativo criado no último tutorial, denominado **HumanResources**. 

Se não tiver o aplicativo HumanResources do tutorial anterior, siga estas etapas:

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-keyphrase-HumanResources.json).

2. Importe o JSON em um novo aplicativo.

3. Na seção **Gerenciar**, na guia **Versões**, clone a versão e nomeie-a como `sentiment`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL.

## <a name="employeefeedback-intent"></a>Intenção de EmployeeFeedback 
Adicione uma nova intenção para capturar comentários do funcionário entre os membros da empresa. 

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selecione **Criar nova intenção**.

3. Insira o nome da nova intenção `EmployeeFeedback`.

    ![Criar nova caixa de diálogo de intenção com EmployeeFeedback como nome](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Adicione vários enunciados que indicam que um funcionário está fazendo algo bem ou uma área que precise de melhorias:

    Lembre-se que neste aplicativo de recursos humanos, os funcionários são definidos na entidade de lista, `Employee`, pelo nome, email, ramal telefônico, número de telefone celular e seus números do seguro social dos EUA. 

    |Declarações|
    |--|
    |425-555-1212 fez um ótimo trabalho de boas-vindas para uma colega de trabalho que estava de licença-maternidade|
    |234-56-7891 fez um ótimo trabalho consolando um colega de trabalho que estava de luto.|
    |jill-jones@mycompany.com não tem todas as faturas necessárias para a documentação.|
    |john.w.smith@mycompany.com entregou os formulários necessários um mês atrasado sem nenhuma assinatura|
    |x23456 não compareceu à reunião importante de marketing externa.|
    |x12345 faltou a reunião para as análises do mês de junho.|
    |Jill Jones deu um show na apresentação de vendas em Harvard|
    |John W. Smith fez um ótimo trabalho na apresentação em Stanford|

    [ ![Captura de tela do aplicativo LUIS com exemplos de enunciados na intenção EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="train"></a>Treinar

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurar aplicativo para incluir a análise de sentimento
1. Selecione **Gerenciar** na navegação superior à direita, em seguida, selecione **Configurações de publicação** no menu à esquerda.

2. Habilite a **Análise de Sentimento** para habilitar essa configuração. 

    ![](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish"></a>Publicar

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-sentiment-of-utterance-from-endpoint"></a>Obter o sentimento do enunciado do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá até o final da URL no endereço e insira `Jill Jones work with the media team on the public portal was amazing`. O último parâmetro de querystring é `q`, o enunciado **consulta**. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, ele é um bom teste e deve retornar a intenção `EmployeeFeedback` com a extração da análise de sentimento.
    
    ```JSON
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.4983256
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.4983256
        },
        {
          "intent": "MoveEmployee",
          "score": 0.06617523
        },
        {
          "intent": "GetJobInformation",
          "score": 0.04631853
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0103248553
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.007531875
        },
        {
          "intent": "FindForm",
          "score": 0.00344597152
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00337914471
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0026357458
        },
        {
          "intent": "None",
          "score": 0.00214573368
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00157622492
        },
        {
          "intent": "Utilities.Confirm",
          "score": 7.379545E-05
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 0,
          "endIndex": 9,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "media team",
          "type": "builtin.keyPhrase",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "public portal",
          "type": "builtin.keyPhrase",
          "startIndex": 43,
          "endIndex": 55
        },
        {
          "entity": "jill jones",
          "type": "builtin.keyPhrase",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    O sentimentAnalysis é positivo com uma pontuação de 0.86. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas
Este tutorial adiciona a análise de sentimento como uma configuração de publicação para extrair valores de sentimento do enunciado como um todo.

> [!div class="nextstepaction"] 
> [Examinar enunciados de ponto de extremidade no aplicativo de RH](luis-tutorial-review-endpoint-utterances.md) 

