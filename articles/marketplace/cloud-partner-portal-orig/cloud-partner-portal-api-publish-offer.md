---
title: Publicar uma oferta | O Azure Marketplace
description: API para publicar a oferta especificada.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 117a4e5e238e754524ff813ce25ebc1105e2153c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64934973"
---
<a name="publish-an-offer"></a>Publicar uma oferta
================

Inicia o processo de publicação da oferta especificada. Essa ligação é uma operação demorada.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>Parâmetros de URI
--------------

|  **Nome**      |    **Descrição**                               |  **Tipo de dados** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identificador do editor, por exemplo `contoso`      |   Cadeia de caracteres       |
|  offerId       | Identificador da oferta                                 |   Cadeia de caracteres       |
|  api-version   | Última versão da API                        |   Data         |
|  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**        |    **Valor**          |
|  --------        |    ---------          |
|  Tipo de conteúdo    | `application/json`    |
|  Autorização   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Exemplo de corpo
------------

### <a name="request"></a>Solicitação

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Solicitar propriedades do corpo

|  **Nome**               |   **Descrição**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  emails de notificação    | Lista separada por vírgula de endereços de email para ser notificado sobre o andamento da operação de publicação. |
|  |  |


### <a name="response"></a>Response

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |    **Valor**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operation-Location    | A URL que pode ser consultada para determinar o status atual da operação.    |
|  |  |


### <a name="response-status-codes"></a>Códigos de status de resposta

| **Código** |  **Descrição**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` – a solicitação foi aceita com êxito. A resposta contém um local que pode ser usado para rastrear a operação iniciada. |
| 400   | `Bad/Malformed request` – O corpo da resposta de erro pode fornecer mais informações.                                                               |
| 422   | `Un-processable entity` – Indica que a entidade a ser publicada apresentou falha na validação.                                                        |
| 404   | `Not found` – A entidade especificada pelo cliente não existe.                                                                              |
|  |  |
