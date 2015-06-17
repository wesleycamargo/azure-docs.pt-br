<properties 
   pageTitle="Recuperar um banco de dados do SQL Azure usando a restauração geográfica com a API REST" 
   description="Restauração geográfica, Banco de dados SQL do Microsoft Azure, banco de dados de restauração, banco de dados de recuperação, API REST" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="02/24/2015"
   ms.author="elfish; v-romcal"/>

# Recuperar um banco de dados do SQL Azure usando a restauração geográfica com a API REST

> [AZURE.SELECTOR]
- [Restauração geográfica - portal](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/)
- [Restauração geográfica - PowerShell](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-powershell/)

## Visão geral

Este guia mostra como recuperar um banco de dados do SQL Azure usando a API REST. São fornecidos links para operações mais detalhadas. A restauração geográfica é a proteção de recuperação de desastres do núcleo incluída para camadas de serviço do banco de dados do SQL Azure Basic, Standard e Premium.

## Segurança e restrições

Consulte [Recuperar um banco de dados do SQL Azure usando a restauração geográfica no portal do Azure](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/).

## Como: Recuperar um banco de dados do SQL Azure usando a API REST

1.	Obter a lista de bancos de dados recuperáveis usando a operação [Listar bancos de dados recuperáveis](http://msdn.microsoft.com/library/azure/dn800984.aspx).
	
2.	Obter o banco de dados que você deseja recuperar usando a operação [Obter banco de dados recuperável](http://msdn.microsoft.com/library/azure/dn800985.aspx).
	
3.	Criar a solicitação de recuperação usando a operação [Criar solicitação de recuperação de banco de dados](http://msdn.microsoft.com/library/azure/dn800986.aspx).
	
4.	Acompanhar o status de recuperação usando a operação [Status da operação de banco de dados](http://msdn.microsoft.com/library/azure/dn720371.aspx).

## Próximas etapas

Para obter mais informações, consulte o seguinte:

[Restaurar um banco de dados do SQL Azure usando o ponto de restauração pontual no portal do Azure](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/).

[Restaurar um banco de dados do SQL Azure excluído no portal do Azure](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)

[Continuidade dos negócios no Banco de dados SQL do Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Restauração e Backup de banco de dados do SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Restauração geográfica do banco de dados do SQL Azure (blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

[Referência de API REST de gerenciamento de serviço](https://msdn.microsoft.com/library/azure/ee460799.aspx)

<!--HONumber=47-->
 