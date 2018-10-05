---
title: Exemplos de insights do Bing – Pesquisa Visual do Bing
titleSuffix: Azure Cognitive Services
description: Mostra exemplos de informações de imagem mostrados em Bing.com.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: scottwhi
ms.openlocfilehash: ce0c056ace550d3db2f4f51030177436fb194e0f
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219831"
---
# <a name="examples-of-bing-insights-usage"></a>Exemplos de uso de insights do Bing

Esta seção contém exemplos de como o Bing pode exibir informações no Bing.com.

## <a name="pagesincluding-insight-example"></a>Exemplo de insights do PagesIncluding

O exemplo a seguir mostra como o Bing pode exibir páginas da Web que incluem a imagem. O exemplo exibe um link para a primeira página da Web e permite que o usuário expanda e recolha a lista das outras páginas da Web que incluem a imagem.

![Páginas expandidas, incluindo](./media/pages-including.PNG)


## <a name="shoppingsources-insight-example"></a>Exemplo de insights de ShoppingSources

O exemplo a seguir mostra como o Bing pode exibir fontes de compras de produtos mostrados na imagem.

![Fontes de compra](./media/shopping-sources.PNG)


## <a name="visualsearch-insight-example"></a>Exemplo de insight de VisualSearch

Veja a seguir como o Bing pode exibir imagens visualmente semelhantes (consulte **Imagens relacionadas** no exemplo).

![Imagens visualmente semelhantes](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Exemplo de insight de receitas

Veja a seguir como o Bing pode exibir receitas para o alimento mostrado na imagem. O exemplo permite que o usuário saiba que há receitas disponíveis.

![Receitas e páginas incluindo](./media/recipes-pages-including.PNG)

 E fornece o link para as receitas quando o usuário expande a lista.

![Páginas de receita expandidas, incluindo](./media/expanded-recipes-pages-including.PNG)


## <a name="relatedsearches-insight-example"></a>Exemplo de insight RelatedSearches

O exemplo a seguir mostra como o Bing pode exibir pesquisas relacionadas de imagens feitas por outros usuários. Se o usuário clicar na imagem, será levado à página de resultados da pesquisa de Bing.com/images para essa consulta relacionada.

![Pesquisas relacionadas para imagens](./media/bordered-related-searches.PNG)


## <a name="entity-insight-example"></a>Exemplo de insight de entidade

O exemplo a seguir mostra como o Bing pode exibir informações sobre a entidade (pessoa, lugar ou coisa) mostrada na imagem. Se o usuário clicar no link da entidade, o usuário será direcionado para a página de resultados de pesquisa Bing.com da entidade.

![Entidade mostrada na imagem](./media/entity.PNG)


## <a name="displaying-other-insights-that-the-user-might-explore"></a>Exibir outros insights que o usuário pode explorar

O exemplo a seguir mostra como o Bing pode exibir outras informações sobre a imagem que o usuário pode explorar.

![Explore outros insights sobre a imagem](./media/apple-pie-more-tags.PNG)


## <a name="bounding-boxes-and-hot-spots"></a>Caixas delimitadoras e pontos de acesso

Marcas não padrão incluem a caixa delimitadora que identifica a área de interesse na imagem a qual a marca se aplica. Se a caixa delimitadora não identificar a imagem inteira, use a caixa delimitadora para criar um ponto de acesso na imagem. O usuário pode clicar no ponto de acesso para obter informações relacionadas ao conteúdo encontrado no ponto de acesso (ou retângulo). Por exemplo, se a imagem for uma imagem de alta costura, os resultados poderão conter marcas (e caixas delimitadoras) para acessórios mostrados na imagem, como bolsa, joias, cachecóis etc. O exemplo a seguir mostra um retângulo de ponto de acesso para os óculos de sol mostrados na imagem.

![Caixas delimitadoras e ponto de acesso](./media/click-to-search.PNG)



## <a name="next-steps"></a>Próximas etapas

Para conferir o JSON por trás desses exemplos, consulte [Insights padrão](default-insights-tag.md) e [Resposta JSON](overview.md#the-response).

Para se familiarizar rapidamente com sua primeira solicitação, confira os inícios rápidos: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





