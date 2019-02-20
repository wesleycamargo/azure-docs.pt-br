---
title: Limites de recursos baseados em DTU do Banco de Dados SQL do Azure – banco de dados único | Microsoft Docs
description: Esta página descreve alguns limites de recursos baseados em DTU comuns para o Banco de Dados SQL do Microsoft Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: bf4c823d42d26d9fa97ee6eb5ee6ea20b857cde8
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992989"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Limites de recursos para bancos de dados individuais usando o modelo de compra baseado em DTU

Este artigo fornece os limites de recursos detalhados para bancos de dados individuais do Banco de Dados SQL do Azure usando o modelo de compra com base em DTU.

Para limites de recurso de modelo de compra com base em DTU para pools elásticos, consulte [Limites de recurso com base em DTU - bancos de pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md). Para saber os limites de recurso vCore, confira [Limites de recurso baseados em vCore  – bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md) e [Limites de recurso baseados em vCore do Banco de Dados SQL - pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md). Para obter mais informações sobre os diferentes modelos de compras, consulte [Modelos de compras e camadas de serviço](sql-database-purchase-models.md).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL](sql-database-file-space-management.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Banco de dados individual: Tamanhos de armazenamento e tamanhos de computação

As tabelas a seguir mostram os recursos disponíveis para um único banco de dados individual em cada camada de serviço e tamanho de computação. É possível definir a camada de serviço, o tamanho de computação e a quantidade de armazenamento para um banco de dados individual usando o [portal do Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), o [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), o [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), a [CLI do Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) ou a [API REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

### <a name="basic-service-tier"></a>Camada de serviço Básica

| **Tamanho de computação** | **Básico** |
| :--- | --: |
| Número máximo de DTUs | 5 |
| Armazenamento incluído (GB) | 2 |
| Opções de espaço de armazenamento máximo (GB) | 2 |
| Armazenamento máximo OLTP na memória (GB) |N/D |
| Máximo de trabalhos simultâneos (solicitações) | 30 |
| Máximo de sessões simultâneas | 300 |
|||

### <a name="standard-service-tier"></a>Camada de serviço Standard

| **Tamanho de computação** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Número máximo de DTUs | 10 | 20 | 50 | 100 |
| Armazenamento incluído (GB) | 250 | 250 | 250 | 250 |
| Opções de espaço de armazenamento máximo (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Armazenamento máximo OLTP na memória (GB) | N/D | N/D | N/D | N/D |
| Máximo de trabalhos simultâneos (solicitações)| 60 | 90 | 120 | 200 |
| Máximo de sessões simultâneas |600 | 900 | 1.200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Camada de serviço Standard (continuação)

| **Tamanho de computação** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Número máximo de DTUs | 200 | 400 | 800 | 1600 | 3000 |
| Armazenamento incluído (GB) | 250 | 250 | 250 | 250 | 250 |
| Opções de espaço de armazenamento máximo (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Armazenamento máximo OLTP na memória (GB) | N/D | N/D | N/D | N/D |N/D |
| Máximo de trabalhos simultâneos (solicitações)| 400 | 800 | 1600 | 3200 |6000 |
| Máximo de sessões simultâneas |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Camada de serviço Premium

| **Tamanho de computação** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Número máximo de DTUs | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Armazenamento incluído (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Opções de espaço de armazenamento máximo (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Armazenamento máximo OLTP na memória (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Máximo de trabalhos simultâneos (solicitações)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Máximo de sessões simultâneas | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

> [!IMPORTANT]
> Mais de 1 TB de armazenamento na camada Premium está atualmente disponível em todas as regiões, exceto as seguintes: Leste da China, Norte da China, Alemanha Central, Nordeste da Alemanha, Norte do Reino Unido, Sul do Reino Unido, US DoD Central, US DoD Leste, US Government Central e Centro-oeste dos EUA.  Nessas regiões, o armazenamento máximo na camada Premium é limitado a 1 TB. Consulte [Limitações atuais de P11-P15](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="single-database-change-storage-size"></a>Banco de dados individual: Alterar o tamanho de armazenamento

- O preço de DTU para um único banco de dados inclui uma determinada quantidade de armazenamento sem custo adicional. O armazenamento extra além da quantidade incluída pode ser provisionado mediante um custo adicional até o limite máximo de tamanho, em incrementos de 250 GB até 1 TB e, em seguida, em incrementos de 256 GB além de 1 TB. Para conhecer os valores de armazenamento incluídos e os limites máximos de tamanho, confira [Banco de dados individual: Tamanhos de armazenamento e tamanhos de computação](#single-database-storage-sizes-and-compute-sizes).
- É possível provisionar o armazenamento extra para um único banco de dados aumentando seu tamanho máximo usando o [Portal do Azure](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-storage-size), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [CLI do Azure](/cli/azure/sql/db#az-sql-db-update) ou [API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- O preço do armazenamento extra para um único banco de dados é a quantidade de armazenamento extra multiplicada pelo preço unitário do armazenamento extra da camada de serviço. Para obter detalhes sobre o preço de armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Banco de dados individual: Alterar DTUs

Depois de escolher inicialmente um nível de serviço, tamanho de computação e quantidade de armazenamento, será possível aumentar ou diminuir um banco de dados individual dinamicamente com base na experiência real usando o [portal do Azure](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-compute-resources-dtus), o [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), o [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), a [CLI do Azure](/cli/azure/sql/db#az-sql-db-update) ou a [API REST](https://docs.microsoft.com/rest/api/sql/databases/update).

O vídeo a seguir mostra como alterar dinamicamente o nível de serviço e o tamanho de computação para aumentar os DTUs disponíveis para um banco de dados individual.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

A alteração da camada de serviço e/ou tamanho da computação de um banco de dados cria uma réplica do banco de dados original com o novo tamanho de computação e, então, faz a transição das conexões para réplica. Nenhum dado é perdido durante esse processo, mas durante o breve momento em que realizamos a transição para a réplica, conexões com o banco de dados são desabilitadas, então algumas transações em andamento podem ser revertidas. O período de tempo para a alternância varia, mas é inferior a 30 segundos em 99% do tempo. Se houver muitas transações em andamento no momento de desabilitação das conexões, o período de transição poderá ser maior.

A duração de todo o processo de expansão depende a camada tamanho e de serviço do banco de dados antes e após a alteração. Por exemplo, um banco de dados de 250 GB que está mudando para, de ou dentro de uma camada de serviço Standard deverá ser concluída dentro de seis horas. Um banco de dados do mesmo tamanho cujos tamanhos de computação estão mudando dentro da camada de serviço Premium deverá concluir a escala vertical dentro de três horas.

> [!TIP]
> Para monitorar operações em andamento, consulte: [Gerenciar operações usando a API REST SQL](https://docs.microsoft.com/rest/api/sql/databaseoperations/listbydatabase
), [Gerenciar operações usando a CLI](/cli/azure/sql/db/op), [Monitorar operações usando T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e esses dois comandos do PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) e [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

- Se você estiver atualizando para um nível de serviço ou tamanho de computação maior, o tamanho máximo do banco de dados não aumentará, a menos que você especifique explicitamente um tamanho maior (tamanho máximo).
- Para fazer downgrade de um banco de dados, o espaço usado dele deve ter um tamanho menor do que o máximo permitido para a camada de serviço e o tamanho de computação de destino.
- Ao fazer o downgrade da camada **Premium** para a camada **Standard**, um custo de armazenamento extra será aplicado se (1) o tamanho máximo do banco de dados tiver suporte no tamanho de computação de destino e (2) o tamanho máximo ultrapassar a quantidade de armazenamento incluída de tamanho de computação de destino. Por exemplo, se um banco de dados P1 com um tamanho máximo de 500 GB for reduzido para S3, um custo de armazenamento extra será aplicado, pois S3 dá suporte a um tamanho máximo de 500 GB, e a quantidade de armazenamento incluído é somente de 250 GB. Assim, a quantidade de armazenamento extra será 500 GB – 250 GB = 250 GB. Para obter o preço do armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço usado for menor do que a quantidade de armazenamento incluído, esse custo extra poderá ser evitado por meio da redução do tamanho máximo do banco de dados para a quantidade incluída.
- Ao atualizar um banco de dados com [replicação geográfica](sql-database-geo-replication-portal.md) habilitada, atualize seus bancos de dados secundários para a camada de serviço e o tamanho de computação desejados antes de atualizar o banco de dados primário (orientação geral para o melhor desempenho). Ao fazer upgrade para uma diferente, o upgrade do banco de dados secundário será exigido antes.
- Ao fazer downgrade de um banco de dados com [replicação geográfica](sql-database-geo-replication-portal.md) habilitada, faça downgrade dos seus bancos de dados primários para a camada de serviço e o tamanho de computação desejados antes de atualizar o banco de dados secundário (orientação geral para o melhor desempenho). Ao fazer o downgrade para uma edição diferente, antes será necessário fazer o downgrade do banco de dados primário.
- As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se você estiver fazendo downgrade para camada **Básica**, haverá um período de retenção de backup inferior. Consulte [Backups de Banco de Dados SQL do Azure](sql-database-automated-backups.md).
- As novas propriedades do banco de dados não serão aplicadas até que as alterações sejam concluídas.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Banco de dados individual: Limitações de P11 e P15 quando o tamanho máximo for superior a 1 TB

As seguintes considerações e limitações se aplicam aos bancos de dados P11 e P15 com um tamanho máximo superior a 1 TB:

- Se você escolher um tamanho máximo superior a 1 TB durante a criação de um banco de dados (usando um valor de 4 TB ou 4096 GB), o comando create falhará com um erro se o banco de dados for provisionado em uma região sem suporte.
- Para bancos de dados P11 e P15 existentes localizados em uma das regiões com suporte, você pode aumentar o armazenamento máximo para além de 1 TB em incrementos de 256 GB até 4 TB. Para ver se há suporte para um tamanho maior em sua região, use a função [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) ou inspecione o tamanho do banco de dados no Portal do Azure. A atualização de um banco de dados existente P11 ou P15 só pode ser executada por um logon principal no nível de servidor ou por membros da função de banco de dados dbmanager.
- Se uma operação de atualização for executada em uma região com suporte, a configuração será atualizada imediatamente. O banco de dados permanece online durante o processo de atualização. No entanto, não é possível utilizar a quantidade total de armazenamento além de 1 TB de armazenamento até que os arquivos de banco de dados reais sejam atualizados para o novo tamanho máximo. O período de tempo necessário depende do tamanho do banco de dados que está sendo atualizado.
- Ao criar ou atualizar um banco de dados P11 ou P15, você só pode escolher entre 1 TB e 4 TB de tamanho máximo em incrementos de 256 GB. Ao criar um P11/P15, a opção de armazenamento padrão de 1 TB é pré-selecionada. Para bancos de dados localizados em uma das regiões com suporte, você pode aumentar o número máximo de armazenamento de 4 TB para um banco de dados individual novo ou existente. Para todas as outras regiões, o tamanho máximo não pode ultrapassar 1 TB. O preço não é alterado quando você seleciona 4 TB de armazenamento incluído.
- Se o tamanho máximo de um banco de dados for definido como superior a 1 TB, não poderá ser alterado para 1 TB, mesmo se o armazenamento real usado estiver abaixo de 1 TB. Portanto, não é possível fazer downgrade de um P11 ou P15 com um tamanho máximo superior a 1 TB para um P11 de 1 TB ou P15 de 1 TB ou um tamanho de computação inferior, como P1-P6. Esta restrição também se aplica aos cenários de restauração e de cópia, incluindo restauração pontual e geográfica, retenção de backup a longo prazo e cópia de banco de dados. Quando um banco de dados estiver configurado com um tamanho máximo superior a 1 TB, todas as operações de restauração desse banco de dados devem ser executadas em um P11/P15 com um tamanho máximo superior a 1 TB.
- Para cenários com replicação geográfica ativa:
  - Configurando um relacionamento de replicação geográfica: Se o banco de dados primário for P11 ou P15, os secundários também deverão ser P11 ou P15. Os tamanhos de computação inferiores serão rejeitados como secundários porque não são capazes de dar suporte a mais de 1 TB.
  - Atualizando o banco de dados primário em uma relação de replicação geográfica: Alterar o tamanho máximo para mais de 1 TB em um banco de dados primário disparará a mesma alteração no banco de dados secundário. As duas atualizações devem ser bem-sucedidas para que a alteração no primário entre em vigor. Há limitações de região para a opção superior a 1 TB. Se o secundário estiver em uma região que não oferece suporte a mais de 1 TB, o primário não será atualizado.
- Não há suporte para o uso do serviço de Importação/Exportação para carregar bancos de dados P11/P15 com mais de 1 TB. Use SqlPackage.exe para [importar](sql-database-import.md) e [exportar](sql-database-export.md) dados.

## <a name="next-steps"></a>Próximas etapas

- Para os limites de recursos do vCore de um único banco de dados, confira os [limites de recursos para bancos de dados individuais usando o modelo de compras baseado no vCore](sql-database-vcore-resource-limits-single-databases.md)
- Para ver os limites de recursos do vCore para os pools elásticos, confira os [limites de recursos para pools elásticos usando o modelo de compras baseado no vCore](sql-database-vcore-resource-limits-elastic-pools.md)
- Para ver os limites de recursos de DTU para os pools elásticos, confira os [limites de recursos para pools elásticos usando o modelo de compras baseado em DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Para os limites de recursos das instâncias gerenciadas, confira [limites de recursos para instâncias gerenciadas](sql-database-managed-instance-resource-limits.md).
- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
- Para se informar sobre os limites de recursos em um servidor do banco de dados, confira a [visão geral dos limites de recursos em um servidor de banco de dados SQL](sql-database-resource-limits-database-server.md) e verá informações sobre limites nos níveis do servidor e da assinatura.