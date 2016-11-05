---
title: Configurar uma regra de firewall no nível de servidor do Banco de Dados SQL | Microsoft Docs
description: Saiba como configurar o firewall para endereços IP que acessam o Azure SQL Server.
services: sql-database
documentationcenter: ''
author: BYHAM
manager: jhubbard
editor: ''

ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/30/2016
ms.author: rickbyh;carlrab

---
# Configurar uma regra de firewall no nível de servidor do Banco de Dados SQL do Azure usando o Portal do Azure
> [!div class="op_single_selector"]
> * [Visão geral](sql-database-firewall-configure.md)
> * [Portal do Azure](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [API REST](sql-database-configure-firewall-settings-rest.md)
> 
> 

O SQL Server do Azure usa regras de firewall para permitir conexões com servidores e bancos de dados. Você pode definir configurações de firewall nos níveis de banco de dados e de servidor para o banco de dados de usuário ou mestre no seu servidor lógico do SQL Server do Azure a fim de permitir seletivamente o acesso ao banco de dados. Este tópico discute as regras de firewall no nível do servidor.

> [!IMPORTANT]
> Para permitir que os aplicativos do Azure se conectem ao seu SQL Server do Azure, as conexões do Azure devem estar habilitadas. Para entender como as regras de firewall funcionam, confira [Como configurar um firewall do Azure SQL Server - visão geral](sql-database-firewall-configure.md). Se você estiver fazendo conexões dentro do limite de nuvem do Azure, terá que abrir algumas portas TCP adicionais. Para obter mais informações, veja a seção **V12 do Banco de Dados SQL: fora versus dentro** de [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)
> 
> 

**Recomendação**: use regras de firewall no nível do servidor quando você tiver muitos bancos de dados com os mesmos requisitos de acesso e não quiser gastar tempo configurando cada um individualmente. A Microsoft recomenda o uso de regras de firewall no nível do banco de dados sempre que possível, a fim de tornar seu banco de dados mais portátil.

[!INCLUDE [Criar Banco de Dados SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Gerenciar regras de firewall existentes no nível de servidor pelo Portal do Azure
Repita as etapas para gerenciar as regras de firewall de nível de servidor.

* Para adicionar o computador atual, clique em Adicionar IP do cliente.
* Para adicionar mais endereços IP, digite o Nome da Regra, o Endereço IP Inicial e o Endereço IP Final.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique.
* Para excluir uma regra existente, passe o mouse sobre a regra até que o X apareça no final da linha. Clique no X para remover a regra.

Clique em **Salvar** para salvar as alterações.

## Próximas etapas
Para ver um artigo sobre como usar o Transact-SQL para criar regras de firewall no nível do banco de dados e no nível do servidor, consulte [Configurar regras de firewall nos níveis do banco de dados e do servidor do Banco de Dados SQL do Azure usando o T-SQL](sql-database-configure-firewall-settings-tsql.md).

Para ver artigos sobre como criar regras de firewall no nível de servidor usando outros métodos, confira:

* [Configurar regras de firewall no nível de servidor do Banco de Dados SQL do Azure usando o PowerShell](sql-database-configure-firewall-settings-powershell.md)
* [Configurar regras de firewall no nível de servidor do Banco de Dados SQL do Azure usando a API REST](sql-database-configure-firewall-settings-rest.md)

Para ver um tutorial sobre como criar um banco de dados, confira [Criar um banco de dados SQL em alguns minutos usando o Portal do Azure](sql-database-get-started.md). Para obter ajuda com a conexão de um Banco de Dados SQL do Azure a partir de aplicativos de fonte aberta ou de terceiros, consulte [Exemplos de código do cliente de início rápido para o Banco de Dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx). Para entender como navegar para os bancos de dados, consulte [Gerenciar o acesso ao banco de dados e a segurança de logon](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## Recursos adicionais
* [Protegendo o banco de dados](sql-database-security.md)
* [Central de segurança do mecanismo de banco de dados do SQL Server e banco de dados SQL do Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->



<!---HONumber=AcomDC_0831_2016-->