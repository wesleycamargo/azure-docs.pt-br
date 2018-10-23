---
title: O que é a Pesquisa Personalizada do Bing?
titlesuffix: Azure Cognitive Services
description: Fornece uma visão geral de alto nível da Pesquisa Personalizada do Bing.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: overview
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: f2946918f0c1a7a516788989042825e8f49d7b0b
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318717"
---
# <a name="what-is-bing-custom-search"></a>O que é a Pesquisa Personalizada do Bing?

A Pesquisa Personalizada do Bing permite que você crie experiências de pesquisa personalizada para os tópicos importantes para você. Por exemplo, se você possuir um site que fornece uma experiência de pesquisa, será possível especificar os domínios, sites e as páginas da Web que o Bing pesquisará. Os usuários veem os resultados da pesquisa personalizados ao conteúdo importante para eles, em vez de terem que acessar os resultados da pesquisa com conteúdo irrelevante.

Para criar a exibição personalizada da Web, use o [portal](https://customsearch.ai) da Pesquisa Personalizada do Bing. O portal permite criar uma instância de pesquisa personalizada que especifica os domínios, os sites e páginas da Web que você quer que o Bing pesquise, e aqueles que você não quer que ele pesquise. Além de especificar as URLs do conteúdo que você conhece, também é possível usar o portal para localizar o conteúdo relevante que queira adicionar.

O portal também permite fixar uma página específica na parte superior do resultado da pesquisa, se o usuário inserir um termo de pesquisa específico. 

Após definir a instância, será possível integrar a pesquisa personalizada no site, aplicativo da área de trabalho ou aplicativo móvel, chamando a API de Pesquisa Personalizada. Se você tiver um site ou aplicativo baseado na Web, poderá deixar a interface do usuário hospedada renderizar a interface de pesquisa.

A imagem a seguir mostra a simplicidade da integração de pesquisa personalizada.

![alt imagem](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/cognitive-services/Bing-Custom-Search/media/BCS-Overview.png "Como funciona a Pesquisa Personalizada do Bing.")

## <a name="adding-custom-search-box-suggestions"></a>Adicionando sugestões de caixa de pesquisa personalizada

Você pode enriquecer sua experiência de pesquisa personalizada com sugestões de caixas de pesquisa personalizadas. Esse recurso permite que você forneça sugestões de pesquisa personalizadas relevantes para sua experiência de pesquisa. À medida que o usuário digita na caixa de pesquisa, a lista suspensa contém strings de consulta sugeridas com base na cadeia de caracteres de consulta parcial do usuário. Você pode especificar se deseja retornar apenas suas sugestões personalizadas ou incluir sugestões do Bing. [Leia mais](define-custom-suggestions.md).

## <a name="adding-custom-image-search-experience"></a>Adicionando experiência de pesquisa de imagem personalizada

Você pode enriquecer sua experiência de pesquisa personalizada com imagens. Semelhante aos resultados da Web, a pesquisa personalizada oferece suporte à pesquisa de imagens na lista de sites da sua instância. [Leia mais](get-images-from-instance.md).

## <a name="adding-custom-video-search-experience"></a>Adicionando experiência de pesquisa de vídeo personalizada

Você pode enriquecer sua experiência de pesquisa personalizada com vídeos. Semelhante aos resultados da Web, a pesquisa personalizada dá suporte à pesquisa de vídeos na lista de sites da sua instância. [Leia mais](get-videos-from-instance.md).

## <a name="sharing-your-custom-search-instance-with-others"></a>Compartilhando sua instância de pesquisa personalizada com outras pessoas

Você pode facilmente permitir a edição e o teste colaborativo de sua instância, compartilhando-a com membros de sua equipe. [Leia mais](share-your-custom-search.md).

## <a name="next-steps"></a>Próximas etapas

Para começar rapidamente, consulte [Criar a primeira instância de Pesquisa Personalizada do Bing](quick-start.md).

Para detalhes sobre como personalizar sua instância de pesquisa, consulte [Definir uma instância de pesquisa personalizada](define-your-custom-view.md).

Familiarize-se com o conteúdo de referência para cada um dos pontos de extremidade de pesquisa personalizados. A referência contém os pontos finais, cabeçalhos e parâmetros de consulta que você usaria para solicitar resultados de pesquisa. Ela também inclui as definições dos objetos de resposta.

- [API de pesquisa personalizada](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [API de imagem personalizada](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [API de vídeo personalizada](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [API de sugestão automática personalizada](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)


Leia os [Requisitos de exibição e uso do Bing](./use-and-display-requirements.md) para não violar nenhuma das regras sobre como usar os resultados da pesquisa.
