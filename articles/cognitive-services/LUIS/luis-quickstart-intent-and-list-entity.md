---
title: Tutorial sobre como criar um aplicativo LUIS para obter dados listados de correspondência exata do texto - Azure | Microsoft Docs
description: Neste tutorial, saiba como criar um aplicativo LUIS simples usando intenções e entidades de lista para extrair dados.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: afad3fe725fddd0748cc206517a7274815cf1653
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495257"
---
# <a name="tutorial-4-add-list-entity"></a>Tutorial: 4. Adicionar listar de entidades
Neste tutorial, crie um aplicativo que demonstra como obter dados que correspondam a uma lista predefinida. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Entender as entidades de lista 
> * Criar novo aplicativo LUIS para o domínio de RH (recursos humanos) com a intenção MoveEmployee
> * Adicionar entidade de lista para extrair Funcionário do enunciado
> * Treinar e publicar o aplicativo
> * Consulte ponto de extremidade do aplicativo para ver a resposta JSON do LUIS

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Antes de começar
Caso não tenha o aplicativo de recursos humanos do tutorial da [entidade regex](luis-quickstart-intents-regex-entity.md), [importe](luis-how-to-start-new-app.md#import-new-app) o JSON em um aplicativo novo no site do [LUIS](luis-reference-regions.md#luis-website). O aplicativo a ser importado pode ser encontrado no repositório Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-regex-HumanResources.json).

Caso queira manter o aplicativo de recursos humanos original, clone a versão na página [Configurações](luis-how-to-manage-versions.md#clone-a-version) e nomeie-a como `list`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. 

## <a name="purpose-of-the-list-entity"></a>Finalidade da entidade de lista
Esse aplicativo prevê enunciados sobre como mover um funcionário de um prédio para um prédio diferente. Esse aplicativo usa uma entidade de lista para extrair um funcionário. O funcionário pode ser referenciado por meio de nome, número de telefone, email ou número do seguro social federal. 

Uma entidade de lista pode conter muitos itens com sinônimos para cada item. Para uma empresa de pequeno a médio porte, a entidade de lista é usada para extrair as informações de funcionário. 

O nome canônico de cada item é o número de funcionários. Para este domínio, exemplos de sinônimos são: 

|Finalidade do sinônimo|Valor do sinônimo|
|--|--|
|NOME|John W. Smith|
|Endereço de email|john.w.smith@mycompany.com|
|Ramal do telefone|x12345|
|Número de celular pessoal|425-555-1212|
|Número do seguro social|123-45-6789|

Uma entidade de lista é uma boa escolha para esse tipo de dados quando:

* Os valores de dados são um conjunto conhecido.
* O conjunto não excede os [limites](luis-boundaries.md) máximos do LUIS para esse tipo de entidade.
* O texto no enunciado corresponde exatamente a um sinônimo. 

O LUIS extrai o funcionário de forma que uma ordem padrão para mover o funcionário possa ser criada pelo aplicativo cliente.
<!--
## Example utterances
Simple example utterances for a `MoveEmployee` inent:

```
move John W. Smith from B-1234 to H-4452
mv john.w.smith@mycompany from office b-1234 to office h-4452

```
-->

## <a name="add-moveemployee-intent"></a>Adicionar intenção MoveEmployee

1. Verifique se o seu aplicativo de recursos humanos está na seção **Compilar** do LUIS. Você pode alterar essa seção selecionando **Compilar** na barra de menus da parte superior direita. 

2. Selecione **Criar nova intenção**. 

3. Insira `MoveEmployee` na caixa de diálogo pop-up, depois selecione **Concluído**. 

    ![Captura de tela do diálogo Criar nova intenção com](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Adicione enunciados de exemplo para a intenção.

    |Exemplo de enunciados|
    |--|
    |Mover John W. Smith de B-1234 para H-4452|
    |mv john.w.smith@mycompany.com da sala b-1234 para a sala h-4452|
    |desloca x12345 para h-1234 amanhã|
    |coloca 425-555-1212 em HH 2345|
    |move 123-45-6789 de A-4321 para J-23456|
    |mv Jill Jones de D-2345 para J-23456|
    |desloca jill-jones@mycompany.com para M-12345|
    |x23456 para M-12345|
    |425-555-0000 para h-4452|
    |234-56-7891 para hh-2345|

    [ ![Captura de tela da página Intenção com novos enunciados realçados](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png) ](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png#lightbox)

## <a name="create-an-employee-list-entity"></a>Criar uma entidade de lista de funcionários
Agora que a intenção **MoveEmployee** tem enunciados, LUIS precisa entender o que é um funcionário. 

1. Selecione **Entidades** no painel esquerdo.

2. Selecione **Criar nova entidade**.

3. No diálogo pop-up da entidade, insira `Employee` como o nome de entidade e **List** como o tipo de entidade. Selecione **Concluído**.  

    [![](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png "Captura de tela do diálogo pop-up de criação de nova entidade")](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png#lightbox)

4. Na página da entidade Funcionário, digite `Employee-24612` como o novo valor.

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "Captura de tela da inserção do valor")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

5. Adicione os seguintes valores como Sinônimos:

    |Finalidade do sinônimo|Valor do sinônimo|
    |--|--|
    |NOME|John W. Smith|
    |Endereço de email|john.w.smith@mycompany.com|
    |Ramal do telefone|x12345|
    |Número de celular pessoal|425-555-1212|
    |Número do seguro social|123-45-6789|

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "Captura de tela da inserção de sinônimos")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

6. Insira o `Employee-45612` como um novo valor.

7. Adicione os seguintes valores como Sinônimos:

    |Finalidade do sinônimo|Valor do sinônimo|
    |--|--|
    |NOME|Jill Jones|
    |Endereço de email|jill-jones@mycompany.com|
    |Ramal do telefone|x23456|
    |Número de celular pessoal|425-555-0000|
    |Número do seguro social|234-56-7891|

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicar o aplicativo para obter a URL do ponto de extremidade

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Consultar o ponto de extremidade com um enunciado diferente

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Vá até o final da URL no endereço e insira `shift 123-45-6789 from Z-1242 to T-54672`. O último parâmetro de querystring é `q`, o enunciado **q**uery. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, é um bom teste e deve retornar a intenção `MoveEmployee` com `Employee` extraído.

  ```JSON
  {
    "query": "shift 123-45-6789 from Z-1242 to T-54672",
    "topScoringIntent": {
      "intent": "MoveEmployee",
      "score": 0.9882801
    },
    "intents": [
      {
        "intent": "MoveEmployee",
        "score": 0.9882801
      },
      {
        "intent": "FindForm",
        "score": 0.016044287
      },
      {
        "intent": "GetJobInformation",
        "score": 0.007611245
      },
      {
        "intent": "ApplyForJob",
        "score": 0.007063288
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00684710965
      },
      {
        "intent": "None",
        "score": 0.00304174074
      },
      {
        "intent": "Utilities.Help",
        "score": 0.002981
      },
      {
        "intent": "Utilities.Confirm",
        "score": 0.00212222221
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.00191026414
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0007461446
      }
    ],
    "entities": [
      {
        "entity": "123 - 45 - 6789",
        "type": "Employee",
        "startIndex": 6,
        "endIndex": 16,
        "resolution": {
          "values": [
            "Employee-24612"
          ]
        }
      },
      {
        "entity": "123",
        "type": "builtin.number",
        "startIndex": 6,
        "endIndex": 8,
        "resolution": {
          "value": "123"
        }
      },
      {
        "entity": "45",
        "type": "builtin.number",
        "startIndex": 10,
        "endIndex": 11,
        "resolution": {
          "value": "45"
        }
      },
      {
        "entity": "6789",
        "type": "builtin.number",
        "startIndex": 13,
        "endIndex": 16,
        "resolution": {
          "value": "6789"
        }
      },
      {
        "entity": "-1242",
        "type": "builtin.number",
        "startIndex": 24,
        "endIndex": 28,
        "resolution": {
          "value": "-1242"
        }
      },
      {
        "entity": "-54672",
        "type": "builtin.number",
        "startIndex": 34,
        "endIndex": 39,
        "resolution": {
          "value": "-54672"
        }
      }
    ]
  }
  ```

  O funcionário foi encontrado e retornado como tipo `Employee` com um valor de resolução de `Employee-24612`.

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>Onde está o processamento de linguagem natural na entidade de lista? 
Como a entidade de lista é uma correspondência exata do texto, ela não depende de processamento de linguagem natural (ou aprendizado de máquina). O LUIS usa o processamento de linguagem natural (ou o aprendizado de máquina) para selecionar a intenção correta com maior pontuação. Além disso, um enunciado pode ser uma mistura de mais de uma entidade, ou até mesmo de mais de um tipo de entidade. Cada enunciado é processado para todas as entidades no aplicativo, incluindo entidades de processamento de linguagem natural (ou aprendizado de máquina).

## <a name="what-has-this-luis-app-accomplished"></a>O que esse aplicativo de LUIS realizou?
Esse aplicativo, com uma entidade de lista, extraiu o funcionário correto. 

Agora seu chatbot tem informações suficientes para determinar a ação primária, `MoveEmployee` e os funcionários que serão movidos. 

## <a name="where-is-this-luis-data-used"></a>Onde esses dados do LUIS são usados? 
O LUIS é feito com essa solicitação. O aplicativo de chamada, como um chatbot, pode levar o resultado de topScoringIntent e os dados da entidade para realizar a próxima etapa. O LUIS não realiza esse trabalho de programação para o bot ou para o aplicativo de chamada. O LUIS só determina qual é a intenção do usuário. 

## <a name="clean-up-resources"></a>Limpar recursos

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar uma entidade hierárquica ao aplicativo](luis-quickstart-intent-and-hier-entity.md)

