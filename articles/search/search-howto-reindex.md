---
title: Recriar um índice do Azure Search ou atualizar o conteúdo pesquisável | Microsoft Docs
description: Adicione novos elementos, atualize elementos ou documentos existentes ou exclua documentos obsoletos em uma indexação de recompilação completa ou incremental parcial para atualizar um índice do Azure Search.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: f38054eaf2829149a496f840366b6f2f9e03e12b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942085"
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
| Alterar um nome de campo, um tipo de dados ou seus [atributos de índice](https://docs.microsoft.com/rest/api/searchservice/create-index) | Alterar uma definição de campo normalmente resulta em uma penalidade de recompilação, com exceção deste [atributos de índice](https://docs.microsoft.com/rest/api/searchservice/create-index): Retrievable, SearchAnalyzer, SynonymMaps. Você pode adicionar os atributos Retrievable, SearchAnalyzer e SynonymMaps a um campo existente sem precisar recompilar o índice.|
| Adicionar um campo | Não há nenhum requisito rígido para recompilar. Os documentos indexados existentes recebem um valor nulo para o novo campo. Em uma futura reindexação, os valores da fonte de dados substituirão os nulos adicionados pelo Azure Search. |
| Excluir um campo | Não é possível excluir um campo diretamente de um índice do Azure Search. Nesse caso, você deve ter fazer com que o aplicativo ignore o campo "excluído" para evitar seu uso. Fisicamente, a definição e o conteúdo do campo permanecem no índice até a próxima vez em que você recompilar o índice usando um esquema que omita o campo em questão.|

> [!Note]
> Uma recompilação também será necessária se você alternar entre camadas. Se em algum momento você decidir adicionar mais capacidade, não haverá nenhuma atualização in-loco. Um novo serviço precisará ser criado no novo ponto de capacidade e os índices precisarão ser compilados desde o início no novo serviço. 

## <a name="partial-or-incremental-indexing"></a>Indexação parcial ou incremental

Quando um índice estiver em produção, o foco mudará para a indexação incremental, geralmente sem interrupções de serviço perceptíveis. A indexação parcial ou incremental é uma carga de trabalho apenas de conteúdo que sincroniza o conteúdo de um índice de pesquisa para refletir o estado do conteúdo em uma fonte de dados de contribuição. Um documento adicionado ou excluído na fonte é adicionado ou excluído no índice. No código, chame a operação [Adicionar, Atualizar ou Excluir Documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ou o equivalente do .NET.

> [!Note]
> Quando são usados indexadores que rastreiam fontes de dados externas, os mecanismos de controle de alterações nos sistemas de origem são utilizados para a indexação incremental. Para o [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), um campo `lastModified` é usado. No [Armazenamento de Tabela do Azure](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` tem a mesma finalidade. Da mesma forma, tanto o [indexador do Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) quanto o [indexador do Azure Cosmos DB](search-howto-index-cosmosdb.md#indexing-changed-documents) têm campos para sinalizar as atualizações de linhas. Para obter mais informações sobre indexadores, confira a [Visão geral do indexador](search-indexer-overview.md).

## <a name="scale-out-indexing"></a>Indexação de expansão

Com o crescimento dos volumes de dados ou as alterações nas necessidades de processamento, é possível que as tarefas simples de recompilação e reindexação não sejam suficientes. Como uma primeira etapa para atender ao aumento da demanda, é recomendável que você aumente a [escala e a capacidade](search-capacity-planning.md) dentro dos limites do serviço existente. 

Se você puder usar [indexadores](search-indexer-overview.md), haverá mecanismos de expansão adicionais disponíveis. Os indexadores vêm com um agendador interno que permite dividir a indexação em intervalos regulares ou estender o processamento para além da janela de 24 horas. Além disso, quando combinados com as definições de fonte de dados, os indexadores ajudam a obter uma forma de paralelismo por meio do particionamento de dados e do uso de agendas para executar em paralelo.

### <a name="scheduled-indexing-for-large-data-sets"></a>Indexação agendada para conjuntos de dados grandes

O agendando é um mecanismo importante para processar conjuntos de dados grandes e análises de execução lenta, como a análise de imagens em um pipeline de pesquisa cognitiva. O processamento do indexador opera em uma janela de 24 horas. Se o processamento não for concluído em até 24 horas, o agendamento de comportamentos do indexador poderá entrar em ação. 

Por design, a indexação agendada começa em intervalos específicos, normalmente com o trabalho concluído antes da retomada no próximo intervalo agendado. No entanto, se o processamento não for concluído dentro do intervalo, o indexador será interrompido (por esgotar o tempo). No próximo intervalo, o processamento será retomado de onde parou, com o sistema controlando onde isso ocorreu. 

Em termos práticos, para cargas de índice que abrangem vários dias, você pode colocar o indexador em um agendamento de 24 horas. Quando a indexação é retomada para o próximo período de 24 horas, ela é reiniciada no último documento válido conhecido. Dessa forma, um indexador pode percorrer uma lista de pendências de documentos em uma série de dias até que todos os documentos não processados sejam processados. Para obter mais informações sobre essa abordagem, confira [Indexando conjuntos de dados grandes](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Indexação paralela

Uma segunda opção é configurar uma estratégia de indexação paralela. Para requisitos de indexação com atividade de computação intensa, que não sejam de rotina, como o OCR em documentos digitalizados em um pipeline de pesquisa cognitiva, uma estratégia de indexação paralela pode ser a abordagem certa para essa meta específica. Em um pipeline de enriquecimento de pesquisa cognitiva, a análise de imagens e o processamento de idioma natural são de longa execução. A indexação paralela em um serviço que não esteja tratando simultaneamente de solicitações de consulta pode ser uma opção viável trabalhar em um grande corpo de conteúdo de processamento lento. 

Uma estratégia para processamento paralelo tem estes elementos:

+ Divida a fonte de dados entre vários contêineres ou várias pastas virtuais dentro do mesmo contêiner. 
+ Mapeie cada miniconjunto de dados para uma [fonte de data](https://docs.microsoft.com/rest/api/searchservice/create-data-source), emparelhados com seu próprio [indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Para pesquisa cognitiva, referencie o mesmo [conjunto de habilidades](ref-create-skillset.md) em cada definição de indexador.
+ Grave no mesmo índice de pesquisa de destino. 
+ Agende todos os indexadores para serem executados ao mesmo tempo.

> [!Note]
> O Azure Search não permite a dedicação de réplicas ou de partições para cargas de trabalho específicas. O risco de indexação simultânea pesada sobrecarrega o sistema prejudicando o desempenho da consulta. Se você tiver um ambiente de teste, implemente a indexação paralela lá primeiro para entender as vantagens e desvantagens.

### <a name="configure-parallel-indexing"></a>Configurar a indexação paralela

Para os indexadores, a capacidade de processamento geralmente é baseada em um subsistema de indexador para cada SU (unidade de serviço) usada pelo serviço de pesquisa. É possível provisionar vários indexadores simultâneos nos serviços do Azure Search nos níveis Básico ou Standard com pelo menos duas réplicas. 

1. No [portal do Azure](https://portal.azure.com), na página **Visão geral** do painel do serviço de pesquisa, verifique o **Tipo de preço** para confirmar se ele permite a indexação paralela. Os níveis Básico e Standard oferecem várias réplicas.

2. Em **Configurações** > **Escala**, [aumente as réplicas](search-capacity-planning.md) para o processamento paralelo: uma réplica adicional para cada carga de trabalho do indexador. Deixe um número suficiente para o volume de consulta existente. Não é vantajoso sacrificar as cargas de trabalho de consulta para indexação.

3. Distribua os dados em vários contêineres em um nível que os indexadores do Azure Search possam alcançar. Podem ser várias tabelas no Banco de Dados SQL do Azure, vários contêineres no Armazenamento de Blobs do Azure ou várias coleções. Defina um objeto de fonte de dados para cada tabela ou contêiner.

4. Crie e agende vários indexadores a serem executados em paralelo:

   + Considere um serviço com seis réplicas. Configure seis indexadores, cada um mapeado para uma fonte de dados que contenha um sexto do conjunto de dados para uma divisão de 6 partes de todo o conjunto de dados. 

   + Aponte cada indexador para o mesmo índice. Para cargas de trabalho de pesquisa cognitiva, aponte cada indexador para o mesmo conjunto de habilidades.

   + Dentro de cada definição de indexador, agende o mesmo padrão de execução de tempo de execução. Por exemplo, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` cria uma agenda em 15/05/2018 em todos os indexadores, em execução em intervalos de oito horas.

No horário agendado, todos os indexadores começam a execução, o carregamento de dados, a aplicação de enriquecimentos (se você tiver configurado um pipeline de pesquisa cognitiva) e a gravação no índice. o Azure Search não bloqueia o índice para atualizações. As gravações simultâneas são gerenciadas, com repetição quando uma gravação específica não é bem-sucedida na primeira tentativa.

> [!Note]
> Ao aumentar réplicas, considere aumentar a contagem de partições se o tamanho do índice for projetado para aumentar significativamente. As partições armazenam fatias de conteúdo indexado. Quanto mais partições houver, menor será a fatia que cada uma precisará armazenar.

## <a name="see-also"></a>Consulte também

+ [Visão geral do indexador](search-indexer-overview.md)
+ [Indexando no portal](search-import-data-portal.md)
+ [Indexador do Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexador de Banco de dados do Azure Cosmos](search-howto-index-cosmosdb.md)
+ [Indexador do Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexador do Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
+ [Segurança no Azure Search](search-security-overview.md)