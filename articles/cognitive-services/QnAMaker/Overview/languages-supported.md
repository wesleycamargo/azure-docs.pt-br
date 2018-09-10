---
title: Idiomas compatíveis – QnA Maker – Serviços Cognitivos do Azure | Microsoft Docs
description: Saiba quais idiomas são compatíveis com o QnA Maker.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b210f59129a962046787b27d003c2872a54f6c8e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364537"
---
# <a name="supported-languages"></a>Idiomas com suporte

O idioma de uma base de dados de conhecimento afeta a capacidade do QnA Maker de extrair automaticamente as perguntas e respostas das [fontes](../Concepts/data-sources-supported.md), bem como a relevância de resultados fornecidos pelo QnA Maker em resposta às consultas do usuário.

## <a name="auto-extraction"></a>Extração automática
O QnA Maker dá suporte à extração de perguntas/respostas em qualquer página de idioma, mas a eficácia da extração é muito maior nos idiomas a seguir, pois o QnA Maker usa palavras-chave para identificar perguntas.

|Idiomas compatíveis| Localidade|
|-----|----|
|Inglês|en-*|
|Francês|fr-*|
|Italiano|it-*|
|Alemão|de-*|
|Espanhol|es-*|

## <a name="query-matching-and-relevance"></a>Correspondência de consulta e relevância
O QnA Maker depende de [analisadores de idioma](https://docs.microsoft.com/en-us/rest/api/searchservice/language-support) no Azure Search para fornecer resultados. Os recursos de nova classificação especiais estão disponíveis para idiomas En-* que permitem uma maior relevância.

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
|Punjabi|
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
