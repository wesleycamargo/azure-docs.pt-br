---
title: "PowerShell: configuração das regras de firewall do Banco de dados SQL | Microsoft Docs"
description: "Saiba como configurar as regras de firewall de nível de servidor para os endereços IP que acessam os bancos de dados SQL do Azure usando o PowerShell."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 30dcea72-61c1-48b6-8e1d-b1db2eb61567
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: a88c38f01b62dee9454f612e795b6722db2547e8
ms.lasthandoff: 02/17/2017


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-by-using-powershell"></a>Configurar regras de firewall no nível de servidor do Banco de Dados SQL do Azure usando o PowerShell

O Banco de Dados SQL do Azure usa regras de firewall para permitir conexões com servidores e bancos de dados. Você pode definir configurações de firewall nos níveis de banco de dados e de servidor para o banco de dados de usuário ou mestre no seu servidor do Banco de Dados SQL a fim de permitir seletivamente o acesso ao banco de dados.

> [!IMPORTANT]
> Para permitir que os aplicativos do Azure se conectem ao seu servidor de banco de dados, as conexões do Azure devem estar habilitadas. Para obter mais informações sobre regras de firewall e como habilitar conexões do Azure, confira [Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md). Se você estiver fazendo conexões dentro do limite de nuvem do Azure, terá que abrir algumas portas TCP adicionais. Para obter mais informações, veja a seção "V12 do Banco de Dados SQL: fora versus dentro" de [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).
> 
> 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-server-firewall-rules"></a>Criar regras de firewall de servidor
As regras de firewall no nível de servidor podem ser criadas, atualizadas e excluídas usando o Azure PowerShell.

Para criar uma nova regra de firewall no nível de servidor, execute o cmdlet [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx). O exemplo a seguir permite um intervalo de endereços IP no servidor Contoso.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'        

Para modificar uma regra de firewall existente no nível de servidor, execute o cmdlet [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx). O exemplo a seguir altera o intervalo de endereços IP aceitáveis para a regra denominada ContosoFirewallRule.

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -StartIPAddress 192.168.1.4 -EndIPAddress 192.168.1.10 -FirewallRuleName 'ContosoFirewallRule' -ServerName 'Contoso'

Para excluir uma regra de firewall existente no nível de servidor, execute o cmdlet [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx). O exemplo a seguir exclui a regra denominada ContosoFirewallRule.

    Remove-AzureRmSqlServerFirewallRule -FirewallRuleName 'ContosoFirewallRule' -ServerName 'Contoso'


## <a name="manage-firewall-rules-by-using-powershell"></a>Gerenciar regras de firewall usando o PowerShell
Você também pode usar o PowerShell para gerenciar regras de firewall. Para saber mais, consulte os tópicos a seguir:

* [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx)
* [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx)
* [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx)
* [Get-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603586\(v=azure.300\).aspx)

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre como usar o Transact-SQL para criar regras de firewall no nível de banco de dados e no nível de servidor, consulte [Configurar um firewall no nível de banco de dados e no nível de servidor do Banco de Dados SQL do Azure usando T-SQL](sql-database-configure-firewall-settings-tsql.md).

Para obter informações sobre como criar regras de firewall no nível de servidor usando outros métodos, consulte:

* [Configurar regras de firewall no nível do servidor do Banco de Dados SQL do Azure usando o portal do Azure](sql-database-configure-firewall-settings.md)
* [Configurar regras de firewall no nível de servidor do Banco de Dados SQL do Azure usando a API REST](sql-database-configure-firewall-settings-rest.md)

Para ver um tutorial sobre como criar um banco de dados, consulte [Seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).
Para obter ajuda com a conexão a um Banco de Dados SQL do Azure por meio de aplicativos de software livre ou de terceiros, consulte [Exemplos de código do cliente de início rápido do Banco de Dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Para entender como navegar para bancos de dados, consulte [Gerenciar o acesso ao banco de dados e a segurança de logon](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Recursos adicionais
* [Protegendo o banco de dados](sql-database-security-overview.md)
* [Central de segurança do mecanismo de banco de dados do SQL Server e banco de dados SQL do Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

