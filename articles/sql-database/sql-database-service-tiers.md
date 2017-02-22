---
title: "Desempenho do Banco de Dados SQL: camadas de serviço | Microsoft Docs"
description: "Compare as camadas de serviço do Banco de Dados SQL."
keywords: "opções de banco de dados, desempenho do banco de dados"
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/09/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 920143756a0e0b369cf839681f9c4308f77a4af0


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Opções e desempenho de Banco de Dados SQL: compreender o que está disponível em cada camada de serviço
O [Banco de Dados SQL do Azure](sql-database-technical-overview.md) oferece três camadas de serviço, **Basic**, **Standard** e **Premium**, com vários níveis de desempenho para lidar com cargas de trabalho diferentes. Os níveis de desempenho mais elevados fornecem recursos cada vez maiores, projetados para oferecer uma taxa de transferência crescente. Você pode alterar as [camadas de serviços e os níveis de desempenho](sql-database-service-tiers.md) sem tempo de inatividade. Todas as camadas de serviço, Basic, Standard e Premium têm um SLA de tempo de atividade de 99,99%, opções de continuidade dos negócios flexíveis, recursos de segurança e cobrança por hora. 

Você pode criar bancos de dados individuais com recursos dedicados no [nível de desempenho](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) selecionado. Você também pode gerenciar vários bancos de dados em um [pool elástico](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) no qual os recursos são compartilhados entre os bancos de dados. Os recursos disponíveis para bancos de dados individuais são expressos em termos de DTUs (Unidades de transação de banco de dados), e para pools elásticos em termos de DTUs elásticos (eDTUs). Para saber mais sobre DTUs e eDTUs, confira [O que é uma DTU?](sql-database-what-is-a-dtu.md) 

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
* Máximo de armazenamento total em um pool elástico (117 GB para Basic, 1200 GB para Standard e 750 GB para Premium)
* Número máximo de bancos de dados por pool (400 para Basic, 400 para Standard e 50 para Premium)
* Período de retenção de backup do banco de dados (7 dias para Basic, 35 dias para Standard e Premium)

Depois de determinar a camada de serviço mínima, você estará pronto para determinar o nível de desempenho do banco de dados (o número de DTUs). Os níveis de desempenho do S2 e S3 padrão são quase sempre um bom ponto de partida. Para bancos de dados com altas exigências de CPU ou E/S, os níveis de desempenho Premium são o ponto de partida correto. O Premium oferece mais CPU e começa em 10 vezes mais E/S em comparação com o nível de desempenho Standard mais alto.

## <a name="single-database-service-tiers-and-performance-levels"></a>Camadas de serviço e níveis de desempenho de banco de dados individual
Para bancos de dados individuais, há vários níveis de desempenho dentro de cada camada de serviço. Você tem a flexibilidade de escolher o nível que melhor atenda às suas demandas de carga de trabalho. Se você precisar expandir ou reduzir, poderá alterar facilmente as camadas do banco de dados. Consulte [Alterando camadas de serviços e níveis de desempenho do banco de dados](sql-database-service-tiers.md) para obter detalhes.

Independentemente do número de bancos de dados hospedados, o banco de dados ainda obterá um conjunto garantido de recursos e as características de desempenho esperadas de seu banco de dados não são afetadas.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Para obter uma explicação detalhada de todas as outras linhas nesta tabela de camadas de serviço, consulte [Limites e recursos da camada de serviço](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>Escalar verticalmente ou reduzir um banco de dados

Depois de escolher inicialmente um nível de desempenho e da camada de serviço, você pode dimensionar um banco de dados para cima ou para baixo dinamicamente com base na experiência real. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

A alteração da camada de serviço e/ou nível de desempenho de um banco de dados cria uma réplica do banco de dados original com o novo nível de desempenho e então faz a transição das conexões para réplica. Nenhum dado é perdido durante esse processo, mas durante o breve momento em que realizamos a transição para a réplica, conexões com o banco de dados são desabilitadas, então algumas transações em andamento podem ser revertidas. Essa janela varia, mas é em média inferior a quatro segundos e, em mais de 99% dos casos, de menos de 30 segundos. Se houver grandes números de transações em andamento no momento em que as conexões estiverem desabilitadas, esta janela poderá ser maior.  

A duração de todo o processo de expansão depende a camada tamanho e de serviço do banco de dados antes e após a alteração. Por exemplo, um banco de dados de 250 GB que está mudando para, de ou dentro de uma camada de serviço Standard deverá ser concluída dentro de 6 horas. Um banco de dados do mesmo tamanho cujos níveis de desempenho estão mudando dentro da camada de serviço Premium deverá ser concluído dentro de 3 horas.

* Para fazer downgrade de um banco de dados, este deve ter um tamanho menor do que o máximo permitido para a camada de serviço de destino. 
* Ao atualizar um banco de dados com [Replicação Geográfica](sql-database-geo-replication-portal.md) habilitada, é necessário primeiro atualizar seus bancos de dados secundários para o nível de desempenho desejado antes de atualizar o banco de dados primário.
* Ao fazer downgrade de uma camada de serviço Premium, primeiro, você deve encerrar todos os relacionamentos de Replicação Geográfica. Você pode seguir as etapas descritas no tópico [Recuperação de uma interrupção](sql-database-disaster-recovery.md) para interromper o processo de replicação entre os bancos de dados primários e secundários ativos.
* As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se estiver fazendo downgrade, talvez você perca a capacidade de fazer uma restauração pontual ou tenha um período menor de retenção do backup. Para saber mais, confira [Backup e restauração do Banco de dados SQL do Azure](sql-database-business-continuity.md).
* As novas propriedades do banco de dados não serão aplicadas até que as alterações sejam concluídas.

> [!IMPORTANT]
> Para obter etapas detalhadas, consulte [Gerenciando bancos de dados individuais com o portal do Azure](sql-database-manage-single-databases-portal.md), [Gerenciando bancos de dados individuais com o Powershell](sql-database-manage-single-databases-powershell.md), ou [Gerenciando bancos de dados individuais com o Transact-SQL](sql-database-manage-single-databases-tsql.md).
>

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Camadas de serviço e desempenho em eDTUs do pool elástico

Os pools permitem que esses bancos de dados compartilhem e consumam os recursos de eDTU sem a necessidade de atribuir um nível de desempenho específico para todos os bancos de dados no pool. Por exemplo, um banco de dados individual em um pool Standard pode usar de 0 eDTUs até o máximo de eDTU de banco de dados configurado por você durante a definição do pool. Os pools permitem que vários bancos de dados com diferentes cargas de trabalho usem os recursos de eDTU disponíveis para todo o pool de forma eficiente. Confira [Considerações de preço e desempenho para um pool elástico](sql-database-elastic-pool-guidance.md) para obter detalhes.

A tabela a seguir descreve as características das camadas de serviço do pool.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Cada banco de dados dentro de um pool também cumpre as características do banco de dados individual para essa camada. Por exemplo, o pool Basic tem um limite máximo de sessões por pool de 4800 a 28800, mas um banco de dados individual dentro de um pool Básico tem um limite de banco de dado de 300 sessões.

## <a name="scaling-up-or-scaling-down-an-elastic-pool"></a>Escalar verticalmente ou reduzir um pool Elástico

Depois de escolher inicialmente uma camada de serviço e um nível de desempenho, você poderá ampliar ou reduzir o pool elástico dinamicamente com base na experiência real. 

* Normalmente, a alteração das eDTUs mínimas ou das eDTUs máximas por banco de dados é um processo concluído em cinco minutos ou menos.
* O tempo levado para alterar o tamanho do pool (eDTUs) depende do tamanho combinado de todos os bancos de dados no pool. As alterações levam, em média, 90 minutos ou menos a cada 100 GB. Por exemplo, se o espaço total de todos os bancos de dados no pool for de 200 GB, a latência prevista para alterar os eDTUs do pool será de 3 horas por pool ou menos.

> [!IMPORTANT]
> Para obter etapas detalhadas, veja [Gerenciar pools Elásticos com o portal do Azure](sql-database-elastic-pool-manage-portal.md), [Gerenciar pools Elásticos com o Powershell](sql-database-elastic-pool-manage-powershell.md), [Gerenciar pools Elásticos com o Transact-SQL](sql-database-elastic-pool-manage-tsql.md), ou [Gerenciar pools Elásticos com o c#](sql-database-elastic-pool-manage-csharp.md).
>

## <a name="next-steps"></a>Próximas etapas

* Conheça os detalhes dos [pools elásticos](sql-database-elastic-pool-guidance.md) e [considerações sobre o preço e o desempenho dos pools elásticos](sql-database-elastic-pool-guidance.md).
* Saiba como [Monitorar, gerenciar e redimensionar pools elásticos](sql-database-elastic-pool-manage-portal.md) e [Monitorar o desempenho de bancos de dados individuais](sql-database-single-database-monitor.md).
* Agora que você conhece as camadas do Banco de Dados SQL, teste-as usando uma versão de [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) e [aprenda a criar seu primeiro banco de dados SQL](sql-database-get-started.md).
* Para cenários de migração, use a [Calculadora de DTU](http://dtucalculator.azurewebsites.net/) para ter uma ideia aproximada do número de DTUs necessários. 




<!--HONumber=Feb17_HO3-->


