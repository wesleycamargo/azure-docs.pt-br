<properties
	pageTitle="Criar seu primeiro pipeline do Azure Data Factory usando o Data Factory Editor"
	description="Neste tutorial, você criará um pipeline de exemplo do Azure Data Factory usando o Data Factory Editor no Portal do Azure."
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
	ms.topic="hero-article" 
	ms.date="11/02/2015"
	ms.author="spelluru"/>

# Criar seu primeiro pipeline do Azure Data Factory usando o Data Factory Editor (Portal do Azure)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


Neste artigo, você aprenderá a usar o [Portal do Azure](https://portal.azure.com/) para criar seu primeiro pipeline. Este tutorial consiste nas seguintes etapas:

1.	Criação da data factory
2.	Criação dos serviços vinculados (armazenamentos de dados, cálculos) e dos conjuntos de dados
3.	Criação do pipeline

Este artigo não fornece uma visão geral conceitual do serviço Azure Data Factory. Para obter uma visão geral detalhada do serviço, consulte o artigo [Introdução ao Azure Data Factory](data-factory-introduction.md).

> [AZURE.IMPORTANT]Percorra o artigo [Visão geral do Tutorial](data-factory-build-your-first-pipeline.md) e conclua as etapas de pré-requisito antes de executar este tutorial.

## Etapa 1: Criação da data factory

1.	Depois de fazer logon no [Portal do Azure](http://portal.azure.com/), faça o seguinte:
	1.	Clique em **NOVO** no menu à esquerda. 
	2.	Clique em **Análise de dados** na folha **Criar**.
	3.	Clique em **Data Factory** na folha **Análise de dados**.

		![Folha Criar](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.	Na folha **Nova data factory**, insira **DataFactoryMyFirstPipeline** como o Nome.

	![Folha Nova data factory](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

	> [AZURE.IMPORTANT]O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **O nome da data factory "DataFactoryMyFirstPipeline" não está disponível**, altere o nome da data factory (por exemplo, seunomeDataFactoryMyFirstPipeline) e tente criá-la novamente. Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos de Data Factory.
	>  
	> O nome da data factory pode ser registrada futuramente como um nome DNS e tornar-se publicamente visível.

3.	Se não criou nenhum grupo de recursos, você precisará criar um grupo de recursos. Para fazer isso:
	1.	Clique em **NOME DO GRUPO DE RECURSOS**.
	2.	Selecione **Criar um novo grupo de recursos** na folha **Grupo de recursos**.
	3.	Digite **ADF** como o **Nome** na folha **Criar grupo de recursos**.
	4.	Clique em **OK**.
	
		![Criar grupo de recursos](./media/data-factory-build-your-first-pipeline-using-editor/create-resource-group.png)
4.	Depois de selecionar o grupo de recursos, verifique se que você está usando a assinatura correta na qual deseja que a data factory seja criada.
5.	Clique em **Criar** na folha **Nova data factory**.
6.	Você verá a data factory que está sendo criada no **Quadro inicial** do Portal do Azure da seguinte maneira:   

	![Status da criação da data factory](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. Parabéns! Você criou com êxito sua primeira data factory. Após a data factory ter sido criada com êxito, você verá a página da data factory, que exibe seu conteúdo. 	

	![Folha Data Factory](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Nas etapas subsequentes, você aprenderá a criar serviços vinculados, conjuntos de dados e o pipeline que você usará neste tutorial.

## Etapa 2: Criação de serviços vinculados e conjuntos de dados
Nesta etapa, você vinculará sua conta de Armazenamento do Azure e um cluster do Azure HDInsight sob demanda ao data factory e criará um conjunto de dados para representar os dados de saída do processamento do Hive.

### Criar o serviço vinculado do armazenamento do Azure
1.	Clique em **Criar e implantar** na folha **DATA FACTORY** para **DataFactoryFirstPipeline**. Isso inicia o Data Factory Editor. 
	 
	![Bloco Criar e implantar](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.	Clique em **Novo armazenamento de dados** e escolha **Armazenamento do Azure**
	
	![Serviço vinculado de armazenamento do Azure](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

	Você deve ver o script JSON para criar um serviço de armazenamento vinculado do Azure no editor. 
4. Substitua **nome da conta** pelo nome da sua conta de armazenamento do Azure e **chave de conta** pela chave de acesso da sua conta de armazenamento do Azure. Para saber como obter sua chave de acesso de armazenamento, confira [Exibir, copiar e regenerar chaves de acesso de armazenamento](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
5. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.

	![Botão Implantar](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

### Criar o serviço vinculado do Azure HDInsight
Agora, você criará um serviço vinculado para um cluster HDInsight sob demanda que será usado para executar o script Hive.

1. No **Data Factory Editor**, clique em **Nova computação** na barra de comandos e selecione **Cluster HDInsight sob demanda**.

	![Nova computação](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. Copie e cole o trecho abaixo na janela de Rascunho-1. O trecho JSON descreve as propriedades que serão usadas para criar o cluster do HDInsight sob demanda. 

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}
	
	A tabela a seguir fornece descrições das propriedades de JSON usadas no trecho de código:
	
	Propriedade | Descrição
	-------- | -----------
	Versão | Isso especifica a versão do HDInsight criada como a 3.2. 
	ClusterSize | Cria um cluster do HDInsight de um nó. 
	TimeToLive | Especifica tempo ocioso de cluster HDInsight antes de ser excluído.
	linkedServiceName | Especifica a conta de armazenamento que será usada para armazenar os logs gerados pelo HDInsight
3. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado. 
4. Confirme que você vê StorageLinkedService e HDInsightOnDemandLinkedService modo de exibição de árvore à esquerda.

	![Modo de exibição de árvore com serviços vinculados](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)
 
### Criar o conjunto de dados de saída
Agora, você criará o conjunto de dados de saída para representar os dados armazenados no armazenamento de Blob do Azure.

1. No **Editor da Fábrica de Dados**, clique em **Novo conjunto de dados** na barra de comandos e selecione **Armazenamento de Blobs do Azure**.  

	![Novo conjunto de dados](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. Copie e cole o trecho abaixo na janela de Rascunho-1. No trecho JSON, você está criando um conjunto de dados chamado **AzureBlobOutput** e especificando a estrutura dos dados que serão produzidos pelo script do Hive. Além disso, você especifica que os resultados são armazenados no contêiner de blob denominado **data** e na pasta chamada **partitioneddata**. A seção **availability** especifica que o conjunto de dados de saída é produzido mensalmente.
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "data/partitioneddata",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Month",
		      "interval": 1
		    }
		  }
		}

3. Clique em **Implantar** na barra de comandos para implantar o conjunto de dados recém-criado.
4. Verifique se o conjunto de dados foi criado com êxito.

	![Modo de exibição de árvore com serviços vinculados](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## Etapa 3: Criação do seu primeiro pipeline
Nesta etapa, você criará seu primeiro pipelines.

1. No **Editor da Fábrica de Dados**, clique em **Reticências (...)** e em **Novo pipeline**.
	
	![botão novo pipeline](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. Copie e cole o trecho abaixo na janela de Rascunho-1.

	> [AZURE.IMPORTANT]Substitua **storageaccountname** pelo nome da sua conta de armazenamento no JSON.

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "StorageLinkedService",
		          "defines": {
		            "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		          }
		        },
		        "outputs": [
		          {
		            "name": "AzureBlobOutput"
		          }
		        ],
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
		        "policy": {
		          "concurrency": 1,
		          "retry": 3
		        },
		        "name": "RunSampleHiveActivity",
		        "linkedServiceName": "HDInsightOnDemandLinkedService"
		      }
		    ],
		    "start": "2014-01-01",
		    "end": "2014-01-02"
		  }
		}
 
	No trecho de JSON, você cria um pipeline que consiste de uma única atividade que usa o Hive para processar dados em um cluster HDInsight.
	
	O arquivo de script do Hive, **partitionweblogs.hql**, é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService chamado **StorageLinkedService**) e em um contêiner chamado **script**.

	A seção **defines** é usada para especificar as configurações de tempo de execução que serão passadas para o script do hive como valores de configuração do Hive (por exemplo, ${hiveconf:PartitionedData}).

	As propriedades **start** e **end** do pipeline especificam o período ativo do pipeline.

	Na atividade de JSON, você especifica que o script do Hive deve ser executado na computação especificada pelo serviço vinculado – **HDInsightOnDemandLinkedService**.
3. Clique em **Implantar** na barra de comandos para implantar o pipeline.
4. Confirme que você vê o pipeline no modo de exibição de árvore.

	![Modo de exibição de árvore com pipeline](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. Parabéns, você criou com sucesso seu primeiro pipeline!
6. Clique em **X** para fechar as folhas do Editor da Fábrica de Dados e para navegar de volta à folha Fábrica de Dados, e clique em **Diagrama**.
  
	![Bloco do diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. Na Exibição de diagrama, você terá uma visão geral dos pipelines e conjuntos de dados usados neste tutorial.
	
	![Exibição de diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. Na Exibição de Diagrama, clique duas vezes no conjunto de dados **AzureBlobOutput**. Você verá que a fatia que está sendo processada.

	![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. Quando o processamento for concluído, você verá a fatia no estado **Pronto**. Observe que a criação de um cluster do HDInsight sob demanda geralmente leva algum tempo. 

	![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)	
10. Quando a fatia estiver no estado **Pronto**, verifique a pasta **partitioneddata** no contêiner **data** em seu armazenamento de blobs para os dados de saída.  
 

 

## Próximas etapas
Neste artigo, você criou um pipeline com uma atividade de transformação (atividade do HDInsight) que executa um script Hive em um cluster do HDInsight sob demanda. Para saber como usar uma Atividade de Cópia para copiar dados de um Blob do Azure para o SQL do Azure, confira [Tutorial: copiar dados de um Blob do Azure para o SQL do Azure](./data-factory-get-started.md).
  

<!---HONumber=AcomDC_1217_2015-->