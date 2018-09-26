---
title: Exibir e editar informações do Video Indexer
titlesuffix: Azure Cognitive Services
description: Este tópico demonstra como exibir e editar insights do Video Indexer.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: c9b229e2fb3297d724ec8de02bf54e9765689ab7
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984532"
---
# <a name="view-and-edit-video-indexer-insights"></a>Exibir e editar informações do Video Indexer

Este tópico mostra como exibir e editar os insights do Video Indexer de um vídeo.

1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.
2. Encontre um vídeo do qual você deseja criar seus insights do Video Indexer. Para obter mais informações, consulte [Localizar os minutos exatos nos vídeos](video-indexer-search.md).
3. Pressione **Reproduzir**.

    A página mostra os insights resumidos do vídeo. 

    ![Insights](./media/video-indexer-view-edit/video-indexer-summarized-insights.png)

4. Veja os insights resumidos do vídeo. 

    Os insights resumidos mostram uma exibição agregada dos dados: faces (rostos), keywords (palavras-chave), sentiments (sentimentos). Por exemplo, é possível ver os rostos das pessoas e os intervalos de tempo nos quais cada rosto é exibido e a porcentagem de tempo mostrado.

    O player e os insights são sincronizados. Por exemplo, se clicar em uma palavra-chave ou em linha de transcrição, o player direcionará você a esse momento no vídeo. É possível obter a exibição do player/insights e a sincronização no seu aplicativo. Para obter mais informações, consulte [Embed Azure Indexer widgets into your application](video-indexer-embed-widgets.md) (Inserir widgets do Azure Indexer em seu aplicativo). 

3. Edite os insights do Video Indexer.

    Pressione Editar embaixo do vídeo. A página mostrando a análise completa de um vídeo é exibida. A análise é dividida em blocos. Os blocos estão aqui para tornar mais fácil percorrer os dados. Por exemplo, o bloco pode ser dividido com base em quando os locutores mudam ou quando há uma longa pausa. É possível criar sua própria playlist que contém apenas as linhas desejadas. Para mostrar apenas partes específicas do vídeo de origem, é possível filtrar por topics/keywords (tópicos/palavras-chave), sentiments (sentimentos), people (pessoas), speakers (locutores). É possível optar por exibir apenas a transcrição ou o OCR do vídeo.  

    ![Insights](./media/video-indexer-view-edit/video-indexer-create-new-playlist.png)

## <a name="next-steps"></a>Próximas etapas

[Saiba como criar seus próprios insights do Video Indexer com base em algum outro vídeo](video-indexer-create-new.md).

## <a name="see-also"></a>Consulte também

[Visão geral do indexador vídeo](video-indexer-overview.md)

