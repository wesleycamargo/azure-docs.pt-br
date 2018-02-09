---
title: "Criar tempo de execução de integração do Azure-SSIS no Azure Data Factory | Microsoft Docs"
description: "Saiba como criar um tempo de execução de integração do Azure-SSIS para poder executar o pacote SSIS na nuvem do Azure."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: spelluru
ms.openlocfilehash: bb63a3d882d50f509fff220d3eb2c1eb6bf0d70f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Criar um Integration Runtime do Azure-SSIS no Azure Data Factory
Este artigo fornece etapas para o provisionamento de um tempo de execução de integração do Azure-SSIS no Azure Data Factory. Em seguida, você pode usar o SSDT (SQL Server Data Tools) ou o SSMS (SQL Server Management Studio) para implantar pacotes do SSIS (SQL Server Integration Services) para esse tempo de execução no Azure.

O tutorial: [Tutorial: implantar pacotes do SQL Server Integration Services (SSIS) no Azure](tutorial-create-azure-ssis-runtime-portal.md) mostra como criar um IR (Integration Runtime) do Azure-SSIS usando o Banco de Dados SQL do Microsoft Azure como o repositório para o catálogo do SSIS. Este artigo expande o tutorial e mostra como fazer o seguinte: 

- Use a Instância Gerenciada SQL do Azure (visualização particular) para hospedar um catálogo do SSIS (banco de dados SSISDB).
- Una o IR do Azure-SSIS a uma rede virtual do Azure (VNet). Consulte informações conceituais sobre como ingressar em um IR do Azure-SSIS para uma VNet e configurar uma VNet no portal do Azure em [Ingressar no IR do Azure-SSIS para VNet](join-azure-ssis-integration-runtime-virtual-network.md). 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira a [documentação do Data Factory versão 1](v1/data-factory-introduction.md).


## <a name="overview"></a>Visão geral
Este artigo mostra diferentes maneiras de provisionar um IR do Azure-SSIS:

- [portal do Azure](#azure-portal)
- [PowerShell do Azure](#azure-powershell)
- [Modelo do Azure Resource Manager](#azure-resource-manager-template)

Ao criar um IR do Azure-SSIS, o Data Factory conecta-se ao Banco de Dados SQL do Microsoft Azure para preparar o banco de dados do catálogo do SSIS (SSISDB). O script também configura permissões e configurações para sua VNet, se especificadas, e ingressa a nova instância de Integration Runtime do Azure-SSIS na VNet.

Quando você provisiona uma instância do IR do Azure SSIS, o Azure Feature Pack para SSIS e o Acesso Redistribuível também são instalados. Esses componentes fornecem conectividade para arquivos do Excel e do Access e para várias fontes de dados do Azure, além das fontes de dados com suporte dos componentes internos. Neste momento, não é possível instalar componentes de terceiros para SSIS (incluindo componentes de terceiros da Microsoft, como os componentes do Oracle e Teradata da Attunity e os componentes SAP BI).

## <a name="prerequisites"></a>pré-requisitos

- **Assinatura do Azure**. Se você não tiver uma assinatura, poderá criar uma conta de [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).
- **Servidor de Banco de Dados SQL do Azure** ou **Instância Gerenciada do SQL Server (versão prévia privada) (Versão Prévia Privada Estendida)**. Se você ainda não tiver um servidor de banco de dados, crie um no Portal do Azure antes de começar. Este servidor hospeda o SSISDB (banco de dados do Catálogo do SSIS). É recomendável que você crie o servidor de banco de dados na mesma região do Azure que a do Integration Runtime. Essa configuração permite que o Integration Runtime grave logs de execução SSISDB sem cruzar regiões do Azure. Anote o tipo de preço do SQL Server do Azure. Para obter uma lista de tipos de preços com suporte para o Banco de Dados SQL do Azure, veja [Limites de recursos de Banco de Dados SQL](../sql-database/sql-database-resource-limits.md).

    Confirme se o servidor do Banco de Dados SQL do Microsoft Azure ou a instância gerenciada do SQL Server (versão prévia privada estendida) não possui um catálogo do SSIS (banco de dados SSIDB). O provisionamento do IR do Azure-SSIS não oferece suporte ao uso de um Catálogo do SSIS existente.
- **VNet (Rede Virtual) do Azure Resource Manager ou clássica (opcional)**. Você deverá ter uma VNet (Rede Virtual) do Azure se pelo menos uma das seguintes condições for verdadeira:
    - Você hospeda o banco de dados de Catálogo do SSIS em uma Instância Gerenciada do SQL Server (versão prévia privada) que faz parte de uma VNet.
    - Você deseja se conectar a armazenamentos de dados locais de pacotes SSIS em execução em um tempo de execução de integração do Azure-SSIS.
- **PowerShell do Azure**. Siga as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps). Você usa o PowerShell para executar um script para provisionar um Integration Runtime do Azure-SSIS que executa pacotes do SSIS na nuvem. 

> [!NOTE]
> Para obter uma lista de regiões com suporte pelo Azure Data Factory V2 e pelo Integration Runtime do Azure-SSIS, consulte [Products available by region](https://azure.microsoft.com/regions/services/) (Produtos disponíveis por região). Expanda **Dados + Análise** para ver **Data Factory V2** e **Integration Runtime do SSIS**.

## <a name="azure-portal"></a>Portal do Azure
Nesta seção, você utiliza o Portal do Azure, especificamente a interface do usuário do Data Factory, para criar um IR do Azure-SSIS. 

### <a name="create-a-data-factory"></a>Criar uma data factory

1. Faça logon no [Portal do Azure](https://portal.azure.com/).    
2. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Na página **Novo data factory**, insira **MyAzureSsisDataFactory** como o **nome**. 
      
     ![Página de novo data factory](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se você receber o erro a seguir, altere o nome do data factory (por exemplo, yournameMyAzureSsisDataFactory) e tente criar novamente. Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
      - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
      Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2 (Versão Prévia)** para a **versão**.
5. Selecione o **local** do data factory. Somente os locais com suporte para a criação de data factories são mostrados na lista.
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.
8. No painel, você vê o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
   ![Página inicial da data factory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Clique em **Criar e Monitorar** para iniciar a interface do usuário (IU) do Azure Data Factory em uma guia separada. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Provisionar um tempo de execução de integração do Azure SSIS

1. Na página de introdução, clique no bloco **Configurar o Integration Runtime do SSIS**. 

   ![Configurar o bloco do Integration Runtime SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Na página **Configurações Gerais** da **Instalação do Integration Runtime**, siga estas etapas: 

   ![Configurações gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. Especifique um **nome** para o tempo de execução de integração.
    2. Selecione o **local** para o tempo de execução de integração. Somente os locais com suporte são exibidos.
    3. Selecione o **tamanho do nó** que é configurado com o tempo de execução do SSIS.
    4. Especifique o **número de nós** no cluster.
    5. Clique em **Próximo**. 
1. Nas **Configurações do SQL**, siga estas etapas: 

    ![Configurações do SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Especifique a **assinatura** do Azure que tem o servidor SQL do Azure. 
    2. Selecione o servidor SQL do Azure para o **Ponto de Extremidade de Servidor de Banco de Dados de Catálogo**.
    3. Insira o nome de usuário **administrador**.
    4. Insira a **senha** do administrador.  
    5. Selecione a **camada de serviço** para o banco de dados SSISDB. O valor padrão é Básico.
    6. Clique em **Próximo**. 
1.  Na página **Configurações Avançadas**, selecione um valor para **Máximo de Execuções em Paralelo por Nó**.   

    ![Configurações avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Esta etapa é **opcional**. Se você tiver uma VNet (Clássica ou Azure Resource Manager) que deseja que o tempo de execução de integração ingresse, escolha a opção **Selecionar uma rede virtual para o tempo de execução de integração do Azure SSIS ingressar e permitir que os serviços do Azure configurem permissões/configurações de rede virtual** e, em seguida, siga as etapas a seguir: 

    ![Configurações avançadas com rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. Para **Assinatura**, especifique a **assinatura** que tem a VNet. 
    2. Para tipo, especifique o **tipo** da VNet (rede virtual clássica ou rede virtual do Azure Resource Manager). 
    3. Para **Nome da VNet**, selecione o nome da **VNet**. 
    4. Para **Nome da Sub-rede**, selecione o nome da **sub-rede** na VNet.
1. Clique em **Concluir** para iniciar a criação do tempo de execução de integração do Azure SSIS. 

    > [!IMPORTANT]
    > - Esse processo leva aproximadamente 20 minutos para ser concluída
    > - O serviço Data Factory se conecta ao Banco de Dados SQL do Azure para preparar o SSISDB (banco de dados do catálogo do SSIS). O script também configura permissões e configurações para sua VNet, se especificadas, e ingressa a nova instância de Integration Runtime do Azure-SSIS na VNet.
7. Na janela **Conexões**, alterne para **Tempos de Execução de Integração**, se necessário. Clique em **Atualizar** para atualizar o status. 

    ![Status de criação](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Use os links na coluna **Ações** para parar/iniciar, editar ou excluir o tempo de execução de integração. Use o último link para exibir o código JSON para o tempo de execução de integração. Os botões editar e excluir são habilitados somente quando o IR é interrompido. 

    ![IR do Azure SSIS - ações](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Tempos de execução de integração do Azure SSIS no portal

1. Na interface do usuário do Azure Data Factory, alterne para a guia **Editar**, clique em **Conexões** e, em seguida, alterne para a guia **Tempos de Execução de Integração** para exibir os tempos de execução de integração existentes no data factory. 
    ![Exibir IRs existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Clique em **Novo** para criar um novo IR do Azure SSIS. 

    ![Tempo de execução de integração por meio do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Para criar um tempo de execução de integração do Azure SSIS, clique em **Novo** conforme mostrado na imagem. 
3. Na janela Instalação do Integration Runtime, selecione **Pacotes SSIS existentes de lift-and-shift para execução no Azure** e, em seguida, clique em **Avançar**.

    ![Especificar o tipo de tempo de execução de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Consulte a seção [Provisionar um tempo de execução de integração do Azure SSIS](#provision-an-azure-ssis-integration-runtime) para o restante das etapas para configurar um IR do Azure-SSIS.

## <a name="azure-powershell"></a>Azure PowerShell
Nesta seção, você utiliza o Azure PowerShell para criar um IR do Azure-SSIS.

### <a name="create-variables"></a>Criar variáveis
Defina as variáveis para usar no script neste tutorial:

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet). 
$VnetId = "[your VNet resource ID or leave it empty]" 
$SubnetName = "[your subnet name or leave it empty]" 

```
### <a name="log-in-and-select-subscription"></a>Fazer logon e selecionar a assinatura
Adicione o código a seguir ao script para fazer logon e selecionar sua assinatura do Azure: 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Validar a conexão ao banco de dados
Adicione o script a seguir para validar seu servidor de Banco de Dados SQL do Azure server.database.windows.net ou seu ponto de extremidade de servidor de Instância Gerenciada do SQL do Azure (versão prévia privada). 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

### <a name="configure-virtual-network"></a>Configurar rede virtual
Adicione o script a seguir para configurar automaticamente as permissões/configurações de rede virtual para o ingresso do seu Integration Runtime do Azure-SSIS.

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usando o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westeurope`.

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Criar uma data factory
Execute o comando a seguir para criar um data factory:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Criar um Integration Runtime
Execute o seguinte comando para criar um tempo de execução de integração do Azure-SSIS que execute os pacotes do SSIS no Azure: Use o script da seção de acordo com o tipo de banco de dados (banco de dados SQL do Azure vs. Instância Gerenciada do SQL do Azure (visualização particular)) que você está usando. 

#### <a name="azure-sql-database-to-host-the-ssisdb-database-ssis-catalog"></a>Banco de Dados SQL do Azure para hospedar o banco de dados do SSISDB (catálogo do SSIS) 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode
```

Você não precisa passar valores para VNetId e sub-rede, a menos que você precise de acesso a dados no local, ou seja, que você tenha fontes de dados/destinos em seus pacotes SSIS. Você deve passar o valor para o parâmetro CatalogPricingTier. Para obter uma lista de tipos de preços com suporte para o Banco de Dados SQL do Azure, veja [Limites de recursos de Banco de Dados SQL](../sql-database/sql-database-resource-limits.md).

#### <a name="azure-sql-managed-instance-private-preview-to-host-the-ssisdb-database"></a>Instância Gerenciada do SQL do Azure (visualização particular) para hospedar o banco de dados do SSISDB

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

Você precisa passar valores para parâmetros VnetId e de sub-rede com a Instância Gerenciada do Banco de Dados SQL do Azure (visualização particular) que associa uma VNet. O parâmetro CatalogPricingTier não se aplica à Instância Gerenciada do Banco de Dados SQL do Azure. 

### <a name="start-integration-runtime"></a>Iniciar o Integration Runtime
Execute o comando a seguir para iniciar o Integration Runtime do Azure-SSIS: 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Esse comando o levará de **20 a 30 minutos** para concluir. 


### <a name="full-script"></a>Script completo
Encontre aqui o script completo que cria um IR Azure-SSIS e o une a uma VNet. Esse script presume que você está usando a Instância Gerenciada do SQL do Azure (MI) para hospedar o catálogo do SSIS. 

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS (IR)>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 

$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName

$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager
Nesta seção, você usa um modelo do Azure Resource Manager para criar um tempo de execução de integração do Azure-SSIS. Aqui é apresentado um passo a passo de exemplo: 

1. Crie um arquivo JSON com o seguinte modelo do Resource Manager. Substitua valores nos colchetes angulados (espaços reservados) com seus próprios valores. 

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2017-09-01-preview",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for the Azure SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D1_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 1
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL server>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL user",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```
2. Para implantar o modelo do Resource Manager, execute o comando New-AzureRmResourceGroupDeployment como mostrado no seguinte exemplo. Neste exemplo, ADFTutorialResourceGroup é o nome do grupo de recursos. ADFTutorialARM.json é o arquivo que contém a definição JSON para o data factory e o IR do Azure-SSIS. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Este comando cria o data factory e cria um IR do Azure-SSIS nele, mas não inicia o IR. 
3. Para iniciar o IR do Azure-SSIS, execute o comando Start-AzureRmDataFactoryV2IntegrationRuntime: 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name> `
                                             -DataFactoryName <Data Factory Name> `
                                             -Name <Azure SSIS IR Name> `
                                             -Force
    ``` 

## <a name="deploy-ssis-packages"></a>Implantar pacotes do SSIS
Agora, use o SSDT (SQL Server Data Tools) ou o SSMS (SQL Server Management Studio) para implantar seus pacotes SSIS no Azure. Conecte-se ao Azure SQL Server que hospeda o SSISDB (catálogo do SSIS). O nome do SQL Server do Azure está no formato: &lt;servername&gt;.database.windows.net (para o Banco de Dados SQL do Azure). Consulte o artigo [Implantar pacotes](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) para obter instruções. 

## <a name="next-steps"></a>Próximas etapas
Consulte os outros tópicos de IR do Azure-SSIS nesta documentação:

- [Tempo de execução de integração do Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais sobre tempos de execução de integração em geral, incluindo o IR do Azure-SSIS. 
- [Tutorial: implantar pacotes do SSIS para o Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo fornece instruções passo a passo para criar um IR do Azure-SSIS e usa um banco de dados SQL do Azure para hospedar o catálogo do SSIS. 
- [Monitore um IR do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como recuperar informações sobre um IR do Azure-SSIS e descrições de status nas informações retornadas. 
- [Gerencie um IR do Azure-SSIS](manage-azure-ssis-integration-runtime.md). Este artigo mostra como parar, iniciar ou remover um IR do Azure-SSIS. Ele também mostra como expandir o IR do Azure-SSIS adicionando mais nós ao IR. 
- [Unir um IR do Azure-SSIS a uma VNet](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo fornece informações conceituais sobre como unir um IR do Azure-SSIS a uma rede virtual (VNet) do Azure. Ele também apresenta as etapas para usar o portal do Azure para configurar a VNet para que o IR do Azure-SSIS possa unir-se à VNet. 