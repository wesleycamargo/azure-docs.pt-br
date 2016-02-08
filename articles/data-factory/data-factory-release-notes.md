<properties 
	pageTitle="Data Factory - Notas de Versão | Microsoft Azure" 
	description="Notas de versão da Data Factory" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# Notas de versão do Data Factory do Azure
Consulte o artigo [Data Factory - Log de Alterações do API .NET](data-factory-api-change-log.md) para saber mais sobre as alterações para a SDK do .NET da Data Factory em uma versão específica.

## Notas da versão de 07/17/2015 da Data Factory
As seguintes alterações JSON foram introduzidas na versão de julho de 2015 do Azure PowerShell.

## Atualizar para tipos de serviços, tabelas e atividades vinculados
Tipo de recurso | Nome atual em JSON | Novo nome em JSON
------------- | -------------------- | ----------------
Serviço vinculado (fonte de dados) | AzureSqlLinkedService | AzureSqlDatabase
Serviço vinculado (fonte de dados) | AzureStorageLinkedService | AzureStorage
Serviço vinculado (fonte de dados) | DocumentDbLinkedService | Banco de Dados de Documentos
Serviço vinculado (fonte de dados) | OnPremisesFileSystemLinkedService | OnPremisesFileServer
Serviço vinculado (fonte de dados) | OnPremisesOracleLinkedService | OnPremisesOracle
Serviço vinculado (fonte de dados) | OnPremisesSqlLinkedService | OnPremisesSqlServer
Serviço vinculado (fonte de dados) | OnPremisesMySqlLinkedService | OnPremisesMySql
Serviço vinculado (fonte de dados) | OnPremisesDb2LinkedService | OnPremisesDb2
Serviço vinculado (fonte de dados) | OnPremisesTeradataLinkedService | OnPremisesTeradata
Serviço vinculado (fonte de dados) | OnPremisesSybaseLinkedService | OnPremisesSybase
Serviço vinculado (fonte de dados) | OnPremisesPostgreSqlLinkedService | OnPremisesPostgreSql
Serviço vinculado (computação) | AzureMlLinkedService | AzureML
Serviço vinculado (computação) | HDInsightBYOCLinkedService | HDInsight
Serviço vinculado (computação) | HDInsightOnDemandLinkedService | HDInsightOnDemand
Serviço vinculado (computação) | AzureBatchLinkedService | AzureBatch
Conjunto de dados | AzureBlobLocation | AzureBlob
Conjunto de dados | AzureTableLocation | AzureTable
Conjunto de dados | AzureSqlTableLocation | AzureSqlTable
Conjunto de dados | DocumentDbCollectionLocation | DocumentDbCollection 
Conjunto de dados | OnPremisesFileSystemLocation | FileShare
Conjunto de dados | OnPremisesOracleTableLocation | OracleTable
Conjunto de dados | OnPremisesSqlServerTableLocation | SqlServerTable
Conjunto de dados | RelationTableLocation | RelationalTable
Atividade | CopyActivity | Copiar
Atividade | HDInsightActivity (transformação do Hive) | HDInsightHive
Atividade | HDInsightActivity (transformação Pig) | HDInsightPig
Atividade | HDInsightActivity (transformação de MapReduce) | HDInsightMapReduce
Atividade | HDInsightActivity (Streaming) | HDInsightHadoopStreaming
Atividade | AzureMLBatchScoringActivity | AzureMLBatchScoring
Atividade | StoredProcedureActivity | SqlServerStoredProcedure

## Novo elemento typeProperties
O novo elemento **typeProperties** contém propriedades específicas de tipo para um serviço/tabela/atividade vinculados.

### Antigo JSON de serviço vinculado
	{
	    "name": "StorageLinkedService",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

### Novo JOSN de serviço vinculado
	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

Observe o seguinte:

- A propriedade **tipo** é movida um nível para cima e definida para **AzureStorage** (alteração de **AzureStorageLinkedService** para **AzureStorage**) 
- Novo elemento **typeProperties** que contém as propriedades que têm suporte no serviço de armazenamento do Azure vinculado (**connectionString** neste exemplo).  

### Conjunto de dados antigo do JSON
	{
	    "name": "EmpTable",
	    "properties":
	    {
	        "location":
	        {
	            "type": "AzureTableLocation",
	            "tableName": "myazuretable",
	            "linkedServiceName": "MyLinkedService"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

### Novo conjunto de dados do JSON

	{
	    "name": "EmpTable",
	    "properties": {
	        "type": "AzureTable",
	        "linkedServiceName": "MyLinkedServiceName",
	        "typeProperties": {
	            "tableName": "myazuretable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

Observe o seguinte:

- A propriedade **type** foi movida um nível acima e o nome do tipo **AzureTableLocation** foi alterado para **AzureTable**.
- O **linkedServiceName** foi movido um nível para cima. 
- O elemento **local** é removido agora e as propriedades específicas de tipo como **tableName** que foram especificados na seção **local** são especificadas na nova seção **typeProperties**.  

### Atividade antiga do JSON

	{
	    "name": "CopyFromSQLtoBlob   ",
	    "description": "description", 
	    "type": "CopyActivity",
	    "inputs":  [ { "name": "InputSqlDA"  } ],
	    "outputs":  [ { "name": "OutputBlobDA" } ],
	    "transformation":
	    {
	        "source":
	        {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        }
	    }
	}   

### Nova atividade do JSON
	
	{
	    "name": "CopyFromSQLtoBlob   ",
	    "description": "description", 
	    "type": "Copy",
	    "inputs":  [ { "name": "InputSqlDA"  } ],
	    "outputs":  [ { "name": "OutputBlobDA" } ],
	    "typeProperties":
	    {
	        "source":
	        {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        }
	    }
	}

Observe o seguinte:

- Observe que o elemento **transformation** foi substituído pelo novo elemento **typeProperties**.

## O elemento waitOnExternal é removido
O elemento **waitOnExternal** é substituído pelas novas propriedades **external** e **externalData**.

### JSON antigo
	{
	    "name": "EmpTableFromBlob",
	    "properties":
	    {
	        "location": 
	        {
	            "type": "AzureBlobLocation",
	            "folderPath": "adftutorial/",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            },
	            "linkedServiceName": "StorageLinkedService"
	        },
	        "availability": 
	        {
	            "frequency": "hour",
	            "interval": 1,
                "waitOnExternal": 
				{
			        "retryInterval": "00:01:00",
			        "retryTimeout": "00:10:00",
			        "maximumRetry": "3"			
				}
	        }
	    }
	} 

### Novo JSON
	{
	  "name": "EmpTableFromBlob",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "adftutorial/",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ","
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": "3"
	      }
	    }
	  }
	}

Observe o seguinte:

- A propriedade **waitOnExternal** é removida da seção **disponibilidade** 
- Uma nova propriedade **external** é adicionada um nível acima e definida como **true** para uma tabela externa. 
- As propriedades do elemento **waitOnExternal**, como **retryInterval**, são adicionados à nova seção **externalData** no elemento **Política**.
- O elemento **externalData** é um elemento opcional. 
- Quando você usa o elemento **externalData**, você deve ter a propriedade **external** definida para **true**. 
 

## Nova propriedade copyBehavior para o BlobSink
O **BlobSink** dá suporte à nova propriedade chamada: **copyBehavior**. Essa propriedade define o comportamento de cópia quando a origem for **BlobSource** ou **FileSystem**. Há três valores possíveis para a propriedade **copyBehavior**.

**PreserveHierarchy**: preserva a hierarquia de arquivos na pasta de destino, ou seja, o caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.


**FlattenHierarchy**: todos os arquivos da pasta de origem estarão no primeiro nível da pasta de destino. Os arquivos de destino terão o nome gerado automaticamente.


**MergeFiles**: mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/blob for especificado, o nome do arquivo mesclado será o nome especificado; caso contrário, será o nome de arquivo gerado automaticamente.
 
## Nova propriedade getDebugInfo para todas as atividades do HDInsight
As atividades do HDInsight (Hive, Pig, MapReduce, Streaming do Hadoop) oferecem suporte à nova propriedade: **getDebugInfo**. A propriedade **getDebugInfo** é um elemento opcional. Quando ela é definida como **Falha**, os logs são baixados somente em caso de falha de execução. Quando ela é definida como **Todos**, os logs sempre são baixados, não importa o status de execução. Quando ela é definida para **Nenhum**, nenhum log é baixado.

  
     

## Notas da versão de 10/04/2015 da Data Factory
Você verá as listas **Fatias atualizadas recentemente** e **Fatias com falha recente** na folha **TABELA** agora. Essas listas são classificadas segundo o horário de atualização da fatia. A hora de atualização de uma fatia é alterada nas situações a seguir.

-  Você atualiza o status da fatia manualmente, por exemplo, usando o **Set-AzureRmDataFactorySliceStatus** (ou) clicando em **EXECUTAR** na folha **FATIA** da fatia.
-  A fatia é alterada devido a uma execução (por exemplo, uma execução iniciada, uma execução finalizada e com falha, uma execução finalizada e bem-sucedida, etc).

Clique no título das listas ou em **... (reticências)** para ver a lista maior de fatias. Clique em **Filtrar** na barra de ferramentas para filtrar as fatias.
 
Você ainda poderá exibir fatias classificadas segundo os horários da fatia clicando no bloco **Fatias de dados (por horário da fatia)**. As fatias nessas coleções são ordenadas segundo o horário da fatia. Por exemplo, se for um agendamento por hora, as fatias serão: - 4/4/2015 às 17h em andamento - 4/4/2015 às 16h Bem-sucedida - 4/4/2015 15h Falhou

Porém, se uma fatia mais antiga for executada novamente, ela não aparecerá na parte superior desta lista mesmo que provavelmente seja nisso que o usuário esteja mais interessado.

## Notas da versão de 31/03/2015 da Data Factory
- Atualizado **Gateway de gerenciamento de dados**, pacote de instalação foi lançado no [Centro de Download da Microsoft][adf-gateway-download].
- Agora há suporte para cópia por meio do **sistema de arquivos local para blob do Azure**. Para obter mais informações, consulte os tópicos a seguir.
	-  [Serviço vinculado de sistema de arquivos local](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [Propriedades de OnPremisesFileSystemLocation em uma tabela de JSON](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [Origens e coletores com suporte](https://msdn.microsoft.com/library/dn894007.aspx). Consulte a matriz de cópia atualizada e as propriedades **FileSystemSource**. 
-  Agora há suporte para cópia por meio do **Banco de dados Oracle local para blob do Azure**. Para obter mais informações, consulte os tópicos a seguir. 
	-  [Serviço vinculado Oracle local](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [Propriedades de OnPremisesOracleTableLocation em uma tabela de JSON](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [Origens e coletores com suporte](https://msdn.microsoft.com/library/dn894007.aspx). Consulte a matriz de cópia atualizada e as propriedades **OracleSource**.
-  Você pode especificar a codificação para arquivos de texto em um Blob do Azure. Consulte a nova [propriedade encodingName](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob). 
- Você pode invocar um procedimento armazenado com parâmetros adicionais ao copiar em um coletor de SQL.    

Consulte a postagem de blog: [Atualização da Azure Data Factory - novos armazenamentos de dados](https://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/) para obter mais informações, incluindo exemplos.

## Notas da versão de 27/02/2015 da Data Factory

### Novos aprimoramentos
- **Editor da Azure Data Factory**. O Editor do Data Factory, que faz parte do Portal do Azure, permite criar, editar e implantar arquivos JSON que definem os pipelines, conjuntos de dados e serviços vinculados. O principal objetivo do editor é fornecer uma interface do usuário rápida e leve para criar artefatos do Azure Data Factory sem a necessidade de instalar o Azure PowerShell, possibilitando um uso mais ágil dos cmdlets do PowerShell. Consulte a postagem do blog [Editor da Azure Data Factory - um editor Web leve][adf-editor-blog] para uma visão geral e um vídeo sobre o Editor do Data Factory.  

### Alterações

## Notas da versão de 26/01/2015 da Data Factory ##

### Alterações
- Atualizado **Gateway de gerenciamento de dados**, pacote de instalação foi lançado no [Centro de Download da Microsoft][adf-gateway-download]. A partir desta versão, você pode encontrar o Gateway de gerenciamento de dados mais recente para usar com a Azure Data Factory nesse local de download. Esse pacote de instalação atende à Azure Data Factory e ao Power BI para serviços do Office 365. Se você estiver usando o serviços, observe que gateways para Data Factory e Power BI devem ser instalados em computadores diferentes e configurados diferentemente, de acordo com a orientação da documentação da Data Factory ou Power BI.
- A **Atividade de Cópia** agora dá suporte para copiar dados entre o banco de dados do SQL Server local e um banco de dados SQL do Azure. 
- **SqlSink** dá suporte a uma nova propriedade: **WriteBatchTimeout**. Essa propriedade oferece a flexibilidade para configurar quanto tempo esperar para que a operação de inserção em lotes seja concluída antes da operação expirar. Para obter uma cópia híbrida (operação de cópia que envolve uma fonte de dados local e uma fonte de dados na nuvem), você deve ter o gateway da versão 1.4 ou superior para usar esta propriedade. 
- O **serviço vinculado do SQL Server** agora dá suporte a **autenticação do Windows**. 
	- Ao criar um serviço vinculado do SQL Server usando o portal, você agora pode optar por usar a autenticação do Windows e definir as credenciais apropriadas. Isso requer que você tenha o gateway de versão 1.4 ou superior. 
	- Ao criar um serviço vinculado do SQL Server usando o Azure PowerShell, você pode especificar informações de conexão em texto sem formatação ou criptografar as informações de conexão usando um [cmdlet New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) atualizado e, em seguida, usar a cadeia de caracteres criptografada para a propriedade Cadeia de Conexão no serviço vinculado JSON. O recurso de criptografia ainda não tem suporte pelo cmdlet New-AzureRmDataFactoryEncryptValue. 

## Notas da versão de 12/11/2014 da Data Factory ##

### Novos aprimoramentos

- Integração de Aprendizado de Máquina do Azure
	- Esta versão do serviço Azure Data Factory permite que você integre a Azure Data Factory com o AM (Aprendizado de Máquina) do Azure usando **AzureMLLinkedService** e **AzureMLBatchScoringActivity**. Consulte [Criar pipelines de previsão usando a Data Factory e o Aprendizado de Máquina do Azure][adf-azure-ml] para obter detalhes. 
- É fornecido o status da versão do gateway
	- O status de "NewVersionAvailable" será mostrado no Portal do Azure e na saída do cmdlet Get-AzureRmDataFactoryGateway, se houver uma versão mais recente do gateway disponível do que a que está instalada no momento. Você pode seguir o trajeto do portal para baixar o novo arquivo de instalação (. msi) e executá-lo para instalar o gateway mais recente. Não é necessária nenhuma configuração adicional.

### Alterações

- O JobsContainer em HdInsightOnDemandLinkedService é removido.
	- Na definição de JSON para um HDInsightOnDemandLinkedService, você não precisa mais especificar a propriedade **jobsContainer**. Se você tiver a propriedade especificada para um serviço vinculado sob demanda, a propriedade será ignorada. Você pode remover a propriedade da definição do JSON para o serviço vinculado e atualizar a definição de serviço vinculada usando o cmdlet New-AzureRmDataFactoryLinkedService.
- Parâmetros de configuração opcional para HDInsightOnDemandLinkedService
	- Esta versão apresenta suporte para alguns parâmetros de configuração opcional para HDInsightOnDemandLinked (cluster de HDInsight sob demanda). Consulte [Propriedades ClusterCreateParameters][on-demand-hdi-parameters] para obter detalhes.
- O gateway local foi removido
	- Ao criar um gateway do Azure Data Factory por meio do portal ou do PowerShell (New-AzureRmDataFactoryGateway), você não precisa mais especificar o local para o gateway. A região do Data Factory será herdada. Da mesma forma, para configurar um serviço vinculado do SQL Server usando JSON, a propriedade "gatewayLocation" não é mais necessária. O SDK do .NET do Data Factory também é atualizado para refletir essas alterações.
	- Se você usar uma versão anterior do SDK e do PowerShell do Azure, ainda será necessário que você forneça a configuração local.
 
     

#### Alterações de última hora
	
- CustomActivity para DotNetActivity
	- A interface **ICustomActivity** é renomeada para **IDotNetActivity**. Você precisará atualizar os pacotes do NuGet do Data Factory e alterar o ICustomActivity para IDotNetActivity no código-fonte para a atividade personalizada.  
	- O tipo de atividade personalizada na definição de JSON para atividade personalizada deve ser alterado de **CustomActivity** para **DotNetActivity**. 
	- As classes **CustomActivity** e **CustomActivityProperties** foram renomeadas para **DotNetActivity** e **DotNetActivityProperties** com o mesmo conjunto de propriedades.

		Se você usar a versão mais antiga do SDK e do PowerShell do Azure, você pode continuar usando o CustomActivity em vez de DotNetActivity.
    
  		Consulte [Usar atividades personalizadas em um pipeline da Azure Data Factory][adf-custom-activities] para obter uma explicação passo a passo sobre como criar uma atividade personalizada e usá-la em um pipeline da Azure Data Factory.

[adf-azure-ml]: data-factory-azure-ml-batch-execution-activity.md
[adf-custom-activities]: data-factory-use-custom-activities.md

[adf-editor-video]: http://channel9.msdn.com/Blogs/Windows-Azure/New-Azure-Data-Factory-Editor-UI
[adf-editor-blog]: http://azure.microsoft.com/blog/2015/03/02/azure-data-factory-editor-a-light-weight-web-editor/
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx
[adf-gateway-download]: http://www.microsoft.com/download/details.aspx?id=39717
[adf-github-samples]: https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON
[adf-msdn-linked-services]: https://msdn.microsoft.com/library/dn834986.aspx
[adf-encrypt-value-cmdlet]: https://msdn.microsoft.com/library/dn834940.aspx



 

<!---HONumber=AcomDC_0128_2016-->