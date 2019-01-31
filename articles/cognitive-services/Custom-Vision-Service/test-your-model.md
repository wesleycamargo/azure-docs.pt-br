---
title: Testar e treinar novamente um modelo - Serviço de Visão Personalizada
titlesuffix: Azure Cognitive Services
description: Saiba como testar uma imagem e, em seguida, usá-la para treinar novamente o modelo.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: d53e9a7c6c4e797de66b50feae81ee291bf3da4b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212198"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Testar e treinar novamente um modelo com o Serviço de Visão Personalizada

Depois de treinar seu modelo, você pode testar rapidamente usando uma imagem armazenada no local ou uma imagem online. O teste usa a iteração treinada mais recentemente.

## <a name="test-your-model"></a>Testar o modelo

1. Na [página da Web Visão Personalizada](https://customvision.ai), selecione o seu projeto. Selecione **Teste Rápido** à direita da barra de menus superior. Essa ação abre uma janela rotulada **Teste Rápido**.

    ![O botão Teste Rápido é mostrado no canto superior direito da janela.](./media/test-your-model/quick-test-button.png)

2. Na janela **Teste Rápido**, clique no campo **Enviar Imagem** e digite a URL da imagem que você deseja usar para o teste. Se você quiser usar uma imagem armazenada localmente em vez disso, clique no botão **Procurar arquivos locais** e selecione um arquivo de imagem local.

    ![Imagem da página de imagem de envio](./media/test-your-model/submit-image.png)

A imagem selecionada aparece no meio da página. Em seguida, os resultados são exibidos abaixo da imagem na forma de uma tabela com duas colunas, chamadas **Marcas** e **Confiança**. Depois de exibir os resultados, você poderá fechar a janela **Teste Rápido**.

Agora você pode adicionar esta imagem de teste ao seu modelo e, em seguida, treinar novamente seu modelo.

## <a name="use-the-predicted-image-for-training"></a>Use a imagem prevista para o treinamento.

Para usar a imagem enviada anteriormente para treinamento, use as seguintes etapas:

1. Para exibir imagens enviadas para o classificador, abra a [página da Web Visão Personalizada](https://customvision.ai) e selecione a guia __Previsões__.

    ![Imagem da guia de previsões](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > A exibição padrão mostra imagens da iteração atual. Você pode usar o campo suspenso __Iteração__ para exibir as imagens enviadas durante as iterações anteriores.

2. Passe o mouse sobre uma imagem para ver as marcas que foram previstas pelo classificador.

    > [!TIP]
    > As imagens são classificadas, de forma que as imagens que possam trazer mais ganhos ao classificador fiquem na parte superior. Para selecionar uma classificação diferente, use a seção __Classificar__.

    Para adicionar uma imagem aos seus dados de treinamento, selecione a imagem, selecione a marca e, em seguida, selecione __Salvar e fechar__. A imagem será removida de __Previsões__ e adicionada às imagens de treinamento. Você pode exibi-la selecionando a guia __Imagens de Treinamento__.

    ![Imagem da página de marcação](./media/test-your-model/tag-image.png)

3. Use o botão __Treinar__ para treinar novamente o classificador.

## <a name="next-steps"></a>Próximas etapas

[Melhorar o seu classificador](getting-started-improving-your-classifier.md)
