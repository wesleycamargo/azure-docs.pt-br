---
title: Pontuação de confiança – QnA Maker
titleSuffix: Azure Cognitive Services
description: Uma pontuação de confiança indica o grau de correspondência entre a pergunta de usuário e a resposta retornada.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 33da5cf5724b8314ce813f12eb077d9a15ec1b2a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041516"
---
# <a name="confidence-score"></a>Pontuação de Confiança

Uma pontuação de confiança indica o grau de correspondência entre a pergunta de usuário e a resposta retornada.

Quando uma consulta de usuário é comparada ao conteúdo da base de dados de conhecimento, pode haver mais de uma resposta retornada. As respostas são retornadas em uma ordem de classificação decrescente de pontuação de confiança.

Uma pontuação de confiança está entre 0 e 100.

|Valor da pontuação|Confiança|
|--|--|
|100|Uma correspondência exata de consulta de usuário e uma pergunta da KB|
|90|Confiança alta - a maioria das palavras corresponde|
|40-60|Confiança razoável - palavras importantes correspondem|
|10|Confiança baixa - palavras importantes não correspondem|
|0|Nenhuma correspondência de palavra|


## <a name="similar-confidence-scores"></a>Pontuações de confiança semelhantes
Quando várias respostas tiverem uma pontuação de confiança semelhante, é provável que a consulta tenha sido muito genérica e, portanto, com correspondências com probabilidades iguais com várias respostas. Tente estruturar melhor suas perguntas e respostas para que cada entidade QnA tenha uma intenção distinta.


## <a name="improving-confidence-scores"></a>Como melhorar pontuações de confiança
Para melhorar a pontuação de confiança de uma resposta específica a uma consulta de usuário, você poderá adicionar a consulta do usuário à base de dados de conhecimento como uma pergunta alternativa na resposta.
   
## <a name="confidence-score-differences"></a>Diferenças de pontuação de confiança
A pontuação de confiança de resposta pode alterar pouco entre o teste e a versão publicada da base de dados de conhecimento, mesmo se o conteúdo for o mesmo. Isso ocorre porque o conteúdo do teste e a base de dados de conhecimento publicada estão localizados em diferentes índices do Azure Search.

Veja aqui como funciona a operação [publicar](../How-To/publish-knowledge-base.md).


## <a name="no-match-found"></a>Nenhuma correspondência encontrada
Quando nenhuma boa correspondência for encontrada pelo classificador, a pontuação de confiança 0,0 ou "Nenhuma" é retornada e a resposta padrão é "Nenhuma correspondência boa encontrada na KB". Você pode substituir essa resposta padrão no código do aplicativo ou do bot chamando o ponto de extremidade. Como alternativa, você também pode definir a resposta de substituição no Azure, e isso altera o padrão para todas as bases de dados de conhecimento implantadas em um determinado serviço QnA Maker.

1. Vá para o [portal do Azure](http://portal.azure.com) e navegue até o grupo de recursos que representa o serviço QnA Maker que você criou.

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
