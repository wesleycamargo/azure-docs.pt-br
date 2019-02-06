---
title: Criar insights de vídeo com base em vídeos existentes – Azure
titlesuffix: Azure Media Services
description: Este tópico mostra como criar e publicar insights de vídeo com base em arquivos de vídeo existentes.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 2f6ceeebd18a91472ee12f04c0ac8e602b05f269
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197544"
---
# <a name="create-highlights-from-existing-videos"></a>Criar destaques com base em vídeos existentes

Este tópico mostra como criar e publicar insights de vídeo com base em algum outro vídeo.

1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.
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
