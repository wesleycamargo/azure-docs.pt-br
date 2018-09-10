---
title: 'Pesquisa Personalizada do Bing: Obtenha sugestões personalizadas da Sugestão Automática | Microsoft Docs'
description: Descreve como recuperar sugestões personalizadas da Sugestão Automática
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 73059038018602f7aa76377bf7c98d4658576576
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363844"
---
# <a name="get-custom-suggestions"></a>Obter sugestões personalizadas
Antes de enviar consultas de Pesquisa Personalizada do Bing, chame a API de Sugestão Automática Personalizada para fazer sugestões de termo de pesquisa e aprimorar a experiência de pesquisa. A API de Sugestão Automática Personalizada retorna uma lista de consultas sugeridas com base em uma cadeia de caracteres de consulta parcial que o usuário fornece. Qualquer termo de consulta personalizada relevante que você especificar aparece antes das sugestões geradas pela Sugestão Automática. Para obter mais informações, consulte [Definir sugestões de pesquisa personalizadas](define-custom-suggestions.md).

## <a name="endpoint"></a>Ponto de extremidade
Para obter os resultados de pesquisa usando a API de Pesquisa Personalizada do Bing, envie uma solicitação `GET` para o ponto de extremidade a seguir.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

## <a name="response-json"></a>Resposta JSON
A resposta contém uma lista de objetos de SearchAction que contêm os termos de consulta sugeridos.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Cada sugestão inclui um campo `displayText` e `query`. O campo `displayText` contém a consulta sugerida que você usa para preencher a lista suspensa de sua caixa de pesquisa.

Se o usuário seleciona uma consulta sugerida na lista suspensa, use o termo de consulta no campo `query` ao chamar a [API de Pesquisa Personalizada do Bing](overview.md).

## <a name="throttling-requests"></a>Solicitações de limitação

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Próximas etapas

- [Chamar sua pesquisa personalizada](./search-your-custom-view.md)
- [Configurar a experiência de interface do usuário hospedada](./hosted-ui.md)