---
title: Quais são as APIs de Pesquisa do Bing?
titleSuffix: Azure Cognitive Services
description: Use este artigo para saber mais sobre as APIs de Pesquisa do Bing e como você pode habilitar pesquisas de Internet cognitivas em seus aplicativos e serviços.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 60de69c71df61cb53e446aa8759842f72c2acf5d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864172"
---
# <a name="what-are-the-bing-search-apis"></a>Quais são as APIs de Pesquisa do Bing?

As APIs de Pesquisa do Bing permitem que você crie aplicativos e serviços conectados à Web que encontram páginas da Web, imagens, notícias, locais e muito mais, sem anúncios. Enviando solicitações de pesquisa usando os SDKs ou as APIs REST de Pesquisa do Bing, você pode obter conteúdo e informações relevantes para pesquisas na Web. Use este artigo para saber mais sobre as diversas APIs de Pesquisa do Bing e como você pode integrar pesquisas cognitivas em seus aplicativos e serviços. Preços e limites de taxa podem variar entre as APIs.

## <a name="the-bing-web-search-api"></a>A API de Pesquisa na Web do Bing

A [API de Pesquisa na Web do Bing](../Bing-Web-Search/index.yml) retorna páginas da Web, imagens, vídeo, notícias e muito mais. As consultas de pesquisa enviadas para esta API podem ser filtradas para incluir ou excluir determinados tipos de conteúdo.

Considere usar a API de Pesquisa na Web do Bing em aplicativos que talvez precisem pesquisar por todos os tipos de conteúdo da Web relevante. Se seu aplicativo pesquisa por um tipo específico de conteúdo online, considere uma das APIs de pesquisa abaixo: 

## <a name="content-specific-bing-search-apis"></a>APIs de Pesquisa do Bing específicas a um conteúdo

As seguintes APIs de Pesquisa do Bing retornam conteúdo específico da Web, tal como imagens, notícias, empresas locais e vídeos.

| API do Bing | DESCRIÇÃO |
| -- | -- | 
| [Pesquisa de Entidade](../Bing-Entities-Search/index.yml) | A API de Pesquisa de Entidade do Bing retorna resultados da pesquisa contendo entidades, as quais podem ser pessoas, lugares ou coisas. Dependendo da consulta, a API retornará uma ou mais entidades que satisfaçam a consulta da pesquisa, as quais podem incluir indivíduos, empresas locais, pontos de referência e destinos interessantes e muito mais. |
| [Pesquisa de Imagem](../Bing-Image-Search/index.yml) | A API de Pesquisa de Imagem do Bing permite que você procure e encontre imagens estáticas e animadas de alta qualidade semelhantes àquelas em [Bing.com/images](https://www.Bing.com/images). Você pode refinar a pesquisa para incluir ou excluir imagens por atributo, incluindo o tamanho, cor, licença e atualização. Você também pode pesquisar imagens mais populares, carregar imagens para obter informações sobre eles e exibir visualizações em miniatura. |
| [Pesquisa de Notícias](../Bing-News-Search/index.yml) | A API de Pesquisa de Notícias do Bing permite encontrar notícias semelhantes àquelas em [Bing.com/news](https://www.Bing.com/news). A API retorna artigos de notícias de várias fontes ou domínios específicos. Você pode pesquisar em categorias para obter artigos populares, bem como as principais histórias e manchetes.
| [Pesquisa de Vídeo](../Bing-Video-Search/index.yml) | A API de Pesquisa de Vídeo do Bing permite que você encontre vídeos na Web. Obtenha vídeos populares, conteúdo relacionado e visualizações em miniatura. |
| [Pesquisa Visual](../Bing-visual-search/index.yml) | Carregue uma imagem ou use uma URL para obter informações criteriosas sobre ela, tais como pesquisas relacionadas, imagens e produtos visualmente similares. |
 [Pesquisa de Empresas Locais](../bing-local-business-search/index.yml) | A API de Pesquisa de Locais e Negócios do Bing permite que seus aplicativos encontrem informações de contato e localização sobre locais e negócios com base em consultas de pesquisa. |

## <a name="the-bing-custom-search-api"></a>A API de Pesquisa Personalizada do Bing

Criar uma instância de pesquisa personalizada com a API de [Pesquisa Personalizada do Bing](../Bing-Custom-Search/index.yml) permite que você crie uma experiência de pesquisa focada apenas em conteúdo e nos tópicos importantes para você. Por exemplo, depois de especificar os domínios, sites e páginas da Web específicas nos quais o Bing pesquisará, os resultados devem ser adaptados a esse conteúdo específico. Você pode incorporar as APIs de Pesquisa de Vídeo, de Imagem e a Sugestão Automática Personalizada do Bing para personalizar ainda mais sua experiência de pesquisa.  

## <a name="additional-bing-search-apis"></a>APIs de Pesquisa do Bing adicionais

As APIs de Pesquisa do Bing a seguir permitem que você aprimore sua experiência de pesquisa, combinando-as com outras APIs de Pesquisa do Bing.

| API | DESCRIÇÃO |
| -- | -- | 
| [Sugestão Automática do Bing](../Bing-Autosuggest/index.yml) | Melhore a experiência de pesquisa do seu aplicativo com a API de Sugestão Automática do Bing, retornando pesquisas sugeridas em tempo real.  |
| [Estatísticas do Bing](bing-web-stats.md) | As Estatísticas do Bing fornecem análise para as APIs de Pesquisa do Bing usadas pelo aplicativo. Algumas das análises disponíveis incluem o volume de chamadas, as principais cadeias de caracteres de consulta e a distribuição geográfica. |

## <a name="next-steps"></a>Próximas etapas

* [Detalhes de preço](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) da API de Pesquisa do Bing
* Os [Requisitos de exibição e uso do Bing](./use-display-requirements.md) especificam os usos aceitáveis do conteúdo e das informações obtidas por meio das APIs de pesquisa do Bing.
