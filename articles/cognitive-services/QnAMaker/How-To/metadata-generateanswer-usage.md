---
title: Metadados com a API de GenerateAnswer – QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker permite adicionar metadados na forma de pares chave-valor aos conjuntos de perguntas/respostas. Essas informações podem ser usadas para filtrar resultados para consultas de usuários e armazenar informações adicionais que podem ser usadas em conversas de acompanhamento.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 12/18/2018
ms.author: tulasim88
ms.openlocfilehash: 004f09eb77d1bc32e44e1940186e8a631c45846d
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53608470"
---
# <a name="using-metadata-and-the-generateanswer-api"></a>Usar metadados e API do GenerateAnswer

O QnA Maker permite adicionar metadados na forma de pares chave e valor aos conjuntos de perguntas/respostas. Essas informações podem ser usadas para filtrar resultados para consultas de usuários e armazenar informações adicionais que podem ser usadas em conversas de acompanhamento. Para mais informações, consulte a [Base de dados de conhecimento](../Concepts/knowledge-base.md).

## <a name="qna-entity"></a>Entidade QnA

Primeiro, é importante reconhecer como o QnA Maker armazena os dados de pergunta/resposta. A ilustração a seguir mostra uma entidade QnA:

![Entidade QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Cada entidade QnA tem uma ID exclusiva e persistente. A ID pode ser usada para fazer atualizações em uma entidade QnA específica.

## <a name="generateanswer-api"></a>API do GenerateAnswer

Você usa a API do GenerateAnswer no bot ou aplicativo para consultar a base de dados de conhecimento com uma pergunta de usuário para obter a melhor correspondência dos conjuntos de pergunta/resposta.

### <a name="generateanswer-endpoint"></a>Participante do GenerateAnswer

Após publicar a base de dados de conhecimento, a partir do [portal do QnA Maker](https://www.qnamaker.ai), ou usando a [API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff), será possível obter os detalhes do ponto de extremidade do GenerateAnswer.

Para obter os detalhes do ponto de extremidade:
1. Faça logon em [https://www.qnamaker.ai](https://www.qnamaker.ai).
2. Em **Minhas bases de dados de conhecimento**, clique em **Exibir Código** para a base de dados de conhecimento.
![minhas bases de dados de conhecimento](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
3. Obtenha os detalhes do ponto de extremidade do GenerateAnswer.

![detalhes do ponto de extremidade](../media/qnamaker-how-to-metadata-usage/view-code.png)

Também é possível obter os detalhes de ponto de extremidade na guia **Configurações** da base de dados de conhecimento.

### <a name="generateanswer-request"></a>Solicitação do GenerateAnswer

Você chama o GenerateAnswer com uma solicitação HTTP POST. Para código de exemplo que mostra como chamar o GenerateAnswer, consulte os [inícios rápidos](../quickstarts/csharp.md).

- **URL de solicitação**: https://{ponto de extremidade do QnA Maker}/knowledgebases/{ID da base de dados de conhecimento}/generateAnswer

- **Parâmetros de solicitações**: 
    - **ID da base de dados de conhecimento** (cadeia de caracteres): o GUID da base de dados de conhecimento.
    - **Ponto de extremidade do QnAMaker** (cadeia de caracteres): o nome do host do ponto de extremidade implantado na sua assinatura do Azure.
- **Cabeçalhos da solicitação**
    - **Content-Type** (cadeia de caracteres): o tipo de mídia do corpo enviado para a API.
    - **Autorização** (cadeia de caracteres): sua chave de ponto de extremidade (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).
- **Corpo da solicitação**
    - **pergunta** (cadeia de caracteres): uma pergunta do usuário a ser consultada na base de dados de conhecimento.
    - **superior** (opcional, inteiro): o número de resultados classificados para incluir na saída. O valor padrão é 1.
    - **userId** (opcional, cadeia de caracteres): ID exclusiva para identificar o usuário. Essa ID será registrada nos logs de chat.
    - **strictFilters** (opcional, cadeia de caracteres): se especificado, informa ao QnA Maker para retornar apenas as respostas com os metadados especificados. Para obter mais informações, veja abaixo.
    ```json
    {
        "question": "qna maker and luis",
        "top": 6,
        "strictFilters": [
        {
            "name": "category",
            "value": "api"
        }],
        "userId": "sd53lsY="
    }
    ```

### <a name="generateanswer-response"></a>Resposta do GenerateAnswer

- **Resposta 200** - Uma chamada com êxito retorna o resultado da pergunta. A resposta contém os seguintes campos:
    - **respostas** - Uma lista de respostas para a consulta do usuário, classificada em ordem decrescente de pontuação do ranking.
        - **pontuação**: uma pontuação de classificação entre 0 e 100.
        - **perguntas**: as perguntas fornecidas pelo usuário.
        - **resposta**: a resposta à pergunta.
        - **fonte**: o nome da fonte da qual a resposta foi extraída ou salva na base de dados de conhecimento.
        - **metadados**: os metadados associados à resposta.
            - nome: nome dos metadados. (cadeia de caracteres, Comprimento máx.: 100, obrigatório)
            - valor: valor dos metadados. (cadeia de caracteres, Comprimento máx.: 100, obrigatório)
        - **Id**: a ID exclusiva atribuída à resposta.
    ```json
    {
        "answers": [
            {
                "score": 28.54820341616869,
                "Id": 20,
                "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
                "source": "Custom Editorial",
                "questions": [
                    "How can I integrate LUIS with QnA Maker?"
                ],
                "metadata": [
                    {
                        "name": "category",
                        "value": "api"
                    }
                ]
            }
        ]
    }
    ```

## <a name="metadata-example"></a>Exemplo de metadados

Considere os dados de perguntas frequentes abaixo. Adicione metadados à base de dados de conhecimento, clicando no ícone de metadados.

![adicionar metadados](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

### <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrar resultados com strictFilters para marcas de metadados

Considere a pergunta do usuário "Quando esse hotel fecha?" onde a intenção está implícita para o restaurante "Paradise."

Como os resultados são necessários somente para o restaurante "Paradise", é possível definir um filtro na chamada do GenerateAnswer nos metadados "Nome do restaurante", conforme a seguir.

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

### <a name="keep-context"></a>Manter o contexto
A resposta ao GenerateAnswer contém as informações de metadados correspondentes do conjunto de perguntas/respostas correspondente, conforme a seguir.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

Essas informações podem ser usadas para registrar o contexto da conversa anterior e usar em conversas posteriores. 

## <a name="next-steps"></a>Próximas etapas

A página de publicação também fornece informações para gerar uma resposta com [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) e [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento](./create-knowledge-base.md)
