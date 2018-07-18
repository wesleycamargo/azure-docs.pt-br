---
title: Como paginar os resultados da pesquisa no Azure Search | Microsoft Docs
description: Paginação no Azure Search, um serviço de pesquisa de nuvem hospedado do Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 08/29/2016
ms.author: heidist
ms.openlocfilehash: 516760031918c667b39cc8b3dd94d91c42623efc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186871"
---
# <a name="how-to-page-search-results-in-azure-search"></a>Como paginar os resultados da pesquisa no Azure Search
Este artigo fornece orientação sobre como usar a API REST do Serviço de Azure Search para implementar elementos padrão da página de resultados da pesquisa, por exemplo, contagem total, recuperação de documentos, ordens de classificação e navegação.

Em cada caso mencionado abaixo, as opções relacionadas à página que colaboram com dados ou informações para sua página de resultados da pesquisa são especificadas por meio de solicitações de [Documento de Pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) enviadas ao Serviço de Azure Search. As solicitações incluem um comando GET, um caminho e os parâmetros de consulta que informam ao serviço que está sendo solicitado, e como formular a resposta.

> [!NOTE]
> Uma solicitação válida inclui diversos elementos, como uma URL de serviço e o caminho, o verbo HTTP, `api-version` etc. Para resumir, recortamos os exemplos para destacar apenas a sintaxe relevante para a paginação. Confira a documentação da [API REST do Serviço de Azure Search](https://docs.microsoft.com/rest/api/searchservice) para obter detalhes sobre a sintaxe da solicitação.
> 
> 

## <a name="total-hits-and-page-counts"></a>Total de ocorrências e contagens de página
Mostrar o número total de resultados retornados por uma consulta e, em seguida, retornar esses resultados em pedaços menores, é fundamental para praticamente todas as páginas de pesquisa.

![][1]

No Azure Search, você deve usar os parâmetros `$count`, `$top` e `$skip` para retornar esses valores. O exemplo a seguir mostra um exemplo de solicitação pelo total de ocorrências, a retorna como `@OData.count`:

        GET /indexes/onlineCatalog/docs?$count=true

Recupera documentos em grupos de 15 e também mostra o total de ocorrências, começando na primeira página:

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

A paginação de resultados exige `$top` e `$skip`, sendo que `$top` especifica quantos itens devem retornar em um lote, e `$skip` especifica quantos itens ignorar. No exemplo a seguir, cada página mostra os próximos 15 itens, indicados por saltos incrementais no parâmetro `$skip` .

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>Layout
Talvez você queira mostrar em uma página de resultados da pesquisa uma imagem em miniatura, um subconjunto dos campos e um link para uma página completa sobre um produto.

 ![][2]

No Azure Search, você usaria `$select` e um comando de pesquisa para implementar essa experiência.

Para retornar um subconjunto dos campos para um layout lado a lado:

        GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

Arquivos de imagem e de mídia não são diretamente pesquisáveis e devem ser armazenados em outra plataforma de armazenamento, por exemplo, o armazenamento do Blob do Azure, para reduzir os custos. No índice e nos documentos, defina um campo que armazena o endereço da URL do conteúdo externo. Em seguida, use o campo como uma referência de imagem. A URL da imagem deve estar no documento.

Para recuperar uma página de descrição de produto em um evento **onClick** , use [Pesquisar Documento](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) para passar a chave do documento que será recuperada. O tipo de dados da chave é `Edm.String`. Neste exemplo, é *246810*. 

        GET /indexes/onlineCatalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Classificar por relevância, por classificação ou por preço
Por padrão, as ordens de classificação normalmente usam a opção de relevância, mas é comum disponibilizar ordens de classificação alternativas para que os clientes possam reorganizar rapidamente esses resultados em uma ordem de classificação diferente.

 ![][3]

No Azure Search, a classificação baseia-se na expressão `$orderby`, para todos os campos indexados como `"Sortable": true.`

A relevância está fortemente associada a perfis de pontuação. Você pode usar a pontuação padrão, que depende de análise de texto e estatísticas para classificar todos os resultados, com pontuações mais altas sendo atribuídas a documentos com mais correspondências, ou correspondências mais sólidas, de um termo de pesquisa.

As ordens de classificação alternativas normalmente são associadas a eventos **onClick** que remetem a um método que cria a ordem de classificação. Por exemplo, este elemento de página:

 ![][4]

Você criaria um método que aceitasse a opção de classificação selecionada como entrada, e retornaria uma lista ordenada dos critérios associados a essa opção.

 ![][5]

> [!NOTE]
> Embora a pontuação padrão seja suficiente para muitos cenários, recomendamos basear a relevância em um perfil personalizado de pontuação. Um perfil personalizado de pontuação permite um aumento dos itens mais úteis para o seu negócio. Confira [Adicionar um perfil de pontuação](https://docs.microsoft.com/rest/api/searchservice/Add-scoring-profiles-to-a-search-index) para saber mais. 
> 
> 

## <a name="faceted-navigation"></a>Navegação facetada
A navegação de pesquisa é comum em uma página de resultados, e normalmente fica na lateral ou na parte superior de uma página. No Azure Search, a navegação facetada proporciona uma pesquisa autodirecionada com base em filtros predefinidos. Confira [Navegação facetada no Azure Search](search-faceted-navigation.md) para mais detalhes.

## <a name="filters-at-the-page-level"></a>Filtros no nível da página
Se o design da solução incluir páginas de pesquisa dedicada para tipos específicos de conteúdo (por exemplo, um aplicativo de varejo online com os departamentos relacionados na parte superior da página), você poderá inserir uma expressão de filtro, junto com um evento **onClick** , para abrir uma página em um estado pré-filtrado. 

Você pode enviar um filtro com ou sem uma expressão de pesquisa. Por exemplo, a seguinte solicitação filtrará o nome da marca, retornando somente os documentos que correspondem a ele.

        GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

Confira [Pesquisar Documentos (API de Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para saber mais sobre expressões `$filter`.

## <a name="see-also"></a>Veja também
* [API REST do Serviço de Azure Search](https://docs.microsoft.com/rest/api/searchservice)
* [Operações de índice](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
* [Operações de documento.](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
* [Vídeos e tutoriais sobre o Azure Search](search-video-demo-tutorial-list.md)
* [Navegação facetada no Azure Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png 
