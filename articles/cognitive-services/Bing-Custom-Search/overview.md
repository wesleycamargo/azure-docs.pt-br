---
title: O que é API de Pesquisa Personalizada do Bing?
titlesuffix: Azure Cognitive Services
description: A API de Pesquisa Personalizada do Bing permite que você crie experiências de pesquisa personalizada para os tópicos de seu interesse.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 09/29/2017
ms.author: aahi
ms.openlocfilehash: 56be136aff3aa7fbe813fee500dcf2fe35ca0859
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875376"
---
# <a name="what-is-the-bing-custom-search-api"></a>O que é API de Pesquisa Personalizada do Bing?

A API de Pesquisa Personalizada do Bing permite que você crie experiências de pesquisa personalizada sem anúncios para os tópicos de seu interesse. Você pode especificar as páginas da Web e os domínios para pesquisa no Bing, bem como fixar, aumentar ou rebaixar um conteúdo específico para criar uma exibição personalizada da Web e ajudar os usuários a localizar rapidamente os resultados da pesquisa relevantes. 

## <a name="features"></a>Recursos

|Recurso  |DESCRIÇÃO  |
|---------|---------|
|[Sugestões de pesquisa personalizadas em tempo real](define-custom-suggestions.md)     | Fornece sugestões de pesquisa que podem ser exibidas como uma lista suspensa conforme os usuários digitam.       | 
|[Experiências de pesquisa de imagem personalizada](get-images-from-instance.md)     | Permita que os usuários pesquisem imagens dos domínios e dos sites especificados em sua instância de pesquisa personalizada.        |        
|[Experiências de pesquisa de vídeo personalizada](get-videos-from-instance.md)     | Permita que os usuários pesquisem vídeos dos domínios e dos sites especificados em sua instância de pesquisa personalizada.        |    
|[Compartilhar sua instância de pesquisa personalizada](share-your-custom-search.md)     | Edite e teste sua instância de pesquisa de forma colaborativa, compartilhando-a com membros de sua equipe.        | 
|[Configurar uma interface do usuário para seus aplicativos e seus sites ](hosted-ui.md)     | Edite e teste sua instância de pesquisa de forma colaborativa, compartilhando-a com membros de sua equipe.        | 
## <a name="workflow"></a>Fluxo de trabalho

Crie uma instância de pesquisa personalizada usando o [portal de Pesquisa Personalizada do Bing](https://customsearch.ai). O portal permite criar uma instância de pesquisa personalizada que especifica os domínios, os sites e as páginas da Web que você deseja pesquisar no Bing, juntamente com aqueles que você não deseja pesquisar. Use também o portal para: visualizar a experiência de pesquisa, ajustar as classificações de pesquisa fornecidas pela API e, opcionalmente, configurar uma interface do usuário pesquisável para que ela seja renderizada em seus sites e seus aplicativos.

Depois de criar sua instância de pesquisa, você pode integrá-la (e, opcionalmente, integrar uma interface do usuário) ao seu site ou seu aplicativo chamando a API de Pesquisa Personalizada do Bing:

![Imagem mostrando que você pode conectar a Pesquisa Personalizada do Bing por meio da API](media/BCS-Overview.png "Como funciona a Pesquisa Personalizada do Bing.")


## <a name="next-steps"></a>Próximas etapas

Para começar rapidamente, consulte [Criar a primeira instância de Pesquisa Personalizada do Bing](quick-start.md).

Para detalhes sobre como personalizar sua instância de pesquisa, consulte [Definir uma instância de pesquisa personalizada](define-your-custom-view.md).

Leia os [Requisitos de uso e exibição do Bing](./use-and-display-requirements.md) para usar os resultados da pesquisa em seus serviços e seus aplicativos.

Familiarize-se com o conteúdo de referência para cada um dos pontos de extremidade de pesquisa personalizados. A referência contém os pontos finais, cabeçalhos e parâmetros de consulta que você usaria para solicitar resultados de pesquisa. Ela também inclui as definições dos objetos de resposta.

- [API de pesquisa personalizada](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [API de imagem personalizada](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [API de vídeo personalizada](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [API de sugestão automática personalizada](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)

