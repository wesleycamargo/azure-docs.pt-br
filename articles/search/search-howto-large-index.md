---
title: Expandir a indexação com indexadores internos – Azure Search
description: Adicione novos elementos, atualize elementos ou documentos existentes ou exclua documentos obsoletos em uma indexação de recompilação completa ou incremental parcial para atualizar um índice do Azure Search.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 5f268de43f4f860458c062cb80e5bea0134b4407
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316653"
---
# <a name="how-to-scale-out-indexing-in-azure-search"></a>Como expandir a indexação no Azure Search

Com o crescimento dos volumes de dados ou as alterações nas necessidades de processamento, é possível que as [tarefas simples de recompilação e reindexação](search-howto-reindex.md) não sejam suficientes. 

Como uma primeira etapa para atender ao aumento da demanda, é recomendável que você aumente a [escala e a capacidade](search-capacity-planning.md) dentro dos limites do serviço existente. 

Uma segunda etapa, se você puder usar [indexadores](search-indexer-overview.md), adicionará mecanismos para indexação escalonável. Os indexadores vêm com um agendador interno que permite dividir a indexação em intervalos regulares ou estender o processamento para além da janela de 24 horas. Além disso, quando combinados com as definições de fonte de dados, os indexadores ajudam a obter uma forma de paralelismo por meio do particionamento de dados e do uso de agendas para executar em paralelo.

### <a name="scheduled-indexing-for-large-data-sets"></a>Indexação agendada para conjuntos de dados grandes

O agendando é um mecanismo importante para processar conjuntos de dados grandes e análises de execução lenta, como a análise de imagens em um pipeline de pesquisa cognitiva. O processamento do indexador opera em uma janela de 24 horas. Se o processamento não for concluído em até 24 horas, o agendamento de comportamentos do indexador poderá entrar em ação. 

Por design, a indexação agendada começa em intervalos específicos, normalmente com o trabalho concluído antes da retomada no próximo intervalo agendado. No entanto, se o processamento não for concluído dentro do intervalo, o indexador será interrompido (por esgotar o tempo). No próximo intervalo, o processamento será retomado de onde parou, com o sistema controlando onde isso ocorreu. 

Em termos práticos, para cargas de índice que abrangem vários dias, você pode colocar o indexador em um agendamento de 24 horas. Quando a indexação é retomada para o próximo período de 24 horas, ela é reiniciada no último documento válido conhecido. Dessa forma, um indexador pode percorrer uma lista de pendências de documentos em uma série de dias até que todos os documentos não processados sejam processados. Para obter mais informações sobre essa abordagem, confira [Indexando conjuntos de dados grandes](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Indexação paralela

Uma segunda opção é configurar uma estratégia de indexação paralela. Para requisitos de indexação com atividade de computação intensa, que não sejam de rotina, como o OCR em documentos digitalizados em um pipeline de pesquisa cognitiva, uma estratégia de indexação paralela pode ser a abordagem certa para essa meta específica. Em um pipeline de enriquecimento de pesquisa cognitiva, a análise de imagens e o processamento de idioma natural são de longa execução. A indexação paralela em um serviço que não esteja tratando simultaneamente de solicitações de consulta pode ser uma opção viável trabalhar em um grande corpo de conteúdo de processamento lento. 

Uma estratégia para processamento paralelo tem estes elementos:

+ Divida a fonte de dados entre vários contêineres ou várias pastas virtuais dentro do mesmo contêiner. 
+ Mapeie cada miniconjunto de dados para uma [fonte de data](https://docs.microsoft.com/rest/api/searchservice/create-data-source), emparelhados com seu próprio [indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Para pesquisa cognitiva, referencie o mesmo [conjunto de habilidades](https://docs.microsoft.com/rest/api/searchservice/create-skillset) em cada definição de indexador.
+ Grave no mesmo índice de pesquisa de destino. 
+ Agende todos os indexadores para serem executados ao mesmo tempo.

> [!Note]
> O Azure Search não permite a dedicação de réplicas ou de partições para cargas de trabalho específicas. O risco de indexação simultânea pesada sobrecarrega o sistema prejudicando o desempenho da consulta. Se você tiver um ambiente de teste, implemente a indexação paralela lá primeiro para entender as vantagens e desvantagens.

## <a name="configure-parallel-indexing"></a>Configurar a indexação paralela

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