---
title: 'Início Rápido: Criar um SQL Data Warehouse do Azure - Azure PowerShell| Microsoft Docs'
description: Criar rapidamente um servidor lógico do Banco de Dados SQL, uma regra de firewall de nível de servidor e data warehouse com o Azure PowerShell.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/01/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 72ed9e921d96faea155c1da88dd32fcbd467d549
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413993"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Início Rápido: Criar e consultar um SQL Data Warehouse do Azure com o Azure PowerShell

Crie rapidamente um SQL Data Warehouse do Azure usando o Azure PowerShell.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Este tutorial requer o módulo do Azure PowerShell, versão 5.1.1 ou posterior. Execute `Get-Module -ListAvailable AzureRM` descobrir a versão que você tem atualmente. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). 


> [!NOTE]
> A criação de um SQL Data Warehouse pode resultar em um novo serviço faturável.  Para obter mais informações, confira [Preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon em sua assinatura do Azure usando o comando [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) e siga as instruções na tela.

```powershell
Add-AzureRmAccount
```

Para ver qual assinatura que você está usando, execute [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Se você precisar usar uma assinatura diferente do padrão, execute [AzureRmSubscription selecione](/powershell/module/azurerm.profile/select-azurermsubscription).

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>Criar variáveis

Defina quais variáveis usar nos scripts neste início rápido.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (do not capitalize)
$servername = "server-$(Get-Random)"
# Set an admin login and password for your database
# The login information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usando o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westeurope`.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Criar um servidor lógico

Crie um [servidor lógico do SQL do Azure](../sql-database/sql-database-logical-servers.md) usando o comando [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver). Um servidor lógico contém um grupo de bancos de dados gerenciados conjuntamente. O exemplo a seguir cria um servidor nomeado aleatoriamente no seu grupo de recursos com logon de administrador `ServerAdmin` e senha `ChangeYourAdminPassword1`. Substitua esses valores predefinidos como desejado.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall de servidor

Crie uma [regra de firewall no nível do servidor do SQL do Azure](../sql-database/sql-database-firewall-configure.md) usando o comando [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule). Uma regra de firewall no nível de servidor permite que um aplicativo externo, como o SQL Server Management Studio ou o utilitário SQLCMD, se conecte a um SQL data warehouse através do firewall do serviço do SQL Data Warehouse. No exemplo a seguir, o firewall está aberto somente para os outros recursos do Azure. Para habilitar a conectividade externa, altere o endereço IP para um endereço apropriado para seu ambiente. Para abrir todos os endereços IP, use 0.0.0.0 como o endereço IP inicial e 255.255.255.255 como o endereço final.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Banco de Dados SQL e SQL Data Warehouse se comunicam pela porta 1433. Se você estiver tentando conectar-se a partir de uma rede corporativa, o tráfego de saída pela porta 1433 poderá não ser permitido pelo firewall de sua rede. Se isto acontecer, você não conseguirá se conectar ao servidor do SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>


## <a name="create-a-data-warehouse-with-sample-data"></a>Criar um data warehouse com dados de exemplo
Este exemplo cria um data warehouse usando as variáveis definidas anteriormente.  Especifica o objetivo de serviço como DW400, que é um ponto de partida de baixo custo para o data warehouse. 

```Powershell
New-AzureRmSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW400" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Os parâmetros requeridos são:

* **RequestedServiceObjectiveName**: A quantidade de [unidades de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) que você está solicitando. Aumentar esse valor aumenta os custos de computação. Para obter uma lista de valores com suporte, consulte [limites de memória e simultaneidade](memory-and-concurrency-limits.md).
* **DatabaseName**: o nome do SQL Data Warehouse que você está criando.
* **ServerName**: o nome do servidor que você está usando para a criação.
* **ResourceGroupName**: o grupo de recursos que você está usando. Para encontrar os grupos de recursos na sua assinatura, use Get-AzureResource.
* **Edition**: deve ser "DataWarehouse" para criar um SQL Data Warehouse.

Os parâmetros opcionais são:

- **CollationName**: o agrupamento padrão, se não especificado, é SQL_Latin1_General_CP1_CI_AS. O agrupamento não pode ser alterado em um banco de dados.
- **MaxSizeBytes**: o tamanho máximo padrão de um banco de dados é de 10 GB.

Para obter mais informações sobre as opções de parâmetro, consulte [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase).


## <a name="clean-up-resources"></a>Limpar recursos

Outros tutoriais nesta coleção aproveitam este início rápido. 

> [!TIP]
> Se você planeja continuar trabalhando com os tutoriais de início rápido subsequentes, não limpe os recursos criados neste início rápido. Caso contrário, siga estas etapas para excluir todos os recursos criados por esse início rápido no Portal do Azure.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Próximas etapas

Agora você criou um data warehouse, criou uma regra de firewall, conectou-se ao data warehouse e executou algumas consultas. Para saber mais sobre o SQL Data Warehouse do Azure, prossiga para o tutorial de carregamento de dados.
> [!div class="nextstepaction"]
>[Carregar dados no SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
