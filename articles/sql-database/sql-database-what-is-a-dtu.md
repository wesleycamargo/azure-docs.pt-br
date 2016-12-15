---
title: "Banco de Dados SQL: O que é um DTU? | Microsoft Docs"
description: "Noções básicas sobre o que uma unidade de transação do Banco de Dados SQL."
keywords: "opções de banco de dados, desempenho do banco de dados"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler
ms.assetid: 89e3e9ce-2eeb-4949-b40f-6fc3bf520538
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/06/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4edae4ef772dc68b1f0efa9e758f26277dd35c44
ms.openlocfilehash: bbc9bef4039b5c898c44a0e39b78b7b28c225c32


---
# <a name="explaining-database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Explicação de DTUs (Unidades de transação de banco de dados) e eDTUs (Unidades de transação de banco de dados elástico)
Este artigo explica as DTUs (Unidades de transação de banco de dados) e eDTUs (Unidades de transação de banco de dados elástico), e o que acontece quando você atinge o máximo de DTUs ou eDTUs.  

## <a name="what-are-database-transaction-units-dtus"></a>O que são DTUs (Unidades de transação do banco de dados)
Uma DTU é uma unidade de medida dos recursos que têm garantia de disponibilidade para um Banco de Dados SQL do Azure autônomo em um nível de desempenho específico dentro de uma [camada de serviço de banco de dados autônomo](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels). Uma DTU é uma medida combinada de CPU, memória e dados de E/S, e E/S de log de transações, em uma proporção determinada por uma carga de trabalho OLTP de parâmetro projetada para ser igual às cargas de trabalho OLTP reais. Dobrar as DTUs aumentando o nível de desempenho de um banco de dados equivale a dobrar o conjunto de recursos disponíveis para esse banco de dados. Por exemplo, um banco de dados Premium P11 com 1.750 DTUs fornece 350x mais capacidade de computação DTU que um banco de dados básico com 5 DTUs. Para entender a metodologia por trás da carga de trabalho OLTP de parâmetro usada para determinar a combinação de DTU, consulte [Visão geral do Banco de Dados SQL de parâmetro](sql-database-benchmark-overview.md).

![Introdução ao Banco de Dados SQL: DTUs de banco de dados individual por camada e por nível](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Você pode [alterar os níveis de serviço](sql-database-scale-up.md) a qualquer momento com tempo de inatividade mínimo para seu aplicativo (tempo médio de quatro segundos). Para muitos negócios e aplicativos, ser capaz de criar bancos de dados e ajustar o desempenho do banco de dados individual sob demanda é o suficiente, especialmente se os padrões de uso são relativamente previsíveis. Mas se você tiver os padrões de uso imprevisíveis, pode ser difícil de gerenciar os custos e o seu modelo de negócios. Para este cenário, você pode usar um pool elástico com um determinado número de eDTUs.

## <a name="what-are-elastic-database-transaction-units-edtus"></a>O que são eDTUs (Unidades de transação de banco de dados elástico)
Uma eDTU é uma unidade de medida do conjunto de recursos (DTUs) que podem ser compartilhados entre um conjunto de bancos de dados em um servidor SQL do Azure - chamado de [pool elástico](sql-database-elastic-pool.md). Pools elásticos fornecem uma solução simples e econômica para gerenciar as metas de desempenho para vários bancos de dados que têm padrões de uso muito variáveis e imprevisíveis. Confira [Pools e elásticos e camadas de serviço](sql-database-service-tiers.md#elastic-database-pool-service-tiers-and-performance-in-edtus) para saber mais.

![Introdução ao Banco de Dados SQL: eDTUs por camada e por nível](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Um pool é fornecido com um número definido de eDTUs por um preço definido. Dentro do pool, os bancos de dados individuais recebem a flexibilidade para dimensionar automaticamente no conjunto de parâmetros. Sob carga pesada, um banco de dados pode consumir mais eDTUs para atender à demanda. Bancos de dados sob cargas leves consumem menos e bancos de dados sem carga não consomem nenhum eDTU. O provisionamento de recursos para o pool inteiro em vez do bancos de dados único simplifica as tarefas de gerenciamento. Além disso, você tem um orçamento previsível para o pool.

eDTUs adicionais podem ser adicionados a um pool existente sem tempo de inatividade do banco de dados ou impacto sobre os bancos de dados no pool elástico. Da mesma forma, se eDTUs extras não forem mais necessários, eles poderão ser removidos de um pool existente a qualquer momento. Você pode adicionar ou subtrair bancos de dados para o pool. Se um banco de dados é previsível por estar subutilizando recursos, mova-o.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Como determinar o número de DTUs necessárias para minha carga de trabalho?
Se você quiser migrar uma carga de trabalho local existente, ou uma carga de trabalho virtual, para o Banco de Dados SQL do Azure, use a [Calculadora de DTU](http://dtucalculator.azurewebsites.net/) para aproximar o número de DTUs necessárias. Para uma carga de trabalho existente do Banco de Dados SQL do Azure, use [Análise de Desempenho de Consultas do Banco de Dados SQL](sql-database-query-performance.md) para compreender o consumo de recursos do banco de dados (DTUs) e saber mais sobre como otimizar sua carga de trabalho. Você também pode usar o DMV [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) para obter as informações de consumo de recursos da última hora. Como alternativa, também é possível consultar o modo de exibição de catálogo [Sys. resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) para obter os mesmos dados para os últimos 14 dias, porém com uma fidelidade menor de médias de cinco minutos.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Como posso saber se eu teria benefícios com um pool elástico de recursos?
Pools também são indicados para um grande número de bancos de dados com padrões de utilização específicos. Para um determinado banco de dados, esse padrão é caracterizado por baixa utilização média com picos de utilização relativamente pouco frequentes. O Banco de Dados SQL avalia automaticamente a utilização histórica de recursos dos bancos de dados em um servidor de Banco de Dados SQL existente e recomenda a configuração de pool apropriada no portal do Azure. Para saber mais , confira [Quando um pool de banco de dados elástico deve ser usado?](sql-database-elastic-pool-guidance.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>O que acontece quando eu atinjo o máximo de DTUs
Os níveis de desempenho são calibrados e controlados para fornecer os recursos necessários para executar sua carga de trabalho de banco de dados até os limites máximos permitidos para a sua camada de serviço e nível de desempenho selecionados. Se a sua carga de trabalho está atingindo os limites em um dos limites de CPU/E/S de dados/E/S de Log, você continuará a receber os recursos no nível máximo permitido, mas provavelmente você observará latências maiores para suas consultas. Esses limites não resultam em erros, mas apenas em uma lentidão na carga de trabalho, a menos que a lentidão se torne tão grave que as consultas comecem a não serem executadas a tempo. Se você está atingindo os limites do máximo permitido de sessões/solicitações de usuários simultâneos (threads de trabalho), você verá erros explícitos. Confira [Limites de recursos do Banco de Dados SQL](sql-database-resource-limits.md) para saber mais sobre limite de recursos, além de CPU, memória, E/S de dados e E/S do log de transações.

## <a name="next-steps"></a>Próximas etapas
* Consulte [Camada de serviço](sql-database-service-tiers.md) para obter informações sobre as DTUs e eDTUs disponíveis para bancos de dados autônomos e pools elásticos.
* Confira [Limites de recursos do Banco de Dados SQL](sql-database-resource-limits.md) para saber mais sobre limite de recursos, além de CPU, memória, E/S de dados e E/S do log de transações.
* Confira [Análise de Desempenho de Consultas de Banco de Dados SQL](sql-database-query-performance.md) para compreender o consumo (DTUs).
* Confira [Visão geral do parâmetro de Banco de Dados SQL](sql-database-benchmark-overview.md) para entender a metodologia por trás da carga de trabalho OLTP de parâmetro usada para determinar a combinação de DTU.



<!--HONumber=Dec16_HO1-->


