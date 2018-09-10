---
title: Usar Azure Video Indexer para criar insights de vídeo com base em vídeos existentes | Microsoft Docs
description: Este tópico mostra como criar e publicar insights de vídeo com base em algum outro vídeo.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: 161a47f72a0f8038a515c09f25ec2a8e8a520547
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390833"
---
# <a name="create-highlights-from-existing-videos"></a>Criar destaques com base em vídeos existentes

Este tópico mostra como criar e publicar insights de vídeo com base em algum outro vídeo.

1. Entre na sua conta do [Video Indexer](https://api-portal.videoindexer.ai/).
2. Encontre um vídeo do qual você deseja criar seus insights de vídeo.
3. Pressione **Reproduzir**.

    A página mostra os insights resumidos do vídeo. 

    ![Insights](./media/video-indexer-create-new/video-indexer-summarized-insights.png)

3. Pressione o botão **Editar**.

    Esta página mostra a análise completa de um vídeo. A análise é dividida em blocos. Os blocos estão aqui para tornar mais fácil percorrer os dados. Por exemplo, o bloco pode ser dividido com base em quando os locutores mudam ou quando há uma longa pausa. É possível criar sua própria playlist que contém apenas as linhas desejadas. Para mostrar apenas partes específicas do vídeo de origem, é possível filtrar por topics/keywords (tópicos/palavras-chave), sentiments (sentimentos), people (pessoas), speakers (locutores). É possível optar por exibir apenas a transcrição ou o OCR do vídeo.    

    ![Insights](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)

4. Crie sua playlist.

    Para adicionar ou remover linhas à/da sua playlist, pressione **+**/**-**.

5. Visualize sua playlist.

    Depois de terminar de criar a playlist, pressione **Visualizar**.
6. Publique a playlist.

    Depois de visualizar a playlist, será possível publicá-la.

    Depois de publicar a playlist, ela será adicionada à lista dos seus insights de vídeo.


## <a name="next-steps"></a>Próximas etapas 

Depois de criar a nova playlist, será possível continuar processando-a, conforme descrito em um destes tópicos: 

- [Processar conteúdo com a API REST do Video Indexer](video-indexer-use-apis.md)
- [Inserir widgets visuais em seu aplicativo](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Consulte também

[Visão geral do indexador vídeo](video-indexer-overview.md) 
