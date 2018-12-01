---
title: Localizar momentos exatos em vídeos – Video Indexer
titlesuffix: Azure Media Services
description: Este tópico demonstra como localizar momentos exatos em vídeos usando o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: bbe26b19f9467034a4612ac1b40124122bee7f0c
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291647"
---
# <a name="find-exact-moments-within-videos"></a>Localizar os minutos exatos nos vídeos

Este tópico mostra as opções de pesquisa que permitem que você localize os minutos exatos nos vídeos.

1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.
2. Pesquise entre todos os vídeos da sua conta.

    No exemplo a seguir, pesquisamos todos os vídeos criados pelo Channel9 com Scott Hanselman.

    ![Search](./media/video-indexer-search/video-indexer-search01.png)
    
3. Pesquise os insights resumidos do vídeo.

    Em seguida, é possível pesquisar dentro de um vídeo clicando em **Reproduzir** no vídeo. Em seguida, é possível pesquisar no vídeo selecionando a guia **Pesquisar**. Por exemplo, procuramos todos os locais em que o texto "proteção de identidade" é usado. 

    ![Search](./media/video-indexer-search/video-indexer-search02.png)

    Se você clicar em um dos resultados, o player direcionará você até esse momento no vídeo. É possível obter a exibição do player/insights e a sincronização no seu aplicativo. Para obter mais informações, consulte [Embed Video Indexer widgets into your application](video-indexer-embed-widgets.md) (Inserir widgets do Video Indexer em seu aplicativo). 

    
4. Pesquise a análise detalhada do vídeo.

    Se desejar criar sua própria análise com base no vídeo encontrado, pressione o botão **Editar**. Esta página mostra a análise completa de um vídeo. É possível pesquisar na análise para mostrar apenas as linhas nas quais você está interessado. Para obter mais informações, consulte [Exibir e editar insights do Video Indexer](video-indexer-view-edit.md).

    Nesse exemplo, pesquisamos o texto "proteção de identidade". Também aplicamos filtros adicionais, conforme mostrado na tela abaixo.

    ![Search](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Próximas etapas 

Depois de encontrar o vídeo com o qual você deseja trabalhar, será possível continuar processando-o, conforme descrito em um destes tópicos: 

- [Criar novos insights de vídeo com base no vídeo existente](video-indexer-create-new.md)
- [Processar conteúdo com a API REST do Video Indexer](video-indexer-use-apis.md)
- [Inserir widgets visuais em seu aplicativo](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Consulte também

[Visão geral do indexador vídeo](video-indexer-overview.md)
