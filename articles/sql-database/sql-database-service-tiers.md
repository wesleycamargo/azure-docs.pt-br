---
title: "Desempenho do Banco de Dados SQL: camadas de serviço | Microsoft Docs"
description: "Compare as camadas de serviço do Banco de Dados SQL."
keywords: "opções de banco de dados, desempenho do banco de dados"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/04/2017
ms.author: carlrab; janeng
translationtype: Human Translation
ms.sourcegitcommit: a40319d3e53c07a94bc34714ca7393c2747fb50c
ms.openlocfilehash: 340656b896763914c2f6d37c72ce1d5323d1411e


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Opções e desempenho de Banco de Dados SQL: compreender o que está disponível em cada camada de serviço
O [Banco de Dados SQL do Azure](sql-database-technical-overview.md) oferece três camadas de serviço, **Basic**, **Standard** e **Premium**, com vários níveis de desempenho para lidar com cargas de trabalho diferentes. Os níveis de desempenho mais elevados fornecem recursos cada vez maiores, projetados para oferecer uma taxa de transferência crescente. Você pode alterar as [camadas de serviços e os níveis de desempenho](sql-database-scale-up.md) sem tempo de inatividade. Todas as camadas de serviço, Basic, Standard e Premium têm um SLA de tempo de atividade de 99,99%, opções de continuidade dos negócios flexíveis, recursos de segurança e cobrança por hora. 

Você pode criar bancos de dados individuais com recursos dedicados no [nível de desempenho](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) selecionado. Você também pode gerenciar vários bancos de dados em um [pool elástico](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) no qual os recursos são compartilhados entre os bancos de dados. Os recursos disponíveis para bancos de dados individuais são expressos em termos de DTUs (Unidades de transação de banco de dados), e para pools elásticos em termos de DTUs elásticos (eDTUs). Para saber mais sobre DTUs e eDTUs, confira [O que é uma DTU?](sql-database-what-is-a-dtu.md). 

Nos dois casos, as camadas de serviço incluem **Básico**, **Standard** e **Premium**. 

## <a name="choosing-a-service-tier"></a>Como escolher uma camada de serviço
A tabela a seguir fornece exemplos das camadas mais adequadas para cargas de trabalho de aplicativos diferentes.

| Camada de serviço | Cargas de trabalho de destino |
| :--- | --- |
| **Básico** | Mais adequada para um banco de dados pequeno, suporta normalmente uma única operação ativa em um determinado momento. Os exemplos incluem bancos de dados usados para desenvolvimento ou teste ou aplicativos de pequena escala usados com pouca frequência. |
| **Standard** |Para ele ir para a opção para aplicativos em nuvem com requisitos de desempenho de E/S baixo a médio, oferecendo suporte a várias consultas simultâneas. Os exemplos incluem aplicativos da web ou de grupo de trabalho. |
| **Premium** | Projetado para volume transacional alto com requisitos de desempenho de e/s altos, suportando muitos usuários simultâneos. Os exemplos são bancos de dados com suporte a aplicativos de missão críticos. |

Primeiro, decida se deseja executar um banco de dados individual ou se deseja agrupar bancos de dados que compartilham recursos. Examine as [considerações de pool elástico](sql-database-elastic-pool-guidance.md). Para decidir sobre uma camada de serviço, inicie determinando os recursos de banco de dados mínimos que você precisa:

* Tamanho máximo do banco de dados individual (no máximo 2 GB para o Basic, 250 GB para o Standard e de 500 GB a 1 TB para o Premium nos níveis de desempenho de alto nível)
* Máximo de armazenamento total no caso de um pool elástico (117 GB para Basic, 1200 para Standard e 750 para Premium)
* Número máximo de bancos de dados por pool (400 para Basic, 400 para Standard e 50 para Premium)
* Período de retenção de backup do banco de dados (7 dias para Basic, 35 dias para Standard e Premium)

Depois de determinar a camada de serviço mínima, você estará pronto para determinar o nível de desempenho do banco de dados (o número de DTUs). Os níveis de desempenho standard S2 e S3 são, em muitos casos, um bom ponto de partida. Para bancos de dados com altas exigências de CPU ou E/S, os níveis de desempenho Premium são o ponto de partida correto. O Premium oferece mais CPU e começa em 10 vezes mais E/S em comparação com o nível de desempenho Standard mais alto.

Depois de escolher inicialmente um nível de desempenho, você pode e, em seguida, dimensionar o [banco de dados individual](sql-database-scale-up.md) ou o [pool elástico](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) ou reduzir dinamicamente com base na experiência real. Para cenários de migração, você também pode usar a [Calculadora de DTU](http://dtucalculator.azurewebsites.net/) para ter uma ideia aproximada do número de DTUs necessários. 

>
> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

## <a name="single-database-service-tiers-and-performance-levels"></a>Camadas de serviço e níveis de desempenho de banco de dados individual
Para bancos de dados individuais, há vários níveis de desempenho dentro de cada camada de serviço. Você tem a flexibilidade de escolher o nível que melhor atenda às suas demandas de carga de trabalho. Se você precisar expandir ou reduzir, poderá alterar facilmente as camadas do banco de dados. Consulte [Alterando camadas de serviços e níveis de desempenho do banco de dados](sql-database-scale-up.md) para obter detalhes.

Independentemente do número de bancos de dados hospedados, o banco de dados ainda obterá um conjunto garantido de recursos e as características de desempenho esperadas de seu banco de dados não são afetadas.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Para obter uma explicação detalhada de todas as outras linhas nesta tabela de camadas de serviço, consulte [Limites e recursos da camada de serviço](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Camadas de serviço e desempenho em eDTUs do pool elástico

Os pools permitem que esses bancos de dados compartilhem e consumam os recursos de eDTU sem a necessidade de atribuir um nível de desempenho específico para todos os bancos de dados no pool. Por exemplo, um banco de dados individual em um pool Standard pode usar de 0 eDTUs até o máximo de eDTU de banco de dados configurado por você durante a definição do pool. Os pools permitem que vários bancos de dados com diferentes cargas de trabalho usem os recursos de eDTU disponíveis para todo o pool de forma eficiente. Confira [Considerações de preço e desempenho para um pool elástico](sql-database-elastic-pool-guidance.md) para obter detalhes.

A tabela a seguir descreve as características das camadas de serviço do pool.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Cada banco de dados dentro de um pool também cumpre as características do banco de dados individual para essa camada. Por exemplo, o pool Basic tem um limite máximo de sessões por pool de 4800 a 28800, mas um banco de dados individual dentro de um pool Básico tem um limite de banco de dado de 300 sessões.


## <a name="next-steps"></a>Próximas etapas

* Conheça os detalhes dos [pools elásticos](sql-database-elastic-pool-guidance.md) e [considerações sobre o preço e o desempenho dos pools elásticos](sql-database-elastic-pool-guidance.md).
* Saiba como [Monitorar, gerenciar e redimensionar pools elásticos](sql-database-elastic-pool-manage-portal.md) e [Monitorar o desempenho de bancos de dados individuais](sql-database-single-database-monitor.md).
* Agora que você conhece as camadas do Banco de Dados SQL, teste-as usando uma versão de [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) e [aprenda a criar seu primeiro banco de dados SQL](sql-database-get-started.md).




<!--HONumber=Dec16_HO3-->


