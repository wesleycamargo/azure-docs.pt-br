---
title: Desempenho e preço do pool elástico de Banco de Dados SQL
description: Informações sobre preços específicas para Pools de Banco de Dados Elástico.
services: sql-database
documentationcenter: ''
author: srinia
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 05/27/2016
ms.author: srinia
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA

---
# Informações sobre preços e cobrança do Pool de Banco de Dados Elástico
Pools de bancos de dados elásticos são cobrados de acordo com as seguintes características:

* Um pool elástico é cobrado após sua criação, mesmo quando ele não contém bancos de dados.
* Um pool elástico é cobrado por hora. Essa frequência de medição é a mesma dos níveis de desempenho de bancos de dados individuais.
* Se um pool elástico for redimensionado para um novo valor de eDTUs, ele não será cobrado de acordo com esse novo valor até que a operação de redimensionamento seja concluída. Isso segue o mesmo padrão que a alteração do nível de desempenho de bancos de dados autônomos.
* O preço de um pool elástico baseia-se no número de eDTUs do pool. O preço de um pool elástico é independente do número e da utilização dos bancos de dados elásticos dentro dele.
* O preço é calculado por (número de eDTUs do pool) x (preço unitário por eDTU).

O preço unitário por eDTU de um pool elástico é maior que o preço unitário por DTU de um banco de dados autônomo na mesma camada de serviço. Para obter detalhes, confira [Preços de Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

Para entender as camadas de serviço e eDTUs, consulte [Opções e desempenho de Banco de Dados SQL](sql-database-service-tiers.md).

## Próximas etapas
* [Criar um pool de banco de dados elástico](sql-database-elastic-pool-create-portal.md)
* [Monitorar, gerenciar e dimensionar um pool de banco de dados elástico](sql-database-elastic-pool-manage-portal.md)
* [Opções e desempenho de Banco de Dados SQL: compreender o que está disponível em cada camada de serviço](sql-database-service-tiers.md)
* [Script do PowerShell para identificar os bancos de dados adequados para um pool de banco de dados elástico](sql-database-elastic-pool-database-assessment-powershell.md)

<!---HONumber=AcomDC_0601_2016-->