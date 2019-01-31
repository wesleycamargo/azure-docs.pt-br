---
title: O que é a Pesquisa Visual do Bing?
titleSuffix: Azure Cognitive Services
description: A Pesquisa Visual do Bing fornece detalhes ou insights sobre uma imagem, como imagens semelhantes ou fontes de compra.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 61a851b0efbcc4fdb55308e47447d218014ef9e0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154347"
---
# <a name="what-is-the-bing-visual-search-api"></a>O que é a API da Pesquisa Visual do Bing?

A API da Pesquisa Visual do Bing fornece detalhes de imagem semelhantes aos mostrados em Bing.com/images. Ao carregar uma imagem ou fornecer uma URL para uma, essa API pode identificar uma variedade de detalhes sobre ela, incluindo imagens visualmente semelhantes, fontes de compra, páginas da Web que incluem a imagem e muito mais. Se você usar a [API de Pesquisa de Imagem do Bing](../bing-image-search/overview.md), poderá usar tokens de insight anexados aos resultados da pesquisa da API em vez de carregar uma imagem.

## <a name="insights"></a>Insights

Veja a seguir os insights que a Pesquisa Visual permite que você descubra:

| Insight                              | DESCRIÇÃO |
|--------------------------------------|-------------|
| Imagens visualmente semelhantes              | Uma lista de imagens que são visualmente semelhantes à imagem de entrada. |
| Produtos visualmente semelhantes            | Produtos visualmente similares ao produto mostrado.            |
| Fontes de compra                     | Uma lista dos locais em que você pode comprar o item mostrado na imagem de entrada.            |
| Pesquisas relacionadas                     | Pesquisas relacionadas feitas por outras pessoas ou que se baseiam no conteúdo da imagem.            |
| Páginas da Web que incluem a imagem     | Páginas da Web que incluem a imagem de entrada.            |
| Receitas                              | Páginas da Web que incluem receitas para preparação do prato mostrado na imagem de entrada            |

Além desses insights, a Pesquisa Visual também retorna um conjunto diversificado de termos (marcas) derivados da imagem de entrada. Essas marcas permitem aos usuários explorar os conceitos encontrados na imagem. Por exemplo, se a imagem de entrada for a de um atleta famoso, uma das marcações poderá ser o nome do atleta e a outra poderá ser Esportes. Ou se a imagem de entrada for a de uma torta de maçã, as marcas poderão ser Torta de Maçã, Tortas, Sobremesas, de modo que os usuários possam explorar conceitos relacionados.

Os resultados da Pesquisa Visual também incluem caixas delimitadoras para as áreas de interesse na imagem. Por exemplo, se a imagem contiver várias celebridades, os resultados poderão incluir caixas delimitadoras para cada uma das celebridades reconhecidas na imagem. Ou se o Bing reconhecer um produto ou um item de vestuário na imagem, o resultado poderá incluir uma caixa delimitadora para o produto ou o item de vestuário reconhecido.

> [!IMPORTANT]
> Se você receber insights de imagem usando a API de Pesquisa de Imagem do Bing, considere mudar para a API da Pesquisa Visual do Bing, que oferece insights mais abrangentes.

## <a name="workflow"></a>Fluxo de trabalho

A API da Pesquisa Visual do Bing é um serviço Web RESTful, facilitando a chamada de qualquer linguagem de programação que pode fazer solicitações HTTP e analisar JSON. É possível usar o serviço usando a API REST ou o SDK.

1. Crie uma conta da API dos Serviços Cognitivos com acesso às APIs de Pesquisa do Bing. Caso não tenha uma assinatura do Azure, é possível criar uma conta gratuitamente.
2. Envie uma solicitação para a API, com uma consulta de pesquisa válida.
3. Processe a resposta da API analisando a mensagem JSON retornada.


## <a name="next-steps"></a>Próximas etapas

Primeiro, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/) da API de Pesquisa de Imagem do Bing.
Essa demonstração mostra como você pode, de modo rápido, personalizar uma consulta de pesquisa e vasculhar a Web em busca de imagens.

Quando você estiver pronto para chamar a API, crie uma [Conta de API de serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuitamente.

Para uma introdução rápida à primeira solicitação, confira os guias de início rápido: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).


## <a name="see-also"></a>Consulte também

* A documentação de [referência da Pesquisa Visual do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) contém definições e informações sobre os pontos de extremidade, cabeçalhos, respostas de API e parâmetros de consulta que podem ser usados para solicitar os resultados da pesquisa baseada em imagem.

* Os [Requisitos de exibição e uso do Bing](./use-and-display-requirements.md) especificam os usos aceitáveis do conteúdo e das informações obtidas por meio das APIs de pesquisa do Bing.
