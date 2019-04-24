---
title: Códigos de resposta HTTP da API – QnA Maker
titleSuffix: Azure Cognitive Services
description: Entender quais códigos de resposta HTTP são retornados pelas APIs de QnA Maker. Isso ajudará você a solucionar quaisquer erros.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/20/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 319623487f37308b5b8fe3fd107b01733825184d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60233339"
---
# <a name="qna-maker-api-http-response-codes"></a>Códigos de resposta HTTP da API de QnA Maker
As APIs de [gerenciamento](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) e previsão retornam códigos de resposta HTTP. Enquanto as mensagens de resposta incluem informações específicas a uma solicitação, o código de status de resposta HTTP é geral. 

### <a name="management"></a>Gerenciamento

|Código|Explicação|
|:--|--|
|2xx|Sucesso|
|400|Os parâmetros da solicitação estão incorretos, o que significa que os parâmetros necessários estão ausentes, errados ou são muitos grandes|
|400|O corpo da solicitação está incorreto, o que significa que o JSON está ausente, errado ou é muito grande|
|401|Chave inválida|
|403|Proibido – você não tem as permissões corretas|
|404|O KB não existe|
|410|Essa API foi preterida e não está mais disponível|
