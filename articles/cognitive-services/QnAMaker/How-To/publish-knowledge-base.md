---
title: Publicar uma base de dados de conhecimento – QnA Maker
titleSuffix: Azure Cognitive Services
description: A publicação da base de dados de conhecimento é a etapa final para tornar a base de dados de conhecimento disponível como um ponto de extremidade de resposta a perguntas. Ao publicar uma base de dados de conhecimento, o conteúdo do QnA da base de dados de conhecimento é movido do índice de teste para um índice de produção na pesquisa do Azure.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: eceb0554d35935f1aee77c4c054cb7e65b327845
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033406"
---
# <a name="publish-a-knowledge-base"></a>Como publicar uma base de dados de conhecimento

A publicação da base de dados de conhecimento é a etapa final para tornar a base de dados de conhecimento disponível como um ponto de extremidade de resposta a perguntas. 

Ao publicar uma base de dados de conhecimento, o conteúdo do QnA da base de dados de conhecimento é movido do índice de teste para um índice de produção na pesquisa do Azure.

![Publicar índice de teste de produção](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>Como publicar uma base de dados de conhecimento

1. Após fazer as alterações na base de dados de conhecimento, selecione **Publicar** na barra de navegação superior. É possível publicar até o número alocado de bases de dados de conhecimento para o Azure Search. 

    ![Publicar base de dados de conhecimento](../media/qnamaker-how-to-publish-kb/publish.png)

2. Selecione **Publicar** novamente para ver os detalhes do ponto de extremidade que podem ser usados no aplicativo ou código bot.

    ![Publicar base de dados de conhecimento](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Obter análises na base de dados de conhecimento](./get-analytics-knowledge-base.md)
