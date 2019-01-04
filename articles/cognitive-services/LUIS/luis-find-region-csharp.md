---
title: Região do ponto de extremidade, C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: Com C#, localize a região de publicação com a chave de ponto de extremidade e a ID de aplicativo para LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 5d4c564ac797f40d7f6e9b169a6c864d3ef8147b
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53165156"
---
# <a name="find-endpoint-region-with-c"></a>Encontrar a região do ponto de extremidade com o C# 
Se você tiver a ID do aplicativo LUIS e a ID da assinatura do LUIS, poderá encontrar a região que será usada para consultas de ponto de extremidade.

> [!NOTE] 
> A solução C# completa está disponível nos [**exemplos de LUIS** no repositório do GitHub](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/).

## <a name="luis-endpoint-query-strategy"></a>Estratégia de consulta de ponto de extremidade do LUIS
Cada consulta de ponto de extremidade do LUIS exige:

* Uma chave de ponto de extremidade
* Uma ID do aplicativo
* Uma região

Se a consulta do ponto de extremidade de LUIS usar a chave do ponto de extremidade e a ID do aplicativo corretos, mas a região errada, o código de resposta será 401. A solicitação 401 não é considerada na cota da assinatura. Transforme esta solicitação em uma estratégia para pesquisar todas as regiões para localizar a região correta. A região correta é a única solicitação que retorna um código de status 2xx. 

|Código de resposta|parâmetros|
|--|--|
|2xx|chave de ponto de extremidade correta<br>ID do aplicativo correta<br>região do host correta|
|401|chave de ponto de extremidade correta<br>ID do aplicativo correta<br>região do host _incorreta_|

## <a name="c-class-code-to-find-region"></a>Código de classe C# para localizar a região
O aplicativo de console usa a ID do aplicativo do LUIS e a chave de ponto de extremidade e retorna todas as regiões associadas a ele. Atualmente, uma chave de ponto de extremidade é criada por região, de modo que apenas uma região deve retornar.

Inclua as dependências da biblioteca do .Net:

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

Adicione esta classe personalizada do LUIS criada para localizar a região. Substitua os valores de variável para `luisAppId` e `luisSubscriptionKey` pelos seus próprios valores. Todas as regiões que retornam 401 serão gravadas no console de depuração. 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

Este é um exemplo de como chamar a classe personalizada do LUIS no método Main do aplicativo de console:

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

Quando o aplicativo é executado, o console mostra a região da ID do aplicativo.

![Captura de tela do aplicativo de console mostrando a região do LUIS](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as [regiões](luis-reference-regions.md) do LUIS.
