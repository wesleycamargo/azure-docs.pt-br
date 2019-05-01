---
title: Entrar no ar | O Azure Marketplace
description: A API do Go Live inicia o processo de listagem de ofertas em tempo real.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: ac56f86bad132f3e00a4b5c2507d65c0722c628c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935483"
---
<a name="go-live"></a>Go Live
=======

Essa API inicia o processo de envio de um aplicativo para produção. Essa operação geralmente é demorada. Essa chamada usa a lista de email de notificação da operação da API [Publicar](./cloud-partner-portal-api-publish-offer.md).

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>Parâmetros de URI
--------------

|  **Nome**      |   **Descrição**                                                           | **Tipo de dados** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identificador do publicador da oferta a ser recuperada, por exemplo `contoso`       |  Cadeia de caracteres       |
| offerId        | O identificador da oferta a ser recuperada                                   |  Cadeia de caracteres       |
| api-version    | Última versão da API                                                   |  Data         |
|  |  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**       |     **Valor**       |
|  ---------      |     ----------      |
| Tipo de conteúdo    | `application/json`  |
| Autorização   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Exemplo de corpo
------------

### <a name="response"></a>Response

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |      **Valor**                                                            |
|  --------             |      ----------                                                           |
| Operation-Location    |  A URL a ser consultada para determinar o status atual da operação            |
|  |  |


### <a name="response-status-codes"></a>Códigos de status de resposta

| **Código** |  **Descrição**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` – a solicitação foi aceita com êxito. A resposta contém um local para rastrear o status da operação. |
|  400     | `Bad/Malformed request` – as informações de erro adicionais são encontradas dentro do corpo da resposta. |
|  404     |  `Not found` – a entidade especificada não existe.                                       |
|  |  |
