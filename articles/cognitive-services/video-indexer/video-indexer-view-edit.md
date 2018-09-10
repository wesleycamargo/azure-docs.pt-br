---
title: Exibir e editar insights do Azure Video Indexer | Microsoft Docs
description: Este tópico demonstra como exibir e editar insights do Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 797c09d72402cfc1ee2524e7792cc1310a53fb1e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399470"
---
# <a name="view-and-edit-video-indexer-insights"></a>Exibir e editar informações do Video Indexer

Este tópico mostra como exibir e editar os insights do Video Indexer de um vídeo.

1. Entre na sua conta do [Video Indexer](https://api-portal.videoindexer.ai/).
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

