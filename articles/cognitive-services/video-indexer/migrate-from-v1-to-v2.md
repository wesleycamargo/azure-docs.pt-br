---
title: Migrar da API v1 do Azure Video Indexer para a v2 | Microsoft Docs
description: Este tópico explica como migrar da API v1 do Azure Video Indexer para a v2.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: b1737960a4142f5c0d949ce8c2524c34fe9cd79e
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "40187360"
---
# <a name="migrate-from-the-video-indexer-api-v1-to-v2"></a>Migrar da API v1 do Video Indexer para a v2

> [!Note]
> A API do Video Indexer V1 foi descontinuada em 1º de agosto de 2018. Agora, você deve usar a API do indexador de vídeo v2. <br/>Para desenvolver com as APIs do Video Indexer v2, consulte as instruções encontradas [aqui](https://api-portal.videoindexer.ai/). 

Este artigo descreve as alterações que foram introduzidas na v2.  

## <a name="api-changes"></a>Alterações de API

### <a name="authorization-and-operations"></a>Autorização e operações

Na versão v2, o Video Indexer alterou o modelo de autenticação e de autorização da API. Há dois conjuntos de APIs: 

* Autorização 
* Operações

A API de **autorização** é usada para obter tokens de acesso para chamar a API de **operações**. A API de **Operações** contém todas as APIs do Video Indexer, como Carregar vídeo, Obter insights e outras operações.

Depois de [assinar](video-indexer-get-started.md) a API de **Autorização**, será possível obter tokens de acesso passando a sua chave de assinatura (exatamente como você fez na v1.)

## <a name="getting-and-using-the-access-token-for-operations-apis"></a>Obtendo e usando o token de acesso para APIs de operações

Ao chamar as APIs de **operações**, a chave de assinatura não será mais usada. Em vez disso, você passará os tokens de acesso obtidos pela API de **autorização**. 

Cada solicitação deve ter um token válido, correspondendo ao nível de acesso da API que você está chamando. Por exemplo, operações em seu usuário, como obter suas contas, exigem um token de acesso de usuário. As operações no nível da conta, como listar todos os vídeos, exigem um token de acesso de conta. As operações em vídeos, como indexar vídeo novamente, requerem um token de acesso de conta ou um token de acesso de vídeo.

Para facilitar, é possível usar a API de **Autorização** > **GetAccounts** para obter suas contas sem obter primeiro um token de usuário. Você também pode pedir para obter as contas com tokens válidos, permitindo que você ignore uma chamada adicional para obter um token de conta.

Para obter mais informações sobre os diferentes tokens de acesso, consulte [Use Azure Video Indexer API](video-indexer-use-apis.md) (Usar API do Azure Video Indexer).

### <a name="locations"></a>Locais

Cada chamada à API deve incluir o local da sua conta do Video Indexer. As chamadas à API sem o local ou com um local incorreto falharão.

Os valores descritos na tabela a seguir se aplicam. O **valor Param** é o valor que você passa ao usar a API.

|**Nome**|**Valor do parâmetro**|**Descrição**|
|---|---|---|
|Avaliação|versão de avaliação|Usado para contas de avaliação. Por exemplo: https://api.videoindexer.ai/trial/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Oeste dos EUA|westus2|Usado para a região do Azure Oeste dos EUA 2.  Por exemplo: https://api.videoindexer.ai/westus2/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Norte da Europa |northeurope|Usado para a região do Azure Norte da Europa. Por exemplo: https://api.videoindexer.ai/northeurope/Accounts/{accountId}/Videos/{videoId}/Index?language=English. |
|Ásia Oriental|eastasia|Usado para a região do Azure East Asia. Por exemplo: https://api.videoindexer.ai/eastasia/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|

### <a name="data-model"></a>Modelo de dados

Agora o Video Indexer tem um modelo de dados simplificado para fornecer insights muito mais claros. Para obter mais informações sobre a saída produzida pela API v2, consulte [Examine a saída do Video Indexer produzida pela API v2](video-indexer-output-json-v2.md).

### <a name="swagger"></a>Swagger

As definições de API do Video Indexer foram atualizadas adequadamente e estão disponíveis para download por meio do [portal de API](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).


### <a name="v1-vs-v2-examples"></a>Exemplos de v1 vs. v2

As novas APIs v2 envolvem três parâmetros principais:

1. [LOCATION] – Conforme descrito acima. Trial, westus2, northeurope ou eastasia.
2. [YOUR_ACCOUNT_ID] – Uma ID Guid de sua conta. Recuperada ao obter todas as contas (descrito abaixo).
3. [YOUR_VIDEO_ID] – A ID do seu vídeo (por ex., "d4fa369abc"). Retornada ao carregar um vídeo ou ao pesquisar vídeos.

#### <a name="uploading-a-video-in-v1"></a>Carregando um vídeo na V1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns?name=some_name&description=some_description&privacy=private&videoUrl=http://URL_TO_YOUR_VIDEO
```

#### <a name="uploading-a-video-in-v2"></a>Carregando um vídeo na V2:

1. Obter um token de acesso para a solicitação de upload:

  É possível obter todas as contas e seus tokens de acesso:

    ```
    https://api.videoindexer.ai/auth/[LOCATION]/Accounts?generateAccessTokens=true&allowEdit=true
    ```

  Ou obter o token de acesso de conta específico:
  
  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/AccessToken?allowEdit=true
  ```
2. Carregar um vídeo:

  ```
  POST https://api.videoindexer.ai/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos?name=MySample&description=MySampleDescription&videoUrl=[URL_ENCODED_VIDEO_URL]&accessToken=eyJ0eXAiOiJ...
  ```

#### <a name="getting-insights-in-v1"></a>Obtendo insights na V1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]
```
  
#### <a name="getting-insights-in-v2"></a>Obtendo insights na V2:

1. Use o token de acesso de conta ou obtenha um token de acesso de nível de vídeo:

  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/AccessToken
  ```
  
2. Obter insights:

  ```
  https://api.videoindexer.ai/[LOCATION]/Accounts[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

#### <a name="getting-video-processing-state-in-v1"></a>Obtendo estado de processamento de vídeo na V1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]/State
```
  
#### <a name="getting-video-processing-state-in-v2"></a>Obtendo estado de processamento de vídeo na V2:

Na API v2, o estado de processamento é retornado como parte da API Obter índice de vídeo.

1. Use o token de acesso de conta ou obtenha um token de acesso de nível de vídeo:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```
  
2. Obter insights:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

## <a name="next-steps"></a>Próximas etapas

[Usar API do Azure Video Indexer](video-indexer-use-apis.md)

