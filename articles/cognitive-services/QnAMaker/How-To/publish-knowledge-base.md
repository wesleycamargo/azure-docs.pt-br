---
title: Como publicar uma base de dados de conhecimento
titleSuffix: QnA Maker API - Azure Cognitive Services
description: A publicação de sua base de dados de conhecimento com o serviço de API do QnA Maker é a etapa final para tornar sua base de dados de conhecimento disponível como um ponto de extremidade responder a pergunta. Ao publicar uma base de dados de conhecimento, o conteúdo do QnA da base de dados de conhecimento é movido do índice de teste para um índice de produção na pesquisa do Azure.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/11/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c65654e7d6b2e66a07116ea9555ed316ace88912
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091946"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Publicar uma base de dados de conhecimento usando o portal do serviço de API do QnA Maker

A publicação de sua base de dados de conhecimento com o serviço de API do QnA Maker é a etapa final para tornar sua base de dados de conhecimento disponível como um ponto de extremidade responder a pergunta. 

Ao publicar uma base de dados de conhecimento, o conteúdo de perguntas e respostas base de dados de conhecimento é movido do índice de teste para um índice de produção no Azure Search.

![Publicar índice de teste de produção](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="publish-a-knowledge-base"></a>Como publicar uma base de dados de conhecimento

1. Após fazer as alterações na base de dados de conhecimento, selecione **Publicar** na barra de navegação superior. É possível publicar até o número alocado de bases de dados de conhecimento para o Azure Search. 

    ![Publicar base de dados de conhecimento](../media/qnamaker-how-to-publish-kb/publish.png)

2. Selecione **Publicar** novamente para ver os detalhes do ponto de extremidade que podem ser usados no aplicativo ou código bot.

    ![Base de dados de conhecimento publicada com êxito](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar de usar a base de dados de conhecimento, remova a base no portal do QnA Maker.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Obter análises na base de dados de conhecimento](./get-analytics-knowledge-base.md)
