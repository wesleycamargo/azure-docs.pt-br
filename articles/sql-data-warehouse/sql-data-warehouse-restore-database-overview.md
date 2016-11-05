---
title: Restaurar um SQL Data Warehouse do Azure (Visão geral) | Microsoft Docs
description: Visão geral das opções de restauração para recuperar um banco de dados no SQL Data Warehouse do Azure.
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/28/2016
ms.author: lakshmir;barbkess;sonyama

---
# Recuperar um SQL Data Warehouse do Azure (Visão geral)
> [!div class="op_single_selector"]
> * [Visão geral][Visão geral]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

O Azure SQL Data Warehouse protege seus dados com armazenamento com redundância local e backups automatizados. Os Backups Automatizados fornecem uma maneira sem administradores de proteger seus bancos de dados contra danos ou exclusão acidental. No caso em que um usuário modifica ou exclui dados involuntária ou acidentalmente, você pode garantir a continuidade de negócios com a restauração do seu banco de dados para um determinado ponto anterior. O SQL Data Warehouse usa instantâneos do Armazenamento do Azure para fazer backup de seu banco de dados sem interrupções e sem a necessidade de tempo de inatividade.

## Backups automatizados
Os bancos de dados **ativos** terão o backup feito automaticamente pelo menos a cada oito horas e mantidos por sete dias. Isso permite que você restaure o banco de dados ativo para um dos vários pontos de restauração dos últimos sete dias.

Quando um banco de dados é pausado, os novos backups serão interrompidos e os backups anteriores serão removidos depois que completarem sete dias. Se um banco de dados for pausado por mais de sete dias, o backup mais recente será salvo, garantindo que você sempre tenha pelo menos um backup.

Quando um banco de dados for descartado, o último backup será salvo por sete dias.

Execute essa consulta no seu SQL Data Warehouse ativo para ver quando o último backup foi feito:

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

Se você precisar manter um backup por mais de sete dias, poderá simplesmente restaurar um dos seus pontos de restauração para um novo banco de dados e opcionalmente pausar esse banco de dados para pagar apenas o espaço de armazenamento do backup.

## Redundância de dados
Além de backups, o SQL Data Warehouse também protege seus dados com Armazenamento Premium [LRS (localmente redundante)][LRS (localmente redundante)] do Azure. Várias cópias síncronas dos dados são mantidas no datacenter local para garantir a proteção transparente de dados em caso de falhas localizadas. A redundância de dados faz com que os dados possam sobreviver problemas de infraestrutura, como falhas de disco, etc. A redundância de dados garante a continuidade de negócios com uma infraestrutura altamente disponível e tolerante a falhas.

## Restaurar um banco de dados
Restaurar um SQL Data Warehouse é uma operação simples que pode ser feita no portal do Azure ou automatizada usando o PowerShell ou APIs REST.

## Próximas etapas
Para saber mais sobre os recursos de continuidade de negócios das edições do Banco de Dados SQL do Azure, leia a [Visão geral de continuidade de negócios do Banco de Dados SQL do Azure][Visão geral de continuidade de negócios do Banco de Dados SQL do Azure].

<!--Image references-->

<!--Article references-->
[Visão geral de continuidade de negócios do Banco de Dados SQL do Azure]: ./sql-database-business-continuity.md
[LRS (localmente redundante)]: ../storage/storage-redundancy.md
[Visão geral]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0824_2016-->