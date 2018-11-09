---
title: Pontuação de Confiança – Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Explicação sobre a pontuação de confiança
services: cognitive-services
author: tulasim88
manager: pchoudh
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 09/27/2018
ms.author: tulasim
ms.openlocfilehash: 46b6b789b2bab7e647e10a61939bd16b5ea8726d
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209678"
---
# <a name="confidence-score"></a>Pontuação de confiança
Quando uma consulta de usuário é comparada com uma base de conhecimento, o QnA Maker retorna respostas relevantes, juntamente com uma pontuação de confiança. Essa pontuação indica a confiança de que a resposta é a correspondência ideal da consulta do usuário. 

A pontuação de confiança é um número entre 0 e 100. Uma pontuação 100 é, provavelmente, uma correspondência exata; no entanto, uma pontuação 0 significa que nenhuma resposta correspondente foi encontrada. Quanto maior a pontuação, maior a confiança na resposta. Pode haver várias respostas retornadas para uma determinada consulta. Nesse caso, as respostas são retornadas em uma ordem de pontuação de confiança decrescente.

No exemplo abaixo, você pode ver uma entidade QnA com duas perguntas. 


![Par QnA de exemplo](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

No caso do exemplo acima, você pode esperar pontuações como o intervalo de pontuação de exemplo abaixo para diferentes tipos de consultas de usuário:


![Intervalo de pontuação do classificador](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


A tabela a seguir indica a confiança típica associada a uma determinada pontuação.

|Valor da pontuação|Significado da pontuação|Consulta de exemplo|
|--|--|--|
|90 - 100|Uma correspondência quase exata da consulta de usuário e uma pergunta da KB|"Minhas alterações não são atualizadas na base de conhecimento após a publicação"|
|> 70|Alta confiança – geralmente uma boa resposta que responde por completo à consulta do usuário|"Publiquei minha Base de conhecimento, mas ela não está atualizada"|
|50 - 70|Confiança média – normalmente, uma resposta relativamente boa que deve responder à intenção principal da consulta do usuário|"Devo salvar minhas atualizações antes de publicar minha Base de conhecimento?"|
|30 - 50|Pouca confiança – tipicamente uma resposta relacionada que responde parcialmente à intenção do usuário|"O que salvar e treinar faz?"|
|< 30|Muito pouca confiança – normalmente não responde à consulta do usuário, mas apresenta algumas palavras ou frases correspondentes |"Onde posso adicionar sinônimos à minha Base de conhecimento"|
|0|Nenhuma correspondência, portanto, a resposta não é retornada.|"Quanto custa o serviço"|

## <a name="choose-a-score-threshold"></a>Escolher um limite de pontuação
A tabela acima mostra as pontuações que são esperadas na maioria das Bases de conhecimento. No entanto, como cada base é diferente e tem diferentes tipos de palavras, intenções e metas, recomendamos que você teste e escolha o limite que melhor funciona para você. O limite padrão e recomendado que deve funcionar para a maioria das Bases de conhecimento é **50**.

Ao escolher seu limite, tenha em mente o equilíbrio entre Precisão e Cobertura e ajuste seu limite com base em seus requisitos.

- Se **Precisão** for mais importante para seu cenário, aumente seu limite. Dessa forma, sempre que você retornar uma resposta, será muito mais SEGURO e muito mais provável que seja a resposta que os usuários estão procurando. Nesse caso, você pode acabar deixando mais perguntas sem resposta. *Por exemplo:* se seu limite for **70**, é provável que você perca alguns exemplos ambíguos como "o que é salvar e treinar?".

- Se a **Cobertura** (ou o retorno de chamada) for mais importante, e você quiser responder ao maior número de perguntas possível, mesmo que haja apenas uma relação parcial com a pergunta do usuário, então DIMINUA o limite. Isso significa que pode haver mais casos em que a resposta não responda à consulta real do usuário, mas forneça alguma outra resposta relacionada. *Por exemplo:* se você estabelecer o limite como **30**, poderá fornecer respostas não muito relacionadas, como, respondendo com o exemplo acima, para consultas como "Onde posso editar minha Base de conhecimento?"


## <a name="improve-confidence-scores"></a>Melhorar as pontuações de confiança
Para melhorar a pontuação de confiança de uma resposta específica a uma consulta de usuário, você poderá adicionar a consulta do usuário à base de dados de conhecimento como uma pergunta alternativa na resposta.


## <a name="similar-confidence-scores"></a>Pontuações de confiança semelhantes
Quando várias respostas tiverem uma pontuação de confiança semelhante, é provável que a consulta tenha sido muito genérica e, portanto, com correspondências com probabilidades iguais com várias respostas. Tente estruturar melhor suas perguntas e respostas para que cada entidade QnA tenha uma intenção distinta.


## <a name="confidence-score-differences"></a>Diferenças de pontuação de confiança
A pontuação de confiança de resposta pode alterar pouco entre o teste e a versão publicada da base de dados de conhecimento, mesmo se o conteúdo for o mesmo. Isso ocorre porque o conteúdo do teste e a base de dados de conhecimento publicada estão localizados em diferentes índices do Azure Search.
Veja aqui como funciona a operação [publicar](../How-To/publish-knowledge-base.md).


## <a name="no-match-found"></a>Nenhuma correspondência encontrada
Quando nenhuma boa correspondência for encontrada pelo classificador, a pontuação de confiança 0,0 ou "Nenhuma" é retornada e a resposta padrão é "Nenhuma correspondência boa encontrada na KB". Você pode substituir essa resposta padrão no código do aplicativo ou do bot chamando o ponto de extremidade. Como alternativa, você também pode definir a resposta de substituição no Azure, e isso altera o padrão para todas as bases de dados de conhecimento implantadas em um determinado serviço QnA Maker.

### <a name="change-default-answer"></a>Alterar a resposta padrão

1. Vá para o [portal do Azure](https://portal.azure.com) e navegue até o grupo de recursos que representa o serviço QnA Maker que você criou.

2. Clique para abrir o **Serviço de Aplicativo**.

    ![Acessar o serviço Aplicativo](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Clique em **Configurações do Aplicativo** e edite o campo **DefaultAnswer** para a resposta padrão desejada. Clique em **Salvar**.

    ![Alterar resposta padrão](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Reinicie o serviço Aplicativo

    ![Reinício do serviço de aplicativo QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Fontes de dados com suporte](./data-sources-supported.md)
## <a name="see-also"></a>Consulte também 
[Visão geral do QnA Maker](../Overview/overview.md)
