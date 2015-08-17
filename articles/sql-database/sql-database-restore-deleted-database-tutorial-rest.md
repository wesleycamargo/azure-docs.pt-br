<properties 
   pageTitle="Restaurar um banco de dados do SQL Azure excluído com a API REST" 
   description="Banco de dados SQL do Microsoft Azure, restaurar banco de dados excluído, recuperar banco de dados excluído, API REST" 
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
   ms.date="07/24/2015"
   ms.author="elfish; v-romcal"/>

# Restaurar um banco de dados do SQL Azure excluído com a API REST

> [AZURE.SELECTOR]
- [Restore deleted database - portal](sql-database-restore-deleted-database-tutorial-management-portal.md)
- [Restore deleted database - PowerShell](sql-database-restore-deleted-database-tutorial-powershell.md) 

## Visão geral

Este guia mostra como restaurar um banco de dados do SQL Azure excluído com a API REST. São fornecidos links para operações mais detalhadas.

Restaurar um banco de dados do SQL Azure excluído cria um novo banco de dados. O serviço seleciona automaticamente a camada de serviço com base no backup usado no ponto de restauração. Certifique-se de ter cota disponível no servidor lógico para criar outro banco de dados. Se desejar solicitar uma cota maior, entre em contato com [Suporte do Azure](http://azure.microsoft.com/support/options/).

## Segurança e restrições

Consulte [Restaurar um banco de dados do SQL Azure excluído no portal do Azure](sql-database-restore-deleted-database-tutorial-management-portal.md)

## Como: Restaurar um banco de dados do SQL Azure excluído usando a API REST

1.	Liste todos os bancos de dados excluídos restauráveis usando a operação [Listar bancos de dados descartados restauráveis](http://msdn.microsoft.com/library/azure/dn509562.aspx).
	
2.	Obtenha os detalhes do banco de dados excluído que deseja restaurar usando a operação [Obter banco de dados descartados restauráveis](http://msdn.microsoft.com/library/azure/dn509574.aspx).

3.	Comece sua restauração usando a operação [Criar solicitação de restauração do banco de dados](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
4.	Acompanhe o status de sua restauração usando a operação [Status de operações do banco de dados](http://msdn.microsoft.com/library/azure/dn720371.aspx).

## Próximas etapas

Para obter mais informações, consulte o seguinte:

[Continuidade dos negócios no Banco de dados SQL do Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Restauração e Backup de banco de dados do SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Referência de API REST de gerenciamento de serviço](http://msdn.microsoft.com/library/azure/ee460799.aspx)

<!---HONumber=August15_HO6-->