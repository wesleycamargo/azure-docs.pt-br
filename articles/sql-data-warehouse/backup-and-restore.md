---
title: Backups do SQL Data Warehouse do Azure e restauração - instantâneos, redundância geográfica | Microsoft Docs
description: Aprenda a fazer backup e restauração do banco de dados no SQL Data Warehouse do Azure. Use backups de data warehouse para restaurar o data warehouse para um ponto de restauração na região primária. Use backups com redundância geográfica para restaurar para uma região geográfica diferente.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/24/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e9b5005fad1eeb13314e1fb6a5708bb02b96cbf9
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43248649"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Faça o backup e restauração no SQL Data Warehouse
Aprenda a fazer backup e restauração do banco de dados no SQL Data Warehouse do Azure. Utilize instantâneos de data warehouse para recuperação ou copie seu data warehouse para um ponto de restauração anterior na região primária. Use backups de data warehouse com redundância geográfica para restaurar para uma região geográfica diferente. 

## <a name="what-is-a-data-warehouse-snapshot"></a>O que é um instantâneo de data warehouse?
Um *instantâneo de data warehouse* cria um ponto de restauração que pode ser aproveitado para recuperar ou copiar seu data warehouse para um estado anterior.  Considerando que o SQL Data Warehouse é um sistema distribuído, um instantâneo do data warehouse consiste em vários arquivos localizados no armazenamento do Azure. Os instantâneos capturam as alterações incrementais dos dados armazenados no data warehouse.

A *restauração de um data warehouse* é um novo data warehouse criado por meio de um ponto de restauração de um data warehouse existente ou excluído. Restaurar o seu banco de dados é uma parte essencial de qualquer estratégia de recuperação de desastre e de continuidade dos negócios, porque ela recria seus dados após uma exclusão ou corrupção acidental. O data warehouse também é um mecanismo eficiente para criar cópias do seu data warehouse para fins de teste ou desenvolvimento.  O SQL Data Warehouse usa mecanismos de restauração rápida na mesma região que foi medida para levar menos de 20 minutos para qualquer tamanho de dados. 

## <a name="automatic-restore-points"></a>Pontos de restauração automática
Os instantâneos são um recurso interno do serviço que cria pontos de restauração. Não é necessário habilitar essa funcionalidade. No momento, os pontos de restauração automática não podem ser excluídos por usuários em que o serviço utiliza tais pontos de restauração para manter SLAs para recuperação.

O SQL Data Warehouse cria instantâneos do seu data warehouse ao longo do dia, gerando pontos de restauração que ficam disponíveis por sete dias. Esse período de retenção não pode ser alterado. O SQL Data Warehouse dá suporte a um RPO (objetivo de ponto de recuperação) de oito horas. É possível restaurar seu data warehouse na região primária com base em qualquer um dos instantâneos tirados nos últimos sete dias.

Para ver quando o último instantâneo iniciado, execute esta consulta no SQL Data Warehouse online. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Pontos de restauração definidos pelo usuário
Com esse recurso, é possível disparar instantâneos manualmente para criar pontos de restauração do seu data warehouse antes e depois de grandes modificações. A funcionalidade garante que os pontos de restauração sejam logicamente consistentes, o que oferece uma proteção de dados adicional em caso de interrupções da carga de trabalho ou erros do usuário para um tempo de recuperação rápido. Os pontos de restauração definidos pelo usuário ficam disponíveis por sete dias e são excluídos automaticamente em seu nome. Não é possível alterar o período de retenção de pontos de restauração definidos pelo usuário. Há suporte para apenas 42 pontos de restauração definidos pelo usuário a qualquer momento; portanto, precisam ser [excluídos](https://go.microsoft.com/fwlink/?linkid=875299) antes da criação de outro ponto de restauração. É possível disparar instantâneos para criar pontos de restauração definidos pelo usuário por meio do [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabaserestorepoint?view=azurermps-6.2.0#examples) ou do Portal do Azure.


> [!NOTE]
> Caso precise de pontos de restauração de mais de 7 dias, escolha esta funcionalidade [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Você também pode criar um ponto de restauração definido pelo usuário e restaurar com base no ponto de restauração criado recentemente para um novo data warehouse. Após a restauração, seu data warehouse estará online e será possível pausá-lo indefinidamente para em economizar custos de computação. O banco de dados em pausa tem encargos de armazenamento com a taxa de armazenamento Premium do Azure. Se você precisar de uma cópia ativa do data warehouse restaurado, poderá retomar, o que deve levar apenas alguns minutos.
>

### <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Retenção de instantâneo quando um data warehouse está em pausa
O SQL Data Warehouse não cria instantâneos nem expira pontos de restauração enquanto um data warehouse está em pausa. Os pontos de restauração não serão alterados enquanto o data warehouse estiver em pausa. A retenção do ponto de restauração baseia-se no número de dias pelos quais o data warehouse estiver online e não pelos dias do calendário.

Por exemplo, se um instantâneo iniciar em 1º de outubro às 16h e o data warehouse for colocado em pausa em 3 de outubro às 16h, os pontos de restauração terão até dois dias. Quando o data warehouse voltar a ficar online, o ponto de restauração terá dois dias. Se o data warehouse voltar a ficar online em 5 de outubro às 16h, o ponto de restauração terá dois dias e permanecerá por mais cinco dias.

Quando o data warehouse voltar a ficar online, o SQL Data Warehouse retomará a criação de novos pontos de restauração, que expirarão quando tiverem mais de sete dias de dados.

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Retenção de instantâneo quando um data warehouse está em pausa
Quando você remove um data warehouse, o SQL Data Warehouse cria um instantâneo final e o salva por sete dias. É possível restaurar o data warehouse para o ponto de restauração final criado na exclusão. 

> [!IMPORTANT]
> Se você excluir uma instância lógica do SQL Server, todos os bancos de dados que pertencerem a essa instância também serão excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído.
>

## <a name="geo-backups"></a>Backups geográficos
O SQL Data Warehouse executa um backup geográfico uma vez por dia em um [data center emparelhado](../best-practices-availability-paired-regions.md). O RPO de uma restauração geográfica é de 24 horas. Você pode restaurar o backup geográfico para um servidor em qualquer outra região em que o SQL Data Warehouse tem suporte. Um backup geográfico garante que você possa restaurar um data warehouse caso não seja possível acessar os pontos de restauração em sua região primária.

Os backups geográficos são ativados por padrão. Se o data warehouse for Gen1, será possível [recusar](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy) se desejar. Você não pode recusar backups geográficos para Gen2, já que a proteção de dados é uma garantia interna.

> [!NOTE]
> Se precisar de um RPO mais curto para backups geográficos, escolha esta funcionalidade [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse). Você também pode criar um ponto de restauração definido pelo usuário e restaurar com base no ponto de restauração criado recentemente para um novo data warehouse em uma região diferente. Após a restauração, seu data warehouse estará online e será possível pausá-lo indefinidamente para em economizar custos de computação. O banco de dados em pausa tem encargos de armazenamento com a taxa de armazenamento Premium do Azure. e, em seguida, faça uma pausa. Caso você precise de uma cópia ativa do data warehouse, poderá retomar, o que deve levar apenas alguns minutos.
>


## <a name="backup-and-restore-costs"></a>Custos de backup e restauração
Você observará que a fatura do Azure tem um item de linha para Armazenamento e um item de linha para Armazenamento para Recuperação de Desastre. A taxa de Armazenamento é o custo total para armazenar seus dados na região primária, juntamente com as alterações incrementais capturadas por instantâneos. Para obter uma explicação mais detalhada sobre como os instantâneos são criados no momento, consulte esta [documentação](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios). O encargo com redundância geográfica abrange o custo para armazenar os backups geográficos.  

O custo total para o data warehouse primário e de sete dias de alterações de instantâneos é arredondado para o TB mais próximo. Por exemplo, se seu data warehouse é de 1,5 TB e os instantâneos capturam 100 GB, você será cobrado pelo equivalente a 2 TB de dados segundo as taxas do Armazenamento Premium do Azure. 

Se você estiver usando armazenamento com redundância geográfica, você receberá uma cobrança de armazenamento separada. O armazenamento com redundância geográfica é cobrado segundo a taxa padrão de RA-GRS (Armazenamento com Redundância Geográfica com Acesso de Leitura).

Para obter mais informações sobre os preços do SQL Data Warehouse, consulte [Preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) e [taxas de saída](https://azure.microsoft.com/pricing/details/bandwidth/) para restauração em região diferente.

## <a name="restoring-from-restore-points"></a>Restauração a partir dos pontos de restauração
Cada instantâneo cria um ponto de restauração que representa a hora em que o instantâneo é iniciado. Para restaurar um data warehouse, escolha um ponto de restauração e emita um comando de restauração.  

É possível manter o data warehouse restaurado e o atual ou excluir um deles. Se você deseja substituir o data warehouse atual pelo depósito de data warehouse, você pode renomeá-la usando [ALTERAR BANCO DE DADOS (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) com a opção MODIFICAR NOME. 

Para restaurar um data warehouse, consulte [Restaurar um data warehouse usando o portal do Azure](sql-data-warehouse-restore-database-portal.md), [Restaurar de um data warehouse usando o PowerShell](sql-data-warehouse-restore-database-powershell.md), ou [Restaurar de um data warehouse usando o T-SQL](sql-data-warehouse-restore-database-rest-api.md).

Para restaurar um data warehouse excluído ou em pausa, será possível [criar um tíquete de suporte](sql-data-warehouse-get-started-create-support-ticket.md). 


## <a name="geo-redundant-restore"></a>Restauração com redundância geográfica
Você pode [restaurar seu data warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) para qualquer região com suporte do SQL Data Warehouse com o nível de desempenho escolhido. 

> [!NOTE]
> Para executar uma restauração com redundância geográfica, você não deve ter recusado esse recurso.
>

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre planejamento de desastre, consulte [Visão geral sobre a continuidade dos negócios](../sql-database/sql-database-business-continuity.md)
