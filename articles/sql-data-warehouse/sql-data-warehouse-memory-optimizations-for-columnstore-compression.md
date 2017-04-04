---
title: "Melhorar o desempenho do índice columnstore no Azure SQL | Microsoft Docs"
description: "Reduza os requisitos de memória ou aumente a memória disponível para maximizar o número de linhas que um índice columnstore compacta em cada rowgroup."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 11/18/2016
ms.author: shigu;barbkess
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 8d189256ed4c876859203406cda95ce0be36c96c
ms.lasthandoff: 03/29/2017


---

# <a name="memory-optimizations-for-columnstore-compression"></a>Otimizações de memória para compactação de columnstore

Reduza os requisitos de memória ou aumente a memória disponível para maximizar o número de linhas que um índice columnstore compacta em cada rowgroup.  Use estes métodos para melhorar as taxas de compactação e o desempenho da consulta em índices columnstore.

## <a name="why-the-rowgroup-size-matters"></a>Por que o tamanho do rowgroup é importante
Como um índice columnstore examina uma tabela com o exame de segmentos de coluna de rowgroups individuais, maximizar o número de linhas em cada rowgroup melhora o desempenho da consulta. Quando os rowgroups têm um número elevado de linhas, a compactação de dados melhora, o que significa que há menos dados para serem lidos do disco.

Para obter mais informações sobre rowgroups, consulte [Guia de índices Columnstore](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Tamanho de destino para rowgroups
Para o melhor desempenho de consulta, o objetivo é maximizar o número de linhas por rowgroup em um índice columnstore. Um rowgroup pode ter, no máximo, 1.048.576 linhas. Não é um problema ter o número máximo de linhas por rowgroup. Os índices Columnstore obtêm um bom desempenho quando os rowgroups têm, pelo menos, 100.000 linhas.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Os rowgroups podem ser cortados durante a compactação

Durante um carregamento em massa ou uma recompilação de índices columnstore, às vezes, não há memória suficiente disponível para compactar todas as linhas designadas para cada rowgroup. Quando há pressão de memória, os índices columnstore cortam o tamanho do rowgroup para que a compactação no columnstore possa ser bem-sucedida.

Quando não há memória suficiente para compactar, pelo menos, 10.000 linhas em cada rowgroup, o SQL Data Warehouse gera um erro.

Para obter mais informações sobre o carregamento em massa, consulte [Carregamento em massa em um índice columnstore clusterizado](https://msdn.microsoft.com/en-us/library/dn935008.aspx#Bulk load into a clustered columnstore index).

## <a name="how-to-estimate-memory-requirements"></a>Como estimar os requisitos de memória

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

O máximo de memória necessário para compactar um rowgroup é aproximadamente

- 72 MB +
- \#linhas \* \#colunas \* 8 bytes +
- \#linhas \* \#colunas de cadeia de caracteres curta \* 32 bytes +
- \#colunas de cadeia de caracteres longa \* 16 MB para o dicionário de compactação

em que as colunas de cadeia de caracteres curta usam tipos de dados de cadeia de caracteres de < = 32 bytes e as colunas de cadeia de caracteres longa usam tipos de dados de cadeia de caracteres de > 32 bytes.

As cadeias de caracteres longas são compactadas com um método de compactação projetado para a compactação de texto. Esse método de compactação usa um *dicionário* para armazenar os padrões de texto. O tamanho máximo de um dicionário é de 16 MB. Há apenas um dicionário para cada coluna de cadeia de caracteres longa no rowgroup.

Para obter uma discussão detalhada sobre os requisitos de memória de columnstore, assista ao vídeo [Escala do SQL Data Warehouse do Azure: configuração e diretrizes](https://myignite.microsoft.com/videos/14822).

## <a name="ways-to-reduce-memory-requirements"></a>Maneiras de reduzir os requisitos de memória

Use as técnicas a seguir para reduzir os requisitos de memória para compactar rowgroups em índices columnstore.

### <a name="use-fewer-columns"></a>Usar menos colunas
Se possível, crie a tabela com menos colunas. Quando um rowgroup é compactado no columnstore, o índice columnstore compacta cada segmento de coluna separadamente. Portanto, os requisitos de memória para compactação de um rowgroup aumentam de acordo com o número de colunas.


### <a name="use-fewer-string-columns"></a>Usar menos colunas de cadeia de caracteres
As colunas de tipos de dados de cadeia de caracteres exigem mais memória do que os tipos de dados numéricos e de data. Para reduzir os requisitos de memória, considere remover as colunas de cadeia de caracteres de tabelas de fatos e colocá-las em tabelas de dimensão menores.

Requisitos de memória adicionais para a compactação de cadeia de caracteres:

- Tipos de dados de cadeia de caracteres de até 32 caracteres podem exigir 32 bytes adicionais por valor.
- Tipos de dados de cadeia de caracteres com mais de 32 caracteres são compactados usando métodos de dicionário.  Cada coluna no rowgroup pode exigir até 16 MB adicionais para a criação do dicionário.

### <a name="avoid-over-partitioning"></a>Evitar o excesso de particionamento

Os índices Columnstore criam um ou mais rowgroups por partição. No SQL Data Warehouse, o número de partições aumenta rapidamente porque os dados são distribuídos e cada distribuição é particionada. Se a tabela tiver um número excessivo de partições, talvez não haja linhas suficientes para preencher os rowgroups. A falta de linhas não cria a pressão de memória durante a compactação, mas leva a rowgroups que não obtêm o melhor desempenho de consulta de columnstore.

Outro motivo para evitar o excesso de particionamento é que há uma sobrecarga de memória no carregamento de linhas em um índice columnstore em uma tabela particionada. Durante o carregamento, várias partições poderão receber as linhas de entrada, que são mantidas na memória até que cada partição tenha linhas suficientes para ser compactada. Ter um número excessivo de partições cria pressão de memória adicional.

### <a name="simplify-the-load-query"></a>Simplificar a consulta de carga

O banco de dados compartilha a concessão de memória para uma consulta entre todos os operadores na consulta. Quando uma consulta de carga tem classificações e junções complexas, a memória disponível para compactação é reduzida.

Crie a consulta de carga para que ela se concentre apenas no carregamento da consulta. Se você precisar executar transformações nos dados, execute-as separadamente da consulta de carga. Por exemplo, prepare os dados em uma tabela de heap, execute as transformações e carregue a tabela de preparo no índice columnstore. Você também pode carregar os dados primeiro e usar o sistema MPP para transformar os dados.

### <a name="adjust-maxdop"></a>Ajustar o MAXDOP

Cada distribuição compacta rowgroups no columnstore em paralelo quando há mais de um núcleo de CPU disponível por distribuição. O paralelismo exige recursos de memória adicionais, o que pode levar à pressão de memória e ao corte de rowgroup.

Para reduzir a pressão de memória, use a dica de consulta MAXDOP para forçar a operação de carregamento a ser executada em modo serial em cada distribuição.

```
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQUota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Maneiras de alocar mais memória

O tamanho da DWU e a classe de recurso de usuário em conjunto determinam a quantidade de memória disponível para uma consulta de usuário. Para aumentar a concessão de memória para uma consulta de carga, você pode aumentar o número de DWUs ou aumentar a classe de recurso.

- Para aumentar as DWUs, consulte [Como faço para escalar o desempenho?](sql-data-warehouse-manage-compute-overview.md#scale-compute)
- Para alterar a classe de recurso de uma consulta, consulte [Alterar um exemplo de classe de recurso de usuário](sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example).

Por exemplo, em 100 DWUs, um usuário na classe de recurso smallrc pode usar 100 MB de memória para cada distribuição. Para obter detalhes, consulte [Simultaneidade no SQL Data Warehouse](sql-data-warehouse-develop-concurrency.md).

Suponha que você determina que precisa de 700 MB de memória para obter tamanhos de rowgroup de alta qualidade. Esses exemplos mostram como você pode executar a consulta de carga com memória suficiente.

- Usando 1000 DWUs e mediumrc, a concessão de memória é de 800 MB
- Usando 600 DWUs e largerc, a concessão de memória é de 800 MB.


## <a name="next-steps"></a>Próximas etapas

Para encontrar mais maneiras de melhorar o desempenho no SQL Data Warehouse, consulte a [Visão geral do desempenho](sql-data-warehouse-overview-manage-user-queries.md).

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->

