<properties 
   pageTitle="Recuperação de erro do usuário no Banco de Dados SQL" 
   description="Saiba como fazer a recuperação de um erro do usuário, da corrupção de dados acidental ou de um banco de dados excluído usando o recurso PITR (Restauração Pontual) do Banco de Dados SQL do Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="05/10/2016"
   ms.author="elfish"/>

# Recuperar um Banco de Dados SQL do Azure de um erro do usuário

O Banco de Dados SQL do Azure oferece dois recursos principais para recuperação de erro do usuário ou modificação de dados não intencional.

- [Restauração Pontual](sql-database-point-in-time-restore.md) 
- [Restaurar banco de dados excluído](sql-database-point-in-time-restore.md#restoring-a-recently-deleted-database)


O Banco de Dados SQL do Azure sempre restaura para um novo banco de dados. Esses recursos de restauração são oferecidos para todos os bancos de dados Basic, Standard e Premium.

##Recuperação Pontual

No caso de um erro do usuário ou uma modificação de dados não intencional, a Restauração Pontual pode ser usada para fazer uma restauração pontual do banco de dados dentro do período de retenção dos bancos de dados.

Os bancos de dados Basic tem 7 dias de retenção, os bancos de dados Standard tem 14 dias de retenção e os bancos de dados Premium têm 35 dias de retenção. Para saber mais sobre a retenção de banco de dados, consulte a [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).

Para executar uma recuperação pontual:

- [Recuperação pontual com o Portal do Azure](sql-database-point-in-time-restore-portal.md)
- [Recuperação pontual com o PowerShell](sql-database-point-in-time-restore-powershell.md)
- [Recuperação Pontual com a API REST (createmode=PointInTimeRestore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) 


## Restaurar um banco de dados excluído

No caso de um banco de dados excluído, o Banco de Dados SQL do Azure permite restaurar o banco de dados excluído até o momento da exclusão. O Banco de Dados SQL do Azure armazena o backup do banco de dados excluído pelo período de retenção do banco de dados.

O período de retenção de um banco de dados excluído é determinado pela camada de serviço do banco de dados enquanto ela existiu ou pelo número de dias em que o banco de dados existe, o que for menor. Para saber mais sobre retenção de banco de dados, leia nossa [visão geral da continuidade dos negócios](sql-database-business-continuity.md).

Para restaurar um banco de dados excluído:

- [Restaurar um banco de dados excluído com o Portal do Azure](sql-database-restore-deleted-database-portal.md)
- [Restaurar um banco de dados excluído com o PowerShell](sql-database-restore-deleted-database-powershell.md)
- [Restaurar um banco de dados excluído com a API REST (createmode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)


## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Documentação do Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->