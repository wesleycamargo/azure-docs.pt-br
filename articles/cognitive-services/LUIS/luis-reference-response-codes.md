---
title: Códigos de resposta HTTP da API de Reconhecimento Vocal (LUIS) - Azure | Microsoft Docs
titleSuffix: Azure
description: Entender quais códigos de resposta HTTP são retornados pelas APIs de Criação e de Ponto de extremidade
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 6ffab581420c3c74ed659be2b82e2f9c7fda13d0
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443824"
---
# <a name="luis-api-http-response-codes"></a>Códigos de resposta HTTP da API de LUIS
As APIs de [criação](https://aka.ms/luis-authoring-apis) e de [ponto de extremidade](https://aka.ms/luis-endpoint-apis) retornam códigos de resposta HTTP. Enquanto as mensagens de resposta incluem informações específicas a uma solicitação, o código de status de resposta HTTP é geral. 

## <a name="common-status-codes"></a>Códigos de status comuns
A tabela a seguir lista alguns dos códigos de status de resposta HTTP mais comuns para as APIs de [criação](https://aka.ms/luis-authoring-apis) e de [ponto de extremidade](https://aka.ms/luis-endpoint-apis):

|Código|API|Explicação|
|:--|--|--|
|400|Criação, ponto de extremidade|os parâmetros da solicitação estão incorretos, o que significa que os parâmetros necessários estão ausentes, errados ou são muitos grandes|
|400|Criação, ponto de extremidade|o corpo da solicitação está incorreto, o que significa que o JSON está ausente, errado ou é muito grande|
|401|Criação|chave de assinatura do ponto de extremidade usada, em vez da chave de criação|
|401|Criação, ponto de extremidade|chave inválida, errada ou vazia|
|401|Criação, ponto de extremidade| a chave não corresponde à região|
|401|Criação|você não é o proprietário ou colaborador|
|401|Criação|ordem inválida das chamadas à API|
|403|Criação, ponto de extremidade|limite de cota mensal total de chave excedido|
|409|Ponto de extremidade|o aplicativo ainda está sendo carregado|
|410|Ponto de extremidade|o aplicativo precisa ser treinado e publicado novamente|
|414|Ponto de extremidade|a consulta excede o limite máximo de caracteres|
|429|Criação, ponto de extremidade|O limite de taxa foi excedido (solicitações/segundo)|