---
title: Treinar seu aplicativo de LUIS – Azure | Microsoft Docs
description: Use o LUIS (Serviço Inteligente de Reconhecimento Vocal) para treinar seu modelo.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: diberry
ms.openlocfilehash: e947df20141b0b9870f318f410488aea23bafcf5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223177"
---
# <a name="train-your-luis-app"></a>Treinar seu aplicativo de LUIS

O treinamento é o processo de ensinar seu aplicativo de LUIS (Serviço Inteligente de Reconhecimento Vocal) para melhorar seu reconhecimento vocal de idioma natural. Treine seu aplicativo de LUIS após atualizações ao modelo, como adicionar, editar, rotular ou excluir entidades, intenções ou enunciados. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Treinar e [testar](luis-concept-test.md) um aplicativo é um processo iterativo. Após treinar seu aplicativo de LUIS, teste-o com enunciados de amostra para ver se as intenções e as entidades são corretamente reconhecidas. Caso não sejam, faça atualizações ao aplicativo de LUIS, treine e teste novamente. 

## <a name="train-your-app"></a>Treinar seu aplicativo
Para começar o processo iterativo, primeiro você precisa treinar seu aplicativo de LUIS pelo menos uma vez. Verifique se que cada intenção tem pelo menos um enunciado antes do treinamento.

1. Acesse seu aplicativo selecionando seu nome na página **Meus Aplicativos**. 

2. Em seu aplicativo, selecione **Treinar** no painel superior. 

    ![Botão Treinar](./media/luis-how-to-train/train-button.png)

3. Quando o treinamento estiver concluído, uma barra verde de notificação aparecerá na parte superior do navegador.

    ![Página Treinar e Testar Aplicativo](./media/luis-how-to-train/train-success.png)

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Se você tiver uma ou mais intenções no seu aplicativo que não contenham enunciados de exemplo, você não poderá treinar seu aplicativo. Adicione enunciados para todas as suas intenções. Para obter mais informações, veja [Adicionar enunciados de exemplo](luis-how-to-add-example-utterances.md).

## <a name="next-steps"></a>Próximas etapas

* [Rotular enunciados sugeridos com o LUIS](luis-how-to-review-endoint-utt.md) 
* [Usar recursos para melhorar o desempenho do aplicativo de LUIS](luis-how-to-add-features.md) 