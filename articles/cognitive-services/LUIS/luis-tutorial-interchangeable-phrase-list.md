---
title: Tutorial que usa uma lista de frases para melhorar as previsões do LUIS – Azure | Microsoft Docs
description: Neste tutorial, adicione uma lista de frases a um aplicativo LUIS e veja o aprimoramento da pontuação.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 2fd53225a455a34d03772487a10f62a94ac86735
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868966"
---
# <a name="tutorial-add-phrase-list-to-improve-predictions"></a>Tutorial: Adicionar lista de frases para aprimorar previsões
Neste tutorial, aprimore a precisão das pontuações de intenção e identifique entidades para palavras que têm o mesmo significado (sinônimos) adicionando um [recurso de lista de frases](./luis-concept-feature.md) intercambiável.

> [!div class="checklist"]
* Importar um novo aplicativo  
* Consultar ponto de extremidade com declaração conhecida 
* Consultar ponto de extremidade com declaração _desconhecida_
* Adicionar lista de frases para aprimorar a pontuação da declaração desconhecida
* Verifique se a entidade foi encontrada ao usar uma lista de frases

Para este artigo, você precisa de uma conta gratuita de [LUIS][LUIS] para criar o seu aplicativo LUIS.

## <a name="import-a-new-app"></a>Importar um novo aplicativo
1. Baixe o [aplicativo LUIS de exemplo][LuisSampleApp] criado para este tutorial. Você o usará na próxima seção. 

2. Conforme descrito em [Criar um aplicativo](Create-new-app.md#import-new-app), importe o arquivo baixado para o site do [LUIS][LUIS] como um novo aplicativo. O nome do aplicativo é "Tutorial Minha lista de frases". Ele tem intenções, entidades e declarações. 

3. [Treine](luis-how-to-train.md) seu aplicativo. Até que seja treinado, não é possível [testá-lo interativamente](interactive-test.md#interactive-testing) no site do [LUIS][LUIS]. 

4. Na página [Publicar](luis-how-to-publish-app.md), marque a caixa de seleção **Incluir todas as pontuações de intenções previstas**. Quando a caixa de seleção for marcada, todas as intenções serão retornadas. Quando a caixa de seleção for desmarcada, somente a intenção superior será retornada. 

5. [Publique](luis-how-to-publish-app.md) o aplicativo. Publicar o aplicativo permite que você o teste usando o ponto de extremidade HTTPS. 

## <a name="test-a-trained-utterance"></a>Testar uma declaração treinada
Use o ponto de extremidade publicado para consultar uma declaração que o aplicativo já sabe. Como o LUIS já conhece a declaração, a pontuação é alta e a entidade é detectada.

1. No site do [LUIS (Reconhecimento vocal)][LUIS], na página **Publicar** do novo aplicativo, selecione a URL do ponto de extremidade na seção **Recursos e chaves**. 

    ![Publicar a URL do ponto de extremidade](./media/luis-tutorial-interchangeable-phrase-list/luis-publish-url.png)

2. No navegador, no final da URL, adicione a seguinte consulta após o `q=`.

    `I want a computer replacement`

    O ponto de extremidade responde com o JSON a seguir:
    
    ```JSON
    {
      "query": "I want a computer replacement",
      "topScoringIntent": {
        "intent": "GetHardware",
        "score": 0.9735458
      },
      "intents": [
        {
          "intent": "GetHardware",
          "score": 0.9735458
        },
        {
          "intent": "None",
          "score": 0.07053033
        },
        {
          "intent": "Whois",
          "score": 0.03760778
        },
        {
          "intent": "CheckCalendar",
          "score": 0.02285902
        },
        {
          "intent": "CheckInventory",
          "score": 0.0110072717
        }
      ],
      "entities": [
        {
          "entity": "computer",
          "type": "Hardware",
          "startIndex": 9,
          "endIndex": 16,
          "score": 0.8465915
        }
      ]
    }
    ```

    A pontuação de intenção de 0,973 e a pontuação de detecção de entidade de 0,846 são altas, porque o aplicativo foi treinado com essa declaração. A declaração está no aplicativo LUIS na página da intenção para **GetHardware**. O texto da declaração, `computer`, é rotulado como a entidade **Hardware**. 
    
    |Status|Word| Pontuação da intenção | Pontuação da entidade |
    |--|--|--|--|
    |Treinado| desejar | 0,973 | 0,846 |
    
    
## <a name="test-an-untrained-utterance"></a>Teste uma declaração não treinada
No navegador, use o ponto de extremidade publicado para consultar com uma declaração que o aplicativo ainda não sabe:

`I require a computer replacement`

Essa declaração usa um sinônimo da declaração anterior:

| Palavra treinada | Sinônimo não treinado |
|--|--|
| desejar | exigir |

A resposta do ponto de extremidade é:

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.840912163
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.840912163
    },
    {
      "intent": "None",
      "score": 0.0972757638
    },
    {
      "intent": "Whois",
      "score": 0.0448251367
    },
    {
      "intent": "CheckCalendar",
      "score": 0.0291390456
    },
    {
      "intent": "CheckInventory",
      "score": 0.0137837809
    }
  ],
  "entities": []
}
```

| Status | Word | Pontuação da intenção | Pontuação da entidade |
|--|--|--|--|
| Treinado| desejar | 0,973 | 0,846 |
| Não treinado| exigir | 0,840 | - |

A pontuação da intenção da declaração não treinada é mais baixa do que a da declaração rotulada, porque o LUIS sabe que a frase é gramaticalmente a mesma. Mas o LUIS não sabe que as declarações têm o mesmo significado. Além disso, sem a lista de frases, a entidade **Hardware** não é encontrada.

É necessário ensinar ao LUIS que *desejar* e *exigir* têm o mesmo significado neste domínio de aplicativo, porque uma palavra pode ter mais de um significado. 

## <a name="improve-the-score-of-untrained-utterance-with-phrase-list"></a>Aprimorar a pontuação da declaração não treinada com lista de frases 
1. Adicione um recurso [lista de frases](luis-how-to-add-features.md) denominado **desejar** com o valor de `want`, e selecione **Enter**.

    > [!TIP]
    > Após cada palavra ou frase, selecione a tecla **Enter**. A palavra ou frase é adicionada à caixa **Valores da lista de frases** enquanto o cursor permanece na caixa **Valor**. É possível inserir muitos valores rapidamente com esse recurso.

2. Para exibir as palavras recomendadas pelo LUIS, selecione **Recomendado**. 

    ![Recomendar valores](./media/luis-tutorial-interchangeable-phrase-list/recommend.png)

3. Adicione todas as palavras. Se `require` não estiver na lista recomendada, adicione-o como um valor obrigatório. 

4. Como essas palavras são sinônimas, mantenha a configuração *intercambiável* e selecione **Salvar**.

    ![Valores da lista de frases](./media/luis-tutorial-interchangeable-phrase-list/phrase-list-values.png)

5. Na barra de navegação superior, selecione **Treinar** para treinar o aplicativo, mas não publicá-lo. Agora você tem dois modelos. É possível comparar valores nos dois modelos.

## <a name="compare-the-phrase-list-model-to-the-published-model"></a>Comparar o modelo da lista de frases com o modelo publicado
Neste aplicativo, o modelo publicado não é treinado com os sinônimos. Apenas o modelo editado no momento inclui a lista de frases de sinônimos. Para comparar os modelos, use o [teste interativo](interactive-test.md#interactive-testing). 

1. Abra o painel **Testar** e insira a declaração a seguir:

    `I require a computer replacement`

2. Para abrir o painel de inspeção, selecione **Inspecionar**. 

    ![Selecione inspecionar](./media/luis-tutorial-interchangeable-phrase-list/inspect-button.png)

3. Para comparar o modelo publicado com o novo modelo de lista da frases, selecione **Comparar com publicado**.

    ![Inspecionar publicado versus atual](./media/luis-tutorial-interchangeable-phrase-list/inspect.png)

Após adicionar a lista de frases, a maior precisão da declaração e a entidade **Hardware** são encontrados. 

|Status | Lista de frases| Pontuação da intenção | Pontuação da entidade |
|--|--|--|--|
| Publicado | - | 0.84 | - |
| Editando no momento |✔| 0,92 | Entidade de hardware identificada |

> [!TIP]
> * Usando o [Teste interativo](interactive-test.md#interactive-testing), é possível comparar o modelo publicado com quaisquer alterações feitas após a publicação. 
> * Usando o [Teste de ponto de extremidade](luis-how-to-publish-app.md#test-your-published-endpoint-in-a-browser), é possível exibir o JSON exato de resposta do LUIS. 

## <a name="get-the-entity-score-with-the-endpoint-test"></a>Obter a pontuação da entidade com o teste de ponto de extremidade
Para exibir a pontuação da entidade, [publique o modelo](luis-how-to-publish-app.md) e consulte o ponto de extremidade. 

`I require a computer replacement`

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.916503668
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.916503668
    },
    {
      "intent": "None",
      "score": 0.136505231
    },
    {
      "intent": "Whois",
      "score": 0.02778677
    },
    {
      "intent": "CheckInventory",
      "score": 0.0144592477
    },
    {
      "intent": "CheckCalendar",
      "score": 0.01401332
    }
  ],
  "entities": [
    {
      "entity": "computer",
      "type": "Hardware",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.5959917
    }
  ]
}
```

A entidade **Hardware** mostra uma pontuação de 0,595 com a lista de frases. Antes de a lista de frases existir, a entidade não era detectada. 

|Status | Lista de frases| Pontuação da intenção | Pontuação da entidade |
|--|--|--|--|
| Publicado | - | 0.84 | - |
| Editando no momento |✔| 0,92 | 0,595 |


## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua o aplicativo LUIS. Para fazer isso, selecione as reticências (***...***) à direita do nome do aplicativo na lista de aplicativos, selecione **Excluir**. Na caixa de diálogo pop-up **Excluir aplicativo?**, selecione **OK**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Obter a previsão de declaração com a consulta de ponto de extremidade](luis-get-started-cs-get-intent.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[LuisFeatures]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[LuisSampleApp]: https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/phrase_list/interchangeable/luis-app-before-phrase-list.json
