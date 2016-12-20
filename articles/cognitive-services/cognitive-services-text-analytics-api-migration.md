---
title: "Atualização para a Versão 2 da API da Análise de Texto | Microsoft Docs"
description: "Análise de Texto do Aprendizado de Máquina do Azure — Atualizar para a versão 2"
services: cognitive-services
documentationcenter: 
author: onewth
manager: jhubbard
editor: cgronlun
ms.assetid: bbf86f80-f677-42f3-8c17-118b16a23c34
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: onewth
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9b28666e384402935ae342788afff6b6e8c7eab8


---
# <a name="upgrading-to-version-2-of-the-text-analytics-api"></a>Atualizando para a versão 2 da API de Análise de Texto
Este guia explicará o processo de atualizar seu código, desde o uso da [primeira versão da API](../machine-learning/machine-learning-apps-text-analytics.md) até o uso da segunda versão. 

Se você não usou a API e gostaria de aprender mais, poderá **[saber mais sobre a API aqui](//go.microsoft.com/fwlink/?LinkID=759711)** ou **[seguir o Guia de Início Rápido](//go.microsoft.com/fwlink/?LinkID=760860)**. Para ter uma referência técnica, consulte a **[Definição da API](//go.microsoft.com/fwlink/?LinkID=759346)**.

### <a name="part-1-get-a-new-key"></a>Parte 1. Obter uma nova chave
Primeiro, você precisará obter uma nova chave de API no **Portal do Azure**:

1. Navegue até o serviço Análise de Texto usando a [Galeria do Cortana Intelligence](//gallery.cortanaintelligence.com/MachineLearningAPI/Text-Analytics-2). Aqui, você também encontrará links para a documentação e os exemplos de código.
2. Clique em **Inscreva-se**. Esse link levará você ao Portal de gerenciamento do Azure, onde é possível se inscrever para o serviço.
3. Escolha um plano. Você pode escolher a **camada gratuita para 5.000 transações/mês**. Por ser um plano gratuito, você não será cobrado pelo uso do serviço. Você precisará fazer logon em sua assinatura do Azure. 
4. Após inscrever-se para obter a Análise de Texto, você receberá uma **Chave de API**. Copie essa chave, pois você precisará dela ao usar os serviços de API.

### <a name="part-2-update-the-headers"></a>Parte 2. Atualizar os cabeçalhos
Atualize os valores de cabeçalho enviados, conforme mostrado abaixo. Observe que a chave da conta não é mais codificada.

**Versão 1**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**Versão 2**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### <a name="part-3-update-the-base-url"></a>Parte 3. Atualizar a URL base
**Versão 1**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**Versão 2**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### <a name="part-4a-update-the-formats-for-sentiment-key-phrases-and-languages"></a>Parte 4a. Atualizar os formatos para sentimento, frases principais e idiomas.
#### <a name="endpoints"></a>Pontos de extremidade
Os pontos de extremidade GET foram preteridos, portanto, todas as entradas devem ser enviadas como uma solicitação POST. Atualize os pontos de extremidade para aqueles mostrados abaixo.

|  | Único ponto de extremidade da versão 1 | Ponto de extremidade em lote da versão 1 | Ponto de extremidade da versão 2 |
| --- | --- | --- | --- |
| Tipo de chamada |GET |POST |POST |
| Sentimento |```GetSentiment``` |```GetSentimentBatch``` |```sentiment``` |
| Frases principais |```GetKeyPhrases``` |```GetKeyPhrasesBatch``` |```keyPhrases``` |
| Idiomas |```GetLanguage``` |```GetLanguageBatch``` |```languages``` |

#### <a name="input-formats"></a>Formatos de entrada
Observe que apenas o formato POST é aceito agora, de modo que você deverá formatar novamente todas as entradas que anteriormente usavam os pontos de extremidade de documento único adequadamente. As entradas não diferenciam maiúsculas de minúsculas.

**Versão 1 (lote)**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Versão 2**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="output-from-sentiment"></a>Saída de sentimento
**Versão 1**

    {
      "SentimentBatch":[{
        "Id":"string",
        "Score":"double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versão 2**

    {
      "documents":[{
        "id":"string",
        "score":"double"
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-key-phrases"></a>Saída de frases principais
**Versão 1**

    {
      "KeyPhrasesBatch":[{
        "Id":"string",
        "KeyPhrases":["string"]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versão 2**

    {
      "documents":[{
        "id":"string",
        "keyPhrases":["string"]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-languages"></a>Saída de idiomas
**Versão 1**

    {
      "LanguageBatch":[{
        "id":"string",
        "detectedLanguages": [{
          "Score":"double"
          "Name":"string",
          "Iso6391Name":"string"
        }]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versão 2**

    {
      "documents":[{
        "id":"string",
        "detectedLanguages": [{
          "score":"double"
          "name":"string",
          "iso6391Name":"string"
        }]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }


### <a name="part-4b-update-the-formats-for-topics"></a>Parte 4b. Atualizar os formatos de tópicos
#### <a name="endpoints"></a>Pontos de extremidade
|  | Ponto de extremidade da versão 1 | Ponto de extremidade da versão 2 |
| --- | --- | --- |
| Enviar para detecção de tópico (POST) |```StartTopicDetection``` |```topics``` |
| Buscar resultados do tópico (GET) |```GetTopicDetectionResult?JobId=<jobId>``` |```operations/<operationId>``` |

#### <a name="input-formats"></a>Formatos de entrada
**Versão 1**

    {
      "StopWords": [
        "string"
      ],
      "StopPhrases": [
        "string"
      ], 
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Versão 2**

    {
      "stopWords": [
        "string"
      ],
      "stopPhrases": [
        "string"
      ],
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="submission-results"></a>Resultados do envio
**Versão 1 (POST)**

Anteriormente, quando o trabalho era concluído, você recebia a saída JSON a seguir, em que a jobId seria anexada a uma URL para buscar a saída.

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**Versão 2 (POST)**

A resposta agora incluirá um valor de cabeçalho, como se segue, em que `operation-location` é usado como o ponto de extremidade para sondar os resultados:

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### <a name="operation-results"></a>Resultados da operação
**Versão 1 (GET)**

    {
      "TopicInfo" : [{
        "TopicId" : "string"
        "Score" : "double"
        "KeyPhrase" : "string"
      }],
      "TopicAssignment" : [{
        "Id" : "string",
        "TopicId" : "string",
        "Distance" : "double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versão 2 (GET)**

Como antes, **sonde a saída periodicamente** (o período sugerido é a cada minuto) até que a saída seja retornada. 

Quando a API de tópicos for concluída, um status apresentando `succeeded` será retornado. Isso incluirá os resultados da saída no formato mostrado abaixo:

    {
        "status": "succeeded",
        "createdDateTime": "string",
        "operationType": "topics",
        "processingResult": {
            "topics" : [{
            "id" : "string"
            "score" : "double"
            "keyPhrase" : "string"
          }],
          "topicAssignments" : [{
            "topicId" : "string",
            "documentId" : "string",
            "distance" : "double"
          }],
          "errors" : [{
              "id":"string",
              "message":"string"
          }]
        }
    }

### <a name="part-5-test-it"></a>Parte 5. Testar
Agora você está pronto para seguir adiante! Teste seu código com um pequeno exemplo para garantir que você possa processar seus dados com êxito.




<!--HONumber=Nov16_HO3-->


