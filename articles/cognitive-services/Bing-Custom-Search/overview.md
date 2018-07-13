---
title: O que é a Pesquisa Personalizada do Bing? | Microsoft Docs
description: Fornece uma visão geral de alto nível da Pesquisa Personalizada do Bing
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: 7cd61fc63d0d7734b842ed222c67c6753da9a418
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363851"
---
# <a name="what-is-bing-custom-search"></a>O que é a Pesquisa Personalizada do Bing?

A Pesquisa Personalizada do Bing permite que você crie experiências de pesquisa personalizada para os tópicos importantes para você. Por exemplo, se você possuir um site que fornece uma experiência de pesquisa, será possível especificar os domínios, sites e as páginas da Web que o Bing pesquisará. Os usuários veem os resultados da pesquisa personalizados ao conteúdo importante para eles, em vez de terem que acessar os resultados da pesquisa com conteúdo irrelevante.

Para criar a exibição personalizada da Web, use o [portal](https://customsearch.ai) da Pesquisa Personalizada do Bing. O portal permite criar uma instância de pesquisa personalizada que especifica os domínios, os sites e páginas da Web que você quer que o Bing pesquise, e aqueles que você não quer que ele pesquise. Além de especificar as URLs do conteúdo que você conhece, também é possível usar o portal para localizar o conteúdo relevante que queira adicionar.

O portal também permite fixar uma página específica na parte superior do resultado da pesquisa, se o usuário inserir um termo de pesquisa específico. 

Após definir a instância, será possível integrar a pesquisa personalizada no site, aplicativo da área de trabalho ou aplicativo móvel, chamando a API de Pesquisa Personalizada. Se você tiver um site ou aplicativo baseado na Web, poderá deixar a interface do usuário hospedada renderizar a interface de pesquisa.

A imagem a seguir mostra a simplicidade da integração de pesquisa personalizada.

![alt imagem](./media/bcs-overview.png "Como funciona a Pesquisa Personalizada do Bing.")

## <a name="customize-search-suggestions"></a>Personalizar as sugestões de pesquisa

Se você se inscreveu na Pesquisa Personalizada no nível apropriado (consulte as [páginas de preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), pode personalizar as sugestões de pesquisa feitas em sua experiência de Pesquisa Personalizada. A API de Sugestão Automática Personalizada retorna uma lista de consultas sugeridas com base em uma cadeia de caracteres de consulta parcial que o usuário fornece. Com a Sugestão Automática Personalizada, você deve fornecer sugestões de pesquisa personalizada relevantes para sua experiência de pesquisa. Você especifica se deve retornar apenas sugestões personalizadas ou incluir sugestões do Bing. Se as sugestões do Bing estiverem incluídas, sugestões personalizadas aparecerão antes das sugestões do Bing. As sugestões do Bing são restritas ao contexto da instância de Pesquisa Personalizada.

## <a name="next-steps"></a>Próximas etapas

Para começar rapidamente, consulte [Criar a primeira instância de Pesquisa Personalizada do Bing](quick-start.md).

Para obter detalhes sobre as opções disponíveis para personalizar a instância de pesquisa, consulte [Definir uma instância de pesquisa personalizada](define-your-custom-view.md).

Familiarize-se com a referência de [API de Pesquisa Personalizada](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference). A referência contém a lista de pontos de extremidade, cabeçalhos e parâmetros de consulta que você usaria para solicitar os resultados da pesquisa. Ela também inclui as definições dos objetos de resposta.

Para saber como personalizar sugestões, consulte [Definir sugestões de pesquisa personalizada](define-custom-suggestions.md).

Leia os [Requisitos de exibição e uso do Bing](./use-and-display-requirements.md) para não violar nenhuma das regras sobre como usar os resultados da pesquisa.