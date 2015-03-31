<properties 
   pageTitle="Restaurar um banco de dados do SQL Azure usando o ponto de restauração pontual com API REST" 
   description="Ponto de restauração pontual, banco de dados do SQL do Microsoft Azure, banco de dados de restauração, banco de dados de recuperação, API REST" 
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

# Restaurar um banco de dados do SQL Azure usando o ponto de restauração pontual com API REST

> [AZURE.SELECTOR]
- [Ponto de restauração pontual - portal](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)
- [Ponto de restauração pontual - PowerShell](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-powershell/)

## Visão geral

Este guia mostra como restaurar um banco de dados do SQL Azure usando o ponto de restauração pontual com API REST. São fornecidos links para operações mais detalhadas.

A restauração pontual cria um novo banco de dados. O serviço seleciona automaticamente a camada de serviço com base no backup usado no ponto de restauração. Certifique-se de ter cota disponível no servidor lógico para criar outro banco de dados. Se desejar solicitar uma cota maior, entre em contato com [Suporte do Azure](http://azure.microsoft.com/support/options/).

## Segurança e restrições

Consulte [Restaurar um banco de dados do SQL Azure usando o ponto de restauração pontual no portal do Azure](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/).

## Como: Restaurar um banco de dados do SQL Azure usando a API REST

1.	Obter o banco de dados que você deseja restaurar utilizando a operação [Obter banco de dados](http://msdn.microsoft.com/library/azure/dn505708.aspx).

2.	Criar a solicitação de restauração usando a operação [Criar solicitação de restauração de banco de dados](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
3.	Controlar a solicitação de restauração usando a operação [Status da operação de banco de dados](http://msdn.microsoft.com/library/azure/dn720371.aspx).

## Próximas etapas

Para obter mais informações, consulte o seguinte: 

[Continuidade dos negócios no Banco de dados SQL do Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Restauração e Backup de banco de dados do SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Ponto de banco de dados do SQL Azure em tempo de restauração (blog)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[Referência de API REST de gerenciamento de serviço](https://msdn.microsoft.com/library/azure/ee460799.aspx)

<!--HONumber=47-->
