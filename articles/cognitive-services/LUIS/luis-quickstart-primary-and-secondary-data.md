---
title: 'Tutorial 7: lista de entidades com frases simples no LUIS'
titleSuffix: Azure Cognitive Services
description: Extrair dados de aprendizado de máquina de um enunciado
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: e0cdda629ddded121a424af61377c04ee8d958d3
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867537"
---
# <a name="tutorial-7-extract-names-with-simple-entity-and-phrase-list"></a>Tutorial 7: extrair os nomes com uma lista de entidades e frases simples

Neste tutorial, extraia dados de aprendizado de máquina de um nome de cargo de um enunciado usando a entidade **Simples**. Para aumentar a precisão da extração, adicione uma lista de frases de termos específicos para a entidade simples.

Este tutorial adiciona uma nova entidade simples para extrair o nome do cargo. A finalidade da entidade simples no aplicativo LUIS é ensinar ao LUIS o que é um nome trabalho e onde um enunciado pode ser encontrado. A parte do enunciado que é o nome do cargo pode mudar de acordo com o enunciado e com base no tamanho do enunciado e na escolha de palavras. O LUIS precisa de exemplos de nomes de cargo em todas as intenções que usam nomes de trabalho.  

A entidade simples é uma boa escolha para esse tipo de dados quando:

* Os dados são um conceito único.
* Os dados não foram bem formatados como uma expressão regular.
* Os dados não são comuns como uma entidade predefinida de dados ou número de telefone.
* Os dados não correspondem exatamente à lista de palavras conhecidas como uma entidade de lista.
* Os dados não contêm outros itens de dados como uma entidade de composição ou hierárquica.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Usar o aplicativo do tutorial existente
> * Adicionar uma entidade simples para extrair a mensagem do aplicativo
> * Adicionar lista de frase para aumentar o sinal de palavras de trabalho
> * Treinar 
> * Publicar 
> * Obter intenções e entidades do ponto de extremidade

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usar o aplicativo existente

Continue com o aplicativo criado no último tutorial, denominado **HumanResources**. 

Se você não tiver o aplicativo HumanResources do tutorial anterior, use as seguintes etapas:

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-composite-HumanResources.json).

2. Importe o JSON em um novo aplicativo.

3. Na seção **Gerenciar**, na guia **Versões**, clone a versão e nomeie-a como `simple`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL.

## <a name="simple-entity"></a>Entidade simples
A entidade simples detecta um conceito de dados único contido em palavras ou frases.

Considere os seguintes enunciados de um chatbot:

|Enunciado|Nome do trabalho extraível|
|:--|:--|
|Desejo me candidatar para o novo trabalho de contabilidade.|contabilidade|
|Enviar meu currículo para o cargo de engenharia.|engenharia|
|Preencher o aplicativo para o trabalho 123456|123456|

É difícil determinar o nome do trabalho porque um nome pode ser um substantivo, um verbo ou uma frase com várias palavras. Por exemplo: 

|Trabalhos|
|--|
|engenheiro|
|engenheiro de software|
|engenheiro de software sênior|
|líder de equipe de engenharia |
|controlador de tráfego aéreo|
|operador de veículo motorizado|
|motorista de ambulância|
|vigia|
|extrusor|
|construtor de moinhos|

Esse aplicativo LUIS tem nomes de trabalho em várias intenções. Ao rotular essas palavras em todos os enunciados de intenções, o LUIS aprende mais sobre o que é um nome de cargo e em que local ele é encontrado em enunciados.

Após as entidades serem marcadas nos enunciados de exemplo, é importante adicionar uma lista de frases para ampliar o sinal da entidade simples. Uma lista de frases **não** é usada como uma correspondência exata e não precisa ser todos os valores possíveis que você espera. 

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Na página **Intenções**, selecione a intenção **ApplyForJob**. 

3. No enunciado, `I want to apply for the new accounting job`, selecione `accounting`, digite `Job` no campo superior do menu pop-up, depois selecione **Criar nova entidade** no menu pop-up. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Captura de tela do LUIS com intenção de “ApplyForJob” com as etapas para criar a entidade realçadas")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. Na janela pop-up, verifique o nome e o tipo da entidade e selecione **Concluído**.

    ![Criar diálogo modal pop-up da entidade simple com o nome do Job e tipo de simple](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. No enunciado, `Submit resume for engineering position`, rotule a palavra `engineering` como uma entidade Job. Selecione a palavra `engineering`, depois selecione **Job** no menu pop-up. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Captura de tela do LUIS rotulando a entidade Job realçada")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)

    Todos os enunciados são rotulados, mas cinco enunciados não são suficientes para ensinar o LUIS sobre palavras e frases relacionadas a trabalho. Os trabalhos que usam o valor de número não precisam de mais exemplos porque usam uma entidade de expressão regular. Os trabalhos que são palavras ou frases precisam de pelo menos mais 15 exemplos. 

6. Adicione mais enunciados e marque as palavras ou frases de trabalho como entidade **Job**. Os tipos de trabalho são gerais entre empregos de um serviço de emprego. Se você quisesse trabalhos relacionados a um setor específico, as palavras de trabalho devem retratá-lo. 

    |Enunciado|Entidade Job|
    |:--|:--|
    |Estou me candidatando para o suporte do Gerente de Programas em P&D|Gerente de Programas|
    |Aqui está minha candidatura para cozinheiro de linha.|cozinheiro de linha|
    |Meu currículo para o monitor de campo está anexado.|monitor de acampamento|
    |Este é o meu CV para assistente administrativo.|assistente administrativo|
    |Desejo me candidatar para o trabalho de gerenciamento em vendas.|gerenciamento, vendas|
    |Este é o meu currículo para o novo cargo de contabilidade.|contabilidade|
    |Minha candidatura para assistente de bar está inclusa.|assistente de bar|
    |Estou enviando minha candidatura para carpinteiro e armador.|carpinteiro, armador|
    |Meu CV para motorista de ônibus está aqui.|motorista de ônibus|
    |Sou enfermeiro registrado. Aqui está o meu currículo.|enfermeiro registrado|
    |Gostaria de enviar minha documentação para o cargo de professor que vi no jornal.|professor|
    |Este é o meu CV para o cargo de estoquista de frutas e legumes.|estoquista|
    |Candidatar-se ao trabalho de azulejista.|bloco|
    |CV anexo para arquiteto paisagista.|arquiteto paisagista|
    |Meu currículo para professor de biologia está anexado.|professor de biologia|
    |Gostaria de me candidatar para o cargo em fotografia.|fotografia|git 

## <a name="label-entity-in-example-utterances"></a>Entidade de rótulo em enunciados de exemplo

Ao rotular, ou _marcar_, a entidade mostra ao LUIS em que local a entidade está nos enunciados de exemplo.

1. Selecione **Intents** no menu esquerdo.

2. Selecione **GetJobInformation** na lista de intenções. 

3. Rotule os trabalhos nos enunciados de exemplo:

    |Enunciado|Entidade Job|
    |:--|:--|
    |Há algum trabalho em bancos de dados?|databases|
    |Procurando uma nova vaga com responsabilidades de contabilidade|contabilidade|
    |Quais cargos estão disponíveis para engenheiros seniores?|engenheiros seniores|

    Há outros enunciados de exemplo, mas eles não contêm palavras de trabalho.

## <a name="train"></a>Treinar

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicar

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obter a intenção e as entidades do ponto de extremidade 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá até o final da URL no endereço e insira `Here is my c.v. for the programmer job`. O último parâmetro de querystring é `q`, o enunciado **consulta**. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, é um bom teste e deve retornar os enunciados `ApplyForJob`.

    ```JSON
    {
      "query": "Here is my c.v. for the programmer job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9826467
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9826467
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0218927357
        },
        {
          "intent": "MoveEmployee",
          "score": 0.007849265
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00349470088
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00348804821
        },
        {
          "intent": "None",
          "score": 0.00319909188
        },
        {
          "intent": "FindForm",
          "score": 0.00222647213
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00211193133
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00172086991
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00138010911
        }
      ],
      "entities": [
        {
          "entity": "programmer",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 33,
          "score": 0.5230502
        }
      ]
    }
    ```
    
    O LUIS encontrou a intenção correta, **ApplyForJob**, e extraiu a entidade correta, **Job**, com um valor de `programmer`.


## <a name="names-are-tricky"></a>Nomes são complicados
O aplicativo LUIS encontrou a intenção correta com alta confiança e extraiu o nome do trabalho, mas nomes são complicados. Tente usar o enunciado `This is the lead welder paperwork`.  

No JSON a seguir, o LUIS responde com a intenção correta, `ApplyForJob`, mas não extrai o nome de trabalho `lead welder`. 

```JSON
{
  "query": "This is the lead welder paperwork.",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.468558252
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.468558252
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0102701457
    },
    {
      "intent": "MoveEmployee",
      "score": 0.009442534
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00639619166
    },
    {
      "intent": "None",
      "score": 0.005859333
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.005087704
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00315379258
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00259344373
    },
    {
      "intent": "FindForm",
      "score": 0.00193389168
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.000420796918
    }
  ],
  "entities": []
}
```

Como um nome pode ser qualquer coisa, o LUIS prevê as intenções mais precisamente caso tenha uma lista de frase de palavras para melhorar o sinal.

## <a name="to-boost-signal-add-phrase-list"></a>Para melhorar o sinal, adicione uma lista de frases

Abra o [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) do repositório Github LUIS-Samples. A lista tem mais de mil palavras de trabalho e frases. Examine a lista de palavras em busca de palavras de trabalho que sejam significativas para você. Se suas palavras ou frases não estiverem na lista, adicione suas próprias.

1. Na seção **Compilar** do aplicativo LUIS, selecione **Listas de frase**, encontrada no menu **Melhorar o desempenho do aplicativo**.

2. Selecione **Criar nova lista de frase**. 

3. Nomeie a nova lista de frase como `Job` e copie a lista de jobs-phrase-list.csv para a caixa de texto **Valores**. Selecione enter. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Captura da caixa de diálogo pop-up Criar nova lista de frase")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Se você quiser adicionar mais palavras à lista de frases, revise os **Valores Relacionados** e adicione os que forem relevantes. 

4. Selecione **Salvar** para ativar a lista de frases.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Captura de tela da caixa de diálogo pop-up Criar nova lista de frases com palavras na caixa Valores da lista de frases")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. [Treine](#train) e [publique](#publish) o aplicativo novamente para usar a lista de frases.

6. Repita a consulta no ponto de extremidade com o mesmo enunciado: `This is the lead welder paperwork.`

    A resposta JSON inclui a entidade extraída:

    ```JSON
    {
        "query": "This is the lead welder paperwork.",
        "topScoringIntent": {
            "intent": "ApplyForJob",
            "score": 0.920025647
        },
        "intents": [
            {
            "intent": "ApplyForJob",
            "score": 0.920025647
            },
            {
            "intent": "GetJobInformation",
            "score": 0.003800706
            },
            {
            "intent": "Utilities.StartOver",
            "score": 0.00299335527
            },
            {
            "intent": "MoveEmployee",
            "score": 0.0027167045
            },
            {
            "intent": "None",
            "score": 0.00259556063
            },
            {
            "intent": "FindForm",
            "score": 0.00224019377
            },
            {
            "intent": "Utilities.Stop",
            "score": 0.00200693542
            },
            {
            "intent": "Utilities.Cancel",
            "score": 0.00195913855
            },
            {
            "intent": "Utilities.Help",
            "score": 0.00162656687
            },
            {
            "intent": "Utilities.Confirm",
            "score": 0.0002851904
            }
        ],
        "entities": [
            {
            "entity": "lead welder",
            "type": "Job",
            "startIndex": 12,
            "endIndex": 22,
            "score": 0.8295959
            }
        ]
    }
    ```

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, o aplicativo de Recursos Humanos usa uma entidade simples de aprendizado de máquina para encontrar nomes de cargo em enunciados. Como os nomes de trabalho podem ser uma ampla variedade de palavras e frases, o aplicativo precisava de uma lista de frases para aumentar as palavras relacionadas a nomes de cargo. 

> [!div class="nextstepaction"]
> [Adicionar uma entidade de keyphrase predefinida](luis-quickstart-intent-and-key-phrase.md)