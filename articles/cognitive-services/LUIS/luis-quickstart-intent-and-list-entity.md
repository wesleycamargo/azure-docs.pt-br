---
title: 'Tutorial 4: Correspondência de texto exata – Entidade de lista do LUIS'
titleSuffix: Azure Cognitive Services
description: Obtenha dados que correspondam a uma lista predefinida de itens. Cada item na lista pode ter sinônimos que também tenham correspondência exata
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 2ce6e7c796faf0c7377a33dabe1e8c05e81fde2f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51280684"
---
# <a name="tutorial-4-extract-exact-text-matches"></a>Tutorial 4: Extrair correspondências de texto exatas
Neste tutorial, entenda como obter dados que correspondam a uma lista predefinida de itens. Cada item na lista pode incluir uma lista de sinônimos. Para o aplicativo de recursos humanos, um funcionário pode ser identificado com várias partes importantes de informações como nome, email, número de telefone e ID de imposto federal dos EUA. 

O aplicativo de Recursos Humanos precisa determinar qual funcionário está sendo transferido de um edifício para um edifício diferente. Para obter um enunciado sobre uma transferência de funcionário, o LUIS determina a intenção e extrai o funcionário para que uma ordem padrão de transferir o funcionário possa ser criada pelo aplicativo cliente.

Esse aplicativo usa uma entidade de lista para extrair o funcionário. O funcionário pode ser referenciado por meio do nome, ramal do telefone da empresa, número do telefone celular, email ou número do seguro social federal dos EUA. 

Uma entidade de lista é uma boa escolha para esse tipo de dados quando:

* Os valores de dados são um conjunto conhecido.
* O conjunto não excede os [limites](luis-boundaries.md) máximos do LUIS para esse tipo de entidade.
* O texto no enunciado corresponde exatamente a um sinônimo ou ao nome canônico. 

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Usar o aplicativo do tutorial existente
> * Adicionar intenção MoveEmployee
> * Adicionar listar de entidades 
> * Treinar 
> * Publicar
> * Obter intenções e entidades do ponto de extremidade

[!INCLUDE[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usar o aplicativo existente
Continue com o aplicativo criado no último tutorial, denominado **HumanResources**. 

Se você não tiver o aplicativo HumanResources do tutorial anterior, use as seguintes etapas:

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-regex-HumanResources.json).

2. Importe o JSON em um novo aplicativo.

3. Na seção **Gerenciar**, na guia **Versões**, clone a versão e nomeie-a como `list`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL. 


## <a name="moveemployee-intent"></a>Intenção MoveEmployee

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

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

    Lembre-se de que o número e datetimeV2 foram adicionados em um tutorial anterior e serão rotulados automaticamente quando forem encontrados em quaisquer exemplos de enunciados.

    [!INCLUDE[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="employee-list-entity"></a>Entidade de lista de funcionários
Agora que a intenção **MoveEmployee** tem enunciados de exemplo, o LUIS precisa entender o que é um funcionário. 

O nome primário, _canônico_, para cada item é o número do funcionário. Para este domínio, os exemplos dos sinônimos de cada nome canônico são: 

|Finalidade do sinônimo|Valor do sinônimo|
|--|--|
|NOME|John W. Smith|
|Endereço de email|john.w.smith@mycompany.com|
|Ramal do telefone|x12345|
|Número de celular pessoal|425-555-1212|
|Número do seguro social|123-45-6789|


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

## <a name="train"></a>Treinar

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicar

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obter a intenção e as entidades do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

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

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas
Este tutorial criou uma nova intenção, adicionou exemplos de enunciados e criou uma entidade de lista para extrair correspondências de texto de enunciados. Após o treinamento e a publicação do aplicativo, uma consulta para o ponto de extremidade identificou a intenção e retornou os dados extraídos.

> [!div class="nextstepaction"]
> [Adicionar uma entidade hierárquica ao aplicativo](luis-quickstart-intent-and-hier-entity.md)

