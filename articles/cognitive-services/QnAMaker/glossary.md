---
title: Glossário – QnA Maker
titleSuffix: Azure Cognitive Services
description: O serviço do QnA Maker tem muitos novos termos de processamento de linguagem natural e aprendizado de máquina, bem como termos específicos do serviço. Essa lista ajudará você a entender esses termos.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 832bf85087b706451a6ee58c538d7a163ef18203
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079935"
---
# <a name="glossary-for-qna-maker-knowledge-base-and-service"></a>Glossário do serviço e base de dados de conhecimento do QnA Maker

## <a name="qna-maker-service"></a>Serviço QnA Maker
Um serviço QnA Maker é um pré-requisito para começar a usar o QnA Maker. Comprar uma camada QnA Maker define os recursos em sua assinatura do Azure para criar e gerenciar a sua base de dados de conhecimento. Cada conta de usuário do QnA Maker pode criar vários serviços QnA Maker em sua assinatura do Azure.

## <a name="knowledge-base"></a>Base de Dados de Conhecimento
Uma base de dados de conhecimento é o repositório de perguntas e respostas criado, mantido e usado por meio do QnA Maker. Cada camada do QnA Maker pode ser usada para várias bases de dados de conhecimento.

## <a name="endpoint"></a>Ponto de extremidade
Um ponto de extremidade HTTP baseado em REST mantendo o conteúdo da sua base de conhecimento que pode ser integrada ao seu aplicativo, geralmente um chatbot. 

## <a name="test-knowledge-base"></a>Base de Dados de Conhecimento de Teste
Uma base de dados de conhecimento tem dois estados - testada e publicada. A base de dados de conhecimento de teste é a versão que está sendo editada, salva e testada para a precisão e a integridade das respostas. As alterações feitas na base de dados de conhecimento de teste não afetam o usuário final do seu aplicativo/chatbot.

## <a name="published-knowledge-base"></a>Base de Dados de Conhecimento Publicada
Uma base de dados de conhecimento tem dois estados - Testada e Publicada.  A base de dados de conhecimento publicada é a versão que é usada no aplicativo/bot de chat. A ação de publicação de uma base de dados de conhecimento coloca o conteúdo da base de dados de conhecimento de Teste na versão Publicada da base de dados de conhecimento. Como a base de dados de conhecimento publicada é a versão que usa o aplicativo por meio do ponto de extremidade, tome cuidado para garantir que o conteúdo está correto e bem testado.

## <a name="query"></a>Consultar
Uma consulta de usuário é a pergunta que o usuário final ou o testador faz para a base de dados de conhecimento. A consulta é feita geralmente em um formato de idioma natural ou em algumas palavras-chave que representam a pergunta.

## <a name="response"></a>Response
A resposta é a resposta recuperada da base de dados de conhecimento, com base na melhor correspondência para uma consulta de determinado usuário.

## <a name="confidence-score"></a>Pontuação de Confiança
A pontuação de confiança da resposta é um valor numérico entre 0 e 100, onde 100 é uma correspondência exata de consulta entre a consulta de usuário e uma pergunta na base de dados de conhecimento, em que a resposta fornecida é a resposta correta, apropriada para uma consulta de determinado usuário. As respostas normalmente são classificadas pela pontuação de confiança e com a pontuação de confiança superior é fornecido como a resposta padrão.
