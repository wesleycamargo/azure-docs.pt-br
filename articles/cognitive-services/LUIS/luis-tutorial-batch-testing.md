---
title: Use o teste de lote para aprimorar as previsões do LUIS | Microsoft Docs
titleSuffix: Azure
description: Carregue o teste de lote, examine os resultados e aprimore as previsões do LUIS com alterações.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 5788f17f2724a0354a1db506971c2343c1800f01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266389"
---
# <a name="use-batch-testing-to-find-prediction-accuracy-issues"></a>Use o teste de lote para localizar problemas de precisão de previsão

Este tutorial demonstra como usar o teste de lote para localizar problemas de previsão de declaração.  

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
* Criar um arquivo de teste de lote 
* Executar um teste de lote
* Examinar resultados de teste
* Corrigir erros para intenções
* Testar novamente o lote

## <a name="prerequisites"></a>pré-requisitos

> [!div class="checklist"]
> * Para este artigo, também é necessário ter uma conta [LUIS][LUIS] para criar o seu aplicativo LUIS.

> [!Tip]
> Se você ainda não tem uma assinatura, é possível se registrar em uma [conta gratuita](https://azure.microsoft.com/free/).

## <a name="create-new-app"></a>Criar novo aplicativo
Este artigo usa o domínio predefinido HomeAutomation. O domínio predefinido tem intenções, entidades e declarações para controlar dispositivos HomeAutomation como luzes. Crie o aplicativo, adicione o domínio, treine e publique.

1. No site do [LUIS], crie um novo aplicativo selecionando **Criar novo aplicativo** na página **MyApps**. 

    ![Criar novo aplicativo](./media/luis-tutorial-batch-testing/create-app-1.png)

2. Insira o nome `Batchtest-HomeAutomation` na caixa de diálogo.

    ![Inserir o nome do aplicativo](./media/luis-tutorial-batch-testing/create-app-2.png)

3. Selecione **Domínios predefinidos** no canto inferior esquerdo. 

    ![Selecionar domínio predefinido](./media/luis-tutorial-batch-testing/prebuilt-domain-1.png)

4. Selecione **Adicionar domínio** para HomeAutomation.

    ![Adicionar domínio HomeAutomation](./media/luis-tutorial-batch-testing/prebuilt-domain-2.png)

5. Selecione **Treinar** na barra de navegação superior direita.

    ![Selecione o botão Treinar](./media/luis-tutorial-batch-testing/train-button.png)

## <a name="batch-test-criteria"></a>Critérios do teste de lote
O teste de lote pode testar até mil declarações por vez. O lote não deve ter duplicatas. [Exporte](create-new-app.md#export-app) o aplicativo para ver a lista de declarações atuais.  

A estratégia de teste do LUIS usa três conjuntos de dados separados: declarações de modelo, declarações de teste de lote e declarações de ponto de extremidade. Para este tutorial, verifique se você não está usando as declarações de quaisquer declarações de modelo (adicionadas a uma intenção) ou declarações de ponto de extremidade. 

Não use nenhuma declaração que já estiver no aplicativo para o teste de lote:

```
'breezeway on please',
'change temperature to seventy two degrees',
'coffee bar on please',
'decrease temperature for me please',
'dim kitchen lights to 25 .',
'fish pond off please',
'fish pond on please',
'illuminate please',
'living room lamp on please',
'living room lamps off please',
'lock the doors for me please',
'lower your volume',
'make camera 1 off please',
'make some coffee',
'play dvd',
'set lights bright',
'set lights concentrate',
'set lights out bedroom',
'shut down my work computer',
'silence the phone',
'snap switch fan fifty percent',
'start master bedroom light .',
'theater on please',
'turn dimmer off',
'turn off ac please',
'turn off foyer lights',
'turn off living room light',
'turn off staircase',
'turn off venice lamp',
'turn on bathroom heater',
'turn on external speaker',
'turn on my bedroom lights .',
'turn on the furnace room lights',
'turn on the internet in my bedroom please',
'turn on thermostat please',
'turn the fan to high',
'turn thermostat on 70 .' 
```

## <a name="create-a-batch-to-test-intent-prediction-accuracy"></a>Criar um lote para testar a precisão da previsão da intenção
1. Crie `homeauto-batch-1.json` em um editor de texto como [VSCode](https://code.visualstudio.com/). 

2. Adicione declarações com a **intenção** que você deseja que seja prevista no teste. Para este tutorial, para simplificar, use as declarações no `HomeAutomation.TurnOn` e no `HomeAutomation.TurnOff` e alterne o texto `on` e `off` nas declarações. Para a intenção `None`, adicione algumas declarações que não fazem parte da área do [domínio](luis-glossary.md#domain) (assunto). 

    A fim de entender como os resultados de teste de lote se correlacionam com o JSON do lote, adicione apenas seis intenções.

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        }
    ]
    ```

## <a name="run-the-batch"></a>Executar o lote
1. Selecione **Testar** na barra de navegação superior. 

    ![Selecione Testar na barra de navegação](./media/luis-tutorial-batch-testing/test-1.png)

2. Selecione **Painel de teste do lote** no painel direito. 

    ![Selecione o painel de teste do lote](./media/luis-tutorial-batch-testing/test-2.png)

3. Selecione **Importar conjunto de dados**.

    ![Selecione Importar conjunto de dados](./media/luis-tutorial-batch-testing/test-3.png)

4. Escolha o local do sistema de arquivos do arquivo `homeauto-batch-1.json`.

5. Nomeie o conjunto de dados `set 1`.

    ![Escolher arquivo](./media/luis-tutorial-batch-testing/test-4.png)

6. Selecione o botão **Executar**. Aguarde até que o teste seja concluído.

    ![Selecionar Executar](./media/luis-tutorial-batch-testing/test-5.png)

7. Selecione **Ver resultados**.

    ![Ver resultados](./media/luis-tutorial-batch-testing/test-6.png)

8. Examine os resultados no grafo e na legenda.

    ![Resultados do lote](./media/luis-tutorial-batch-testing/batch-result-1.png)

## <a name="review-batch-results"></a>Examinar resultados do lote
Os resultados do lote estão em duas seções. A seção superior contém o grafo e a legenda. A seção inferior exibe declarações quando você selecione um nome de área do grafo.

Quaisquer erros são indicados pela cor vermelha. O grafo está em quatro seções, com duas das seções sendo exibidas em vermelho. **Essas são as seções que devem ser privilegiadas**. 

A seção superior direita indica que o teste previu incorretamente a existência de uma intenção ou entidade. A seção inferior esquerda indica que o teste previu incorretamente a ausência de uma intenção ou entidade.

### <a name="homeautomationturnoff-test-results"></a>Resultados de teste HomeAutomation.TurnOff
Na legenda, selecione a intenção `HomeAutomation.TurnOff`. Ela tem um ícone de êxito verde à esquerda do nome na legenda. Não há erros para esta intenção. 

![Resultados do lote](./media/luis-tutorial-batch-testing/batch-result-1.png)

### <a name="homeautomationturnon-and-none-intents-have-errors"></a>As intenções HomeAutomation.TurnOn e None têm erros
As outras duas intenções têm erros, o que significa que as previsões do teste não corresponderam às expectativas do arquivo em lotes. Selecione a intenção `None` na legenda para examinar o primeiro erro. 

![Intenção None](./media/luis-tutorial-batch-testing/none-intent-failures.png)

As falhas são exibidas no gráfico nas seções vermelhas: **Falso positivo** e **Falso negativo**. Selecione o nome da seção **Falso negativo** no gráfico para ver as declarações com falha embaixo do gráfico. 

![Falhas de falso negativo](./media/luis-tutorial-batch-testing/none-intent-false-negative.png)

A declaração com falha, `help`, era esperada como uma intenção `None`, mas o teste previu a intenção `HomeAutomation.TurnOn`.  

Há duas falhas, uma em HomeAutomation.TurnOn e uma em None. Ambas foram causadas pela declaração `help`, porque ela falhou em atender à expectativa em None e era uma correspondência inesperado para a intenção HomeAutomation.TurnOn. 

Para determinar por que as declarações `None` estão falhando, examine as declarações que estão no `None` no momento. 

## <a name="review-none-intents-utterances"></a>Examine as declarações da intenção None

1. Feche o painel **Testar** selecionando o botão **Testar** na barra de navegação superior. 

2. Selecione **Criar** no painel de navegação superior. 

3. Selecione a intenção **None** na lista de intenções.

4. Selecione Control+E para ver a exibição de token das declarações 
    
    |Declarações da intenção None|Pontuação de previsão|
    |--|--|
    |"diminua a temperatura para mim"|0,44|
    |"esmaeça as luzes da cozinha para 25."|0,43|
    |"abaixe seu volume"|0,46|
    |"ligue a Internet no meu quarto"|0,28|

## <a name="fix-none-intents-utterances"></a>Corrigir declarações da intenção None
    
Quaisquer declarações em `None` devem estar fora do domínio do aplicativo. Essas declarações são relativas a HomeAutomation, então elas estão na intenção errada. 

O LUIS também dá às declarações pontuação de previsão de menos de 50% (<0,50). Se você observar as declarações nas outras duas intenções, verá pontuações de previsão muito maiores. Quando o LUIS tem baixas pontuações para declarações de exemplo, isso é uma boa indicação de que as declarações são confusas para o LUIS entre a intenção atual e outras intenções. 

Para corrigir o aplicativo, as declarações que estão na intenção `None` no momento precisam ser movidas para a intenção correta, e a intenção `None` precisa de intenções novas e adequadas. 

Três declarações na intenção `None` destinam-se a diminuir as configurações do dispositivo de automação. Elas usam palavras como `dim`, `lower` ou `decrease`. A quarta declaração solicita que a Internet seja ligada. Como todas as quatro declarações tratam-se de ligar ou alterar o grau de energia de um dispositivo, elas devem ser movidas para a intenção `HomeAutomation.TurnOn`. 

Esta é apenas uma solução. Você também pode criar uma nova intenção de `ChangeSetting` e mover as declarações usando esmaecer, reduzir e diminuir para essa nova intenção. 

## <a name="fix-the-app-based-on-batch-results"></a>Corrigir o aplicativo com base nos resultados do lote
Mova as quatro declarações para a intenção `HomeAutomation.TurnOn`. 

1. Marque a caixa de seleção em cima da lista de declarações para que todas elas sejam selecionadas. 

2. Na lista suspensa **Reatribuir intenção**, selecione `HomeAutomation.TurnOn`. 

    ![Mover declarações](./media/luis-tutorial-batch-testing/move-utterances.png)

    Depois que as quatro declarações forem reatribuídas, a lista de declarações da intenção `None` ficará vazia.

3. Adicione quatro novas intenções para a intenção None:

    ```
    "fish"
    "dogs"
    "beer"
    "pizza"
    ```

    Essas declarações estão definitivamente fora do domínio de HomeAutomation. À medida que você insere cada declaração, observe a pontuação dela. A pontuação pode ser baixa ou até mesmo muito baixa (com uma caixa vermelha em volta dela). Depois de treinar o aplicativo, na etapa 8, a pontuação será muito maior. 

7. Remova rótulos selecionando o rótulo azul na declaração e selecione **Remover rótulo**.

8. Selecione **Treinar** na barra de navegação superior direita. A pontuação de cada declaração é muito maior. Todas as pontuações para a intenção `None` devem estar acima de 0,80 agora. 

## <a name="verify-the-fix-worked"></a>Verificar se a correção funcionou
Para verificar se as declarações no teste de lote estão corretamente previstas para a intenção **None**, execute o teste de lote novamente.

1. Selecione **Testar** na barra de navegação superior. 

2. Selecione **Painel de teste do lote** no painel direito. 

3. Selecione as reticências (...) à direita do nome do lote e selecione **Executar conjunto de dados**. Aguarde até que o teste de lote seja concluído.

    ![Executar conjunto de dados](./media/luis-tutorial-batch-testing/run-dataset.png)

4. Selecione **Ver resultados**. Todas as tentativas devem ter ícones verdes à esquerda dos nomes da intenção. Com o filtro correto definido para a intenção `HomeAutomation.Turnoff`, selecione o ponto verde no painel superior direito mais perto do meio do gráfico. O nome da declaração é exibido na tabela embaixo do gráfico. A pontuação de `breezeway off please` é muito baixa. Uma atividade opcional é adicionar mais declarações à intenção para aumentar sua pontuação. 

    ![Executar conjunto de dados](./media/luis-tutorial-batch-testing/turnoff-low-score.png)

<!--
    The Entities section of the legend may have errors. That is the next thing to fix.

## Create a batch to test entity detection
1. Create `homeauto-batch-2.json` in a text editor such as [VSCode](https://code.visualstudio.com/). 

2. Utterances have entities identified with `startPos` and `endPost`. These two elements identify the entity before [tokenization](luis-glossary.md#token), which happens in some [cultures](luis-supported-languages.md#tokenization) in LUIS. If you plan to batch test in a tokenized culture, learn how to [extract](luis-concept-data-extraction.md#tokenized-entity-returned) the non-tokenized entities.

    Copy the following JSON into the file:

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 4
            }
          ]
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Operation",
              "startPos": 7,
              "endPos": 17
            }
          ]
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 9
            }
          ]
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Device",
              "startPos": 0,
              "endPos": 10
            }
          ]
        }
      ]
    ```

3. Import the batch file, following the [same instructions](#run-the-batch) as the first import, and name the dataset `set 2`. Run the test.

## Possible entity errors
Since the intents in the right-side filter of the test panel still pass the test, this section focuses on correct entity identification. 

Entity testing is diferrent than intents. An utterance will have only one top scoring intent, but it may have several entities. An utterance's entity may be correctly identified, may be incorrectly identified as an entity other than the one in the batch test, may overlap with other entities, or not identified at all. 

## Review entity errors
1. Select `HomeAutomation.Device` in the filter panel. The chart changes to show a single false positive and several true negatives. 

2. Select the False positive section name. The utterance for this chart point is displayed below the chart. The labeled intent and the predicted intent are the same, which is consistent with the test -- the intent prediction is correct. 

    The issue is that the HomeAutomation.Device was detected but the batch expected HomeAutomation.Room for the utterance "coffee bar off please". `Coffee bar` could be a room or a device, depending on the environment and context. As the model designer, you can either enforce the selection as `HomeAutomation.Room` or change the batch file to use `HomeAutomation.Device`. 

    If you want to reinforce that coffee bar is a room, you nee to add an utterances to LUIS that help LUIS decide a coffee bar is a room. 

    The most direct route is to add the utterance to the intent but that to add the utterance for every entity detection error is not the machine-learned solution. Another fix would be to add an utterance with `coffee bar`.

## Add utterance to help extract entity
1. Select the **Test** button on the top navigation to close the batch test panel.

2. On the `HomeAutomation.TurnOn` intent, add the utterance, `turn coffee bar on please`. The uttterance should have all three entities detected after you select enter. 

3. Select **Train** on the top navigation panel. Wait until training completes successfully.

3. Select **Test** on the top navigation panel to open the Batch testing pane again. 

4. If the list of datasets is not visible, select **Back to list**. Select the three dots (...) at the end of `Set 2` and select `Run Dataset`. Wait for the test to complete.

5. Select **See results** to review the test results.

6. 
-->
## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre declarações de exemplo](luis-how-to-add-example-utterances.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions