---
title: Criar Azure-SSIS Integration Runtime no Azure Data Factory | Microsoft Docs
description: Aprenda a criar um Azure-SSIS Integration Runtime no Azure Data Factory para poder implantar e executar pacotes do SSIS no Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/26/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 4339782304f1bc175f1066954f1050bc00f25005
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434233"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Criar um Azure-SSIS Integration Runtime no Azure Data Factory
Este artigo fornece etapas para o provisionamento do Azure-SSIS Integration Runtime (IR) no Azure Data Factory (ADF). Em seguida, você pode usar o SSDT (SQL Server Data Tools) ou o SSMS (SQL Server Management Studio) para implantar e executar os pacotes do SSIS (SQL Server Integration Services) neste tempo de execução de integração no Azure. 

O [Tutorial: Implantar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md) mostra como criar um Azure-SSIS IR usando o servidor do Banco de Dados SQL do Azure para hospedar o banco de dados de catálogo do SSIS (SSISDB). Este artigo expande o tutorial e mostra como fazer o seguinte: 

- Como opção, use o servidor do Banco de Dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada para hospedar o SSISDB. Para obter orientações sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, consulte [Comparar servidor e Instância Gerenciada do Banco de Dados SQL do Azure](create-azure-ssis-integration-runtime.md#compare-sql-database-logical-server-and-sql-database-managed-instance). Como pré-requisito, você precisa ingressar seu Azure-SSIS IR a uma rede virtual e configurar permissões/configurações de rede virtual, conforme o necessário. Consulte [Unir o IR do Azure-SSIS a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

- Como opção, use a autenticação do AAD (Azure Active Directory) com a identidade gerenciada de seu ADF para se conectar ao servidor de banco de dados. Como pré-requisito, você precisará adicionar a identidade gerenciada de seu ADF como um usuário de banco de dados independente capaz de criar o SSISDB em seu servidor/Instância Gerenciada do Banco de Dados SQL do Azure. Consulte [Habilitar a autenticação do AAD para o Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

## <a name="overview"></a>Visão geral
Este artigo mostra diferentes maneiras de provisionar um Azure-SSIS IR: 

- [Portal do Azure](#azure-portal) 
- [PowerShell do Azure](#azure-powershell) 
- [Modelo do Azure Resource Manager](#azure-resource-manager-template) 

Quando você cria um Azure-SSIS IR, o serviço ADF se conecta ao seu servidor/Instância Gerenciada do Banco de Dados SQL do Azure para preparar o SSISDB. Ele também define permissões/configurações para sua rede virtual, se uma for especificada, e ingressa seu Azure-SSIS IR à rede virtual. 

Quando você provisiona o Azure SSIS IR, o Azure Feature Pack para SSIS e o Access Redistribuível também são instalados. Esses componentes fornecem conectividade para arquivos do Excel/Access e para várias fontes de dados do Azure, além das fontes de dados com suporte dos componentes internos. Também é possível instalar outros componentes. Para obter mais informações, confira [Configuração personalizada para o tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). 

## <a name="prerequisites"></a>Pré-requisitos 
- **Assinatura do Azure**. Se você ainda não tiver uma assinatura, crie uma conta de [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). 

- **Servidor do Banco de Dados SQL do Azure ou Instância Gerenciada**. Se você ainda não tiver um servidor de banco de dados, crie um no portal do Azure antes de começar. Esse servidor hospedará o SSISDB. Recomendamos que você crie o servidor de banco de dados na mesma região do Azure que a do Integration Runtime. Essa configuração permite que o Integration Runtime grave logs de execução no SSISDB sem cruzar as regiões do Azure. Com base no servidor de banco de dados selecionado, é possível criar o SSISDB em seu nome como um banco de dados individual, parte de um pool elástico ou em sua Instância Gerenciada, e disponibilizar o acesso na rede pública ou ao ingressar em uma rede virtual. Para obter uma lista de tipos de preços com suporte para o Banco de Dados SQL do Azure, veja [Limites de recursos de Banco de Dados SQL](../sql-database/sql-database-resource-limits.md). 

    Verifique se o seu servidor/Instância Gerenciada do Banco de Dados SQL do Azure ainda não tem um SSISDB. O provisionamento do Azure-SSIS IR não oferece suporte ao uso de um SSISDB existente. 

- **Rede virtual do Azure Resource Manager (opcional)**. Você precisa ter uma rede virtual do Azure Resource Manager se pelo menos uma das seguintes condições for verdadeira: 
    - Você está hospedando o SSISDB no Banco de Dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou Instância Gerenciada dentro de uma rede virtual. 
    - Você quer se conectar a armazenamentos de dados locais de pacotes SSIS em execução em seu Azure-SSIS IR. 

- **Azure PowerShell**. Siga as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps), se você quiser executar um script do PowerShell para provisionar o Azure-SSIS IR. 

### <a name="region-support"></a>Suporte de regiões
Para obter uma lista de regiões do Azure nas quais o ADF e o Azure-SSIS IR estão disponíveis, confira [Disponibilidade do ADF + SSIS IR por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

### <a name="compare-sql-database-logical-server-and-sql-database-managed-instance"></a>Comparar o servidor lógico do Banco de Dados SQL e a Instância Gerenciada do Banco de Dados SQL

A tabela abaixo compara alguns recursos do servidor do Banco de Dados SQL do Azure e da Instância Gerenciada com relação ao Azure-SSIR IR:

| Recurso | Servidor de banco de dados SQL do Azure| Instância Gerenciada |
|---------|--------------|------------------|
| **Agendamento** | O SQL Server Agent não está disponível.<br/><br/>Confira [Agendar a execução de um pacote no pipeline do ADF](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| O Agente da Instância Gerenciada está disponível. |
| **Autenticação** | Você pode criar o SSISDB com um usuário de banco de dados independente que representa qualquer grupo do AAD com a identidade gerenciada de seu ADF como membro na função **db_owner**.<br/><br/>Confira [Habilitar a autenticação do Azure AD para criar o SSISDB no servidor de Banco de Dados SQL do Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Você pode criar o SSISDB com um usuário de banco de dados independente que representa a identidade gerenciada de seu ADF. <br/><br/>Confira [Habilitar a autenticação do Azure AD para criar o SSISDB na Instância Gerenciada do Banco de Dados SQL do Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Camada de serviço** | Ao criar o Azure-SSIS IR no Banco de Dados SQL do Azure, você pode selecionar o tipo de serviço para o SSISDB. Há vários tipos de serviço. | Ao criar o Azure-SSIS IR com sua Instância Gerenciada, você não pode selecionar o tipo de serviço para SSISDB. Todos os bancos de dados em sua Instância Gerenciada compartilham o mesmo recurso alocado a essa instância. |
| **Rede virtual** | Dá suporte apenas a redes virtuais do Azure Resource Manager para ingresso de seu Azure-SSIS IR se você usar o servidor do Banco de Dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou precisar de acesso a armazenamentos de dados locais. | Dá suporte apenas para redes virtuais do Azure Resource Manager para ingresso de seu Azure-SSIS IR. A rede virtual é sempre necessária.<br/><br/>Se você associar seu Azure-SSIS IR à mesma rede virtual da sua Instância Gerenciada, verifique se o seu Azure-SSIS IR está em uma sub-rede diferente da sua Instância Gerenciada. Se você ingressar seu Azure-SSIS IR em uma rede virtual diferente da sua Instância Gerenciada, recomendamos um emparelhamento da rede virtual ou uma conexão entre redes virtuais. Consulte [Conecte seu aplicativo à Instância Gerenciada do Banco de Dados SQL do Azure](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Transações distribuídas** | Possui suporte por meio de Transações Elásticas. As transações do Microsoft Distributed Transaction Coordinator (MSDTC) não possuem suporte. Se os pacotes do SSIS usarem MSDTC para coordenar transações distribuídas, considere a migração para Transações Elásticas para o Banco de Dados SQL do Azure. Para obter mais informações, consulte [Transações distribuídas entre bancos de dados na nuvem](../sql-database/sql-database-elastic-transactions-overview.md). | Sem suporte. |
| | | |

## <a name="azure-portal"></a>Portal do Azure
Nesta seção, use o portal do Azure, especificamente a interface do usuário do ADF, para criar o Azure-SSIS IR. 

### <a name="create-a-data-factory"></a>Criar uma data factory 
1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome. 
1. Faça logon no [Portal do Azure](https://portal.azure.com/). 
1. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 

   ![Novo -> DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. Na página **Novo data factory**, insira **MyAzureSsisDataFactory** como o **nome**. 

   ![Página de novo data factory](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se você receber o erro a seguir, altere o nome do data factory (por exemplo, yournameMyAzureSsisDataFactory) e tente criar novamente. Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory. 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
1. Para o **Grupo de Recursos**, execute uma das seguintes etapas: 

   - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
   - Selecione **Criar novo**e insira o nome de um grupo de recursos. 

   Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md). 

1. Selecione **V2** para a **versão**. 
1. Selecione o **local** do data factory. Somente os locais com suporte para a criação de data factories são mostrados na lista. 
1. Selecione **Fixar no painel**. 
1. Clique em **Criar**. 
1. No painel, você deve ver o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem. 

    ![Página inicial do data factory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Clique em **Criar e Monitorar** para iniciar a interface do usuário (IU) do Azure Data Factory em uma guia separada. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Provisionar um tempo de execução de integração do Azure SSIS 
1. Na página de introdução, clique no bloco **Configurar o Integration Runtime do SSIS**. 

   ![Configurar o bloco do Integration Runtime SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Na página **Configurações Gerais** da **Instalação do Integration Runtime**, execute estas etapas: 

   ![Configurações gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. Para **Name**, insira o nome do seu tempo de execução de integração. 

    b. Para **Descrição**, insira a descrição do seu tempo de execução de integração. 

    c. Para **Local**, selecione o local do seu tempo de execução de integração. Somente os locais com suporte são exibidos. Recomendamos que você selecione o mesmo local do seu servidor de banco de dados para hospedar o SSISDB. 

    d. Para **Tamanho do Nó**, selecione o tamanho do nó no cluster de tempo de execução de integração. Apenas tamanhos de nós suportados são exibidos. Selecione um tamanho de nó grande (scale up), se você quiser executar muitos pacotes de computação / memória intensivos. 

    e. Para **Número do Nó**, selecione o número de nós em seu cluster de tempo de execução de integração. Somente os números de nós com suporte são exibidos. Selecione um grande cluster com muitos nós (scale out), se você deseja executar muitos pacotes em paralelo. 

    f. Para **Edition/License**, selecione a edição/licença do SQL Server para seu tempo de execução de integração: Standard ou Enterprise. Selecione Enterprise se você quiser usar os recursos avançados/premium em seu tempo de execução de integração. 

    g. Para **Economizar**, selecione a opção do Benefício Híbrido do Azure (AHB) para o tempo de execução de integração: Sim ou Não. Selecione Sim se você quiser colocar sua própria licença do SQL Server com o Software Assurance para aproveitar a redução de custos com o uso híbrido. 

    h. Clique em **Próximo**. 

1. Na página **Configurações de SQL**, execute as seguintes etapas: 

   ![Configurações do SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. Para **Assinatura**, selecione a assinatura do Azure que tem o servidor de banco de dados para hospedar o SSISDB. 

    b. Para **Local**, selecione o local do seu servidor de banco de dados para hospedar o SSISDB. É recomendável que você selecione o mesmo local do seu tempo de execução de integração. 

    c. Para **Ponto de extremidade de servidor de banco de dados de catálogo**, selecione o ponto de extremidade do seu servidor de banco de dados para hospedar o SSISDB. Com base no servidor de banco de dados selecionado, o SSISDB pode ser criado em seu nome como um banco de dados individual, parte de um pool elástico ou em uma Instância Gerenciada e acessível na rede pública ou ingressando em uma rede virtual. 

    d. Na caixa de seleção **Usar autenticação do AAD...**, selecione o método de autenticação para o servidor de banco de dados para hospedar o SSISDB: SQL ou do AAD (Azure Active Directory) com a identidade gerenciada do seu Azure Data Factory. Se marcá-la, você precisará adicionar a identidade gerenciada do ADF a um grupo do AAD com permissões de acesso ao servidor de banco de dados; consulte [Habilitar autenticação do AAD para o Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    e. Para **Nome de usuário do administrador**, insira o nome de usuário de autenticação do SQL para o servidor de banco de dados para hospedar o SSISDB. 

    f. Para **Nome de usuário do administrador**, insira a senha de autenticação do SQL para o servidor de banco de dados para hospedar o SSISDB. 

    g. Para **Nível do Serviço de Banco de Dados de Catálogo**, selecione o tipo de serviço para o seu servidor de banco de dados hospedar SSISDB: Tipo Basic/Standard/Premium ou o nome do pool elástico. 

    h. Clique em **Conexão de teste** e, se tiver êxito, clique em **Próximo**. 

1.  Na página **Configurações avançadas**, conclua as etapas a seguir: 

    ![Configurações avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. Para **Execuções paralelas máximas por nó**, selecione o número máximo de pacotes para executar simultaneamente por nó no cluster de tempo de execução de integração. Apenas os números de pacote suportados são exibidos. Selecione um número baixo, se você quiser usar mais de um núcleo para executar um único pacote grande / pesado que seja de computação / memória -intensivo. Selecione um número alto, se você quiser executar um ou mais pacotes pequenos / leves em um único núcleo. 

    b. Para o  **Custom Storage Container SAS URI**, insira opcionalmente o Uniform Resource Identifier (URI) de Assinatura de Acesso Compartilhado (SAS) do seu contêiner Azob Storage Blob onde seu script de instalação e seus arquivos associados estão armazenados, consulte [ Configuração personalizada para o Azure-SSIS IR ](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

1. Na caixa de seleção **Selecionar uma rede virtual ...**, selecione se você deseja unir seu tempo de execução de integração a uma rede virtual. Verifique se você usa o Banco de Dados SQL do Azure com endpoints de serviço de rede virtual / Instância Gerenciada para hospedar o SSISDB ou exigir acesso a dados locais; ou seja, você tem fontes / destinos de dados locais em seus pacotes do SSIS, consulte [Unir o Azure-SSIS IR a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se você verificar, conclua as etapas a seguir: 

   ![Configurações avançadas com rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

     a. Para **Assinatura**, selecione a assinatura do Azure que possui sua rede virtual. 

    b. Para **Local**, o mesmo local do seu tempo de execução de integração é selecionado. 

    c. Para **Tipo**, selecione o tipo da sua rede virtual: Clássico ou Azure Resource Manager. Recomendamos que você selecione a rede virtual do Azure Resource Manager, pois a rede virtual clássica será descontinuada em breve. 

    d. Para **Nome da VNet**, selecione o nome da sua rede virtual. Essa rede virtual deve ser a mesma rede virtual usada para o Banco de Dados SQL do Azure com pontos de extremidade de serviço de rede virtual / Instância Gerenciada para hospedar o SSISDB e / ou o conectado à sua rede local. 

    e. Para **Nome da sub-rede**, selecione o nome da sub-rede da sua rede virtual. Essa deve ser uma sub-rede diferente daquela usada para a Instância Gerenciada para hospedar o SSISDB. 

1. Clique em **VNet Validation** e, se tiver êxito, clique em **Finish** para iniciar a criação do seu tempo de execução de integração do Azure-SSIS. 

    > [!IMPORTANT]
    > - Este processo leva aproximadamente de 20 a 30 minutos para ser concluído.
    > - O serviço Data Factory se conecta ao Banco de Dados SQL do Azure para preparar o SSISDB (banco de dados do catálogo do SSIS). Ele também define permissões e configurações para sua rede virtual, se especificada, e une a nova instância do Azure-SSIS Integration Runtime à rede virtual. 

1. Na janela **Conexões**, alterne para **Tempos de Execução de Integração**, se necessário. Clique em **Atualizar** para atualizar o status. 

   ![Status de criação](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Use os links na coluna **Ações** para parar/iniciar, editar ou excluir o tempo de execução de integração. Use o último link para exibir o código JSON para o tempo de execução de integração. Os botões editar e excluir são habilitados somente quando o IR é interrompido. 

   ![IR do Azure SSIS - ações](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Tempos de execução de integração do Azure SSIS no portal
1. Na interface do usuário do Azure Data Factory, alterne para a guia **Editar**, clique em **Conexões** e, em seguida, alterne para a guia **Tempos de Execução de Integração** para exibir os tempos de execução de integração existentes no data factory. 

   ![Exibir IRs existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Clique em **Novo** para criar um novo IR do Azure SSIS. 

   ![Tempo de execução de integração por meio do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Para criar um tempo de execução de integração do Azure SSIS, clique em **Novo** conforme mostrado na imagem. 
1. Na janela Instalação do Integration Runtime, selecione **Pacotes SSIS existentes de lift-and-shift para execução no Azure** e, em seguida, clique em **Avançar**. 

   ![Especificar o tipo de tempo de execução de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Consulte a seção [Provisionar um tempo de execução de integração do Azure SSIS](#provision-an-azure-ssis-integration-runtime) para o restante das etapas para configurar um IR do Azure-SSIS. 

## <a name="azure-powershell"></a>Azure PowerShell
Nesta seção, você utiliza o Azure PowerShell para criar um IR do Azure-SSIS.

### <a name="create-variables"></a>Criar variáveis
Defina as variáveis para usar no script neste tutorial:

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS" 
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

### <a name="log-in-and-select-subscription"></a>Fazer logon e selecionar a assinatura
Adicione o código a seguir ao script para fazer logon e selecionar sua assinatura do Azure: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Validar a conexão ao banco de dados
Adicione o script a seguir para validar o ponto de extremidade do servidor do Banco de Dados SQL do Azure. 

```powershell
# Validate only when you do not use VNet nor AAD authentication
if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
{
    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
    {
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
    }
}
```

### <a name="configure-virtual-network"></a>Configurar rede virtual
Adicione o script a seguir para configurar automaticamente as permissões/configurações de rede virtual para o ingresso do seu Integration Runtime do Azure-SSIS.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usando o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo. 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Criar uma data factory
Execute o comando a seguir para criar um data factory.

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Criar um Integration Runtime
Execute os seguintes comandos para criar um tempo de execução de integração do Azure-SSIS que executa pacotes do SSIS no Azure. 

Se você não usar o Banco de Dados SQL do Azure com pontos de extremidade de serviço de rede virtual / Instância Gerenciada para hospedar o SSISDB nem exigir acesso a dados locais, poderá omitir os parâmetros VNetId e Subnet ou passar valores vazios para eles. Caso contrário, você não poderá omiti-los e deverá passar os valores válidos de sua configuração de rede virtual, consulte [ Unir o Azure-SSIS IR a uma rede virtual ](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

Se você usar a instância gerenciada para hospedar SSISDB, você pode omitir o parâmetro CatalogPricingTier ou passar um valor vazio para ele. Caso contrário, você não poderá omiti-lo e deverá passar um valor válido na lista de níveis de preços suportados para o Banco de Dados SQL do Azure, consulte [ Limites de recursos do Banco de Dados SQL ](../sql-database/sql-database-resource-limits.md). 

Se usar a autenticação do AAD (Azure Active Directory) com a identidade gerenciada do Azure Data Factory para se conectar ao servidor de banco de dados, você poderá omitir o parâmetro CatalogAdminCredential, mas precisará adicionar a identidade gerenciada de seu ADF a um grupo do AAD com permissões de acesso o servidor de banco de dados; consulte [Habilitar a autenticação do AAD para o Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Caso contrário, você não pode emiti-lo ou deve passar um objeto válido formado pelo nome de usuário e senha de administrador de servidor para autenticação SQL.

```powershell               
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
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

# Add CatalogAdminCredential parameter when you do not use AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}
```

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
Aqui está o script completo que cria um tempo de execução de integração do Azure-SSIS. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS" 
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"

### Log in and select subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database
# Validate only when you do not use VNet nor AAD authentication
if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
{
    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
    {
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
    }
}

### Configure virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
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
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

# Add CatalogAdminCredential parameter when you do not use AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}

### Start integration runtime   
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager
Nesta seção, você usa o modelo do Azure Resource Manager para criar o tempo de execução de integração do Azure-SSIS. Aqui é apresentado um passo a passo de exemplo: 

1. Crie um arquivo JSON com o seguinte modelo do Azure Resource Manager. Substitua valores nos colchetes angulados (espaços reservados) com seus próprios valores. 

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
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
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
    
1. Para implantar o modelo do Azure Resource Manager, execute o comando New-AzureRmResourceGroupDeployment, conforme mostrado no exemplo a seguir, em que ADFTutorialResourceGroup é o nome do seu grupo de recursos e ADFTutorialARM.json é o arquivo que contém a definição JSON do data factory e do Azure-SSIS. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Esse comando cria sua fábrica de dados e o IR do Azure-SSIS, mas não inicia o IR. 

1. Para iniciar seu IR do Azure-SSIS, execute o comando Start-AzureRmDataFactoryV2IntegrationRuntime: 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ``` 

## <a name="deploy-ssis-packages"></a>Implantar pacotes do SSIS
Agora, use o SSDT (SQL Server Data Tools) ou o SSMS (SQL Server Management Studio) para implantar seus pacotes SSIS no Azure. Conecte-se ao seu servidor de banco de dados que hospeda o catálogo SSIS (SSISDB). O nome do servidor de banco de dados está no formato: &lt;Nome do servidor do Banco de Dados SQL do Azure&gt;.database.windows.net ou &lt;Nome da Instância Gerenciada.prefixo DNS&gt;.database.windows.net. Consulte o artigo [Implantar pacotes](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) para obter instruções. 

## <a name="next-steps"></a>Próximas etapas
Consulte os outros tópicos de IR do Azure-SSIS nesta documentação: 

- [Tempo de execução de integração do Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais sobre tempos de execução de integração em geral, incluindo o IR do Azure-SSIS. 
- [Tutorial: implantar pacotes do SSIS para o Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo fornece instruções passo a passo para criar um IR do Azure-SSIS e usa um banco de dados SQL do Azure para hospedar o catálogo do SSIS. 
- [Monitore um IR do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como recuperar informações sobre um IR do Azure-SSIS e descrições de status nas informações retornadas. 
- [Gerencie um IR do Azure-SSIS](manage-azure-ssis-integration-runtime.md). Este artigo mostra como parar, iniciar ou remover um IR do Azure-SSIS. Ele também mostra como expandir o IR do Azure-SSIS adicionando mais nós ao IR. 
- [Unir um IR do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo fornece informações conceituais sobre como associar seu IV do Azure-SSIS a uma rede virtual do Azure. Ele também apresenta as etapas para usar o portal do Azure para configurar a rede virtual para que o IR do Azure-SSIS possa unir-se à rede virtual. 
