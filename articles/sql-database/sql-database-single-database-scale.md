---
title: Escalar recursos de banco de dados individual – Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo descreve como escalar os recursos de computação e armazenamento disponíveis para um banco de dados individual no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: e00f043f99b9a57fad420c380a55789d73047e77
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49352893"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Escalar recursos de banco de dados individual no Banco de Dados SQL do Azure

Este artigo descreve como escalar os recursos de computação e armazenamento disponíveis para um banco de dados individual no Banco de Dados SQL do Azure.

## <a name="vcore-based-purchasing-model-change-storage-size"></a>Modelo de compra com base em vCore: alterar o tamanho de armazenamento

- O armazenamento pode ser provisionado até o limite de tamanho máximo com incrementos de 1 GB. O armazenamento de dados configurável mínimo é de 5 GB
- É possível provisionar o armazenamento para um único banco de dados aumentando ou diminuindo seu tamanho máximo usando o [Portal do Azure](https://portal.azure.com), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [CLI do Azure](/cli/azure/sql/db#az-sql-db-update) ou [API REST](https://docs.microsoft.com/rest/api/sql/databases/databases_update).
- O banco de dados SQL automaticamente aloca 30% do armazenamento adicional para os arquivos de log e 32 GB por vCore para TempDB, mas não deve exceder 384 GB. O TempDB está localizado em um SSD anexado em todas as camadas de serviço.
- O preço do armazenamento para um único banco de dados é a soma das quantidades de armazenamento de dados e armazenamento de log multiplicada pelo preço unitário do armazenamento da camada de serviço. O custo de TempDB está incluído no preço de vCore. Para obter detalhes sobre o preço de armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL](sql-database-file-space-management.md).

## <a name="vcore-based-purchasing-model-change-compute-resources"></a>Modelo de compra com base em vCore: alterar os recursos de computação

Depois de escolher inicialmente o número de vCores, você poderá escalar verticalmente de forma dinâmica um banco de dados individual com base na experiência real usando o [Portal do Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-server), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), a [CLI do Azure](/cli/azure/sql/db#az-sql-db-update) ou a [API REST](https://docs.microsoft.com/rest/api/sql/databases/databases_update).

A alteração da camada de serviço e/ou tamanho da computação de um banco de dados cria uma réplica do banco de dados original com o novo tamanho de computação e, então, faz a transição das conexões para réplica. Nenhum dado é perdido durante esse processo, mas durante o breve momento em que realizamos a transição para a réplica, conexões com o banco de dados são desabilitadas, então algumas transações em andamento podem ser revertidas. O período da transição varia, mas fica geralmente abaixo de 4 segundos e menos de 30 segundos 99% do tempo. Se houver muitas transações em andamento no momento de desabilitação das conexões, o período de transição poderá ser maior.

A duração de todo o processo de expansão depende a camada tamanho e de serviço do banco de dados antes e após a alteração. Por exemplo, um banco de dados de 250 GB que está mudando para, de ou dentro de uma camada de serviço Uso Geral deverá ser concluído dentro de seis horas. Um banco de dados do mesmo tamanho cujos tamanhos de computação estão mudando dentro da camada de serviço Comercialmente Crítico deve concluir a escala vertical dentro de três horas.

> [!TIP]
> Para monitorar operações em andamento, consulte: [Gerenciar operações usando a API REST SQL](https://docs.microsoft.com/rest/api/sql/operations/operations_list), [Gerenciar operações usando a CLI](/cli/azure/sql/db/op), [Monitorar operações usando T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e esses dois comandos do PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) e [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

- Se você estiver atualizando para um nível de serviço ou tamanho de computação maior, o tamanho máximo do banco de dados não aumentará, a menos que você especifique explicitamente um tamanho maior (tamanho máximo).
- Para fazer downgrade de um banco de dados, o espaço usado dele deve ter um tamanho menor do que o máximo permitido para a camada de serviço e o tamanho de computação de destino.
- Ao atualizar um banco de dados com [replicação geográfica](sql-database-geo-replication-portal.md) habilitada, atualize seus bancos de dados secundários para a camada de serviço e o tamanho de computação desejados antes de atualizar o banco de dados primário (orientação geral para o melhor desempenho). Ao fazer upgrade para uma diferente, o upgrade do banco de dados secundário será exigido antes.
- Ao fazer downgrade de um banco de dados com [replicação geográfica](sql-database-geo-replication-portal.md) habilitada, faça downgrade dos seus bancos de dados primários para a camada de serviço e o tamanho de computação desejados antes de atualizar o banco de dados secundário (orientação geral para o melhor desempenho). Ao fazer o downgrade para uma edição diferente, antes será necessário fazer o downgrade do banco de dados primário.
- As novas propriedades do banco de dados não serão aplicadas até que as alterações sejam concluídas.

## <a name="dtu-based-purchasing-model-change-storage-size"></a>Modelo de compra com base em DTU: alterar o tamanho do armazenamento

- O preço de DTU para um único banco de dados inclui uma determinada quantidade de armazenamento sem custo adicional. O armazenamento extra além da quantidade incluída pode ser provisionado mediante um custo adicional até o limite máximo de tamanho, em incrementos de 250 GB até 1 TB e, em seguida, em incrementos de 256 GB além de 1 TB. Para conhecer os valores de armazenamento incluídos e os limites máximos de tamanho, confira [Banco de dados individual: tamanhos de armazenamento e tamanhos de computação](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- É possível provisionar o armazenamento extra para um único banco de dados aumentando seu tamanho máximo usando o [Portal do Azure](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [Transact-SQL](/powershell/module/azurerm.sql/set-azurermsqldatabase), [PowerShell](/cli/azure/sql/db#az-sql-db-update), [CLI do Azure](https://docs.microsoft.com/rest/api/sql/databases/databases_update) ou API REST.
- O preço do armazenamento extra para um único banco de dados é a quantidade de armazenamento extra multiplicada pelo preço unitário do armazenamento extra da camada de serviço. Para obter detalhes sobre o preço de armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL](sql-database-file-space-management.md).

## <a name="dtu-based-purchasing-model-change-compute-resources-dtus"></a>Modelo de compra com base em DTU: alterar os recursos de computação (DTUs)

Depois de escolher inicialmente um nível de serviço, tamanho de computação e quantidade de armazenamento, você pode dimensionar um banco de dados individual dinamicamente com base na experiência real usando o portal do Azure, [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), a [CLI do Azure](/cli/azure/sql/db#az-sql-db-update) ou a [API REST](https://docs.microsoft.com/rest/api/sql/databases/databases_update).

O vídeo a seguir mostra como alterar dinamicamente o nível de serviço e o tamanho de computação para aumentar os DTUs disponíveis para um banco de dados individual.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

A alteração da camada de serviço e/ou tamanho da computação de um banco de dados cria uma réplica do banco de dados original com o novo tamanho de computação e, então, faz a transição das conexões para réplica. Nenhum dado é perdido durante esse processo, mas durante o breve momento em que realizamos a transição para a réplica, conexões com o banco de dados são desabilitadas, então algumas transações em andamento podem ser revertidas. O período de tempo para a alternância varia, mas é inferior a 30 segundos em 99% do tempo. Se houver muitas transações em andamento no momento de desabilitação das conexões, o período de transição poderá ser maior.

A duração de todo o processo de expansão depende a camada tamanho e de serviço do banco de dados antes e após a alteração. Por exemplo, um banco de dados de 250 GB que está mudando para, de ou dentro de uma camada de serviço Standard deverá ser concluída dentro de seis horas. Um banco de dados do mesmo tamanho cujos tamanhos de computação estão mudando dentro da camada de serviço Premium deverá concluir a escala vertical dentro de três horas.

> [!TIP]
> Para monitorar operações em andamento, consulte: [Gerenciar operações usando a API REST SQL](https://docs.microsoft.com/rest/api/sql/operations/operations_list), [Gerenciar operações usando a CLI](/cli/azure/sql/db/op), [Monitorar operações usando T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e esses dois comandos do PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) e [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

- Se você estiver atualizando para um nível de serviço ou tamanho de computação maior, o tamanho máximo do banco de dados não aumentará, a menos que você especifique explicitamente um tamanho maior (tamanho máximo).
- Para fazer downgrade de um banco de dados, o espaço usado dele deve ter um tamanho menor do que o máximo permitido para a camada de serviço e o tamanho de computação de destino.
- Ao fazer o downgrade da camada **Premium** para a camada **Standard**, um custo de armazenamento extra será aplicado se (1) o tamanho máximo do banco de dados tiver suporte no tamanho de computação de destino e (2) o tamanho máximo ultrapassar a quantidade de armazenamento incluída de tamanho de computação de destino. Por exemplo, se um banco de dados P1 com um tamanho máximo de 500 GB for reduzido para S3, um custo de armazenamento extra será aplicado, pois S3 dá suporte a um tamanho máximo de 500 GB, e a quantidade de armazenamento incluído é somente de 250 GB. Assim, a quantidade de armazenamento extra será 500 GB – 250 GB = 250 GB. Para obter o preço do armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço usado for menor do que a quantidade de armazenamento incluído, esse custo extra poderá ser evitado por meio da redução do tamanho máximo do banco de dados para a quantidade incluída.
- Ao atualizar um banco de dados com [replicação geográfica](sql-database-geo-replication-portal.md) habilitada, atualize seus bancos de dados secundários para a camada de serviço e o tamanho de computação desejados antes de atualizar o banco de dados primário (orientação geral para o melhor desempenho). Ao fazer upgrade para uma diferente, o upgrade do banco de dados secundário será exigido antes.
- Ao fazer downgrade de um banco de dados com [replicação geográfica](sql-database-geo-replication-portal.md) habilitada, faça downgrade dos seus bancos de dados primários para a camada de serviço e o tamanho de computação desejados antes de atualizar o banco de dados secundário (orientação geral para o melhor desempenho). Ao fazer o downgrade para uma edição diferente, antes será necessário fazer o downgrade do banco de dados primário.
- As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se você estiver fazendo downgrade para camada **Básica**, há um período de retenção de backup inferior. Consulte [Backups de banco de dados SQL do Azure](sql-database-automated-backups.md).
- As novas propriedades do banco de dados não serão aplicadas até que as alterações sejam concluídas.

## <a name="dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Modelo de compra com base em DTU: limitações de P11 e P15 quando o tamanho máximo for superior a 1 TB

Um tamanho máximo maior que 1 TB para o banco de dados P11 e P15 tem suporte nas seguintes regiões: Leste da Austrália, Sudeste da Austrália, Sul do Brasil, Canadá Central, Leste do Canadá, Centro dos EUA, França Central, Alemanha Central, Leste do Japão, Oeste do Japão, Coreia Central, Centro-Norte dos EUA, Europa Setentrional, Centro-Sul dos EUA, Sudeste Asiático, Sul do Reino Unido, Oeste do Reino Unido, Leste dos EUA 2, Oeste dos EUA, Gov. EUA - Virgínia e Europa Ocidental. As seguintes considerações e limitações se aplicam aos bancos de dados P11 e P15 com um tamanho máximo superior a 1 TB:

- Se você escolher um tamanho máximo superior a 1 TB durante a criação de um banco de dados (usando um valor de 4 TB ou 4096 GB), o comando create falhará com um erro se o banco de dados for provisionado em uma região sem suporte.
- Para bancos de dados P11 e P15 existentes localizados em uma das regiões com suporte, você pode aumentar o armazenamento máximo para além de 1 TB em incrementos de 256 GB até 4 TB. Para ver se há suporte para um tamanho maior em sua região, use a função [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) ou inspecione o tamanho do banco de dados no Portal do Azure. A atualização de um banco de dados existente P11 ou P15 só pode ser executada por um logon principal no nível de servidor ou por membros da função de banco de dados dbmanager.
- Se uma operação de atualização for executada em uma região com suporte, a configuração será atualizada imediatamente. O banco de dados permanece online durante o processo de atualização. No entanto, não é possível utilizar a quantidade total de armazenamento além de 1 TB de armazenamento até que os arquivos de banco de dados reais sejam atualizados para o novo tamanho máximo. O período de tempo necessário depende do tamanho do banco de dados que está sendo atualizado.
- Ao criar ou atualizar um banco de dados P11 ou P15, você só pode escolher entre 1 TB e 4 TB de tamanho máximo em incrementos de 256 GB. Ao criar um P11/P15, a opção de armazenamento padrão de 1 TB é pré-selecionada. Para bancos de dados localizados em uma das regiões com suporte, você pode aumentar o número máximo de armazenamento de 4 TB para um banco de dados individual novo ou existente. Para todas as outras regiões, o tamanho máximo não pode ultrapassar 1 TB. O preço não é alterado quando você seleciona 4 TB de armazenamento incluído.
- Se o tamanho máximo de um banco de dados for definido como superior a 1 TB, não poderá ser alterado para 1 TB, mesmo se o armazenamento real usado estiver abaixo de 1 TB. Portanto, não é possível fazer downgrade de um P11 ou P15 com um tamanho máximo superior a 1 TB para um P11 de 1 TB ou P15 de 1 TB ou um tamanho de computação inferior, como P1-P6. Esta restrição também se aplica aos cenários de restauração e de cópia, incluindo restauração pontual e geográfica, retenção de backup a longo prazo e cópia de banco de dados. Quando um banco de dados estiver configurado com um tamanho máximo superior a 1 TB, todas as operações de restauração desse banco de dados devem ser executadas em um P11/P15 com um tamanho máximo superior a 1 TB.
- Para cenários com replicação geográfica ativa:
  - Configurar uma relação de replicação geográfica: se o banco de dados primário for P11 ou P15, os secundários também deverão ser P11 ou P15; os tamanhos de computação inferiores serão rejeitadas como secundários porque não são capazes de dar suporte a mais de 1 TB.
  - Atualizando o banco de dados primário em uma relação de replicação geográfica: alterar o tamanho máximo para mais de 1 TB em um banco de dados primário disparará a mesma alteração no banco de dados secundário. As duas atualizações devem ser bem-sucedidas para que a alteração no primário entre em vigor. Há limitações de região para a opção superior a 1 TB. Se o secundário estiver em uma região que não oferece suporte a mais de 1 TB, o primário não será atualizado.
- Não há suporte para o uso do serviço de Importação/Exportação para carregar bancos de dados P11/P15 com mais de 1 TB. Use SqlPackage.exe para [importar](sql-database-import.md) e [exportar](sql-database-export.md) dados.

## <a name="next-steps"></a>Próximas etapas

Para saber os limites gerais de recursos, confira [Limites de recurso baseados em vCore do Banco de Dados SQL – bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md) e [Limites de recurso baseados em DTU do Banco de Dados SQL – pools elásticos](sql-database-dtu-resource-limits-single-databases.md).
