---
title: Copiar dados do SQL Server para o Armazenamento de Blobs com o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados de um armazenamento de dados local para a nuvem do Azure usando o Integration Runtime auto-hospedado no Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: afd7735712d03110a67509a7e94d336219a65b34
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="tutorial-copy-data-from-on-premises-sql-server-to-azure-blob-storage"></a>Tutorial: Copiar dados do SQL Server local para o Armazenamento de Blobs do Azure
Neste tutorial, você usa o Azure PowerShell para criar um pipeline do Data Factory que copia dados de um Banco de Dados do SQL Server local para um Armazenamento de Blobs do Azure. Você cria e usa um Integration Runtime (IR) auto-hospedado do Azure Data Factory/ Ele permite a integração de armazenamentos de dados locais e armazenamentos de dados na nuvem.  Para obter informações sobre como usar outras ferramentas/SDKs para criar um data factory, consulte [Guias de início rápido](quickstart-create-data-factory-dot-net.md).

Este artigo não fornece uma introdução detalhada do serviço Data Factory. Para obter uma introdução do serviço do Azure Data Factory, consulte [Introdução ao Azure Data Factory](introduction.md). 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira a [documentação do Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Crie um Integration Runtime auto-hospedado.
> * Criar serviços vinculados do SQL Server e do Armazenamento do Azure. 
> * Criar conjuntos de dados do SQL Server e de Blobs do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Inicie uma execução de pipeline.
> * Monitore a execução de pipeline.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="sql-server-2014-or-2016"></a>SQL Server 2014 ou 2016. 
Neste tutorial, você utiliza um Banco de Dados do SQL Server local como um armazenamento de dados de **origem**. Crie uma tabela chamada **emp** no seu banco de dados do SQL Server e insira alguns exemplos de entradas na tabela.

1. Inicie o **SQL Server Management Studio**. Se você estiver usando o SQL Server 2016, talvez você precise instalar o SQL Server Management Studio separadamente a partir do [Centro de download](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). 
2. Conecte-se ao SQL Server usando suas credenciais. 
3. Criar um banco de dados de exemplo. No modo de exibição de árvore, clique com o botão direito do mouse em **Bancos de Dados** e clique em **Novo Banco de Dados**. Na caixa de diálogo **Novo Banco de Dados**, digite um **nome** para o banco de dados e clique em **OK**. 
4. Execute o seguinte script de consulta no banco de dados, que cria a tabela **emp**. No modo de exibição de árvore, clique com o botão direito do mouse no **banco de dados** que você criou e clique em **Nova consulta**. 

    ```sql   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Execute os seguintes comandos no banco de dados que insere alguns dados de exemplo na tabela:

    ```sql
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="azure-storage-account"></a>Conta de Armazenamento do Azure
Use uma conta de Armazenamento do Azure para fins gerais (especificamente o Armazenamento de Blobs) como repositório de dados de **destino/coletor** neste tutorial. Se você não tiver uma conta de fins gerais de armazenamento do Azure, consulte [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para saber sobre como criar uma.

#### <a name="get-storage-account-name-and-account-key"></a>Obter o nome da conta de armazenamento e a chave da conta
Você usa o nome e a chave do nome da sua conta de armazenamento do Azure neste início rápido. O procedimento a seguir fornece as etapas para obter o nome e a chave da sua conta de armazenamento. 

1. Abra um navegador da Web e navegue até o [portal do Azure](https://portal.azure.com). Faça logon usando seu nome de usuário e senha do Azure. 
2. Clique em **Mais serviços >** no menu à esquerda, filtre pela palavra-chave **Armazenamento** e selecione **Contas de armazenamento**.

    ![Pesquisar conta de armazenamento](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. Na lista de contas de armazenamento, filtre pela sua conta de armazenamento (se necessário) e, em seguida, selecione a **sua conta de armazenamento**. 
4. Na página da **Conta de armazenamento**, selecione **Chaves de acesso** no menu.

    ![Obter nome e chave da conta de armazenamento](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. Copie os valores dos campos **Nome da conta de armazenamento** e **key1** para a área de transferência. Cole-os em um bloco de notas ou qualquer outro editor e salve-os. Use o nome e a chave da conta de armazenamento no tutorial. 

#### <a name="create-the-adftutorial-container"></a>Criar o contêiner adftutorial 
Nesta seção, você cria um contêiner de blob chamado: adftutorial no Armazenamento de Blobs do Azure. 

1. Instale o [Gerenciador de Armazenamento do Azure Storage](https://azure.microsoft.com/features/storage-explorer/) se você não o tiver no seu computador. 
2. Inicie o **Gerenciador de Armazenamento do Microsoft Azure** na sua máquina.   
3. Na janela **Conectar-se ao Armazenamento do Azure**, selecione **Usar uma chave e nome de conta de armazenamento** e clique em **Avançar**. Se você não vir a janela **Conectar-se ao Armazenamento do Azure**, clique com o botão direito do mouse em **Contas de armazenamento** no modo de exibição de árvore e clique em **Conectar-se ao Armazenamento do Azure**. 

    ![Conectar-se ao armazenamento do Azure](media/tutorial-hybrid-copy-powershell/storage-explorer-connect-azure-storage.png)
4. Na janela **Conectar-se usando Nome e Chave**, cole o **Nome da conta** e a **Chave da conta** que você salvou na etapa anterior. Em seguida, clique em **Avançar**. 
5. Na janela **Resumo da conexão**, clique em **Conectar**.
6. Confirme que você vê sua conta de armazenamento na árvore de exibição **(Local e conectada)** -> **Contas de Armazenamento**. 
7. Expanda **Contêineres de Blobs** e confirme se não existe um contêiner de blobs chamado **adftutorial**. Se já existir, ignore as etapas a seguir para criar o contêiner. 
8. Clique com botão direito do mouse em **Contêineres de Blobs** e selecione **Criar Contêiner de Blobs**.

    ![Criar contêiner de blobs](media/tutorial-hybrid-copy-powershell/stroage-explorer-create-blob-container-menu.png)
9. Digite **adftutorial** para o nome e pressione **ENTER**. 
10. Confirme se o contêiner **adftutorial** está selecionado na exibição de árvore. O Data Factory cria automaticamente a pasta de saída nesse contêiner, portanto você não precisa criar uma. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Instalar o Azure Powershell
Instale o Azure PowerShell mais recente se você ainda não o tiver em seu computador. 

1. No navegador da Web, navegue até a página [Downloads do SDK do Azure e SDKS](https://azure.microsoft.com/downloads/). 
2. Clique em **Instalar Windows** na seção **Ferramentas de linha de comando** -> **PowerShell**. 
3. Para instalar o Azure PowerShell, execute o arquivo **MSI**. 

Para saber mais, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Fazer logon no Azure PowerShell
Iniciar o **PowerShell** no seu computador. Mantenha o Azure PowerShell aberto até o fim deste guia de início rápido. Se você fechar e reabrir, precisará executar os comandos novamente.

1. Execute o comando a seguir e insira o nome de usuário e a senha do Azure que você usa para entrar no portal do Azure:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Se você tiver várias assinaturas do Azure, execute o comando a seguir para exibir todas as assinaturas dessa conta:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Substitua **SubscriptionId** pela ID da assinatura do Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Defina uma variável para o nome do grupo de recursos que você usa nos comandos do PowerShell posteriormente. Copie o seguinte texto de comando para o PowerShell, especifique um nome para o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) entre aspas duplas e, em seguida, execute o comando. 
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. Defina uma variável para o nome do data factory que você pode usar nos comandos do PowerShell mais tarde. 

    ```powershell
    $dataFactoryName = "<Specify a name for the data factory. It must be globally unique.>";
    ```
1. Defina uma variável para o local do data factory: 

    ```powershell
    $location = "East US"
    ```
4. Para criar o grupo de recursos do Azure, execute o seguinte comando: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    Se o grupo de recursos já existir, não convém substituí-lo. Atribuir um valor diferente para a variável `$resourceGroupName` e tente novamente. Se você deseja compartilhar o grupo de recursos com outros, vá para a próxima etapa.  
5. Para criar o data factory, execute o cmdlet **Set-AzureRmDataFactoryV2** a seguir: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Observe os seguintes pontos:

* O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome e tente novamente.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Para criar instâncias do Data Factory, você precisa ser um **colaborador** ou **administrador** da assinatura do Azure.
* Atualmente, o Data Factory versão 2 permite que você crie os data factories somente nas regiões Leste dos EUA, Leste dos EUA 2 e Europa Ocidental. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.

## <a name="create-a-self-hosted-ir"></a>Criar um IR auto-hospedado

Nesta seção, você pode criar um Integration Runtime auto-hospedado e associá-lo a um nó local (computador).

1. Crie uma variável para o nome do Integration Runtime. 

    ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
    ```
1. Crie um Integration Runtime auto-hospedado. Use um nome exclusivo no caso exista outro Integration Runtime com o mesmo nome.

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Veja o exemplo de saída:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Execute o comando a seguir para recuperar o status do Integration Runtime criado.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Veja o exemplo de saída:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. Execute o comando a seguir para recuperar as chaves de autenticação para registrar o Integration Runtime auto-hospedado com o serviço Data Factory na nuvem. Copie uma das chaves para registrar o Integration Runtime auto-hospedado.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Veja o exemplo de saída:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

## <a name="install-integration-runtime"></a>Instalar o Integration Runtime
1. [Baixe](https://www.microsoft.com/download/details.aspx?id=39717) o Integration Runtime auto-hospedado em uma máquina local do Windows e execute a instalação. 
2. No **Assistente de instalação Bem-vindo ao Microsoft Integration Runtime**, clique em **Avançar**.  
3. Na página **Contrato de Licença do Usuário Final**, aceite os termos e o contrato de licença e clique em **Avançar**. 
4. Na página **Pasta de destino**, clique em **Avançar**. 
5. Em **Pronto para instalar o Microsoft Integration Runtime**, clique em **Instalar**. 
6. Se você vir uma mensagem de aviso informando que computador está configurado para entrar no modo de suspensão ou hibernação quando não estiver em uso, clique em **OK**. 
7. Na página **Assistente de instalação do Microsoft Integration Runtime concluído**, clique em **Concluir**.
8. Na página **Registrar Integration Runtime (auto-hospedado)**, cole a chave que você salvou na seção anterior e clique em **Registrar**. 

   ![Registrar Integration Runtime](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)
2. Você verá a seguinte mensagem quando o Integration Runtime auto-hospedado for registrado com êxito:

   ![Registrado com êxito](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

3. Na página **Novo nó do Integration Runtime (auto-hospedado)** , clique em **Avançar**. 

    ![Nova página do nó do Integration Runtime](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)
4. No **Canal de comunicação de Intranet**, clique em **Ignorar**. É possível selecionar uma certificação TLS/SSL para proteger a comunicação entre nós em um ambiente de Integration Runtime de vários nós. 

    ![Página de canal de comunicação de Intranet](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)
5. Na página **Registrar Integration Runtime (auto-hospedado)**, clique em **Iniciar o Gerenciador de Configurações**. 
6. Você verá a página a seguir quando o nó estiver conectado ao serviço de nuvem:

   ![O nó está conectado](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>Criar serviços vinculados

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Criar um serviço vinculado do Armazenamento do Azure (destino/coletor)

1. Crie um arquivo JSON chamado **AzureStorageLinkedService.json** na pasta **C:\ADFv2Tutorial** com o seguinte conteúdo: Criar a pasta ADFv2Tutorial se ela ainda não existir.  

    > [!IMPORTANT]
    > Antes de salvar o arquivo, substitua &lt;accountName&gt; e &lt;accountKey&gt; pelo nome e pela chave da sua conta de armazenamento do Azure, respectivamente.

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```
2. No **Azure PowerShell**, mude para a pasta **ADFv2Tutorial**.

   Execute o cmdlet **Set-AzureRmDataFactoryV2LinkedService** para criar o serviço vinculado **AzureStorageLinkedService**. Os cmdlets usados neste tutorial usam valores para os parâmetros **ResourceGroupName** e **DataFactoryName**. Como alternativa, você pode passar o objeto **DataFactory** retornado pelo cmdlet Set-AzureRmDataFactoryV2 sem precisar digitar ResourceGroupName e DataFactoryName sempre que executar um cmdlet.

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Aqui está uma amostra de saída:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Criar e criptografar um serviço vinculado do SQL Server (origem)

1. Criar um arquivo JSON chamado **SqlServerLinkedService.json** na pasta **C:\ADFv2Tutorial** com o seguinte conteúdo: substituir **&lt;servername>**, **&lt;databasename>**, **&lt;username>**, **&lt;servername>** e **&lt;password>** por valores do SQL Server antes de salvar o arquivo. 

    > [!IMPORTANT]
    > Substitua **&lt;integration** **runtime** **name>** pelo nome de seu Integration Runtime.

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```
2. Para criptografar os dados confidenciais do conteúdo JSON no Integration Runtime auto-hospedado local, execute **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** e passe o conteúdo JSON acima. Essa criptografia garante que as credenciais sejam criptografadas usando a Interface de Programação de Aplicativo de Proteção de Dados (DPAPI). As credenciais criptografadas são armazenadas no nó do Integration Runtime auto-hospedado localmente (computador local). O conteúdo de saída pode ser redirecionado para outro arquivo JSON (no caso, 'encryptedLinkedService.json') que contém credenciais criptografadas.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Execute o comando a seguir usando JSON da etapa anterior para criar o **SqlServerLinkedService**:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Criar conjuntos de dados
Nesta etapa, você cria conjuntos de dados de entrada e saída que representam dados de entrada e saída da operação de cópia (banco de dados SQL Server local = > armazenamento de blobs do Azure).

### <a name="create-a-dataset-for-source-sql-database"></a>Criar um conjunto de dados para o Banco de Dados SQL de origem

1. Crie um arquivo JSON denominado **SqlServerDataset.json** na pasta **C:\ADFv2Tutorial** com o seguinte conteúdo:  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. Para criar o conjunto de dados: **SqlServerDataset**, execute o cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Veja o exemplo de saída:

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>Criar um conjunto de dados para o Armazenamento de Blobs do Azure de coletor

1. Crie um arquivo JSON denominado **AzureBlobDataset.json** na pasta **C:\ADFv2Tutorial** com o seguinte conteúdo:

    > [!IMPORTANT]
    > Esse código de exemplo supõe que você tenha um contêiner denominado **adftutorial** no Armazenamento de Blobs do Azure.

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. Para criar o conjunto de dados: **AzureBlobDataset**, execute o cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Veja o exemplo de saída:

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-pipelines"></a>Criar pipelines

### <a name="create-the-pipeline-sqlservertoblobpipeline"></a>Criar o pipeline "SqlServerToBlobPipeline"

1. Crie um arquivo JSON denominado **SqlServerToBlobPipeline.json** na pasta **C:\ADFv2Tutorial** com o seguinte conteúdo:

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Para criar o pipeline **SQLServerToBlobPipeline**, execute o cmdlet **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Veja o exemplo de saída:

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```



## <a name="start-and-monitor-a-pipeline-run"></a>Iniciar e monitorar uma execução de pipeline

1. Inicie a execução de um pipeline para o pipeline "SQLServerToBlobPipeline" e capture a ID da execução de pipeline para monitoramento futuro.  

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
    ```

2. Execute o script a seguir para verificar continuamente o status de execução do pipeline "**SQLServerToBlobPipeline**" e imprimir o resultado final.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Aqui está a saída da execução de exemplo:

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Você pode obter a ID da execução de pipeline "**SQLServerToBlobPipeline**" e verificar o resultado detalhado da execução da atividade conforme demonstrado a seguir.

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Aqui está a saída da execução de exemplo:

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 3,
      "throughput": 0.01171875,
      "errors": [],
      "effectiveIntegrationRuntime": "MyIntegrationRuntime",
      "billedDuration": 3
    }
    ```
## <a name="verify-the-output"></a>Verificar a saída
O pipeline cria automaticamente a pasta de saída chamada `fromonprem` no contêiner de blobs `adftutorial`. Confirme que você vê o arquivo **dbo.emp.txt** na pasta de saída. Use o [Gerenciador do Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para verificar se a saída foi criada. 

## <a name="next-steps"></a>Próximas etapas
O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Você aprendeu como:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Crie um Integration Runtime auto-hospedado.
> * Criar serviços vinculados do SQL Server e do Armazenamento do Azure. 
> * Criar conjuntos de dados do SQL Server e de Blobs do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Inicie uma execução de pipeline.
> * Monitore a execução de pipeline.

Para obter uma lista dos armazenamentos de dados suportados pelo Azure Data Factory, consulte [armazenamentos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).

Avance para o tutorial a seguir para saber mais sobre a cópia de dados em massa de uma origem para um destino:

> [!div class="nextstepaction"]
>[Copiar dados em massa](tutorial-bulk-copy.md)
