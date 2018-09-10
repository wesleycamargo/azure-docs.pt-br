---
title: Glossário - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Glossário
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e28cddec005cb6ba99b9f60d8b03a11f1bc97062
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364260"
---
# <a name="glossary"></a>Glossário

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
