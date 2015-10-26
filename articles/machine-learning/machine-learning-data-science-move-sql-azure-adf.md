<properties
	pageTitle="Mover dados de um SQL Server local para o SQL Azure com o Azure Data Factory | Azure"
	description="Configure um pipeline do ADF que compõe duas atividades de migração de dados que movem os dados juntos diariamente entre bancos de dados locais e na nuvem."
	services="machine-learning"
	documentationCenter=""
	authors="fashah"
	manager="jacob.spoelstra"
	editor=""
	videoId=""
	scriptId="" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/06/2015"
	ms.author="fashah;bradsev" />


# Mover dados de um SQL Server local para o SQL Azure com o Azure Data Factory

Este tópico mostra como mover dados de um banco de dados do SQL Server local para um banco de dados do SQL Azure por meio do armazenamento de Blob do Azure usando o Azure Data Factory (ADF).

## <a name="intro"></a>Introdução: O que é o ADF e quando ele deve ser usado para migrar dados?

O Azure Data Factory é um serviço de integração de dados baseado em nuvem que automatiza a movimentação e a transformação dos dados. O conceito fundamental no modelo do ADF é o pipeline. Um pipeline é um agrupamento lógico de Atividades, cada um deles define as ações a serem executadas nos dados contidos em Conjuntos de dados. Serviços vinculados definem as informações necessárias para o Data Factory conectar-se a recursos de dados.

Com o ADF, serviços de processamento de dados existentes podem ser compostos em pipelines de dados que são altamente disponíveis e gerenciados na nuvem. Esses pipelines de dados podem ser agendados para ingerir, preparar, transformar, analisar e publicar os dados e o ADF irá gerenciar e orquestrar todos os dados complexos e dependências de processamento. As soluções podem ser criadas e implantadas rapidamente na nuvem, conectando um número crescente de fontes de dados em nuvem e locais.

Considere usar o ADF quando dados precisam ser migrados continuamente em um cenário híbrido que acessa os recursos locais e de nuvem e quando os dados são transacionados ou precisam ser modificados ou tiver lógica de negócios adicionada a eles durante a migração. O ADF permite o planejamento e monitoramento de trabalhos usando scripts simples de JSON que gerenciam a movimentação de dados em intervalos periódicos. O ADF também possui outros recursos, como suporte para operações complexas. Para obter mais informações sobre o ADF, consulte a documentação em [Azure Data Factory (ADF)](http://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>O cenário

Criamos um pipeline do ADF que compõe duas atividades de migração de dados que juntas movem dados diariamente entre um banco de dados do SQL local e um banco de dados do SQL Azure na nuvem. As duas atividades são:

* copiar os dados de um banco de dados de SQL Server local para uma conta de armazenamento de blob do Azure
* copiar dados da conta de armazenamento de blob do Azure para um banco de dados do SQL Azure.

**Referência**: as etapas mostradas aqui foram adaptadas do tutorial mais detalhado [Habilitar seus pipelines para trabalhar com dados locais](data-factory-use-onpremises-datasources.md) fornecido pela equipe do ADF e referências para as seções relevantes desse tópico são fornecidas quando apropriado.


## <a name="prereqs"></a>Pré-requisitos
Este tutorial presume que você tenha:

* Uma **assinatura do Azure**. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento do Azure**. Você usará uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [Criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account). Depois de criar a conta de armazenamento, você precisará obter a chave de conta usada para acessar o armazenamento. Consulte [Exibir, copiar e regenerar chaves de acesso de armazenamento](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Acesso a um **Banco de dados do SQL Azure**. Se você deve configurar um banco de dados do SQL Azure, a [Guia de Introdução ao banco de dados SQL do Microsoft Azure](sql-database-get-started.md) fornece informações sobre como provisionar uma nova instância de um banco de dados do SQL Azure.
* **Azure PowerShell** instalado e configurado localmente. Para saber mais, confira [Como instalar e configurar o PowerShell do Azure](powershell-install-configure.md).

> [AZURE.NOTE]Este procedimento usa o [Portal de visualização do Azure](https://ms.portal.azure.com/).

##<a name="upload-data"></a> Carregar os dados para o SQL Server local

Usamos o [conjunto de dados de Táxi de NYC](http://chriswhong.com/open-data/foil_nyc_taxi/) para demonstrar o processo de migração. O conjunto de dados de Táxi de NYC está disponível, como observado nessa postagem, no armazenamento de blob do Azure [aqui](http://www.andresmh.com/nyctaxitrips/). Os dados têm dois arquivos, o arquivo trip\_data.csv que contém detalhes da viagem e o arquivo trip\_far.csv que contém detalhes das tarifas pagas para cada viagem. Um exemplo e uma descrição desses arquivos são fornecidos na [Descrição do Conjunto de Dados de Viagens de Táxi de NYC](machine-learning-data-science-process-sql-walkthrough.md#dataset).


Você pode adaptar o procedimento fornecido aqui para um conjunto de seus próprios dados ou seguir as etapas conforme descrito usando o conjunto de dados de Táxi de NYC. Para carregar o conjunto de dados de Táxi de NYC em seu banco de dados do SQL Server local, execute o procedimento descrito em [Importação de dados em massa para o Banco de Dados do SQL Server](machine-learning-data-science-process-sql-walkthrough.md#dbload). Essas instruções são para um SQL Server em uma máquina Virtual do Azure, mas o procedimento para carregar o SQL Server no local é o mesmo.

##<a name="create-adf"></a>Criar uma Data Factory do Azure

As instruções para criar uma nova Data Factory do Azure e um grupo de recursos no [portal de visualização do Azure](https://ms.portal.azure.com/) são fornecidas [aqui](data-factory-build-your-first-pipeline-using-editor.md#step-1-creating-the-data-factory). Nomeie a nova instância ADF como *adfdsp* e nomeie o grupo de recursos criado como *adfdsprg*.

## Instalar e configurar o Gateway de gerenciamento de dados

Para habilitar os pipelines em uma Data Factory do Azure para trabalhar com um SQL Server local, você precisa adicioná-los como um serviço vinculado à fábrica de dados. Para poder adicionar um Serviço vinculado para o SQL Server local, você primeiro precisa baixar e instalar o Gateway de Gerenciamento de Dados da Microsoft em um computador local e configurar o serviço vinculado para a fonte de dados local para usar o gateway. O Gateway de Gerenciamento de Dados serializa e desserializa dados de origem e do coletor no computador em que está hospedado.

Para obter instruções de instalação e detalhes sobre o Gateway de gerenciamento de dados, consulte [Habilitar seus pipelines para trabalhar com dados locais](data-factory-use-onpremises-datasources.md)


## <a name="adflinkedservices"></a>Criar serviços vinculados para conectar-se aos recursos de dados

Um serviço vinculado define as informações necessárias para o Azure Data Factory conectar-se a um recurso de dados. O procedimento passo a passo para criar serviços vinculados é fornecido em [Criar serviços vinculados](data-factory-use-onpremises-datasources.md#step-2-create-linked-services).

Temos três recursos neste cenário para o qual os serviços vinculados são necessários.

1. [Serviço vinculado para SQL Server local](#adf-linked-service-onprem-sql)
2. [Serviço vinculado do armazenamento de blob do Azure:](#adf-linked-service-blob-store)
3. [Serviço vinculado para o banco de dados do SQL Azure](#adf-linked-service-azure-sql)


###<a name="adf-linked-service-onprem-sql"></a>Serviço vinculado para o banco de dados do SQL Server local
Para criar o serviço vinculado para o SQL Server local, clique em **Repositório de dados**na página de inicial do ADF no Portal do Azure, selecione *SQL*e insira as credenciais para o *nome de usuário* e *senha*do SQL Server local. Você precisa digitar o nome do servidor como um **nome totalmente qualificado do servidor barra invertida nome da instância (servername\\instancename)**. Nomeie o serviço vinculado *adfonpremsql*.

###<a name="adf-linked-service-blob-store"></a>Serviço vinculado para Blob
Para criar o serviço vinculado para a conta de armazenamento de blob do Azure, clique no **Repositório de dados** na página inicial do ADF no Portal do Azure, selecione *Conta de armazenamento do Azure* e insira o nome de chave e o contêiner de conta do armazenamento de blob do Azure. Nomeie o serviço de link como *adfds*.

###<a name="adf-linked-service-azure-sql"></a>Serviço vinculado para o banco de dados do SQL Azure
Para criar o serviço vinculado para o banco de dados do SQL Azure, clique no **Repositório de dados** na página inicial do ADF no Portal do Azure, selecione *SQL Azure* e insira as credenciais para o *nome de usuário* e *senha* do banco de dados do SQL Azure. O *nome de usuário* deve ser especificado como **user@servername*.


##<a name="adf-tables"></a>Definir e criar tabelas para especificar como acessar os conjuntos de dados

Crie tabelas que especificam a estrutura, o local e a disponibilidade dos conjuntos de dados com os procedimentos a seguir baseados em script. Os arquivos JSON são usados para definir as tabelas. Para obter mais informações sobre a estrutura desses arquivos, consulte [Conjuntos de dados](data-factory-create-datasets.md).

> [AZURE.NOTE]Você deve executar o cmdlet `Add-AzureAccount` antes de executar o cmdlet [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) para confirmar que a assinatura correta do Azure esteja selecionada para a execução do comando. Para obter a documentação desse cmdlet, consulte [Add-AzureAccount](https://msdn.microsoft.com/library/azure/dn790372.aspx).

As definições baseadas em JSON nas tabelas usam os seguintes nomes:

* o **nome de tabela** no SQL Server local é *nyctaxi\_data*
* o **nome do contêiner** na conta de armazenamento de Blob do Azure é *containername\\\\\\\\\\\\\*  

Três definições de tabela são necessárias para este pipeline do ADF:

1. [Tabela do SQL local](#adf-table-onprem-sql)
2. [Tabela de blob](#adf-table-blob-store)
3. [Tabela do SQL Azure](#adf-table-azure-sql)

> [AZURE.NOTE]Os procedimentos a seguir usam o Azure PowerShell para definir e criar as atividades do ADF. Mas essas tarefas também podem ser realizadas usando o portal de visualização do Azure. Para obter detalhes, consulte [Criar conjuntos de dados de entrada e saída](data-factory-use-onpremises-datasources.md#step-3-create-input-and-output-datasets).

###<a name="adf-table-onprem-sql"></a>Tabela do SQL local

A definição da tabela do SQL Server local é especificada no seguinte arquivo JSON.

    	{
	    	"name": "OnPremSQLTable",
	    	"properties":
	    	{
		    	"location":
		    	{
		    	"type": "OnPremisesSqlServerTableLocation",
		    	"tableName": "nyctaxi_data",
		    	"linkedServiceName": "adfonpremsql"
		    	},
		    	"availability":
		    	{
		    	"frequency": "Day",
		    	"interval": 1,   
		    	"waitOnExternal":
		    	{
		    	"retryInterval": "00:01:00",
		    	"retryTimeout": "00:10:00",
		    	"maximumRetry": 3
		    	}

		    	}
	    	}
    	}
Observe que os nomes de coluna não foram incluídos aqui, você pode selecionar abaixo a partir dos nomes de coluna incluindo-os aqui (para obter detalhes, verifique a [documentação ADF](data-factory-copy-activity.md)).

Copie a definição de JSON da tabela em um arquivo chamado *onpremtabledef.json* e salve-o em um local conhecido (neste caso deve ser *C:\\temp\\onpremtabledef.json*). Crie a tabela no ADF com o seguinte cmdlet do Azure PowerShell.

	New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


###<a name="adf-table-blob-store"></a>Tabela de blob
A definição da tabela para o local do blob de saída está a seguir (isso mapeia os dados ingeridos localmente para o blob do Azure):

	    {
		    "name": "OutputBlobTable",
		    "properties":
		    {
			    "location":
			    {
			    "type": "AzureBlobLocation",
			    "folderPath": "containername",
			    "format":
			    {
			    "type": "TextFormat",
			    "columnDelimiter": "\t"
			    },
			    "linkedServiceName": "adfds"
			    },
			    "availability":
			    {
			    "frequency": "Day",
			    "interval": 1
			    }
		    }
	    }

Copie a definição de JSON da tabela em um arquivo chamado *bloboutputtabledef.json* e salve-o em um local conhecido (neste caso deve ser*C:\\temp\\bloboutputtabledef.json*). Crie a tabela no ADF com o seguinte cmdlet do Azure PowerShell.

	New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

###<a name="adf-table-azure-sq"></a>Tabela do SQL Azure
A definição da tabela para a saída do SQL Azure está a seguir (esse esquema mapeia os dados provenientes do blob):

	{
	    "name": "OutputSQLAzureTable",
	    "properties":
	    {
	        "structure":
	        [
				{ "name": "column1", type": "String"},
				{ "name": "column2", type": "String"}                
	        ],
	        "location":
	        {
	            "type": "AzureSqlTableLocation",
	            "tableName": "your_db_name",
	            "linkedServiceName": "adfdssqlazure_linked_servicename"
	        },
	        "availability":
	        {
	            "frequency": "Day",
	            "interval": 1            
	        }
	    }
	}

Copie a definição de JSON da tabela em um arquivo chamado *AzureSqlTable.json* e salve-o em um local conhecido (neste caso deve ser *C:\\temp\\AzureSqlTable.json*). Crie a tabela no ADF com o seguinte cmdlet do Azure PowerShell.

	New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  

##<a name="adf-pipeline"></a>Definir e criar o pipeline

Especifique as atividades que pertencem ao pipeline e crie o pipeline com os procedimentos a seguir baseados em script. Um arquivo JSON é usado para definir as propriedades de pipeline.

* O script assume que o **nome do pipeline** é *AMLDSProcessPipeline*.
* Observe também que definimos a periodicidade do pipeline para ser executado diariamente e usar o tempo de execução padrão para o trabalho (12:00 UTC).

> [AZURE.NOTE]Os procedimentos a seguir usam o Azure PowerShell para definir e criar o pipeline do ADF. Mas essa tarefa também pode ser realizada usando o portal de visualização do Azure. Para obter detalhes, consulte [Criar e executar um pipeline](data-factory-use-onpremises-datasources.md#step-4-create-and-run-a-pipeline).

Usando as definições de tabela fornecidas anteriormente, a definição de pipeline para o ADF é especificada da seguinte maneira:

		{
		    "name": "AMLDSProcessPipeline",
		    "properties":
		    {
		        "description" : "This pipeline has one Copy activity that copies data from an on-premise SQL to Azure blob",
		         "activities":
		        [
		            {
		                "name": "CopyFromSQLtoBlob",
		                "description": "Copy data from on-premise SQL server to blob",     
		                "type": "CopyActivity",
		                "inputs": [ {"name": "OnPremSQLTable"} ],
		                "outputs": [ {"name": "OutputBlobTable"} ],
		                "transformation":
		                {
		                    "source":
		                    {                               
		                        "type": "SqlSource",
		                        "sqlReaderQuery": "select * from nyctaxi_data"
		                    },
		                    "sink":
		                    {
		                        "type": "BlobSink"
		                    }   
		                },
		                "Policy":
		                {
		                    "concurrency": 3,
		                    "executionPriorityOrder": "NewestFirst",
		                    "style": "StartOfInterval",
		                    "retry": 0,
		                    "timeout": "01:00:00"
		                }       

		             },

					{
						"name": "CopyFromBlobtoSQLAzure",
						"description": "Push data to Sql Azure",		
						"type": "CopyActivity",
						"inputs": [ {"name": "OutputBlobTable"} ],
						"outputs": [ {"name": "OutputSQLAzureTable"} ],
						"transformation":
						{
							"source":
							{                               
								"type": "BlobSource"
							},
							"sink":
							{
								"type": "SqlSink",
								"WriteBatchTimeout": "00:5:00",				
							}			
						},
						"Policy":
						{
							"concurrency": 3,
							"executionPriorityOrder": "NewestFirst",
							"style": "StartOfInterval",
							"retry": 2,
							"timeout": "02:00:00"
						}
				     }
		        ]
		    }
		}

Copie a definição de JSON da tabela em um arquivo chamado *pipelinedef.json* e salve-o em um local conhecido (neste caso deve ser *C:\\temp\\pipelinedef.json*). Crie o pipeline no ADF com o seguinte cmdlet do Azure PowerShell.

	New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json

Confirme que você pode ver o pipeline no ADF na apresentação de portal do Azure conforme a seguir (quando você clicar no diagrama)

![](media/machine-learning-data-science-move-sql-azure-adf/DJP1kji.png)

##<a name="adf-pipeline-start"></a>Iniciar o Pipeline
O pipeline agora pode ser executado usando o seguinte comando:

	Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Os valores de parâmetro *startdate* e *enddate* precisam ser substituídos pelas datas reais entre os quais você deseja executar o pipeline.

Depois que o pipeline é executado, você poderá ver os dados aparecerem no contêiner selecionado para o blob, um arquivo por dia.

Observe que não utilizamos a funcionalidade fornecida pelo ADF para dados de pipe incrementalmente. Para obter mais detalhes sobre como fazer isso e outros recursos fornecidos pelo ADF, consulte a [documentação do ADF](http://azure.microsoft.com/services/data-factory/).

<!---HONumber=Oct15_HO3-->