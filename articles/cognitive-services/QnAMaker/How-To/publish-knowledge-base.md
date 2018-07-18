---
title: Como publicar uma base de dados de conhecimento - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Como publicar uma base de dados de conhecimento
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e9dbeacfb0df98c6b8f084c263690c05fe966cdc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364209"
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
