---
title: Ciclo de vida da base de dados de conhecimento – QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker aprende melhor em um ciclo iterativo de alterações de modelo, exemplos de expressão, publicação e coleta de dados de consultas de ponto de extremidade.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: f0b2e1afdc42d8aaa0ab8d3af76f51fb6ded24e0
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857759"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Ciclo de vida da base de dados de conhecimento no QnA Maker
O QnA Maker aprende melhor em um ciclo iterativo de alterações de modelo, exemplos de expressão, publicação e coleta de dados de consultas de ponto de extremidade. 

![Ciclo de criação](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Criar uma base de dados de conhecimento do QnA Maker
O ponto de extremidade de KB (base de dados de conhecimento) do QnA Maker fornece uma resposta de melhor correspondência a uma consulta do usuário com base no conteúdo da base de dados de conhecimento. Criar uma base de dados de conhecimento é uma ação única para configurar um repositório de conteúdo de perguntas, respostas e metadados associados. Uma base de dados de conhecimento pode ser criada, rastreando conteúdo pré-existente como páginas de perguntas frequentes, manuais de produtos ou pares P-R estruturados. Saiba como [criar uma base de dados de conhecimento](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testar e atualizar a base de dados de conhecimento
A base de dados de conhecimento está pronta para testes, uma vez que é preenchida com conteúdo, seja editorialmente ou através de extração automática. O teste pode ser feito através do painel de **Teste**, inserindo consultas comuns de usuários e verificando se as respostas retornadas são as esperadas e com pontuação de confiança suficiente. É possível adicionar perguntas alternativas para corrigir as pontuações de confiança baixas. Além disso, é possível adicionar novas respostas quando uma consulta retorna a resposta padrão "nenhuma correspondência encontrada na base de dados de conhecimento". Este loop estreito de atualização de teste continuará até que você esteja satisfeito com os resultados. Saiba como [testar a base de dados de conhecimento](../How-To/test-knowledge-base.md).

Para KBs grandes, o teste pode ser automatizado por meio de APIs do generateAnswer. 

## <a name="publish-the-knowledge-base"></a>Publicar a base de dados de conhecimento
Quando terminar de testar a base de dados de conhecimento, você poderá publicá-la. Publicar efetua push da última versão da base de dados de conhecimento testada para um índice do Azure Search dedicado que representa a base de dados de conhecimento **publicada**. Isso também cria um ponto de extremidade que pode ser chamado no aplicativo ou chat bot.

Dessa forma, quaisquer alterações feitas na versão de teste da base de dados de conhecimento não afetam a versão publicada que pode estar ativa em um aplicativo de produção.

Cada uma dessas bases de dados de conhecimento pode ser direcionada para testes separadamente. Usando as APIs, é possível direcionar a versão de teste da base de dados conhecimento com marca `isTest=true` na chamada do generateAnswer.

Saiba como [publicar a base de dados de conhecimento](../How-To/publish-knowledge-base.md).

## <a name="monitor-usage"></a>Monitorar o uso
Para registrar os logs de chat do seu serviço, é necessário habilitar o Application Insights ao [criar o serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

É possível obter várias análises do uso do serviço. Saiba mais sobre como usar o Application Insights para obter [análises do serviço do QnA Maker](../How-To/get-analytics-knowledge-base.md).

De acordo com o que aprende-se com as análises, faça as [atualizações da base de dados de conhecimento](../How-To/edit-knowledge-base.md) apropriadas.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Pontuação de confiança](./confidence-score.md)

## <a name="see-also"></a>Consulte também 

[Base de Conhecimento](./knowledge-base.md)
[Visão geral do QnA Maker](../Overview/overview.md)
