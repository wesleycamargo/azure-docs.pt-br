---
title: "Azure PowerShell: criar um único Banco de Dados SQL | Microsoft Docs"
description: "Aprenda a criar um servidor lógico do Banco de Dados SQL, uma regra de firewall no nível de servidor e bancos de dados com o portal do Azure."
keywords: tutorial do banco de dados SQL, criar um banco de dados SQL
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: fe527f7de573b87fbc644cb6d71ae13816bc284b
ms.lasthandoff: 03/15/2017

---

# <a name="create-a-single-azure-sql-database-using-powershell"></a>Criar um único Banco de Dados SQL do Azure usando o PowerShell

O PowerShell é usado para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este guia detalha o uso do PowerShell para implantar um Banco de Dados SQL do Azure em um grupo de recursos do Azure em um servidor lógico do Banco de Dados SQL.

Antes de começar, verifique se a versão mais recente do PowerShell está instalada. A CLI do Azure foi instalada. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azureps-cmdlets-docs). 

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando [Add-AzureRmAccount](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.profile/v2.5.0/add-azurermaccount) e acompanhe as instruções na tela.

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westeurope`.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "westeurope"
```
## <a name="create-a-logical-server"></a>Criar um servidor lógico

Crie um servidor lógico do Banco de Dados SQL com o comando [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver). O exemplo a seguir cria um servidor nomeado aleatoriamente em seu grupo de recursos com um logon de administrador chamado `ServerAdmin` e uma senha `ChangeYourAdminPassword1`. Substitua esses valores predefinidos como desejado.

```powershell
$servername = "server-$(Get-Random)"
New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -Location "westeurope" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList "ServerAdmin", $(ConvertTo-SecureString -String "ChangeYourAdminPassword1" -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall de servidor

Crie uma regra de firewall no nível de servidor do Banco de Dados SQL com o comando [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule). Uma regra de firewall no nível de servidor permite que um aplicativo externo, como o SQL Server Management Studio ou o utilitário SQLCMD, se conecte ao banco de dados SQL através do firewall do serviço de Banco de Dados SQL. O exemplo a seguir cria uma regra de firewall para um intervalo de endereços predefinidos, que, neste exemplo, é intervalo inteiro possível de endereços IP. Substitua esses valores predefinidos pelos valores do endereço IP externo ou do intervalo de endereços IP. 

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "255.255.255.255"
```

## <a name="create-a-blank-database"></a>Criar um banco de dados vazio

Crie um Banco de Dados SQL vazio com um nível de desempenho S0 no servidor com o comando [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase). O exemplo a seguir cria um banco de dados chamado `mySampleDatabase`. Substitua esse valor predefinido conforme desejado.

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MySampleDatabase" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os recursos criados por este Início Rápido, execute o seguinte comando:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Próximas etapas

- Para conectar e consultar usando SQL Server Management Studio, veja [Conectar e consultar com o SSMS](sql-database-connect-query-ssms.md)
- Para se conectar usando o Visual Studio, veja [Conectar e consultar com o Visual Studio](sql-database-connect-query.md).
* Para obter uma visão geral técnica do banco de dados SQL, veja [Sobre o serviço Banco de Dados SQL](sql-database-technical-overview.md).

