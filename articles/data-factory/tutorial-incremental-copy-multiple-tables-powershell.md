---
title: "Copiar várias tabelas de maneira incremental usando o Azure Data Factory | Microsoft Docs"
description: "Neste tutorial, você deve criar um pipeline do Azure Data Factory que copie incrementalmente os dados delta de várias tabelas em um banco de dados do SQL Server local para um banco de dados SQL do Azure. "
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
ms.date: 12/01/2017
ms.author: jingwang
ms.openlocfilehash: 2d9213a74fd881a7be52f51ff8ebb49171c77283
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-azure-sql-database"></a>Carregue incrementalmente os dados de várias tabelas no SQL Server para o Banco de Dados SQL do Azure
Neste tutorial, você pode criar um data factory do Azure com um pipeline que carrega dados delta de várias tabelas do servidor SQL local para um banco de dados SQL do Azure.    

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Prepare os armazenamentos de dados de origem e de destino.
> * Criar uma fábrica de dados.
> * Criar um tempo de execução de integração (IR) auto-hospedado
> * Instalar o Integration Runtime 
> * Criar serviços vinculados. 
> * Crie os conjuntos de dados de origem, de coletor e de marca-d'água.
> * Crie, execute e monitore um pipeline.
> * Analisar resultados
> * Adicionar ou atualizar dados nas tabelas de origem
> * Executar novamente e monitorar o pipeline
> * Examinar os resultados finais 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira a [documentação do Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Visão geral
Aqui estão as etapas importantes ao criar essa solução: 

1. **Selecione a coluna de marca-d'água**.
    Selecione uma coluna para cada tabela no armazenamento de dados de origem, que pode ser usada para identificar os registros novos ou atualizados para cada execução. Normalmente, os dados nessa coluna selecionada (por exemplo, ID ou last_modify_time) seguem crescendo quando linhas são criadas ou atualizadas. O valor máximo dessa coluna é usado como uma marca-d'água.
2. **Prepare um armazenamento de dados para armazenar o valor de marca-d'água**.   
    Neste tutorial, você deve armazenar o valor de marca-d'água em um Banco de Dados SQL do Azure.
3. **Crie um pipeline com as seguintes atividades:** 
    
    1. Crie uma atividade **ForEach** que itere em uma lista de nomes de tabela de origem passada como um parâmetro para o pipeline. Para cada tabela de origem, ele chama as seguintes atividades para executar o carregamento delta para essa tabela. 
    2. Criar duas atividades de **pesquisa**. Use a primeira atividade de pesquisa para recuperar o último valor de marca-d'água. Use a segunda atividade de pesquisa para recuperar o novo valor de marca-d'água. Esses valores de marca-d'água são passados para a atividade de cópia. 
    3. Crie uma **atividade de cópia** que copie linhas do armazenamento de dados de origem com o valor da coluna de marca-d'água maior do que o antigo valor de marca-d'água e menor que o novo valor de marca-d'água. Em seguida, ela copia os dados delta do armazenamento de dados de origem para um Armazenamento de Blobs como um novo arquivo. 
    4. Crie uma **atividade de procedimento armazenado** que atualize o valor de marca-d'água para o pipeline que for executado da próxima vez. 

        Aqui está o diagrama da solução de alto nível: 

        ![Carregar dados incrementalmente](media\tutorial-incremental-copy-multiple-tables-powershell\high-level-solution-diagram.png)


Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* **SQL Server**. Neste tutorial, você utiliza um banco de dados do SQL Server local como o armazenamento de dados de **origem**. 
* **Banco de dados SQL do Azure**. Use um banco de dados SQL do Azure como o repositório de dados do **coletor**. Se você não tiver um Banco de Dados SQL do Azure, veja o artigo [Criar um Banco de Dados SQL do Azure](../sql-database/sql-database-get-started-portal.md) para conhecer as etapas para criar um. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Criar tabelas de origem no banco de dados do SQL Server

1. Inicie o **SQL Server Management Studio** e conecte-se ao SQL Server local. 
2. No **Gerenciador de Servidores**, clique com o botão direito do mouse no banco de dados e escolha **Nova Consulta**.
3. Execute o seguinte comando SQL no banco de dados para criar tabelas chamadas `customer_table` e `project_table`.

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql--database"></a>Criar tabelas de destino no banco de dados SQL do Azure
1. Inicie o **SQL Server Management Studio** e conecte-se à réplica principal. 
2. No **Gerenciador de Servidores**, clique com o botão direito do mouse no **banco de dados** e escolha **Nova Consulta**.
3. Execute o comando SQL a seguir no Banco de Dados SQL do Azure para criar tabelas chamadas `customer_table` e `project_table`.  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-azure-sql-database-to-store-the-high-watermark-value"></a>Criar outra tabela no Banco de Dados SQL do Azure para armazenar o valor de marca d'água alta
1. Execute o comando SQL a seguir no Banco de Dados SQL do Azure para criar uma tabela chamada `watermarktable` para armazenar o valor de marca-d'água.  
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
3. Insira valores de marca d'água iniciais para ambas as tabelas de origem na tabela de marca d'água.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-azure-sql-database"></a>Criar um procedimento armazenado no Banco de Dados SQL do Azure 

Execute o comando a seguir para criar um procedimento armazenado no Banco de Dados SQL do Azure. Esse procedimento armazenado atualiza o valor de marca d'água após cada execução de pipeline. 

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures"></a>Criar tipos de dados e procedimentos armazenados adicionais
Crie dois procedimentos armazenados e dois tipos de dados no banco de dados SQL do Azure executando a consulta a seguir: eles serão usados para mesclar os dados das tabelas de origem nas tabelas de destino.

```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE sp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE sp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

### <a name="azure-powershell"></a>Azure PowerShell
Instale os módulos mais recentes do Azure PowerShell seguindo as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Criar uma data factory
1. Defina uma variável para o nome do grupo de recursos que você usa nos comandos do PowerShell posteriormente. Copie o seguinte texto de comando para o PowerShell, especifique um nome para o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) entre aspas duplas e, em seguida, execute o comando. Por exemplo: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Se o grupo de recursos já existir, não convém substituí-lo. Atribua um valor diferente para a variável `$resourceGroupName` e execute o comando novamente
2. Defina uma variável para o local do data factory: 

    ```powershell
    $location = "East US"
    ```
3. Para criar o grupo de recursos do Azure, execute o seguinte comando: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Se o grupo de recursos já existir, não convém substituí-lo. Atribua um valor diferente para a variável `$resourceGroupName` e execute o comando novamente. 
3. Defina uma variável para o nome do data factory. 

    > [!IMPORTANT]
    >  Atualize o Nome do data factory para ser globalmente exclusivo. Por exemplo, ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
5. Para criar o data factory, execute o cmdlet **Set-AzureRmDataFactoryV2** a seguir: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Observe os seguintes pontos:

* O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome e tente novamente.

    ```
    The specified Data Factory name 'ADFIncMultiCopyTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para criar instâncias de Data Factory, a conta de usuário usada para fazer logon no Azure deve ser um membro das funções **colaborador** ou **proprietário**, ou um **administrador** da assinatura do Azure.
* Atualmente, o Data Factory versão 2 permite que você crie os data factories somente nas regiões Leste dos EUA, Leste dos EUA 2 e Europa Ocidental. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]



## <a name="create-linked-services"></a>Criar serviços vinculados
Os serviços vinculados são criados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Nesta seção, você cria serviços vinculados para seu banco de dados SQL Server local e o banco de dados SQL do Azure. 

### <a name="create-sql-server-linked-service"></a>Crie um serviço vinculado do SQL Server.
Nesta etapa, você vincular seu SQL Server local ao data factory.

1. Crie um arquivo JSON chamado **SqlServerLinkedService.json** na pasta **C:\ADFTutorials\IncCopyMultiTableTutorial** com o seguinte conteúdo: selecione a seção correta com base na **autenticação** que você usa para se conectar ao SQL Server. Crie as pastas locais caso elas ainda não existam. 

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

2. No **Azure PowerShell**, alterne para a pasta **C:\ADFTutorials\IncCopyMultiTableTutorial**.
3. Execute o cmdlet **Set-AzureRmDataFactoryV2LinkedService** para criar o serviço vinculado **AzureStorageLinkedService**. No exemplo a seguir, você passa valores para os parâmetros **ResourceGroupName** e **DataFactoryName**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    Veja o exemplo de saída:

    ```json
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Crie um serviço vinculado do Banco de Dados SQL do Azure.
1. Crie um arquivo JSON chamado **AzureSQLDatabaseLinkedService.json** na pasta **C:\ADFTutorials\IncCopyMultiTableTutorial** com o seguinte conteúdo: (crie a pasta ADF se ela ainda não existir). Substitua **&lt;servidor&gt;, &lt;nome do banco de dados&gt;, &lt;id do usuário&gt; e &lt;senha&gt;** pelo nome do seu servidor SQL, nome do seu banco de dados, ID do usuário e senha do Azure antes de salvar o arquivo. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name>; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. No **Azure PowerShell**, execute o cmdlet **Set-AzureRmDataFactoryV2LinkedService** para criar o serviço vinculado: **AzureSQLDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Veja o exemplo de saída:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Criar conjuntos de dados
Nesta etapa, você cria conjuntos de dados para representar a fonte de dados, o destino de dados. e o local para armazenar a marca d'água.

### <a name="create-a-source-dataset"></a>Criar um conjunto de dados de origem

1. Crie um arquivo JSON denominado **SourceDataset.json** na mesma pasta, com o seguinte conteúdo: 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dummyName"
            },
            "linkedServiceName": {
                "referenceName": "SqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```

    O nome da tabela é um nome fictício. A atividade de cópia no pipeline usa uma consulta SQL para carregar os dados em vez de carregar a tabela inteira. 
1.  Para criar o conjunto de dados SourceDataset, execute o cmdlet Set-AzureRmDataFactoryV2Dataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Aqui está a amostra de saída do cmdlet:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Criar um conjunto de dados de coletor

1. Crie um arquivo JSON chamado **SinkDataset.json** na mesma pasta com o seguinte conteúdo: o tableName é definido dinamicamente pelo pipeline em tempo de execução. A atividade ForEach no pipeline itera por meio de uma lista de nomes de tabela e passa o nome da tabela para esse conjunto de dados em cada iteração. 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().SinkTableName}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "SinkTableName": {
                    "type": "String"
                }
            }
        }
    }
    ```

2.  Para criar o conjunto de dados SinkDataset, execute o cmdlet Set-AzureRmDataFactoryV2Dataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Aqui está a amostra de saída do cmdlet:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-watermark"></a>Criar um conjunto de dados para a marca-d'água
Nesta etapa, você deve criar um conjunto de dados para armazenar um valor de marca d'água alta. 

1. Crie um arquivo JSON denominado WatermarkDataset.json na mesma pasta, com o seguinte conteúdo: 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2.  Para criar o conjunto de dados WatermarkDataset, execute o cmdlet Set-AzureRmDataFactoryV2Dataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Aqui está a amostra de saída do cmdlet:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : <data factory name>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Criar uma pipeline
O pipeline usa uma lista de nomes de tabela como um parâmetro. A **atividade ForEach** itera na lista de nomes de tabela e executa as seguintes operações: 

1. Use a **atividade de pesquisa** para recuperar o valor de marca d'água antiga (o valor inicial ou o que foi usado na última iteração).
2. Use a **atividade de pesquisa** para recuperar o novo valor de marca d'água (valor máximo da coluna de marca d'água na tabela de origem).
3. Use a **atividade de cópia** para copiar os dados entre esses dois valores de marca d'água do banco de dados de origem para o banco de dados de destino. 
4. Use a **atividade de procedimento armazenado** para atualizar o valor da marca d'água antiga a ser usado na primeira etapa da próxima iteração. 

### <a name="create-the-pipeline"></a>Criar o pipeline
1. Crie um arquivo JSON denominado IncrementalCopyPipeline.json na mesma pasta, com o conteúdo a seguir: 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [{
    
                "name": "IterateSQLTables",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "false",
                    "items": {
                        "value": "@pipeline().parameters.tableList",
                        "type": "Expression"
                    },
    
                    "activities": [
                        {
                            "name": "LookupOldWaterMarkActivity",
                            "type": "Lookup",
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'"
                                },
    
                                "dataset": {
                                    "referenceName": "WatermarkDataset",
                                    "type": "DatasetReference"
                                }
                            }
                        },
                        {
                            "name": "LookupNewWaterMarkActivity",
                            "type": "Lookup",
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}"
                                },
    
                                "dataset": {
                                    "referenceName": "SourceDataset",
                                    "type": "DatasetReference"
                                }
                            }
                        },
    
                        {
                            "name": "IncrementalCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                                },
                                "sink": {
                                    "type": "SqlSink",
                                    "SqlWriterTableType": "@{item().TableType}",
                                    "SqlWriterStoredProcedureName": "@{item().StoredProcedureNameForMergeOperation}"
                                }
                            },
                            "dependsOn": [{
                                    "activity": "LookupNewWaterMarkActivity",
                                    "dependencyConditions": [
                                        "Succeeded"
                                    ]
                                },
                                {
                                    "activity": "LookupOldWaterMarkActivity",
                                    "dependencyConditions": [
                                        "Succeeded"
                                    ]
                                }
                            ],
    
                            "inputs": [{
                                "referenceName": "SourceDataset",
                                "type": "DatasetReference"
                            }],
                            "outputs": [{
                                "referenceName": "SinkDataset",
                                "type": "DatasetReference",
                                "parameters": {
                                    "SinkTableName": "@{item().TABLE_NAME}"
                                }
                            }]
                        },
    
                        {
                            "name": "StoredProceduretoWriteWatermarkActivity",
                            "type": "SqlServerStoredProcedure",
                            "typeProperties": {
    
                                "storedProcedureName": "sp_write_watermark",
                                "storedProcedureParameters": {
                                    "LastModifiedtime": {
                                        "value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                        "type": "datetime"
                                    },
                                    "TableName": {
                                        "value": "@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                        "type": "String"
                                    }
                                }
                            },
    
                            "linkedServiceName": {
                                "referenceName": "AzureSQLDatabaseLinkedService",
                                "type": "LinkedServiceReference"
                            },
    
                            "dependsOn": [{
                                "activity": "IncrementalCopyActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                            }]
                        }
    
                    ]
    
                }
            }],
    
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```
2. Execute o cmdlet Set-AzureRmDataFactoryV2Pipeline para criar o pipeline: IncrementalCopyPipeline.
    
   ```powershell
   Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Veja o exemplo de saída: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>Executar o pipeline

1. Crie um arquivo de parâmetro: **Parameters.json** na mesma pasta com o seguinte conteúdo:

    ```json
    {
        "tableList": 
        [
            {
                "TABLE_NAME": "customer_table",
                "WaterMark_Column": "LastModifytime",
                "TableType": "DataTypeforCustomerTable",
                "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
            },
            {
                "TABLE_NAME": "project_table",
                "WaterMark_Column": "Creationtime",
                "TableType": "DataTypeforProjectTable",
                "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
            }
        ]
    }
    ```
2. Execute o pipeline **IncrementalCopyPipeline** usando o cmdlet **Invoke-AzureRmDataFactoryV2Pipeline**. Substitua os espaços reservados com seus próprios nomes de grupo de recursos e de data factory.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

1. Faça logon no [portal do Azure](https://portal.azure.com).
2. Clique em **Mais serviços**, pesquise com a palavra-chave `data factories` e selecione **Data factories**. 

    ![Menu Data factories](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-data-factories-menu-1.png)
3. Procure **seu data factory** na lista de data factories e selecione-o para iniciar a página Data factory. 

    ![Procurar seu data factory](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-search-data-factory-2.png)
4. Na página Data factory, clique no bloco **Monitorar e Gerenciar**. 

    ![Bloco Monitorar e Gerenciar](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-monitor-manage-tile-3.png)    
5. O **Aplicativo de Integração de Dados** inicia em uma guia separada. Você pode ver todas as **execuções de pipeline** e seus status. Observe que, no exemplo a seguir, o status do pipeline de execução é **Com Êxito**. Você pode verificar os parâmetros passados para o pipeline ao clicar no link da coluna **Parâmetros**. Se houver um erro, você verá um link na coluna **Erro**. Clique no link na coluna **Ações**. 

    ![Execuções de pipeline](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-4.png)    
6. Quando você clicar no link na coluna **Ações**, verá a página a seguir, que mostra todas as **execuções de atividade** para o pipeline. 

    ![Execuções de atividade](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-activity-runs-5.png)
7. Para alternar novamente para a exibição **Pipeline é executado**, clique em **Pipelines** como mostrado na imagem. 

## <a name="review-the-results"></a>Revise os resultados
No SQL Server Management Studio, execute as seguintes consultas no banco de dados SQL do Azure de destino para verificar se os dados foram copiados das tabelas de origem para as tabelas de destino. 

**Consulta** 
```sql
select * from customer_table
```

**Saída**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Consulta:**

```sql
select * from project_table
```

**Saída**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Consulta**

```sql
select * from watermarktable
```

**Saída**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Observe que os valores de marca d'água para ambas as tabelas foram atualizados. 

## <a name="add-more-data-to-the-source-tables"></a>Adicionar mais dados às tabelas de origem

Execute a consulta a seguir feita no banco de dados do SQL Server de origem para atualizar uma linha existente na customer_table e insira uma nova linha na project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Executar novamente o pipeline

1. Agora, execute novamente o pipeline ao executar o seguinte comando do PowerShell:

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
2. Monitore as execuções d o pipeline seguindo as instruções da seção [Monitorar o pipeline](#monitor-the-pipeline). Como o pipeline de status é **Em Andamento**, você vê outro link de ação em **Ações** para cancelar a execução do pipeline. 

    ![Execuções de pipeline](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-6.png)    
3. Clique em **Atualizar** para atualizar a lista até que a execução do pipeline seja bem-sucedida. 

    ![Execuções de pipeline](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-succeded-7.png)
4. (opcional) clique no link **Exibir Execuções de Atividade** (ícone) em Ações para ver todas as execuções de atividade associadas a esta execução de pipeline. 

## <a name="review-final-results"></a>Examinar os resultados finais
No SQL Server Management Studio, execute as seguintes consultas no banco de dados de destino para verificar se os dados atualizados/novos foram copiados das tabelas de origem para as tabelas de destino. 

**Consulta** 
```sql
select * from customer_table
```

**Saída**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Observe os novos valores de Name e LastModifytime para a PersonID: 3. 

**Consulta:**

```sql
select * from project_table
```

**Saída**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Observe que a entrada NewProject foi adicionada a project_table. 

**Consulta**

```sql
select * from watermarktable
```

**Saída**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Observe que os valores de marca d'água para ambas as tabelas foram atualizados.
     
## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você realizou as seguintes etapas: 

> [!div class="checklist"]
> * Prepare os armazenamentos de dados de origem e de destino.
> * Criar uma fábrica de dados.
> * Criar um tempo de execução de integração (IR) auto-hospedado
> * Instalar o Integration Runtime 
> * Criar serviços vinculados. 
> * Crie os conjuntos de dados de origem, de coletor e de marca-d'água.
> * Crie, execute e monitore um pipeline.
> * Analisar resultados
> * Adicionar ou atualizar dados nas tabelas de origem
> * Executar novamente e monitorar o pipeline
> * Examinar os resultados finais 

Avance para o tutorial a seguir para saber mais sobre como transformar dados usando um cluster Spark no Azure:

> [!div class="nextstepaction"]
>[Carregar incrementalmente os dados do Banco de Dados SQL do Azure para o Armazenamento de Blobs do Azure usando a tecnologia Controle de Alterações](tutorial-incremental-copy-change-tracking-feature-powershell.md)


