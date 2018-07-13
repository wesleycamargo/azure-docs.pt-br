---
title: Localize a região do LUIS com C# nos limites de Reconhecimento vocal (LUIS) | Microsoft Docs
description: Localize programaticamente a região de publicação com chave de assinatura e ID de aplicativo para o LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/31/2018
ms.author: v-geberr
ms.openlocfilehash: c8d2024567255083aec470adfebff0d1706fd472
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/02/2018
ms.locfileid: "35364880"
---
# <a name="region-can-be-determined-from-api-call"></a>A região pode ser determinada da chamada à API 
Se você tiver a ID do aplicativo LUIS e a ID da assinatura do LUIS, poderá encontrar a região que será usada para consultas de ponto de extremidade.

> [!NOTE] 
> A solução C# completa está disponível nos [**exemplos de LUIS** no repositório Github](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/).

## <a name="luis-endpoint-query-strategy"></a>Estratégia de consulta de ponto de extremidade do LUIS
Cada consulta de ponto de extremidade do LUIS exige:

* Uma chave de assinatura
* Uma ID do aplicativo
* Uma região

Se a consulta do ponto de extremidade do LUIS usa a ID de aplicativo e a chave de assinatura correta, mas a região errada, o código de resposta será 401. A solicitação 401 não é considerada na cota da assinatura. Transforme esta solicitação em uma estratégia para pesquisar todas as regiões para localizar a região correta. A região correta é a única solicitação que retorna um código de status 2xx. 

|Código de resposta|parâmetros|
|--|--|
|2xx|chave de assinatura correta<br>ID do aplicativo correta<br>região do host correta|
|401|chave de assinatura correta<br>ID do aplicativo correta<br>região do host _incorreta_|

## <a name="c-class-code-to-find-region"></a>Código de classe C# para localizar a região
O aplicativo de console usa a ID do aplicativo LUIS e a chave da assinatura e retorna todas as regiões associadas a ele. Atualmente, uma chave de assinatura é criada por região, de modo que apenas uma região seja retornada.

Inclua as dependências da biblioteca do .Net:

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

Adicione esta classe personalizada do LUIS criada para localizar a região. Substitua os valores de variável para `luisAppId` e `luisSubscriptionKey` pelos seus próprios valores. Todas as regiões que retornam 401 serão gravadas no console de depuração. 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

Este é um exemplo de como chamar a classe personalizada do LUIS no método Main do aplicativo de console:

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

Quando o aplicativo é executado, o console mostra a região para a chave de assinatura e a ID do aplicativo.

![Captura de tela do aplicativo de console mostrando a região do LUIS](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as [regiões](luis-reference-regions.md) do LUIS.
