---
title: Recompilar um índice do Azure Search ou atualizar o conteúdo pesquisável – Azure Search
description: Adicione novos elementos, atualize elementos ou documentos existentes ou exclua documentos obsoletos em uma indexação de recompilação completa ou incremental parcial para atualizar um índice do Azure Search.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 9c9af69e45af6a70c5327393a1c10385ba2c2aed
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316887"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Como recompilar um índice do Azure Search

Recompilar um índice altera sua estrutura, modificando a expressão física do índice no serviço Azure Search. Por outro lado, a atualização de um índice é uma atualização apenas do conteúdo para obter as alterações mais recentes de uma fonte de dados externa de contribuição. Este artigo fornece instruções de como atualizar índices estruturalmente e substancialmente.

Permissões de leitura/gravação no nível de serviço são necessárias para atualizações de índice. Programaticamente, você pode chamar APIs REST ou do .NET para recompilar o conteúdo de forma completa ou indexá-lo de forma incremental, com parâmetros que especificam as opções de atualização. 

Geralmente, as atualizações de um índice são sob demanda. No entanto, para índices populados usando [indexadores](search-indexer-overview.md) específicos da fonte, você pode usar um agendador interno. O agendador dá suporte à atualização de documento com uma frequência que começa em 15 minutos e vai até o intervalo e o padrão desejados. Uma taxa de atualização mais rápida requer o envio por push manual das atualizações do índice, talvez por meio de uma gravação dupla nas transações, atualizando a fonte de dados externa e o índice do Azure Search simultaneamente.

## <a name="full-rebuilds"></a>Recompilações completas

Para muitos tipos de atualizações, é necessário recompilar completamente. Uma recompilação completa refere-se à exclusão de um índice, com os dados e metadados, seguida por uma nova população do índice por meio de fontes de dados externas. Programaticamente, [exclua](https://docs.microsoft.com/rest/api/searchservice/delete-index), [crie](https://docs.microsoft.com/rest/api/searchservice/create-index) e [recarregue](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) o índice para recompilá-lo. 

Após a recompilação, lembre-se de que se você estava testando padrões de consulta e perfis de pontuação, poderá haver variação nos resultados da consulta se o conteúdo subjacente tiver sido alterado.

## <a name="when-to-rebuild"></a>Quando recompilar

Planeje recompilações completas frequentes durante o desenvolvimento ativo, quando o esquema de índice está no estado de fluxo.

| Modificação | Status da recompilação|
|--------------|---------------|
| Alterar um nome de campo, um tipo de dados ou seus [atributos de índice](https://docs.microsoft.com/rest/api/searchservice/create-index) | A alteração de uma definição de campo normalmente resulta em uma penalidade de recompilação, com exceção destes [atributos de índice](https://docs.microsoft.com/rest/api/searchservice/create-index): Retrievable, SearchAnalyzer e SynonymMaps. Você pode adicionar os atributos Retrievable, SearchAnalyzer e SynonymMaps a um campo existente sem precisar recompilar o índice.|
| Adicionar um campo | Não há nenhum requisito rígido para recompilar. Os documentos indexados existentes recebem um valor nulo para o novo campo. Em uma futura reindexação, os valores da fonte de dados substituirão os nulos adicionados pelo Azure Search. |
| Excluir um campo | Não é possível excluir um campo diretamente de um índice do Azure Search. Nesse caso, você deve ter fazer com que o aplicativo ignore o campo "excluído" para evitar seu uso. Fisicamente, a definição e o conteúdo do campo permanecem no índice até a próxima vez em que você recompilar o índice usando um esquema que omita o campo em questão.|

> [!Note]
> Uma recompilação também será necessária se você alternar entre camadas. Se em algum momento você decidir adicionar mais capacidade, não haverá nenhuma atualização in-loco. Um novo serviço precisará ser criado no novo ponto de capacidade e os índices precisarão ser compilados desde o início no novo serviço. 

## <a name="partial-or-incremental-indexing"></a>Indexação parcial ou incremental

Quando um índice estiver em produção, o foco mudará para a indexação incremental, geralmente sem interrupções de serviço perceptíveis. A indexação parcial ou incremental é uma carga de trabalho apenas de conteúdo que sincroniza o conteúdo de um índice de pesquisa para refletir o estado do conteúdo em uma fonte de dados de contribuição. Um documento adicionado ou excluído na fonte é adicionado ou excluído no índice. No código, chame a operação [Adicionar, Atualizar ou Excluir Documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ou o equivalente do .NET.

> [!Note]
> Quando são usados indexadores que rastreiam fontes de dados externas, os mecanismos de controle de alterações nos sistemas de origem são utilizados para a indexação incremental. Para o [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), um campo `lastModified` é usado. No [Armazenamento de Tabela do Azure](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` tem a mesma finalidade. Da mesma forma, tanto o [indexador do Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) quanto o [indexador do Azure Cosmos DB](search-howto-index-cosmosdb.md#indexing-changed-documents) têm campos para sinalizar as atualizações de linhas. Para obter mais informações sobre indexadores, confira a [Visão geral do indexador](search-indexer-overview.md).


## <a name="see-also"></a>Consulte também

+ [Visão geral do indexador](search-indexer-overview.md)
+ [Indexar grandes conjuntos de dados em escala](search-howto-large-index.md)
+ [Indexando no portal](search-import-data-portal.md)
+ [Indexador do Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexador de Banco de dados do Azure Cosmos](search-howto-index-cosmosdb.md)
+ [Indexador do Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexador do Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
+ [Segurança no Azure Search](search-security-overview.md)