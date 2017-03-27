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
wms.date: 03/06/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 4307797b3961d8efef4045590e340268f0ad226d
ms.lasthandoff: 03/10/2017


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Opções e desempenho de Banco de Dados SQL: compreender o que está disponível em cada camada de serviço

[Banco de dados SQL do Azure](sql-database-technical-overview.md) oferece quatro camadas de serviço: **Basic**, **Standard**, **Premium** e **Premium RS**. Cada camada de serviço tem vários níveis de desempenho para lidar com cargas de trabalho diferentes. Os níveis de desempenho mais elevados fornecem recursos adicionais, projetados para oferecer uma taxa de transferência crescente. Você pode alterar as camadas de serviços e os níveis de desempenho sem tempo de inatividade. Todas as camadas de serviço, Basic, Standard e Premium têm um SLA de tempo de atividade de 99,99%, opções de continuidade dos negócios flexíveis, recursos de segurança e cobrança por hora. A camada Premium RS fornece os mesmos níveis de desempenho, recursos de segurança e recursos de continuidade de negócios que a camada Premium, mas com um SLA reduzido.

> [!IMPORTANT]
> Bancos de dados Premium RS operam com um número menor de cópias redundantes que bancos de dados Premium ou Standard. Portanto, em caso de falha de serviço, você precisará recuperar seu banco de dados de um backup com um retardo de até 5 minutos.
>

Você pode criar bancos de dados individuais com recursos dedicados dentro de uma camada de serviço em um [nível de desempenho](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) específico. Você também pode criar bancos de dados em um [pool elástico](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) no qual os recursos são compartilhados entre os bancos de dados. Os recursos disponíveis para bancos de dados individuais são expressos em termos de DTUs (unidades de transação de banco de dados) e para pools elásticos em termos de eDTUs (unidades de transação de banco de dados elásticos). Para saber mais sobre DTUs e eDTUs, confira [O que é uma DTU?](sql-database-what-is-a-dtu.md) 

## <a name="choosing-a-service-tier"></a>Como escolher uma camada de serviço
A tabela a seguir fornece exemplos das camadas mais adequadas para cargas de trabalho de aplicativos diferentes.

| Camada de serviço | Cargas de trabalho de destino |
| :--- | --- |
| **Básico** | Mais adequada para um banco de dados pequeno, suporta normalmente uma única operação ativa em um determinado momento. Os exemplos incluem bancos de dados usados para desenvolvimento ou teste ou aplicativos de pequena escala usados com pouca frequência. |
| **Standard** |Para ele ir para a opção para aplicativos em nuvem com requisitos de desempenho de E/S baixo a médio, oferecendo suporte a várias consultas simultâneas. Os exemplos incluem aplicativos da web ou de grupo de trabalho. |
| **Premium** | Projetado para volume transacional alto com requisitos de desempenho de e/s altos, suportando muitos usuários simultâneos. Os exemplos são bancos de dados com suporte a aplicativos de missão críticos. |
| **Premium RS** | Projetado para cargas de trabalho com uso intensivo de E/S que não exigem garantias de alta disponibilidade. Exemplos de cargas de trabalho de alto desempenho ou uma carga de trabalho analítica onde o banco de dados não é o sistema de registro. |
|||

Primeiro, decida se deseja executar um único banco de dados com uma quantidade definida de recursos dedicados ou se você deseja compartilhar um pool de recursos em um grupo de bancos de dados. Examine as [considerações de pool elástico](sql-database-elastic-pool-guidance.md). Para decidir sobre uma camada de serviço, inicie determinando os recursos de banco de dados mínimos que você precisa:

| **Recursos de camada de serviço** | **Básico** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Tamanho máximo de banco de dados individuais | 2 GB | 250 GB | 4 TB*  | 500 GB  |
| Armazenamento total máximo em um pool elástico | 117 GB | 1.200 GB | 750 GB | 750 GB |
| Número máximo de bancos de dados por pool | 400  | 400 | 50 | 50 |
| Período de retenção do backup de banco de dados | 7 dias | 35 dias | 35 dias | 35 dias |
||||||

> [!IMPORTANT]
> Os clientes que usam os níveis de desempenho P11 e P15 podem usar até 4 TB de armazenamento incluído sem custo adicional. Essa opção de 4 TB está atualmente em visualização pública e está disponível somente nas seguintes regiões: Leste dos EUA 2, Oeste dos EUA, Europa Ocidental, Sudeste Asiático, Leste do Japão, Leste da Austrália, Central do Canadá e Leste do Canadá. Para limitações atuais, confira [Limitações atuais 4 TB](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)
>

Depois de determinar a camada de serviço mínima, você estará pronto para determinar o nível de desempenho do banco de dados (o número de DTUs). Os níveis de desempenho do S2 e S3 padrão são quase sempre um bom ponto de partida. Para bancos de dados com altas exigências de CPU ou E/S, os níveis de desempenho Premium são o ponto de partida correto. O Premium oferece mais CPU e começa em 10 vezes mais E/S em comparação com o nível de desempenho Standard mais alto.

## <a name="single-database-service-tiers-and-performance-levels"></a>Camadas de serviço e níveis de desempenho de banco de dados individual
Para bancos de dados individuais, há vários níveis de desempenho dentro de cada camada de serviço. Você tem a flexibilidade de escolher o nível que melhor atenda às demandas de sua carga de trabalho, usando o [Portal do Azure](sql-database-manage-single-databases-portal.md), o [PowerShell](sql-database-manage-single-databases-powershell.md), o [Transact-SQL](sql-database-manage-single-databases-tsql.md), o C# e a API REST. 

Independentemente do número de bancos de dados hospedados, o banco de dados ainda obterá um conjunto garantido de recursos e as características de desempenho esperadas de seu banco de dados não são afetadas.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Para obter uma explicação detalhada de todas as outras linhas nesta tabela de camadas de serviço, consulte [Limites e recursos da camada de serviço](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>Escalar verticalmente ou reduzir um banco de dados

Depois de escolher inicialmente um nível de desempenho e da camada de serviço, você pode dimensionar um banco de dados para cima ou para baixo dinamicamente com base na experiência real. Se precisar expandir ou reduzir, você poderá alterar facilmente as camadas do banco de dados usando o [Portal do Azure](sql-database-manage-single-databases-portal.md), o [PowerShell](sql-database-manage-single-databases-powershell.md), o [Transact-SQL](sql-database-manage-single-databases-tsql.md), o C# e a API REST. 

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
> Para obter etapas detalhadas, confira [Gerenciamento de um banco de dados no portal do Azure](sql-database-manage-single-databases-portal.md), [Gerenciamento de um banco de dados com o PowerShell](sql-database-manage-single-databases-powershell.md), ou [Gerenciamento de um banco de dados com Transact-SQL](sql-database-manage-single-databases-tsql.md).
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

Para obter etapas detalhadas, confira [Gerenciamento de um pool elástico no portal do Azure](sql-database-elastic-pool-manage-portal.md), [Gerenciamento de um pool elástico com o Powershell](sql-database-elastic-pool-manage-powershell.md), [Gerenciamento de um pool Elástico com Transact-SQL](sql-database-elastic-pool-manage-tsql.md), ou [Gerenciamento de um pool Elástico com C#](sql-database-elastic-pool-manage-csharp.md).

## <a name="creating-or-upgrading-to-4tb"></a>Criação ou atualização de 4 TB

As seções a seguir discutem os detalhes de implementação para a opção de 4 TB.

### <a name="creating-in-the-azure-portal"></a>Criação no portal do Azure

Ao criar um P11/P15, a opção de armazenamento padrão de 1TB é pré-selecionada. Para bancos de dados localizados em uma das regiões com suporte, você pode aumentar o número máximo de armazenamento para 4 TB. Para todas as outras regiões, o controle deslizante de armazenamento não pode ser alterado. O preço não é alterado quando você seleciona 4 TB de armazenamento incluído.

### <a name="creating-using-powershell-or-transact-sql"></a>Criação e uso do PowerShell ou Transact-SQL

Ao criar um banco de dados P11/P15, você pode definir o valor do maxsize para 1 TB (padrão) ou 4 TB. Valores de "1024" e "4096 GB" também são aceitos. Se você escolher a opção maxsize de 4 TB, o comando create falhará com um erro se o banco de dados for provisionado em uma região sem suporte.

### <a name="upgrading-to-4tb"></a>Atualização para 4 TB 

Para bancos de dados P11 e P15 existentes localizados em uma das regiões com suporte, você pode aumentar o maxsize do armazenamento para 4 TB. Isso pode ser feito no portal do Azure, no PowerShell ou com o Transact-SQL. O exemplo a seguir mostra o maxsize sendo alterado usando o comando ALTER DATABASE:

```ALTER DATABASE <DatabaseName> MODIFY (MAXSIZE = 4096 GB);
```

Upgrading an existing P11 or P15 database can only be performed by a server-level principal login or by members of the dbmanager database role. 
If executed in a supported region the configuration will be updated immediately. This can be checked using the [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) or by inspecting the database size in the Azure portal. The database will remain online during the upgrade process. However, you will not be able to utilize the full 4 TB of storage until the actual database files have been upgraded to the new maxsize. The length of time required depends upon on the size of the database being upgraded.  

### Error messages
When creating or upgrading an P11/P15 database in an unsupported region, the create or upgrade operation will fail with the following error message: **P11 and P15 database with up to 4TB of storage are available in US East 2, West US, South East Asia, West Europe, Canada East, Canada Central, Japan East, and Australia East.**

## Current limitations of P11 and P15 databases with 4 TB maxsize

- When creating or updating a P11 or P15 database, you can only chose between 1 TB and 4 TB maxsize. Intermediate storage sizes are not currently supported.
- The 4 TB database maxsize cannot be changed to 1 TB even if the actual storage used is below 1 TB. Thus, you cannot downgrade a P11-4TB/P15-4TB to a P11-1TB/P15-1TB or a lower performance tier (e.g., to P1-P6) until we are providing additional storage options for the rest of the performance tiers. This restriction also applies to the restore and copy scenarios including point-in-time, geo-restore, long-term-backup-retention, and database copy. Once a database is configured with the 4 TB option, all restore operations of this database must be into a P11/P15 with 4 TB maxsize.
- For Active Geo-Replication scenarios:
   - Setting up a geo-replication relationship: If the primary database is P11 or P15, the secondary(ies) must also be P11 or P15; lower performance tiers will be rejected as secondaries since they are not capable of supporting 4 TB.
   - Upgrading the primary database in a geo-replication relationship: Changing the maxsize to 4 TB on a primary database will trigger the same change on the secondary database. Both upgrades must be successful for the change on the primary to take effect. Region limitations for the 4TB option apply (see above). If the secondary is in a region that does not support 4 TB, the primary will not be upgraded.
- Using the Import/Export service for loading P11-4TB/P15-4TB databases is not supported. Use SqlPackage.exe to [import](sql-database-import-sqlpackage.md) and [export](sql-database-export-sqlpackage.md) data.

## Next steps

* Learn the details of [elastic pools](sql-database-elastic-pool-guidance.md) and [price and performance considerations for elastic pools](sql-database-elastic-pool-guidance.md).
* Learn how to [Monitor, manage, and resize elastic pools](sql-database-elastic-pool-manage-portal.md) and [Monitor the performance of single databases](sql-database-single-database-monitor.md).
* Now that you know about the SQL Database tiers, try them out with a [free account](https://azure.microsoft.com/pricing/free-trial/) and learn [how to create your first SQL database](sql-database-get-started.md).
* For migration scenarios, use the [DTU Calculator](http://dtucalculator.azurewebsites.net/) to approximate the number of DTUs needed. 


