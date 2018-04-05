---
title: Backups do SQL Data Warehouse do Azure e restauração - instantâneos, redundância geográfica | Microsoft Docs
description: Aprenda a fazer backup e restauração do banco de dados no SQL Data Warehouse do Azure. Use backups de data warehouse para restaurar o data warehouse para um ponto de restauração na região primária ou usar backups com redundância geográfica para restaurá-lo para uma região geográfica diferente.
services: sql-data-warehouse
author: ronortloff
manager: jhubbard
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 03/28/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 7f540bca0d2eb2c9009a386bd14a5beda2912014
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2018
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Faça o backup e restauração no SQL Data Warehouse
Aprenda a fazer backup e restauração do banco de dados no SQL Data Warehouse do Azure. Use backups de data warehouse para restaurar o data warehouse para um ponto de restauração na região primária ou usar backups com redundância geográfica para restaurá-lo para uma região geográfica diferente. 

## <a name="what-is-backup-and-restore"></a>O que é backup e a restauração?
Um *backup do data warehouse* é a cópia do seu banco de dados que pode ser usada para restaurar um data warehouse.  Considerando que o SQL Data Warehouse é um sistema distribuído, um backup do data warehouse consiste em vários arquivos localizados no armazenamento do Azure. Um backup do data warehouse inclui instantâneos do banco de dados locais e backups geográficos de todos os bancos de dados e arquivos associados a um data warehouse. 

A *restauração* de um data warehouse é um novo data warehouse criado por meio de um backup de um data warehouse existente ou excluído. O data warehouse restaurado recria o data warehouse com backup realizado em um momento específico. Restaurar o seu banco de dados é uma parte essencial de qualquer estratégia de recuperação de desastre e de continuidade dos negócios, porque ela recria seus dados após uma exclusão ou corrupção acidental.

Restaurações locais e geográficas fazem parte das funcionalidades de recuperação de desastre de SQL Data Warehouse. 

## <a name="local-snapshot-backups"></a>Backups de instantâneo locais
Backups de instantâneos locais são um recurso interno do serviço.  Você não precisa habilitá-los. 

O SQL Data Warehouse usa instantâneos do seu data warehouse ao longo do dia. Os instantâneos estão disponíveis durante sete dias. O SQL Data Warehouse dá suporte a um RPO (objetivo de ponto de recuperação) de oito horas. É possível restaurar seu data warehouse na região primária para qualquer um dos instantâneos tirados nos últimos sete dias.

Para ver quando o último instantâneo iniciado, execute esta consulta no SQL Data Warehouse online. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

### <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Retenção de instantâneo quando um data warehouse está em pausa
O SQL Data Warehouse não cria nem expira instantâneos enquanto um data warehouse está em pausa. A idade de instantâneo não será alterada enquanto o data warehouse estiver em pausa. A retenção do instantâneo baseia-se no número de dias pelos quais o data warehouse estiver online e não pelos dias do calendário.

Por exemplo, se um instantâneo iniciar em 1º de outubro às 16h e o data warehouse for colocado em pausa em 3 de outubro às 16h, os instantâneos terão até dois dias. Quando o data warehouse voltar a ficar online, o instantâneo terá dois dias. Se o data warehouse voltar a ficar online em 5 de outubro às 16h, o instantâneo terá dois dias e permanecerá por mais cinco dias.

Quando o data warehouse de volta a ficar online, o SQL Data Warehouse retoma novos instantâneos e expira instantâneos quando eles têm dados com mais de sete dias.

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Retenção de instantâneo quando um data warehouse está em pausa
Quando você remove um data warehouse, o SQL Data Warehouse cria um instantâneo final e o salva por sete dias. É possível restaurar o data warehouse para o ponto de restauração final criado na exclusão. 

> [!IMPORTANT]
> Se você excluir uma instância lógica do SQL Server, todos os bancos de dados que pertencerem a essa instância também serão excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído.
> 

## <a name="geo-backups"></a>Backups geográficos
O SQL Data Warehouse executa um backup geográfico uma vez por dia em um [data center emparelhado](../best-practices-availability-paired-regions.md). O RPO de uma restauração geográfica é de 24 horas. É possível restaurar o backup geográfico para o servidor na região geográfica emparelhada. Um backup geográfico garante que você possa restaurar um data warehouse caso não seja possível acessar os instantâneos em sua região primária.

Os backups geográficos são ativados por padrão. Se o data warehouse for adequado para elasticidade, será possível [recusar](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy) se desejar. Não é possível recusar os backups geográficos com o nível de desempenho otimizado para computação.

## <a name="backup-costs"></a>Custos de backup
Você observará que a fatura do Azure tem um item de linha para o Armazenamento Premium do Azure e um item de linha para armazenamento com redundância geográfica. O encargo do Armazenamento Premium é o custo total para armazenar seus dados na região primária, que inclui instantâneos.  O encargo com redundância geográfica abrange o custo para armazenar os backups geográficos.  

O custo total para o data warehouse primário e de sete dias de instantâneos de Blob do Azure é arredondado para o TB mais próximo. Por exemplo, se seu data warehouse é de 1,5 TB e os instantâneos usam 100 GB, você será cobrado pelo equivalente a 2 TB de dados segundo as taxas do Armazenamento Premium do Azure. 

> [!NOTE]
> Cada instantâneo está inicialmente vazio e aumenta à medida que você faz alterações ao depósito de dados primário. Todos os instantâneos aumentam de tamanho conforme o data warehouse é alterado. Portanto, os custos de armazenamento de instantâneos crescem de acordo com a taxa de alteração.
> 
> 

Se você estiver usando armazenamento com redundância geográfica, você receberá uma cobrança de armazenamento separada. O armazenamento com redundância geográfica é cobrado segundo a taxa padrão de RA-GRS (Armazenamento com Redundância Geográfica com Acesso de Leitura).

Para obter mais informações sobre os preços do SQL Data Warehouse, consulte [Preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="restoring-from-restore-points"></a>Restauração a partir dos pontos de restauração
Cada instantâneo tem um ponto de restauração que representa a hora em que o instantâneo é iniciado. Para restaurar um data warehouse, escolha um ponto de restauração e emita um comando de restauração.  

O SQL Data Warehouse sempre restaura o backup para um novo data warehouse. É possível manter o data warehouse restaurado e o atual ou excluir um deles. Se você deseja substituir o data warehouse atual pelo depósito de data warehouse, você pode renomeá-la usando [ALTERAR BANCO DE DADOS (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) com a opção MODIFICAR NOME. 

Para restaurar um data warehouse, consulte [Restaurar um data warehouse usando o portal do Azure](sql-data-warehouse-restore-database-portal.md), [Restaurar de um data warehouse usando o PowerShell](sql-data-warehouse-restore-database-powershell.md), ou [Restaurar de um data warehouse usando o T-SQL](sql-data-warehouse-restore-database-rest-api.md).

Para restaurar um data warehouse excluído ou em pausa, será possível [criar um tíquete de suporte](sql-data-warehouse-get-started-create-support-ticket.md). 


## <a name="geo-redundant-restore"></a>Restauração com redundância geográfica
Você pode restaurar seu data warehouse para qualquer região com suporte do SQL Data Warehouse do Azure com o nível de desempenho escolhido. 

> [!NOTE]
> Para executar uma restauração com redundância geográfica, você não deve ter recusado esse recurso.
> 
> 

## <a name="restore-timeline"></a>Restaurar linha do tempo
É possível restaurar um banco de dados para qualquer ponto de restauração disponível nos últimos sete dias. Os instantâneos iniciam a cada 4-8 horas e permanecem disponíveis por sete dias. Quando um instantâneo possui mais de sete dias, ele expira e seu ponto de restauração fica indisponível. 

Backups não acontecem em relação a um ata warehouse em pausa. Se o seu data warehouse estivere em pausa por mais de sete dias, você não terá pontos de restauração. 

## <a name="restore-costs"></a>Restaurar custos
O custo de armazenamento para o data warehouse restaurado é cobrado na taxa de Armazenamento Premium do Azure. 

Caso você pause um data warehouse restaurado, você será cobrado pelo armazenamento com a taxa de Armazenamento Premium do Azure. A vantagem de pausar é que você não é cobrado pelos recursos de computação.

Para obter mais informações sobre os preços do SQL Data Warehouse, consulte [Preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="restore-use-cases"></a>Restauras casos de uso
O uso primário da restauração do data warehouse é recuperar os dados após a perda de dados ou corrupção acidental. Também é possível usar a restauração de data warehouse para manter um backup por mais de sete dias. Quando o backup for restaurado, o seu data warehouse estará online e será possível pausá-lo indefinidamente para economizar custos de computação. O banco de dados em pausa tem encargos de armazenamento com a taxa de armazenamento Premium do Azure. 

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre planejamento de desastre, consulte [Visão geral sobre a continuidade dos negócios](../sql-database/sql-database-business-continuity.md)
