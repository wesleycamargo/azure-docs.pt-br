---
title: Suporte de idioma – QnA Maker
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas naturais compatíveis com o QnA Maker.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/09/2018
ms.author: tulasim
ms.openlocfilehash: 8c47c4a59f03328b1dc8d3df7771bac81864bb34
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566628"
---
# <a name="language-and-region-support-for-qna-maker"></a>Suporte de idioma e região para o QnA Maker

O idioma de uma base de dados de conhecimento afeta a capacidade do QnA Maker de extrair automaticamente as perguntas e respostas das [fontes](../Concepts/data-sources-supported.md), bem como a relevância de resultados fornecidos pelo QnA Maker em resposta às consultas do usuário.

## <a name="auto-extraction"></a>Extração automática
O QnA Maker dá suporte à extração de perguntas/respostas em qualquer página de idioma, mas a eficácia da extração é muito maior nos idiomas a seguir, pois o QnA Maker usa palavras-chave para identificar perguntas.

|Idiomas compatíveis| Local|
|-----|----|
|Inglês|en-*|
|Francês|fr-*|
|Italiano|it-*|
|Alemão|de-*|
|Espanhol|es-*|

## <a name="query-matching-and-relevance"></a>Correspondência de consulta e relevância
O QnA Maker depende de [analisadores de idioma](https://docs.microsoft.com/rest/api/searchservice/language-support) no Azure Search para fornecer resultados. Os recursos de nova classificação especiais estão disponíveis para idiomas En-* que permitem uma maior relevância.

Enquanto os recursos do Azure Search estão no mesmo nível dos idiomas com suporte, o QnA Maker tem um classificador adicional que fica acima dos resultados de pesquisa do Azure. Nesse modelo de classificação, usamos alguns recursos especiais semânticos e baseados em palavras em en- *, que ainda não estão disponíveis para outros idiomas. Nós não disponibilizamos, pois fazem parte do trabalho interno do classificador. 

O QnA Maker detecta automaticamente o idioma da base de dados de conhecimento durante a criação e define o analisador de acordo. Você pode criar bases de dados de conhecimento nos idiomas a seguir. Leia [isto](../How-To/language-knowledge-base.md) para obter mais detalhes sobre como o QnA Maker manipula idiomas.


> [!Tip]
> Os analisadores de idioma, uma vez definidos, não podem ser alterados. Além disso, o analisador de linguagem se aplica a todas as bases de dados de conhecimento de um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Se você planeja ter bases de dados de conhecimento em outro idioma, crie-os em serviços QnA Maker separados.

|Idiomas compatíveis|
|-----|
|Árabe|
|Armênia|,
Bengalês|
|Basco|
|Búlgaro|
|Catalão|
|Chinês_Simplificado|
|Chinês_Tradicional|
|Croata|
|Tcheco|
|Dinamarquês|
|Holandês|
|Inglês|
|Estoniano|
|Finlandês|
|Francês|
|Galego|
|Alemão|
|Grego|
|Guzerate|
|Hebraico|
|Híndi|
|Húngaro|
|Islandês|
|Indonésio|
|Irlandês|
|Italiano|
|Japonês|
|Canarim|
|Coreano|
|Letão|
|Lituano|
|Malaiala|
|Malaio|
|Norueguês|
|Polonês|
|Português|
|Panjabi|
|Romeno|
|Russo|
|Cirílico sérvio|
|sérvio Latino|
|Eslovaco|
|Esloveno|
|Espanhol|
|Sueco|
|Tâmil|
|Télugo|
|Tailandês|
|Turco|
|Ucraniano|
|Urdu|
|Vietnamita|
