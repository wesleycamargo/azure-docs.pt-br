---
title: Localize a região do LUIS com Node.js nos limites de Reconhecimento vocal (LUIS) | Microsoft Docs
description: Localize programaticamente a região de publicação com chave de assinatura e ID de aplicativo para o LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/01/2018
ms.author: v-geberr
ms.openlocfilehash: 18ee324c10f074601c0c04573ca1a5266481f21c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/02/2018
ms.locfileid: "35364881"
---
# <a name="region-can-be-determined-from-api-call"></a>A região pode ser determinada da chamada à API 
Se você tiver a ID do aplicativo LUIS e a ID da assinatura do LUIS, poderá encontrar a região que será usada para consultas de ponto de extremidade.

> [!NOTE] 
> A solução Node.js completa está disponível nos [**exemplos de LUIS** no repositório Github](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/nodejs/).

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

## <a name="nodejs-code-to-find-region"></a>Código de classe Node.js para localizar a região
O aplicativo de console usa a ID do aplicativo LUIS e a chave da assinatura e retorna todas as regiões associadas a ele. Atualmente, uma chave de assinatura é criada por região, de modo que apenas uma região seja retornada.

Inclua as dependências de NPM:

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

Adicione constantes. Substitua os valores de variável para `subscriptionKey` e `appId` pelos seus próprios valores.  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

Adicione a função `searchRegions` para localizar a região. Todas as regiões incorretas retornam 401, que é capturado e ignorado.

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

Chame a função `searchRegions` e retorne uma única região:

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

Quando o aplicativo é executado, o terminal mostra a região para a chave de assinatura e a ID do aplicativo.

![Captura de tela do aplicativo de console mostrando a região do LUIS](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as [regiões](luis-reference-regions.md) do LUIS.