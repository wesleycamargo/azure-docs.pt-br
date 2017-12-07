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
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: ba47f3e3f331929b884f27f49bf6e484ff363765
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2017
---
# <a name="tutorial-copy-data-from-on-premises-sql-server-to-azure-blob-storage"></a>Tutorial: Copiar dados do SQL Server local para o Armazenamento de Blobs do Azure
Neste tutorial, você usa o Azure PowerShell para criar um pipeline do Data Factory que copia dados de um Banco de Dados do SQL Server local para um Armazenamento de Blobs do Azure. Você cria e usa um tempo de execução de integração auto-hospedado, o qual move dados entre locais e armazenamentos de dados da nuvem. 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira a [documentação do Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Este artigo não fornece uma introdução detalhada do serviço Data Factory. Para obter uma introdução do serviço do Azure Data Factory, consulte [Introdução ao Azure Data Factory](introduction.md). 

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Crie um Integration Runtime auto-hospedado.
> * Criar serviços vinculados do SQL Server e do Armazenamento do Azure. 
> * Criar conjuntos de dados do SQL Server e de Blobs do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Inicie uma execução de pipeline.
> * Monitore a execução de pipeline.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="azure-subscription"></a>Assinatura do Azure
Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

### <a name="azure-roles"></a>Funções do Azure
Para criar instâncias de Data Factory, a conta de usuário usada para fazer logon no Azure deve ser um membro das funções **colaborador** ou **proprietário**, ou um **administrador** da assinatura do Azure. No Portal do Azure, clique no seu **nome de usuário** no canto superior direito e selecione **Permissões** para exibir as permissões que você tem com a assinatura. Se tiver acesso a várias assinaturas, selecione a que for adequada. Para obter instruções de exemplo sobre como adicionar um usuário a uma função, consulte o artigo [Adicionar funções](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-201420162017"></a>SQL Server 2014/2016/2017
Neste tutorial, você utiliza um Banco de Dados do SQL Server local como um armazenamento de dados de **origem**. O pipeline no data factory que você cria neste tutorial copia dados deste banco de dados do SQL Server local (fonte) para um armazenamento de Blobs do Azure (coletor). Crie uma tabela chamada **emp** no seu banco de dados do SQL Server e insira alguns exemplos de entradas na tabela. 

1. Inicialize o **SQL Server Management Studio** no seu computador. Se você não tiver o SQL Server Management Studio em seu computador, instale-o no [centro de download](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). 
2. Conecte-se ao SQL Server usando suas credenciais. 
3. Criar um banco de dados de exemplo. No modo de exibição de árvore, clique com o botão direito do mouse em **Bancos de Dados** e clique em **Novo Banco de Dados**. Na caixa de diálogo **Novo Banco de Dados**, digite um **nome** para o banco de dados e clique em **OK**. 
4. Execute o seguinte script de consulta no banco de dados, que cria a tabela **emp** e insira alguns dados de exemplo nela. No modo de exibição de árvore, clique com o botão direito do mouse no **banco de dados** que você criou e clique em **Nova consulta**. 

    ```sql   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )

    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Conta de Armazenamento do Azure
Use uma conta de Armazenamento do Azure para fins gerais (especificamente o Armazenamento de Blobs) como repositório de dados de **destino/coletor** neste tutorial. Se você não tiver uma conta de fins gerais de armazenamento do Azure, consulte [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para saber sobre como criar uma. O pipeline no data factory que você cria neste tutorial copia dados do banco de dados do SQL Server local (fonte) para este armazenamento de Blobs do Azure (coletor). 

#### <a name="get-storage-account-name-and-account-key"></a>Obter o nome da conta de armazenamento e a chave da conta
Você usa o nome e a chave da sua conta de armazenamento do Azure neste tutorial. O procedimento a seguir fornece as etapas para obter o nome e a chave da sua conta de armazenamento. 

1. Abra um navegador da Web e navegue até o [portal do Azure](https://portal.azure.com). Faça logon usando seu nome de usuário e senha do Azure. 
2. Clique em **Mais serviços >** no menu à esquerda, filtre pela palavra-chave **Armazenamento** e selecione **Contas de armazenamento**.

    ![Pesquisar conta de armazenamento](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. Na lista de contas de armazenamento, filtre pela sua conta de armazenamento (se necessário) e, em seguida, selecione a **sua conta de armazenamento**. 
4. Na página da **Conta de armazenamento**, selecione **Chaves de acesso** no menu.

    ![Obter nome e chave da conta de armazenamento](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. Copie os valores dos campos **Nome da conta de armazenamento** e **key1** para a área de transferência. Cole-os em um bloco de notas ou qualquer outro editor e salve-os. Use o nome e a chave da conta de armazenamento no tutorial. 

#### <a name="create-the-adftutorial-container"></a>Criar o contêiner adftutorial 
Nesta seção, você cria um contêiner de blob chamado **adftutorial** no armazenamento de blobs do Azure. 

1. Na página **Conta de armazenamento**, alterne para **Visão geral** e depois clique em **Blobs**. 

    ![Selecione a opção Blobs](media/tutorial-hybrid-copy-powershell/select-blobs.png)
1. Na página **Serviço Blob**, clique em **+ Contêiner** na barra de ferramentas. 

    ![Botão Adicionar contêiner](media/tutorial-hybrid-copy-powershell/add-container-button.png)
3. Na caixa de diálogo **Novo contêiner**, insira **adftutorial** como o nome e clique em **OK**. 

    ![Inserir o nome do contêiner](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)
4. Clique em **adftutorial** na lista de contêineres.  

    ![Selecione o contêiner](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)
5. Mantenha a página **contêiner** para abertura do **adftutorial**. Você o usa para verificar a saída no final do tutorial. O Data Factory cria automaticamente a pasta de saída nesse contêiner, portanto você não precisa criar uma.

    ![Página Contêiner](media/tutorial-hybrid-copy-powershell/container-page.png)

### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-powershell"></a>Instalar o PowerShell
Instale o PowerShell mais recente se você ainda não o tiver em seu computador. 

1. No navegador da Web, navegue até a página [Downloads do SDK do Azure e SDKS](https://azure.microsoft.com/downloads/). 
2. Clique em **Instalar Windows** na seção **Ferramentas de linha de comando** -> **PowerShell**. 
3. Para instalar o Azure PowerShell, execute o arquivo **MSI**. 

Para saber mais, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Fazer logon no PowerShell

1. Iniciar o **PowerShell** no seu computador. Mantenha a janela do PowerShell aberta até o fim deste guia de início rápido. Se você fechá-la e reabri-la, precisará executar esses comandos novamente.

    ![Inicializar o PowerShell](media/tutorial-hybrid-copy-powershell/search-powershell.png)
1. Execute o comando a seguir e insira o nome de usuário e a senha do Azure que você usa para entrar no portal do Azure:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Se você tiver várias assinaturas do Azure, execute o comando a seguir para exibir todas as assinaturas dessa conta:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Se você tiver várias assinaturas do Azure, execute o comando a seguir para selecionar as assinaturas com as quais quer trabalhar. Substitua **SubscriptionId** pela ID da assinatura do Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Defina uma variável para o nome do grupo de recursos que você usa nos comandos do PowerShell posteriormente. Copie o seguinte texto de comando para o PowerShell, especifique um nome para o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) entre aspas duplas e, em seguida, execute o comando. Por exemplo: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```
2. Para criar o grupo de recursos do Azure, execute o seguinte comando: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    Se o grupo de recursos já existir, não convém substituí-lo. Atribua um valor diferente para a variável `$resourceGroupName` e execute o comando novamente.
3. Defina uma variável para o nome do data factory que você pode usar nos comandos do PowerShell mais tarde. O nome deve começar com uma letra ou número e pode conter apenas letras, números e o caractere traço (-).

    > [!IMPORTANT]
    >  Atualize o Nome do data factory para ser globalmente exclusivo. Por exemplo, ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```
1. Defina uma variável para o local do data factory: 

    ```powershell
    $location = "East US"
    ```  
5. Para criar o data factory, execute o cmdlet **Set-AzureRmDataFactoryV2** a seguir: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Observe os seguintes pontos:

* O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome e tente novamente.

    ```
    The specified Data Factory name 'ADFv2TutorialDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Para criar instâncias de Data Factory, a conta de usuário usada para fazer logon no Azure deve ser um membro das funções **colaborador** ou **proprietário**, ou um **administrador** da assinatura do Azure.
* Atualmente, o Data Factory versão 2 permite que você crie os data factories somente nas regiões Leste dos EUA, Leste dos EUA 2 e Europa Ocidental. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.

## <a name="create-a-self-hosted-ir"></a>Criar um IR auto-hospedado

Nesta seção, você cria um tempo de execução de integração auto-hospedado e o associa com um computador local com o banco de dados do SQL Server. O tempo de execução de integração auto-hospedado é o componente que copia dados do SQL Server em seu computador para o armazenamento de Blobs do Azure. 

1. Crie uma variável para o nome do Integration Runtime. Use um nome exclusivo e anote o nome. Você o usará posteriormente neste tutorial. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
1. Crie um Integration Runtime auto-hospedado. 

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

3. Execute o comando a seguir para recuperar as **chaves de autenticação** para registrar o tempo de execução de integração auto-hospedado com o serviço Data Factory na nuvem. 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Veja o exemplo de saída:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    
4. Copie uma das chaves (excluir aspas duplas) para registrar o tempo de execução de integração auto-hospedado que você instala em seu computador na próxima etapa.  

## <a name="install-integration-runtime"></a>Instalar o Integration Runtime
1. [Baixe](https://www.microsoft.com/download/details.aspx?id=39717) o tempo de execução de integração auto-hospedado em uma máquina local do Windows e execute a instalação. 
2. No **Assistente de instalação Bem-vindo ao Microsoft Integration Runtime**, clique em **Avançar**.  
3. Na página **Contrato de Licença do Usuário Final**, aceite os termos e o contrato de licença e clique em **Avançar**. 
4. Na página **Pasta de destino**, clique em **Avançar**. 
5. Em **Pronto para instalar o Microsoft Integration Runtime**, clique em **Instalar**. 
6. Se você vir uma mensagem de aviso informando que computador está configurado para entrar no modo de suspensão ou hibernação quando não estiver em uso, clique em **OK**. 
7. Se você vir a janela **Opções de Energia**, feche-a e alterne para a janela de instalação. 
8. Na página **Assistente de instalação do Microsoft Integration Runtime concluído**, clique em **Concluir**.
9. Na página **Registrar Integration Runtime (auto-hospedado)**, cole a chave que você salvou na seção anterior e clique em **Registrar**. 

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
7. Agora teste a conectividade com o seu banco de dados do SQL Server.

    ![Guia Diagnósticos](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)   

    - Na janela do **Gerenciador de Configurações**, alterne para a guia **Diagnóstico**.
    - Selecione **SqlServer** para o **Tipo da fonte de dados**.
    - Insira o nome do **servidor**.
    - Insira o nome do **banco de dados**. 
    - Selecione o modo de **autenticação**. 
    - Insira o nome de **usuário**. 
    - Insira a **senha** do nome de usuário.
    - Clique em **Testar** para confirmar que esse tempo de execução de integração pode se conectar ao SQL Server. Você verá uma marca de seleção verde se a conexão tiver êxito. Caso contrário, será exibida uma mensagem de erro associada à falha. Corrija todos os problemas e verifique se o tempo de execução de integração pode se conectar ao seu SQL Server.
    - Anote esses valores (tipo de autenticação, servidor, banco de dados, usuário, senha). Você os usará posteriormente neste tutorial. 
    
      
## <a name="create-linked-services"></a>Criar serviços vinculados
Crie serviços vinculados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Neste tutorial, você vincula sua Conta de Armazenamento do Azure e SQL Server local para o armazenamento de dados. Os serviços vinculados têm as informações de conexão que o serviço do Data Factory usa no tempo de execução para se conectar a eles. 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Criar um serviço vinculado do Armazenamento do Azure (destino/coletor)
Nesta etapa, você vincula a Conta de Armazenamento do Azure ao data factory.

1. Crie um arquivo JSON chamado **AzureStorageLinkedService.json** na pasta **C:\ADFv2Tutorial** com o seguinte conteúdo: Criar a pasta ADFv2Tutorial se ela ainda não existir.  

    > [!IMPORTANT]
    > Antes de salvar o arquivo, substitua &lt;accountName&gt; e &lt;accountKey&gt; pelo nome e pela chave da sua **conta de armazenamento do Azure**, respectivamente. Você os anotou como parte dos [pré-requisitos](#get-storage-account-name-and-account-key).

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>;EndpointSuffix=core.windows.net"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

    Se você estiver usando o Bloco de Notas, selecione **Todos os arquivos** para o **Salvar como tipo** preenchido na caixa de diálogo **Salvar como**. Caso contrário, ele pode adicionar a extensão `.txt` para o arquivo. Por exemplo: `AzureStorageLinkedService.json.txt`. Se você criar o arquivo no Explorador de arquivos antes de abri-lo no Bloco de Notas, você não poderá ver a extensão `.txt`, já que a opção **Ocultar extensões de tipos de arquivos conhecidos** será definida por padrão. Remova a extensão `.txt` antes de prosseguir para a próxima etapa. 
2. No **Azure PowerShell**, mude para a pasta **C:\ADFv2Tutorial**.

   Execute o cmdlet **Set-AzureRmDataFactoryV2LinkedService** para criar o serviço vinculado **AzureStorageLinkedService**. 

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

    Se você receber um erro de "arquivo não encontrado". Execute o comando `dir` para confirmar se o arquivo existe. Se o nome do arquivo tiver a extensão `.txt` (por exemplo, AzureStorageLinkedService.json.txt), remova-a e, depois, execute novamente o comando do PowerShell. 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Criar e criptografar um serviço vinculado do SQL Server (origem)
Nesta etapa, você vincular seu SQL Server local ao data factory.

1. Crie um arquivo JSON chamado **SqlServerLinkedService.json** na pasta **C:\ADFv2Tutorial** com o seguinte conteúdo: selecione a seção correta com base na **autenticação** que você usa para se conectar ao SQL Server.  

    > [!IMPORTANT]
    > Selecione a seção correta com base na **autenticação** que você usa para se conectar ao SQL Server.

    **Se você estiver usando uma autenticação do SQL (sa), copie a seguinte definição de JSON:**

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
    **Se você estiver usando uma autenticação do Windows, copie a seguinte definição de JSON:**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
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
    > [!IMPORTANT]
    > - Selecione a seção correta com base na **autenticação** que você usa para se conectar ao SQL Server.
    > - Substitua **&lt;integration** **runtime** **name>** pelo nome de seu Integration Runtime.
    > - Substitua **&lt;servername>**, **&lt;databasename>**, **&lt;username>** e **&lt;password>** por valores do seu SQL Server antes de salvar o arquivo.
    > - Se precisar usar um caractere de barra (`\`) em sua conta de usuário e nome de servidor, use o caractere de escape (`\`). Por exemplo: `mydomain\\myuser`. 

2. Para criptografar os dados confidenciais (nome de usuário, senha, etc.), execute o cmdlet **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential**. Essa criptografia garante que as credenciais sejam criptografadas usando a Interface de Programação de Aplicativo de Proteção de Dados (DPAPI). As credenciais criptografadas são armazenadas localmente no nó do tempo de execução de integração auto-hospedado (computador local). O conteúdo de saída pode ser redirecionado para outro arquivo JSON (no caso, 'encryptedLinkedService.json') que contém credenciais criptografadas.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Execute o comando a seguir, o qual cria o **EncryptedSqlServerLinkedService**:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Criar conjuntos de dados
Nesta etapa, você cria conjuntos de dados de entrada e saída que representam dados de entrada e saída da operação de cópia (banco de dados SQL Server local = > armazenamento de blobs do Azure).

### <a name="create-a-dataset-for-source-sql-database"></a>Criar um conjunto de dados para o Banco de Dados SQL de origem
Nesta etapa, você define um conjunto de dados que representa os dados no banco de dados do SQL Server. O conjunto de dados é do tipo **SqlServerTable**. Ele se refere ao **Serviço vinculado do SQL Server** criado na etapa anterior. O serviço vinculado tem as **informações de conexão** que o serviço do Data Factory usa para se conectar ao seu SQL Server no tempo de execução. Esse conjunto de dados especifica a **tabela SQL** no banco de dados que contém os dados. Neste tutorial, é a tabela `emp` que contém os dados de origem. 

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
Nesta etapa, você deve definir um conjunto de dados que represente os dados que devem ser copiados para o Armazenamento de Blobs do Azure. O conjunto de dados é do tipo **AzureBlob**. Ele se refere ao **Serviço vinculado do Armazenamento do Azure** criado anteriormente neste tutorial. O serviço vinculado tem as **informações de conexão** que o serviço do Data Factory usa no tempo de execução para se conectar à sua Conta de Armazenamento do Azure. Esse **conjunto de dados** especifica a **pasta** no Armazenamento do Azure para a qual os dados são copiados do banco de dados do SQL Server. Neste tutorial, a pasta é: `adftutorial/fromonprem`, em que `adftutorial` é o contêiner de blob e `fromonprem` é a pasta. 

1. Crie um arquivo JSON denominado **AzureBlobDataset.json** na pasta **C:\ADFv2Tutorial** com o seguinte conteúdo:

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

## <a name="create-a-pipeline"></a>Criar uma pipeline
Nesta tutorial, você cria um pipeline com uma atividade de cópia. A atividade de cópia usa o **SqlServerDataset** como o conjunto de dados de entrada e o **AzureBlobDataset** como o conjunto de dados de saída. O tipo de fonte está definido como **SqlSource**, e o tipo de coletor está definido como **BlobSink**.

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


## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline
Inicie a execução de um pipeline para o pipeline "SQLServerToBlobPipeline" e capture a ID da execução de pipeline para monitoramento futuro.

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

1. Execute o script a seguir para verificar continuamente o status de execução do pipeline **SQLServerToBlobPipeline** e imprimir o resultado final. Copie/cole o script a seguir na janela do PowerShell e pressione ENTER.

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

3. Você pode obter a ID da execução de pipeline **SQLServerToBlobPipeline** e verificar o resultado detalhado da execução da atividade executando o seguinte comando: 

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
O pipeline cria automaticamente a pasta de saída chamada `fromonprem` no contêiner de blobs `adftutorial`. Confirme que você vê o arquivo **dbo.emp.txt** na pasta de saída. 

1. No Portal do Azure, na página de contêiner **adftutorial**, clique em **Atualizar** para ver a pasta de saída.

    ![pasta de saída criada](media/tutorial-hybrid-copy-powershell/fromonprem-folder.png)
2. Clique em `fromonprem` na lista de pastas. 
3. Confirme que você vê um arquivo com o nome `dbo.emp.txt`.

    ![arquivo de saída](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


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
