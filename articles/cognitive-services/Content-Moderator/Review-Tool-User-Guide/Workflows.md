---
title: Definir e usar fluxos de trabalho de moderação de conteúdo – Content Moderator
titlesuffix: Azure Cognitive Services
description: Você pode usar o designer de fluxo de trabalho do Azure Content Moderator e APIs para definir os fluxos de trabalho personalizados e limites com base nas suas políticas de conteúdo.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 265d5a2bd6a256d287e6ae164e883ade783f960f
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262397"
---
# <a name="define-test-and-use-workflows"></a>Definir, testar e usar fluxos de trabalho

Você pode usar o designer de fluxo de trabalho do Azure Content Moderator e APIs para definir os fluxos de trabalho personalizados e limites com base nas suas políticas de conteúdo.

Os fluxos de trabalho "se conectam" à API de Content Moderator usando conectores. Você pode usar outras APIs se um conector para essa API estiver disponível. Este exemplo usa o conector do Content Moderator que está incluído por padrão.

## <a name="browse-to-the-workflows-section"></a>Navegue até a seção de fluxos de trabalho

Na guia **Configurações**, selecione **Fluxos de trabalho**.

  ![Configuração de fluxos de trabalho](images/2-workflows-0.png)

## <a name="start-a-new-workflow"></a>Iniciar um novo fluxo de trabalho

Selecione **Adicionar fluxo de trabalho**.

  ![Adicionar um fluxo de trabalho](images/2-workflows-1.png)

## <a name="assign-a-name-and-description"></a>Atribuir nome e descrição

Nomeie seu fluxo de trabalho, insira uma descrição e escolha se o fluxo de trabalho manipula texto ou imagens.

  ![Nome e descrição do fluxo de trabalho](images/ocr-workflow-step-1.PNG)

## <a name="define-the-evaluation-criteria-condition"></a>Defina os critérios de avaliação ("condição")

Na captura de tela a seguir, você verá os campos e as seleções de If-Then-Else que precisam ser definidas para seus fluxos de trabalho. Escolha um conector. Este exemplo usa **Content Moderator**. Dependendo do conector que você escolher, as opções disponíveis para saída alteram.

  ![Definir condição de fluxo de trabalho](images/ocr-workflow-step-2-condition.PNG)

Depois de escolher o conector e a saída que você deseja, selecione um operador e o valor para a condição.

## <a name="define-the-action-to-take"></a>Definir a ação a ser executada

Selecione a ação a ser executada e a condição a ser atendida. O exemplo a seguir cria uma revisão de imagem, atribui uma marca `a` e destaca a condição mostrada. Você também pode combinar vários critérios para obter os resultados desejados. Opcionalmente, adicione um caminho alternativo (Else).

  ![Definir ação do fluxo de trabalho](images/ocr-workflow-step-3-action.PNG)

## <a name="save-your-workflow"></a>Salvar seu fluxo de trabalho

Por fim, salve o fluxo de trabalho e observe o nome do fluxo de trabalho. Você precisará do nome para iniciar um trabalho de moderação usando a API de análise.

## <a name="test-the-workflow"></a>Testar o fluxo de trabalho

Agora que você definiu um fluxo de trabalho personalizado, teste-o com o conteúdo de exemplo.

Selecione o botão **Executar o fluxo de trabalho** correspondente.

  ![Testar fluxo de trabalho](images/ocr-workflow-step-6-list.PNG)

### <a name="upload-a-file"></a>Carregar um arquivo

Salve a [imagem de exemplo](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) na unidade local. Para testar o fluxo de trabalho, selecione **Escolher arquivo(s)** e carregar a imagem.

  ![Carregar arquivo imagem](images/ocr-workflow-step-7-upload.PNG)

### <a name="track-the-workflow"></a>Monitorar o fluxo de trabalho

Monitore o fluxo de trabalho conforme ele é executado.

  ![Monitorar a execução do fluxo de trabalho](images/ocr-workflow-step-4-test.PNG)

### <a name="review-any-images-flagged-for-human-moderation"></a>Examinar as imagens sinalizadas para moderação humana

Para ver a análise de imagem, navegue até a guia **Imagem** em **Análise**.

  ![Analisar imagens](images/ocr-sample-image-workflow1.PNG)

## <a name="next-steps"></a>Próximas etapas 

Para invocar o fluxo de trabalho do código, use fluxos de trabalho personalizados com o [`Job`início rápido do console da API](../try-review-api-job.md) e [início rápido do SDK do .NET](../moderation-jobs-quickstart-dotnet.md).
