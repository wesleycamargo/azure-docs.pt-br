<properties title="Get started using Azure Data Factory" pageTitle="Introdução ao uso do Azure Data Factory" description="Este tutorial mostra como criar um pipeline de dados de exemplo, que copia os dados de um blob para uma instância de banco de dados SQ do Azure." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="spelluru" />

# Introdução ao Azure Data Factory
Este artigo ajuda você a começar a usar o Azure Data Factory. O tutorial neste artigo mostra como criar uma data factory do Azure e como criar uma pipeline na data factory para copiar dados de exemplo de um armazenamento de blobs do Azure para um banco de dados SQL do Azure.

A lista a seguir fornece as etapas comuns que os desenvolvedores precisam executar: 

1.	Criar uma **data factory do Azure**.
2.	Criar **serviços vinculados** para vincular os armazenamentos de dados e os serviços de computação à data factory.  Por exemplo, um serviço vinculado pode vincular um banco de dados SQL do Azure ou um cluster HDInsight para a data factory.
3.	Crie **tabelas** que descrevem os dados de entrada e de saída para as pipelines. Tabelas também especificam o local real dos dados em armazenamentos de dados vinculados a uma data factory. Por exemplo, uma tabela pode especificar o nome da tabela SQL em um banco de dados SQL do Azure, (ou) um contêiner de blob em um blob do Azure. 
4.	Crie **pipelines**. Um pipeline consiste em uma ou mais atividades que consomem dados de entrada e geram dados de saída. Uma Atividade de Cópia copia dados de um armazenamento de dados de origem para um armazenamento de dados de destino e uma atividade HDInsight processa dados de entrada por meio de scripts Hive/Pig.  
5.	Especifique o **período ativo** de pipelines (para processamento de dados). O período ativo define a duração de tempo em que serão produzidas fatias de dados. É possível especificar a data e a hora de início e término para um pipeline (ou) que seja executada o tempo todo.

Neste tutorial, você irá: 

1.	Utilize o **Portal de Visualização do Azure** para criar uma data factory do Azure e serviços vinculados para armazenamentos de dados.
2.	Use o **PowerShell do Azure** para criar tabelas e uma pipeline. O portal não oferece suporte para a criação de tabelas e pipelines nessa versão


##Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

- Uma assinatura do Azure. Para obter informações sobre como adquirir uma assinatura, consulte [Opções de compra] [azure-purchase-options], [ofertas para membros][azure-member-offers] ou [Avaliação Gratuita][azure-free-trial].
- Baixe e instale o [PowerShell do Azure][download-azure-powershell] em seu computador.
- Leia o tópico [Introdução à Data Factory do Azure][data-factory-introduction].
- Conta de Armazenamento do Azure. Você utilizará o armazenamento de blob como um armazenamento de dados de origem nesse tutorial. Consulte [Sobre Contas de Armazenamento][data-factory-create-storage] para conhecer as etapas para riar um armazenamento do Azure.
- Banco de dados SQL do Azure. Você criará um banco de dados de exemplo e o usará como um armazenamento de dados de destino nesse tutorial. Consulte [Como criar e configurar um Banco de Dados do SQL Azure][data-factory-create-sql-database] para conhecer as etapas para criar um banco de dados do SQL Azure.

##Nesse tutorial

Etapa | Descrição
------ | ---------------
[Etapa 1: Criar um Data Factory do Azure](#CreateDataFactory) | Nesta etapa, você criará uma data factory do Azure denominada **ADFTutorialDataFactory**. 
[Etapa 2: Criar serviços vinculados](#CreateLinkedServices) Nesta etapa, você criará dois serviços vinculados: **MyBlobStore** e **MyAzureSQLStore**. O MyBlobStore vincula um armazenamento do Azure e o MyAzureSQLStore vincula um banco de dados SQL do Azure para o ADFTutorialDataFactory.
[Etapa 3: Criar conjuntos de dados de entrada e saída](#CreateInputAndOutputDataSets) | Nesta etapa, você definirá dois conjuntos de dados (**EmpTableFromBlob** e **EmpSQLTable**) que são usados como entrada e saída para a **Atividade de Cópia** no ADFTutorialPipeline que você criará na próxima etapa.
[Etapa 4: Criar e executar um pipeline](#CreateAndRunAPipeline) | Nesta etapa, você criará uma pipeline de chamada **ADFTutorialPipeline**. A pipeline terá uma **Atividade de Cópia** que copia dados de um blob do Azure para uma tabela de banco de dados de saída do Azure.
[Etapa 5: Conjuntos de dados do monitor e pipeline](#MonitorDataSetsAndPipeline) | Nesta etapa, você irá monitorar os conjuntos de dados e a pipeline usando o Management Studio do Azure nesta etapa.
 


## <a name="CreateDataFactory"></a>Etapa 1: Criar uma Data Factory do Azure
Nesta etapa é possível utilizar o Portal de Visualização do Azure para criar uma data factory do Azure denominada **ADFTutorialDataFactory**.

1.	Após fazer logon na [Portal de Visualização do Azure][azure-preview-portal], clique em **novo** no canto inferior esquerdo e clique em **Data Factory** na folha**Nova**. 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Se você não vir **Data Factory** na folha **Novo**, role para baixo.  


6. Na folha **Nova data factory**:
	1. Digite **ADFTutorialDataFactory** para **nome**. 
	
  		![New data factory blade][image-data-factory-getstarted-new-data-factory-blade]
	2. Clique no **NOME DO GRUPO DE RECURSOS** e faça o seguinte:
		1. Clique em **Criar um novo grupo de recursos**.
		2. Na folha **Criar grupo de recursos**, digite **ADFTutorialResourceGroup** para o **nome** do grupo de recursos e clique em **OK**. 

			![Create Resource Group][image-data-factory-create-resource-group]

		Algumas das etapas neste tutorial supõem que você utilize o grupo de recursos denominado **ADFTutorialResourceGroup**. Se você utilizar um grupo de recursos diferentes, será necessário utilizar o grupo de recursos que você selecionou aqui em vez de ADFTutorialResourceGroup.  
7. Na folha **Nova data factory**, observe que **Adicionar ao Quadro Inicial** está selecionada.
8. Clique em **Criar** na folha **Nova Data Factory**.

	> [WACOM.NOTE] O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **O nome da data factory "ADFTutorialDataFactory" não está disponível**, altere o nome (por exemplo, yournameADFTutorialDataFactory). Use esse nome em vez de ADFTutorialFactory ao executar as etapas neste tutorial.  
	 
	![Data Factory name not available][image-data-factory-name-not-available]

9. Clique no hub de **NOTIFICAÇÕES** à esquerda e procure as notificações do processo de criação.
10. Após a conclusão da criação, você verá a folha Data Factory conforme mostrado abaixo
    ![Data factory home page][image-data-factory-get-stated-factory-home-page]

11. É possível também abri-lo no **Quadro Inicial** conforme mostrado abaixo, clicando no **ADFTutorialFactory** 

    ![Startboard][image-data-factory-get-started-startboard]    

## <a name="CreateLinkedServices"></a>Etapa 2: Criar serviços vinculados
Serviços vinculados vinculam armazenamentos de dados ou serviços de computação para uma data factory do Azure. Um armazenamento de dados pode ser um armazenamento do Azure, um banco de dados SQL ou um banco de dados do SQL Server local.

Nesta etapa, você criará dois serviços vinculados: **MyBlobStore** e **MyAzureSQLStore**. O serviço vinculado MyBlobStore vincula uma conta de armazenamento do Azure e o MyAzureSQLStore vincula um banco de dados SQL do Azure ao **ADFTutorialDataFactory**. Você criará uma pipeline posteriormente neste tutorial que copia dados de um contêiner de blob no MyBlobStore em uma tabela SQL no MyAzureSQLStore.

### Criar um serviço vinculado para uma conta de armazenamento do Azure
1.	Na folha **DATA FACTORY**, clique no bloco **Serviços Vinculados** para iniciar a folha **Serviços Vinculados**.

    ![Linked services link][image-data-factory-get-started-linked-services-link]

2. Na folha **Serviços Vinculados**, clique em **Adicionar armazenamento de dados**.

    ![Linked services add store][image-data-factory-get-started-linked-services-add-store-button]

3. Na folha **Novo armazenamento de dados**:  
	1. Insira um **nome** para o armazenamento de dados. Para o tutorial, digite**MyBlobStore** para o **nome**.
	2. Insira a **descrição** (opcional) para o armazenamento de dados.
	3. Clique em **Tipo**.
	4. Selecione a **Conta de armazenamento do Azure** e clique em **OK**.
	
    ![New data store button][image-data-factory-linked-services-get-started-new-data-store]
  
4.  Agora, você está novamente na folha **Novo armazenamento de dados** que tem a seguinte aparência:

    ![New data store blade][image-data-factory-get-started-new-data-store-with-storage]

5. Insira o **Nome da Conta** e a **Chave de Conta** para sua Conta de Armazenamento do Azure e clique em **OK**.   

6. Depois de clicar em **OK** na folha **Novo armazenamento de dados** lâmina, você deverá consultar **myblobstore** na lista de **ARMAZENAMENTOS DE DADOS** na folha **Serviços Vinculados**. Verifique o hub de **NOTIFICAÇÕES** (à esquerda) quanto a quaisquer mensagens.

    ![linked services with blob store][image-data-factory-get-started-linked-services-list-with-myblobstore]

### Criar um serviço vinculado para um banco de dados SQL do Azure
1. Na folha **Serviços Vinculados**, clique em **Adicionar armazenamento de dados** na barra de ferramentas para adicionar outra fonte de dados (banco de dados SQL do Azure).
2. Na folha Novo armazenamento de dados:
	1. Insira um **nome** para o armazenamento de dados. NOME.Para o tutorial, digite **MyAzureSQLStore** para o **NOME**. 
	2. Digite a **DESCRIÇÃO (opcional)** para o repositório.
	3. Clique em **Tipo** e selecione o **Banco de dados SQL do Azure**.
3. Digite **Servidor**, **banco de dados**, **nome de usuário**, e **senha** para o banco de dados do SQL Azure e clique em **OK**.

    ![Azure SQL properties][image-data-factory-get-started-linked-azure-sql-properties]

	
4. Depois de clicar em **OK** na folha **Novo armazenamento de dados**, você deve visualizar ambos os repositórios na folha **Serviços Vinculados**

    ![Linked services with two stores][image-data-factory-get-started-linked-services-list-two-stores]
    

## <a name="CreateInputAndOutputDataSets"></a>Etapa 3: Criar tabelas de entrada e saída

Na etapa anterior, você criou os serviços vinculados **MyBlobStore** e **MyAzureSQLStore** para vincular uma conta de armazenamento do Azure e o banco de dados SQL do Azure para a data factory: **ADFTutorialDataFactory**. Nesta etapa, você criará tabelas que representam a entrada e saída de dados para a Atividade de cópia na pipeline, que será criada na próxima etapa. 

Uma tabela é um conjunto de dados retangular e é o único tipo de conjunto de dados que tem suporte no momento. A tabela de entrada se refere a um contêiner de blob no armazenamento do Azure que o MyBlobStore indica e a tabela de saída se refere a uma tabela SQL no banco de dados SQL do Azure que o MyAzureSQLStore indica.  
 
A criação dos conjuntos de dados e das pipelines não tem suporte no Portal de visualização do Azure no momento, utilize os cmdlets do PowerShell do Azure para criar tabelas e pipelines. Antes de criar tabelas, primeiro é necessário fazer o seguinte (etapas detalhadas seguem a lista).

* Crie um contêiner de blob denominado **adftutorial** no armazenamento de blobs do Azure que o MyBlobStore indicar. 
* Criar e carregar um arquivo de texto **emp.txt**, como um blob para o contêiner **adftutorial**. 
* Crie uma tabela denominada **emp** no banco de dados SQL do Azure que o MyAzureSQLStore indicar.
* Crie uma pasta denominada **ADFGetStarted** no disco rígido.  

### Preparar o armazenamento de blobs do Azure e o banco de dados SQL do Azure para o tutorial
1. Inicie o bloco de notas, cole o seguinte texto e salve-o como **emp.txt** na pasta **C:\ADFGetStarted** no disco rígido. 

        John, Doe
		Jane, Doe
				
2. Use ferramentas como o [Gerenciador de Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/) para criar o contêiner **adftutorial** e carregar o arquivo **emp.txt** no contêiner.

    ![Azure Storage Explorer][image-data-factory-get-started-storage-explorer]
3. Utilize o seguinte script SQL para criar a tabela **emp** no seu Banco de Dados SQL do Azure. Você pode usar o Console de Gerenciamento do SQL Azure para se conectar a um Banco de Dados do SQL Azure e executar o script SQL. Você também pode usar o Management Studio do SQL Server para realizar essa tarefa. 


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 
		GO
				
	Para iniciar o Console de Gerenciamento do SQL Azure, clique **GERENCIAR** conforme mostrado na imagem a seguir:
 
	![Launch Azure SQL Management Console][image-data-factory-sql-management-console]

	![Azure SQL Management Console][image-data-factory-sql-management-console-2]
### Criar tabela de entrada 
Uma tabela é um conjunto de dados retangular e tem um esquema. Nesta etapa, você criará uma tabela denominada **EmpBlobTable** que aponta para um contêiner de blob no Armazenamento do Azure representado pelo serviço vinculado **MyBlobStore**.


1. Crie um arquivo JSON para uma tabela da data factory que representa dados em emp.txt no blob. Inicie o bloco de notas, copie o seguinte script JSON e salve-o como EmpBlobTable.json na pasta C:\ADFGetStarted.


        {
     	    "name": "EmpTableFromBlob",
		    "properties":
    		{
        		"structure":  
       			 [ 
            		{ "name": "FirstName", "type": "String"},
            		{ "name": "LastName", "type": "String"}
        		],
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1,
            		"waitonexternal": {}
       		 	}
    		}
		}

		
     Observe o seguinte: 
	
	- **type** de localização é definido como **AzureBlobLocation**.
	- **linkedServiceName** (nome do serviço vinculado) é definido como **MyBlobStore**. Você criou esses serviços vinculados na etapa 2).
	- **folderPath** é definido como o **adftutorial** contêiner. É possível também especificar o nome de um blob dentro da pasta. Como você não está especificando o nome do blob, dados de todos os blobs no contêiner são considerados como um entrada de dados.  
	- o formato **type** é definido como **TextFormat**
	- - Há dois campos no arquivo de texto, - **FirstName** e **ColumnName** - separado por um caracter de vírgula (columDelimiter)	
	- A **availability** é definida como **hourly** (**frequency** definida como **hour** e **interval** definido como**1**). O serviço da Data Factory procurará dados de entrada a cada hora na pasta entrada no contêiner de blob (**adftutorial**) especificado.

	se você não especificar um **fileName** para uma **tabela de entrada**, todos os arquivos/blobs da pasta de entrada (**folderPath**) serão considerados como entradas. Se você especificar um nome de arquivo em JSON, apenas arquivo/blob especificado será considerado como entrada de asn. Consulte os arquivos do [tutorial][adf-tutorial] para obter exemplos.
 
	Se você não especificar um **fileName** para uma **tabela de saída**, os arquivos gerados no **folderPath** serão nomeados no seguinte formato: Data.<Guid>.txt (por exemplo: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Para definir **folderPath** e **fileName** dinamicamente com base na hora **SliceStart**, use a propriedade partitionedBy. No exemplo a seguir, folderPath usa o ano, mês e dia de SliceStart (hora de início da fatia que está sendo processada) e fileName usa a hora de SliceStart. Por exemplo, se uma fatia é produzida para 2014-10-20T08:00:00, o folderName é definido como wikidatagateway/wikisampledataout/2014/10/20 e o fileName é definido como 08.csv. 

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Consulte a [Referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.

2. Inicie o **PowerShell do Azure** e execute o seguinte comando para alternar para o modo **AzureResourceManager**. Os cmdlets do Data Factory do Azure estão disponíveis no modo **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		
	If you haven't already done so, do the following:


	- Executar **Add-AzureAccount** e insira o mesmo nome de usuário e senha que você usou para entrar no Portal de Visualização do Azure.  
	- Executar **Get-AzureSubscription** para exibir todas as assinaturas para essa conta.
	- Executar **Select-AzureSubscription** para selecionar a assinatura que você deseja trabalhar. Esta assinatura deve ser a mesma que você usou no Portal de Visualização do Azure.

3. Utilize o cmdlet **New-AzureDataFactoryTable** para criar a tabela de entrada utilizando o arquivo **EmpBlobTable.json**.


         Novo AzureDataFactoryTable - ResourceGroupName ADFTutorialResourceGroup - DataFactoryName ADFTutorialDataFactory - arquivo C:\ADFGetStarted\EmpBlobTable.json 

	Consulte a [Referência de cmdlets da Data Factory][cmdlet-reference] para obter detalhes sobre esses cmdlet e outros cmdlets da Data Factory.
 
### Criar tabela de saída
Nesta parte da etapa, você criará uma tabela de saída denominada **EmpSQLTable** que aponta para uma tabela SQL no banco de dados SQL do Azure que é representado pelo serviço **MyAzureSQLStore**. 

1. Crie um arquivo JSON para uma tabela da Data Factory que representa os dados no banco de dados SQL do Azure. Inicie o bloco de notas, copie o seguinte script JSON e salve-o como **EmpSQLTable.json** na pasta **C:\ADFGetStarted**.



        {
    		"name": "EmpSQLTable",
    		"properties":
    		{
        		"structure":
        		[
                	{ "name": "FirstName", "type": "String"},
                	{ "name": "LastName", "type": "String"}
        		],
        		"location":
        		{
            		"type": "AzureSQLTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "MyAzureSQLStore"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1            
        		}
    		}
		}


		
     Observe o seguinte: 
	
	* location **type** é definido como **AzureSQLTableLocation**.
	* **linkedServiceName** é definido como **MyAzureSQLStore** (você criou esse serviço vinculado na etapa 2).
	* **tablename** é definido como **emp**.
	* Há três colunas - **ID**, **FirstName**, e **LastName** - na tabela emp no banco de dados, mas a ID é uma coluna de identidade, portanto você precisa especificar somente **FirstName** e **LastName** aqui.
	* A **availability** é definida como **hourly** (frequency definida como hour e interval definido como 1).  O serviço da data factory gerará uma fatia de dados de saída a cada hora na tabela **emp** no banco de dados SQL do Azure.


2. No **PowerShell do Azure**, execute o seguinte comando para criar outra tabela da data factory para representar a tabela **emp** no banco de dados SQL do Azure.



         New-AzureDataFactoryTable -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpSQLTable.json -ResourceGroupName ADFTutorialResourceGroup 



## <a name="CreateAndRunAPipeline"></a>Etapa 4: Criar e executar uma pipeline
Nesta etapa, você criará uma pipeline com uma **Atividade de Cópia** que utiliza **EmpTableFromBlob** como entrada e **EmpSQLTable** como saída.

1. Crie um arquivo JSON para a pipeline. Inicie o bloco de notas, copie o seguinte script JSON e salve-o como **ADFTutorialPipeline.json** na pasta **C:\ADFGetStarted**.


         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Sql Azure",
						"type": "CopyActivity",
						"inputs": [ {"name": "EmpTableFromBlob"} ],
						"outputs": [ {"name": "EmpSQLTable"} ],		
						"transformation":
						{
							"source":
							{                               
								"type": "BlobSource"
							},
							"sink":
							{
								"type": "SqlSink"
							}	
						},
						"Policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"style": "StartOfInterval",
							"retry": 0,
							"timeout": "01:00:00"
						}		
					}
        		]
      		}
		} 

	Observe o seguinte:

	- Na seção de atividades, há apenas uma atividade cujo **type** é definido como **CopyActivity**.
	- A entrada da atividade é definida como **EmpTableFromBlob** e a saída da atividade é definida como **EmpSQLTable**.
	- Na seção **transformação**, o **BlobSource** é especificado como o tipo de fonte e o **SqlSink** é especificado como o tipo de coletor.

2. Use o cmdlet **New-AzureDataFactoryPipeline** para criar uma pipeline usando o arquivo **ADFTutorialPipeline.json** criado.



         New-AzureDataFactoryPipeline  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\ADFTutorialPipeline.json  

3. Depois que as pipelines são criadas, você pode especificar a duração em que o processamento de dados ocorrerá. Especificando o período ativo de uma pipeline, você está definindo a duração de tempo em que as fatias de dados serão processadas com base nas propriedades de disponibilidade que foram definidas para cada tabela do Azure Data Factory.  Execute o seguinte comando do PowerShell para definir o período ativo na pipeline e digite Y para confirmar. 



         Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline  

	> [WACOM.NOTE] Substitua o valor **StartDateTime** pelo dia atual e o valor **EndDateTime** pelo dia seguinte. StartDateTime e EndDateTime são horas UTC (Tempo Universal Coordenado) e devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O **EndDateTime** é opcional, mas o usaremos neste tutorial. 
	> Se você não especificar **EndDateTime**, ele será calculado como "**StartDateTime + 48 horas**". Para executar a pipeline indefinidamente, especifique **9/9/9999** como o **EndDateTime**.  
	
	No exemplo acima, como cada fatia de dados é produzida por hora, haverá 24 fatias de dados.

4. No **Portal do Azure**, na folha **DATA FACTORY** da **ADFTutorialDataFactory**, clique no **Diagrama**.

	![Diagram link][image-data-factory-get-started-diagram-link]
  
5. Você deverá ver o diagrama semelhante ao seguinte: (Clique duas vezes em um título para ver detalhes sobre o artefato que a folha representa).

	![Diagram view][image-data-factory-get-started-diagram-blade]

**Parabéns!** Você criou uma data factory do Azure, serviços vinculados, tabelas e uma pipeline e a pipeline agendada com êxito.

## <a name="MonitorDataSetsAndPipeline"></a>Etapa 5: Monitorar os conjuntos de dados e o pipeline
Nesta etapa, você utilizará o Portal do Azure para monitorar o que está acontecendo em um data factory do Azure. Você também pode usar os cmdlets do PowerShell para monitorar conjuntos de dados e pipelines. Para obter detalhes sobre como utilizar os cmdlets para monitoramento, consulte [Monitorar e gerenciar a Data Factory utilizando os cmdlets do PowerShell][monitor-manage-using-powershell].

1. Navegue até o [Portal do Azure (Visualização)][azure-preview-portal] se você não estiver com ele aberto. 
2. Se a folha **ADFTutorialDataFactory** não estiver aberta, abra-a clicando em **ADFTutorialDataFactory** no **Quadro inicial**. 
3. Você verá a contagem e os nomes das tabelas e do pipeline criados nessa folha.

	![home page with names][image-data-factory-get-started-home-page-pipeline-tables]

4. Agora, clique no bloco **Conjuntos de Dados**.
5. Na folha **Conjuntos de dados**, clique em **EmpTableFromBlob**. Esta é a tabela de entrada para a **ADFTutorialPipeline**.

	![Datasets with EmpTableFromBlob selected][image-data-factory-get-started-datasets-emptable-selected]   
5. Observe que as fatias de dados até o momento atual já foram produzidas e estão **prontas**, porque o arquivo **emp.txt** existe sempre no contêiner de blob: **adftutorial\input**. Confirme se nenhuma fatia aparecerá na seção **Fatias com problema** na parte inferior.
6. Agora, na folha **conjuntos de dados**, clique em **EmpSQLTable**. Esta é a tabela de saída para a **ADFTutorialPipeline**.

	![data sets blade][image-data-factory-get-started-datasets-blade]

6. Você deve ver a folha **EmpSQLTable** conforme mostrado abaixo:

	![table blade][image-data-factory-get-started-table-blade]
 
7. Observe que as fatias de dados até o momento atual já foram produzidas e estão **Prontas**. Nenhuma fatia apareceu na seção **Fatias com problemas** na parte inferior.
8. Clique em **... (Reticências)** para ver todas as fatias.

	![data slices blade][image-data-factory-get-started-dataslices-blade]

9. Clique em qualquer fatia de dados na lista e você verá a folha **FATIA DE DADOS**.

	![data slice blade][image-data-factory-get-started-dataslice-blade]
  
11. Na folha **FATIA DE DADOS**, você deve ver que toda a atividade é executada na lista na parte inferior. Clique em uma **execução de atividade** para ver a folha **DETALHES DE EXECUÇÃO DE ATIVIDADE**. 

	![Activity Run Details][image-data-factory-get-started-activity-run-details]

12. Clique em **X **para fechar todas folhas até voltar a folha inicial para **ADFTutorialDataFactory**.
14. (opcional) Clique em **Pipelines** na home page do **ADFTutorialDataFactory**, clique em **ADFTutorialPipeline** na folha **Pipelines** e pesquise nas tabelas de entrada (**Consumida**) ou tabelas de saída (**Produzida**).
15. Inicie o **SQL Server Management Studio**, conecte-se ao banco de dados SQL do Azure e verifique as linhas que são inseridas na tabela **emp** no banco de dados.

	![sql query results][image-data-factory-get-started-sql-query-results]


## Resumo 
Neste tutorial, você criou uma data factory do Azure para copiar dados de um blob do Azure para um banco de dados SQL do Azure. Você utilizou o Portal de visualização do Azure para criar a data factory e serviços vinculados. Os cmdlets do PowerShell do Azure foram utilizados para criar tabelas e uma pipeline e, em seguida, agendados para a pipeline. Aqui estão as etapas de alto nível executadas nesse tutorial:  

1.	Criar uma **data factory**do Azure.
2.	Crie **serviços vinculados** que vinculam armazenamentos de dados e computação (mencionados como **serviços vinculado**) para a data factory.
3.	Crie **tabelas** que descrevem os dados de entrada e de saída para as pipelines.
4.	Crie **pipelines**. Uma pipeline consiste em uma ou mais atividades e processa as entradas e produz as saídas. 
5.	Especifique o **período ativo** de pipelines (para processamento de dados). O período ativo define a duração de tempo em que serão produzidas as fatias de dados.

## Próximas etapas

Artigo | Descrição
------ | ---------------
[Tutorial: Mover e processar arquivos de log usando a Data Factory][adf-tutorial] | Este artigo fornece um **passo a passo** que mostra como implementar um **cenário próximo do real** usando a Data Factory do Azure para transformar dados de arquivos de log em informações.
[Copie os dados com a Data factory do Azure - Atividade de Cópia][copy-activity] | Este artigo fornece uma descrição detalhada da **Atividade de Cópia** utilizada neste tutorial.
[Habilitar suas pipelines para trabalhar com dados locais][use-onpremises-datasources] | Este artigo tem um passo a passo que mostra como copiar dados de um **banco de dados SQL Server local** em um blob do Azure.
[Usar o Pig e o Hive com o Data Factory][use-pig-and-hive-with-data-factory] | Este artigo tem um passo a passo que mostra como usar a **Atividade de HDInsight** para executar um script do **hive/pig** para processar dados de entrada a fim de gerar dados de saída.
[Usar atividades personalizadas em uma Data Factory][use-custom-activities] | Este artigo fornece um passo a passo com instruções para criar uma **atividade personalizada** e usá-la em uma pipeline.
[Monitorar e gerenciar a Data Factory do Azure utilizando o PowerShell][monitor-manage-using-powershell] | Este artigo descreve como **monitorar e gerenciar** uma Data Factory do Azure utilizando os **cmdlets do PowerShell do Azure**. Você pode testar os exemplos neste artigo sobre ADFTutorialDataFactory.
[Solucionar problemas de Data Factory][troubleshoot]| Este artigo descreve como **solucionar problemas** da Data Factory do Azure. Você pode testar o passo a passo neste artigo no ADFTutorialDataFactory introduzindo um erro (excluindo a tabela no Banco de Dados SQL do Azure). 
[Referência de cmdlet do Data Factory do Azure][cmdlet-reference] | Este conteúdo de referência apresenta detalhes sobre todos os **cmdlets do Data Factory**.
[Referência do Desenvolvedor da Data Factory do Azure][developer-reference]A Referência do Desenvolvedor tem um conteúdo de referência abrangente de cmdlets, script JSON, funções, etc... 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pt-br/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pt-br/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pt-br/pricing/free-trial/

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/pt-br/documentation/articles/install-configure-powershell

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[copy-activity]: ../data-factory-copy-activity/
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction
[data-factory-create-storage]: ../storage-whatis-account
[data-factory-create-sql-database]: ../sql-database-create-configure/


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png

<!--HONumber=35.2-->
