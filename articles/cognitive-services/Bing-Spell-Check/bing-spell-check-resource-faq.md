---
title: Perguntas frequentes sobre a API de Verificação Ortográfica do Bing
titlesuffix: Azure Cognitive Services
description: Obtenha respostas para perguntas comuns sobre a API de Verificação Ortográfica do Bing no Azure.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: e6662ffcbab9ea274a67bc4437ca1600f1625ff1
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801498"
---
# <a name="frequently-asked-questions-about-the-bing-spell-check-api"></a>Perguntas frequentes sobre a API de Verificação Ortográfica do Bing

 Encontre respostas para as perguntas frequentes sobre conceitos, código e cenários relacionados à API de Verificação Ortográfica do Bing para Serviços Cognitivos da Microsoft no Azure.

## <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-spell-check-api-from-javascript"></a>Como fazer para obter os cabeçalhos opcionais do cliente ao chamar a API de Verificação Ortográfica do Bing do JavaScript?

Os cabeçalhos a seguir são opcionais, mas é recomendável que sejam tratados conforme necessário. Esses cabeçalhos ajudam a API de Verificação Ortográfica do Bing a obter resultados mais precisos.

- X-Search-Location
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

No entanto, ao chamar a API de Verificação Ortográfica do Bing do JavaScript, os recursos de segurança incorporados do navegador podem impedir que você acesse os valores desses cabeçalhos.

Para resolver esse problema, é possível fazer a solicitação da API de Verificação Ortográfica do Bing por meio de um proxy CORS. A resposta desse proxy tem um cabeçalho `Access-Control-Expose-Headers` que inclui os cabeçalhos de resposta na lista de permissões e disponibiliza-os para o JavaScript.

É fácil instalar um proxy CORS para permitir que o [aplicativo de tutorial](tutorials/spellcheck.md) acesse os cabeçalhos opcionais do cliente. Primeiro, caso ainda não tenha, [instale o Node.js](https://nodejs.org/en/download/). Em seguida, insira o comando a seguir em um prompt de comando.

    npm install -g cors-proxy-server

Depois, altere o ponto de extremidade de API de Verificação Ortográfica do Bing no arquivo HTML para:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

Por fim, inicie o proxy CORS com o seguinte comando:

    cors-proxy-server

Deixe a janela de comando aberta enquanto você usa o aplicativo de tutorial, já que se fechar a janela irá parar o proxy. Na seção Cabeçalhos HTTP expansível abaixo dos resultados da pesquisa, é possível ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e verificar se é o mesmo para cada solicitação.

## <a name="next-steps"></a>Próximas etapas

A sua pergunta é sobre a falta de um recurso ou funcionalidade? Considere solicitar ou votar no [site UserVoice](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Consulte também

 [StackOverflow: Serviços Cognitivos](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
