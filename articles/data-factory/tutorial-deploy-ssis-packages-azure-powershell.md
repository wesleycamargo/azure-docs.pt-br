---
title: Provisionar o tempo de execução de integração do Azure-SSIS com PowerShell | Microsoft Docs
description: Saiba como provisionar o tempo de execução de integração do Azure SSIS no Azure Data Factory com PowerShell para que você possa implantar e executar pacotes SSIS no Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.date: 10/28/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 956924714ba265cb14515208be0ebab3c5a458c1
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214504"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>Provisionar um Integration Runtime do Azure-SSIS no Azure Data Factory com PowerShell
Este tutorial fornece etapas para o provisionamento de um Integration Runtime (IR) do Azure-SSIS no Azure Data Factory. Em seguida, você pode usar o SSDT (Ferramentas de dados do SQL Server) ou o SSMS (SQL Server Management Studio) para implantar e executar os pacotes do SSIS (SQL Server Integration Services) neste tempo de execução no Azure. Neste tutorial, você executa as seguintes etapas:

> [!NOTE]
> Este artigo usa o Azure PowerShell para provisionar um IR do Azure SSIS. Para usar a interface do usuário (IU) do Data Factory para provisionar um IR do Azure SSIS, consulte [Tutorial: criar um tempo de execução de integração do Azure SSIS](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Criar um data factory.
> * Criar um Integration Runtime do Azure-SSIS
> * Iniciar o Integration Runtime do Azure-SSIS
> * Implantar pacotes do SSIS
> * Examinar o script completo

## <a name="prerequisites"></a>Pré-requisitos
- **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar. Para obter informações conceituais sobre o IR do SSIS do Azure, consulte [visão geral do Integration Runtime do Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). 
- **Servidor de banco de dados SQL do Azure**. Se você ainda não tiver um servidor de banco de dados, crie um no Portal do Azure antes de começar. Este servidor hospeda o SSISDB (banco de dados do Catálogo do SSIS). É recomendável que você crie o servidor de banco de dados na mesma região do Azure que a do Integration Runtime. Essa configuração permite que o Integration Runtime grave logs de execução SSISDB sem cruzar regiões do Azure. 
    - Com base no servidor de banco de dados selecionado, o SSISDB pode ser criado em seu nome como um banco de dados individual, parte de um pool elástico ou em uma Instância Gerenciada e acessível na rede pública ou ingressando em uma rede virtual. Para obter orientações sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, consulte [Comparar servidor lógico do Banco de Dados SQL e Instância Gerenciada](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-logical-server-and-sql-database-managed-instance). Se você usar o Banco de Dados SQL do Azure com pontos de extremidade de serviços de rede virtual/Instância Gerenciada para hospedar o SSISDB ou exigir acesso a dados locais, será necessário associar o seu Azure-SSIS IR a uma rede virtual, consulte [Criar Azure-SSIS IR em uma rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
    - Confirme se a configuração “**Permitir acesso aos serviços do Azure**” está **ATIVADA** para o servidor do banco de dados. Essa configuração não é aplicável quando você usa o Banco de Dados SQL do Azure com pontos de extremidade do serviço de rede virtual/Instância Gerenciada para hospedar o SSISDB. Para saber mais, confira [Proteger seu banco de dados SQL do Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Para habilitar essa configuração usando o PowerShell, veja [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1). 
    - Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclua o endereço IP do computador cliente à lista de endereços IP do cliente nas configurações do firewall para o servidor de banco de dados. Para saber mais, confira [Regras de firewall no nível do servidor e no nível do banco de dados do Banco de Dados SQL do Azure](../sql-database/sql-database-firewall-configure.md). 
    - Você pode se conectar ao servidor de banco de dados usando a autenticação do SQL com suas credenciais de administrador de servidor ou autenticação do AAD (Azure Active Directory) com a identidade gerenciada para seu Azure Data Factory.  Para o último, você precisa adicionar o ADF a um grupo do AAD com permissões de acesso para o servidor de banco de dados. Confira [Criar Azure-SSIS IR com a autenticação do AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
    - Confirme que o servidor de Banco de Dados SQL não tem um Catálogo SSIS (Banco de dados SSISDB). O provisionamento do IR do Azure-SSIS não oferece suporte ao uso de um Catálogo do SSIS existente. 
- **PowerShell do Azure**. Siga as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps). Você usa o PowerShell para executar um script para provisionar um Integration Runtime do Azure-SSIS que executa pacotes do SSIS na nuvem. 

> [!NOTE]
> - Para obter uma lista de regiões do Azure no qual o Data Factory e o Azure-SSIS Integration Runtime estão disponíveis no momento, veja [Disponibilidade por região do ADF + SSIS IR](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="launch-windows-powershell-ise"></a>Inicialização do ISE do Windows PowerShell
Inicie o **ISE do Windows PowerShell** com privilégios administrativos. 

## <a name="create-variables"></a>Criar variáveis
Copie e cole o script a seguir: especificar valores para as variáveis. Para obter uma lista de **tipos de preços** com suporte para o Banco de Dados SQL do Azure, veja [Limites de recursos de Banco de Dados SQL](../sql-database/sql-database-resource-limits.md).

```powershell
# Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[Azure subscription name]"
$ResourceGroupName = "[Azure resource group name]"
# Data factory name. Must be globally unique
$DataFactoryName = "[Data factory name]"
$DataFactoryLocation = "EastUS"

# Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[Specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[Specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported, but for other nodes, 1-8 are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>)]"
```

## <a name="validate-the-connection-to-database"></a>Validar a conexão ao banco de dados
Adicione o seguinte script para validar seu servidor de Banco de Dados SQL do Azure, `<servername>.database.windows.net`. 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

Para criar um banco de dados SQL do Azure como parte do script, veja o exemplo a seguir: 

Defina valores para as variáveis que ainda não foram definidas. Por exemplo: SSISDBServerName, FirewallIPAddress. 

```powershell
New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="log-in-and-select-subscription"></a>Fazer logon e selecionar a assinatura
Adicione o código a seguir ao script para fazer logon e selecionar sua assinatura do Azure: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usando o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westeurope`.

Se seu grupo de recursos já existir, não copie este código ao seu script. 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Criar uma data factory
Execute o comando a seguir para criar um data factory:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Criar um Integration Runtime
Execute o comando a seguir para criar um Integration Runtime do Azure-SSIS que executa os pacotes do SSIS no Azure: 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
  
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogAdminCredential $serverCreds `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

## <a name="start-integration-runtime"></a>Iniciar o Integration Runtime
Execute o comando a seguir para iniciar o Integration Runtime do Azure-SSIS: 

```powershell
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Esse comando o levará de **20 a 30 minutos** para concluir. 

## <a name="deploy-ssis-packages"></a>Implantar pacotes do SSIS
Agora, use o SSDT (SQL Server Data Tools) ou o SSMS (SQL Server Management Studio) para implantar seus pacotes SSIS no Azure. Conecte-se ao Azure SQL Server que hospeda o SSISDB (catálogo do SSIS). O nome do servidor do Banco de Dados SQL do Azure está no formato: `<servername>.database.windows.net`. 

Consulte os artigos a seguir na documentação do SSIS: 

- [Implantar, executar e monitorar um pacote do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Conecte-se ao catálogo do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Agendar a execução do pacote no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Conecte-se a fontes de dados locais com a autenticação do Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="full-script"></a>Script completo
O script do PowerShell nesta seção configura uma instância de Integration Runtime do Azure-SSIS na nuvem que executa os pacotes do SSIS. Depois de executar esse script com êxito, você pode implantar e executar pacotes do SSIS na nuvem do Microsoft Azure com o SSISDB hospedado no Banco de Dados SQL do Azure.

1. Inicie o ISE (ambiente de script integrado) do Windows PowerShell.
2. No ISE, execute o comando a seguir do prompt de comando.    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. Copie o script do PowerShell nesta seção e cole-o no ISE.
4. Forneça os valores adequados para todos os parâmetros no início do script.
5. Execute o script. O comando `Start-AzureRmDataFactoryV2IntegrationRuntime` próximo ao final do script é executado por **20 a 30 minutos**.

> [!NOTE]
> - O script se conecta ao servidor do Banco de Dados SQL do Azure para preparar o SSISDB (banco de dados do catálogo do SSIS).

> - Quando você provisiona uma instância do IR do Azure SSIS, o Azure Feature Pack para SSIS e o Acesso Redistribuível também são instalados. Esses componentes fornecem conectividade para arquivos do Excel e do Access e para várias fontes de dados do Azure, além das fontes de dados com suporte dos componentes internos. Também é possível instalar outros componentes. Para obter mais informações, confira [Configuração personalizada para o tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

Para obter uma lista de **tipos de preços** com suporte para o Banco de Dados SQL do Azure, veja [Limites de recursos de Banco de Dados SQL](../sql-database/sql-database-resource-limits.md). 

Para obter uma lista de regiões do Azure no qual o Data Factory e o Azure-SSIS Integration Runtime estão disponíveis no momento, veja [Disponibilidade por região do ADF + SSIS IR](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

```powershell
# Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[Azure subscription name]"
$ResourceGroupName = "[Azure resource group name]"
# Data factory name. Must be globally unique
$DataFactoryName = "[Data factory name]"
$DataFactoryLocation = "EastUS"

# Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[Specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[Specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported, but for other nodes, 1-8 are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>)]"

$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
    
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
    
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogAdminCredential $serverCreds `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="join-azure-ssis-ir-to-a-virtual-network"></a>Unir o IR do Azure-SSIS a uma rede virtual
Se você usa o Banco de Dados SQL do Azure com pontos de extremidade de serviço da rede virtual/Instância Gerenciada que se une a uma rede virtual para hospedar o SSISDB, você também deve unir seu tempo de execução de integração do Azure-SSIS Integration Runtime à mesma rede virtual. O Azure Data Factory permite que você una o tempo de execução de integração do Azure-SSIS a uma rede virtual. Para obter mais informações, consulte [Unir o tempo de execução de integração do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).

Para obter um script completo para criar um tempo de execução de integração do SSIS do Azure que se una a uma rede virtual, consulte [Criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md).

## <a name="monitor-and-manage-azure-ssis-ir"></a>Monitorar e gerenciar o IR do Azure-SSIS
Consulte os artigos a seguir para obter detalhes sobre como monitorar e gerenciar um IR do Azure-SSIS. 

- [Monitorar um tempo de execução de integração do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Gerenciar um tempo de execução de integração do Azure-SSIS](manage-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como: 

> [!div class="checklist"]
> * Criar um data factory.
> * Criar um Integration Runtime do Azure-SSIS
> * Iniciar o Integration Runtime do Azure-SSIS
> * Implantar pacotes do SSIS
> * Examinar o script completo

Para saber mais sobre como personalizar o tempo de execução de integração do Azure-SSIS, avance para o artigo a seguir:

> [!div class="nextstepaction"]
>[Personalizar o Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)
