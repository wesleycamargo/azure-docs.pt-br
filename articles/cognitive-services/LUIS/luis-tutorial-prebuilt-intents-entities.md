---
title: Adicionar intenções e entidades predefinidas para extrair dados comuns no Reconhecimento vocal – Azure | Microsoft Docs
description: Saiba como usar intenções e entidades predefinidas para extrair diferentes tipos de dados de entidade.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 06/11/2018
ms.author: v-geberr
ms.openlocfilehash: 20950ced66497fb0dc96365975b37f244f677ce3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266372"
---
# <a name="use-prebuilt-intents-and-entities-to-handle-common-intents-and-data"></a>Usar intenções e entidades predefinidas para manipular intenções e dados comuns
Adicione intenções e entidades predefinidas ao aplicativo de início rápido de Recursos Humanos para adquirir rapidamente previsão de intenção e extração de dados. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
* Adicionar intenções predefinidas 
* Adicionar entidades predefinidas datetimeV2 e number
* Treinar e publicar
* Consultar o LUIS e receber resposta de previsão

## <a name="before-you-begin"></a>Antes de começar
Se você não tiver o aplicativo de Recursos Humanos no início rápido do [domínio personalizado](luis-quickstart-intents-only.md), [importe](create-new-app.md#import-new-app) o JSON para um novo aplicativo no site do [LUIS][LUIS], no repositório GitHub [Exemplos-LUIS](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json).

Caso queira manter o aplicativo de recursos humanos original, clone a versão na página [Configurações](luis-how-to-manage-versions.md#clone-a-version) e nomeie-a como `prebuilts`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. 

## <a name="add-prebuilt-intents"></a>Adicionar intenções predefinidas
O LUIS oferece várias intenções predefinidas para ajudar com intenções do comuns do usuário.  

1. Verifique se o seu aplicativo está na seção **Criar** do LUIS. Você pode alterar essa seção selecionando **Compilar** na barra de menus da parte superior direita. 

    [ ![Captura de tela do aplicativo de LUIS com Compilar realçado na barra de navegação superior direita](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png)](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png#lightbox)

2. Selecione **Adicionar intenção de domínio predefinida**. 

    [ ![Captura de tela da página Intenções com o botão Adicionar intenção de domínio predefinida realçado](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. Pesquise `Utilities`. 

    [ ![Captura de tela da caixa de diálogo de intenções predefinidas com Utilitários na caixa de pesquisa](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Selecione as seguintes intenções e selecione **Concluído**: 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.Stop
    * Utilities.StartOver

## <a name="add-prebuilt-entities"></a>Adicionar entidades predefinidas
O LUIS fornece várias entidades predefinidas para extração de dados comuns. 

1. Selecione **Entidades** no menu de navegação à esquerda.

    [ ![Captura de tela da lista de intenções com Entidades realçado na navegação esquerda](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. Selecione o botão **Gerenciar entidades predefinidas**.

    [ ![Captura de tela da lista Entidades com a opção Gerenciar entidades predefinidas realçada](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. Selecione **number** e **datetimeV2** na lista de entidades predefinidas e selecione **Concluído**.

    ![Captura de tela do número selecionado no diálogo de entidades predefinidas](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>Treinar e publicar o aplicativo
1. No canto superior direito do site do LUIS, selecione o botão **Train** (Treinar). 

    ![Botão Treinar](./media/luis-quickstart-intents-only/train-button.png)

    O treinamento é concluído quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

    ![Barra de status treinado](./media/luis-quickstart-intents-only/trained.png)

2. No canto superior direito do site do LUIS, selecione o botão **Publicar** para abrir a página Publicar. O slot de produção é selecionado por padrão. Selecione o botão **Publicar** pela opção do slot de produção. A publicação estará concluída quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

    Não é necessário criar uma chave LUIS no portal do Azure antes de publicar ou antes de testar a URL do ponto de extremidade. Todo aplicativo LUIS tem uma chave de início gratuita para criação. Ela oferece a você criação ilimitada e [algumas ocorrências de ponto de extremidade](luis-boundaries.md#key-limits). 

## <a name="query-endpoint-with-an-utterance"></a>Consultar o ponto de extremidade com uma declaração
Na página **Publicar**, selecione o link do **ponto de extremidade** na parte inferior da página. Essa ação abre outra janela do navegador com a URL de ponto de extremidade na barra de endereços. Vá até o final da URL no endereço e insira `I want to cancel on March 3`. O último parâmetro da cadeia de caracteres de consulta é `q`, a declaração **query**. 

O resultado previu a intenção Utilities.Cancel e extraiu a data de 3 de março e o número 3. 

    ```
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

Ao adicionar rápida e facilmente intenções e entidades predefinidas, o aplicativo cliente pode adicionar gerenciamento de conversa e extrair tipos de dados comuns. 

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre entidades](luis-concept-entity-types.md). 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
