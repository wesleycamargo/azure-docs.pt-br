---
title: "Backups do SQL Data Warehouse do Azure - instantâneos, redundância geográfica | Microsoft Docs"
description: "Saiba mais sobre os backups do banco de dados interno do SQL Data Warehouse que permitem que você restaure um SQL Data Warehouse do Azure para um ponto de restauração ou outra região geográfica."
services: sql-data-warehouse
documentationcenter: 
author: barbkess
manager: jhubbard
editor: 
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: e76349ef7a2afa02d4f9e5295f299bb8084d1e08
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2017
---
# <a name="backup-and-restore-in-sql-data-warehouse"></a>Backup e restauração no SQL Data Warehouse
Este artigo explica as especificidades de backups no SQL Data Warehouse. Use backups do data warehouse para restaurar um instantâneo do banco de dados na região primária ou restaurar um backup geográfico em sua região geográfica emparelhada. 

## <a name="what-is-a-data-warehouse-backup"></a>O que é um backup do data warehouse?
Um backup do data warehouse é a cópia do seu banco de dados que pode ser usada para restaurar um data warehouse.  Considerando que o SQL Data Warehouse é um sistema distribuído, um backup do data warehouse consiste em vários arquivos localizados no armazenamento do Azure. Um backup do data warehouse inclui instantâneos do banco de dados locais e backups geográficos de todos os bancos de dados e arquivos associados a um data warehouse. 

## <a name="local-snapshot-backups"></a>Backups de instantâneo locais
O SQL Data Warehouse usa instantâneos do seu data warehouse ao longo do dia. Os instantâneos estão disponíveis durante sete dias. O SQL Data Warehouse dá suporte a um RPO (objetivo de ponto de recuperação) de oito horas. É possível restaurar seu data warehouse na região primária para qualquer um dos instantâneos tirados nos últimos sete dias.

Para ver quando o último instantâneo iniciado, execute esta consulta no SQL Data Warehouse online. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

## <a name="geo-backups"></a>Backups geográficos
O SQL Data Warehouse executa um backup geográfico uma vez por dia em um [data center emparelhado](../best-practices-availability-paired-regions.md). O RPO de uma restauração geográfica é de 24 horas. É possível restaurar o backup geográfico para o servidor na região geográfica emparelhada. o backup geográfico garante que você possa restaurar um data warehouse caso não seja possível acessar os instantâneos em sua região primária.

Os backups geográficos são ativados por padrão. Se o data warehouse for adequado para elasticidade, será possível [recusar](https://docs.microsoft.com/powershell/resourcemanager/Azurerm.sql/v2.1.0/Set-AzureRmSqlDatabaseGeoBackupPolicyredirectedfrom=msdn) se desejar. Não é possível recusar os backups geográficos com o nível de desempenho otimizado para computação.

## <a name="backup-costs"></a>Custos de backup
Você observará que a fatura do Azure tem um item de linha para o Armazenamento Premium do Azure e um item de linha para armazenamento com redundância geográfica. O encargo do Armazenamento Premium é o custo total para armazenar seus dados na região primária, que inclui instantâneos.  O encargo com redundância geográfica abrange o custo para armazenar os backups geográficos.  

O custo total para o data warehouse primário e de sete dias de instantâneos de Blob do Azure é arredondado para o TB mais próximo. Por exemplo, se seu data warehouse é de 1,5 TB e os instantâneos usam 100 GB, você será cobrado pelo equivalente a 2 TB de dados segundo as taxas do Armazenamento Premium do Azure. 

> [!NOTE]
> Cada instantâneo está inicialmente vazio e aumenta à medida que você faz alterações ao depósito de dados primário. Todos os instantâneos aumentam de tamanho conforme o data warehouse é alterado. Portanto, os custos de armazenamento de instantâneos crescem de acordo com a taxa de alteração.
> 
> 

Se você estiver usando armazenamento com redundância geográfica, você receberá uma cobrança de armazenamento separada. O armazenamento com redundância geográfica é cobrado segundo a taxa padrão de RA-GRS (Armazenamento com Redundância Geográfica com Acesso de Leitura).

Para obter mais informações sobre os preços do SQL Data Warehouse, consulte [Preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Retenção de instantâneo quando um data warehouse está em pausa
O SQL Data Warehouse não cria nem expira instantâneos enquanto um data warehouse está em pausa. A idade de instantâneo não será alterada enquanto o data warehouse estiver em pausa. A retenção do instantâneo baseia-se no número de dias pelos quais o data warehouse estiver online e não pelos dias do calendário.

Por exemplo, se um instantâneo iniciar em 1º de outubro às 16h e o data warehouse for colocado em pausa em 3 de outubro às 16h, os instantâneos terão até dois dias. Quando o data warehouse voltar a ficar online, o instantâneo terá dois dias. Se o data warehouse voltar a ficar online em 5 de outubro às 16h, o instantâneo terá dois dias e permanecerá por mais cinco dias.

Quando o data warehouse de volta a ficar online, o SQL Data Warehouse retoma novos instantâneos e expira instantâneos quando eles têm dados com mais de sete dias.

## <a name="can-i-restore-a-dropped-data-warehouse"></a>Posso restaurar um data warehouse removido?
Quando você remove um data warehouse, o SQL Data Warehouse cria um instantâneo final e o salva por sete dias. É possível restaurar o data warehouse para o ponto de restauração final criado na exclusão. 

> [!IMPORTANT]
> Se você excluir uma instância lógica do SQL Server, todos os bancos de dados que pertencerem a essa instância também serão excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído.
> 

## <a name="next-steps"></a>Próximas etapas
Para restaurar um SQL data warehouse, veja [Restaurar um SQL data warehouse](sql-data-warehouse-restore-database-overview.md).

Para obter uma visão geral sobre a continuidade de negócios, veja [Visão geral da continuidade de negócios](../sql-database/sql-database-business-continuity.md)
