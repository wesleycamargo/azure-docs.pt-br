<properties
	pageTitle="Crie seu primeiro pipeline usando o Azure Data Factory"
	description="Este tutorial mostra como criar um pipeline de dados de exemplo que transforma os dados usando o Azure HDInsight usando o Visual Studio"
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
	ms.topic="get-started-article" 
	ms.date="07/27/2015"
	ms.author="spelluru"/>

# Crie seu primeiro pipeline usando o Azure Data Factory
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


Neste artigo, você aprenderá a usar o Visual Studio para criar seu primeiro pipeline. Este tutorial consiste nas seguintes etapas:

1.	Criação da data factory
2.	Criação dos serviços vinculados (armazenamentos de dados, cálculos) e dos conjuntos de dados
3.	Criação do pipeline

Este artigo não fornece uma visão geral conceitual do serviço Azure Data Factory. Para obter uma visão geral detalhada do serviço, consulte o artigo [Introdução ao Azure Data Factory](data-factory-introduction.md).

## Etapa 1: Criação da data factory

1.	Depois de fazer logon no [Portal de Visualização do Azure](http://portal.azure.com/), faça o seguinte:
	1.	Clique em **NOVO** no canto inferior esquerdo. 
	2.	Clique em **Análise de dados** na folha **Criar**.
	3.	Clique em **Data Factory** na folha **Análise de dados**.

		![Folha Criar](./media/data-factory-build-your-first-pipeline-using-vs/create-blade.png)

2.	Na folha **Nova data factory**, insira **DataFactoryMyFirstPipeline** como o Nome.

	![Folha Nova data factory](./media/data-factory-build-your-first-pipeline-using-vs/new-data-factory-blade.png)

	> [AZURE.IMPORTANT]Nomes de Azure Data Factory são globalmente exclusivos. Será necessário prefixar o nome da fábrica de dados com seu nome, para habilitar a criação bem-sucedida de fábrica. 
3.	Se não criou nenhum grupo de recursos, você precisará criar um grupo de recursos. Para fazer isso:
	1.	Clique em **NOME DO GRUPO DE RECURSOS**.
	2.	Selecione **Criar um novo grupo de recursos** na folha **Grupo de recursos**.
	3.	Digite **ADF** como o **Nome** na folha **Criar grupo de recursos**.
	4.	Clique em **OK**.
	
		![Criar grupo de recursos](./media/data-factory-build-your-first-pipeline-using-vs/create-resource-group.png)
4.	Depois de selecionar o grupo de recursos, verifique se que você está usando a assinatura correta na qual deseja que a data factory seja criada.
5.	Clique em **Criar** na folha **Nova fábrica de dados**.
6.	Você verá a fábrica de dados que está sendo criada no **Quadro inicial** do Portal de Visualização do Azure da seguinte maneira:   

	![Status da criação da data factory](./media/data-factory-build-your-first-pipeline-using-vs/creating-data-factory-image.png)
7. Parabéns! Você criou com êxito sua primeira data factory. Após a data factory ter sido criada com êxito, você verá a página da data factory, que exibe seu conteúdo. 	

	![Folha Data Factory](./media/data-factory-build-your-first-pipeline-using-vs/data-factory-blade.png)

Nas etapas subsequentes, você aprenderá a criar serviços vinculados, conjuntos de dados e o pipeline que você usará neste tutorial.

## Passo a passo: Criar e implantar as entidades de data factory usando o Visual Studio 

### Pré-requisitos

Você deve ter os seguintes itens instalados no computador: 
- Visual Studio 2013
- Baixe o SDK do Azure para Visual Studio 2013. Navegue até a [página de download do Azure](http://azure.microsoft.com/downloads/) e clique em **instalar VS 2013** na seção **.NET**.


### Criar o projeto do Visual Studio 
1. Inicie o **Visual Studio 2013**. Clique em **Arquivo**, indique **Novo** e, em seguida, clique em **Projeto**. Você deve ver a caixa de diálogo **Novo projeto**.  
2. Na caixa de diálogo **Novo projeto**, selecione o modelo **DataFactory** e clique em **Projeto vazio de Data Factory**. Se você não vir o modelo DataFactory, feche o Visual Studio, instale o SDK do Azure para Visual Studio 2013 e reabra o Visual Studio.  

	![Caixa de diálogo Novo projeto](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Insira um **nome** para o projeto, **local** e um nome para a **solução** e clique em **OK**.

	![Gerenciador de Soluções](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### Criar serviços vinculados
Nesta etapa, você vinculará sua conta do Armazenamento do Azure e um cluster do Azure HDInsight sob demanda à sua data factory e, em seguida, criará um conjunto de dados para representar os dados de saída do processamento do Hive.


#### Criar o serviço vinculado do armazenamento do Azure


4. Clique em **Serviços vinculados** no Gerenciador de Soluções, aponte para **Adicionar** e clique em **Novo item**.      
5. Na caixa de diálogo **Adicionar novo item**, selecione **Serviço vinculado de armazenamento do Azure** na lista e clique em **Adicionar**. 

	![Novo serviço vinculado](./media/data-factory-build-your-first-pipeline-using-vs/new-linked-service-dialog.png)
 
3. Substitua **accountname** e **accountkey** pelo nome da sua conta de armazenamento do Azure e sua chave.

	![Serviço vinculado de armazenamento do Azure](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Salve o arquivo **AzureStorageLinkedService1.json**.

#### Criar o serviço vinculado do Azure HDInsight
Agora, você criará um serviço vinculado para um cluster HDInsight sob demanda que será usado para executar o script Hive.

1. No **Gerenciador de Soluções**, clique em **Serviços vinculados**, aponte para **Adicionar** e clique em **Novo item**.
2. Selecione **Serviço Vinculado sob demanda do HDInsight** e clique em **Adicionar**. 
3. Substitua o **JSON** pelo seguinte código:

		{
		    "name": "HDInsightOnDemandLinkedService",
		    "properties": {
		        "version": "3.1",
		        "clusterSize": 1,
		        "timeToLive": "00:05:00",
		        "jobsContainer": "adfjobs",
		        "linkedServiceName": "StorageLinkedService",
		        "type": "HDInsightOnDemandLinkedService"
		    }
		}
	
	A tabela a seguir fornece descrições das propriedades de JSON usadas no trecho de código:
	
	Propriedade | Descrição
	-------- | -----------
	Versão | Especifica a versão do HDInsight criada como 3.1. 
	ClusterSize | Cria um cluster do HDInsight de um nó. 
	TimeToLive | Especifica tempo ocioso de cluster HDInsight antes de ser excluído.
	JobsContainer | Especifica o nome do contêiner de trabalho que será criado para armazenar os logs gerados pelo HDInsight
	linkedServiceName | Especifica a conta de armazenamento que será usada para armazenar os logs gerados pelo HDInsight

4. Salve o arquivo **HDInsightOnDemandLinkedService1.json**.
 
### Criar o conjunto de dados de saída
Agora, você criará o conjunto de dados de saída para representar os dados armazenados no armazenamento de Blob do Azure.

1. No **Gerenciador de Soluções**, clique com o botão direito em **Adicionar** e clique em **Novo Item**. 
2. Selecione **Blob do Azure** na lista e clique em **Adicionar**. 
3. Substitua o **JSON** no editor pelo seguinte: No trecho de JSON, você cria um conjunto de dados chamado **AzureBlobOutput** e especifica a estrutura dos dados que serão produzidos pelo script do Hive. Além disso, você especifica que os resultados são armazenados no contêiner de blob denominado **data** e na pasta chamada **partitioneddata**. A seção **disponibilidade** especifica que o conjunto de dados de saída é produzido mensalmente.
	
		{
		    "name": "AzureBlobOutput",
		    "properties": {
		        "location": {
		            "type": "AzureBlobLocation",
		            "folderPath": "data/partitioneddata",
		            "format": {
		                "type": "TextFormat",
		                "columnDelimiter": ","
		            },
		            "linkedServiceName": "StorageLinkedService"
		        },
		        "availability": {
		            "frequency": "Month",
		            "interval": 1
		        }
		    }
		}

4. Salve o arquivo **AzureBlobLocation1.json**.


### Criação do seu primeiro pipeline
Nesta etapa, você criará seu primeiro pipelines.

1. Clique com o botão direito do mouse em **Pipelines** no **Gerenciador de Soluções**, aponte para **Adicionar** e clique em **Novo Item**. 
2. Selecione **Pipeline de transformação do Hive** na lista e clique em **Adicionar**. 
3. Substitua o **JSON** pelo seguinte trecho e substitua **storageaccountname** pelo nome da conta de armazenamento.

		{
			"name": "MyFirstPipeline",
			"properties": {
			"description": "My first Azure Data Factory pipeline",
		 	"activities": [
		      {
		            "type": "HDInsightActivity",
		            "transformation": {
		                    "scriptPath": "script/partitionweblogs.hql",
		                    "scriptLinkedService": "StorageLinkedService",
		                    "type": "Hive",
		                    "extendedProperties": {
		                        "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "outputs": [   {  "name": "AzureBlobOutput"    }   ],
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
	
	O arquivo de script do Hive, **partitionweblogs.hql**, é armazenado na conta de armazenamento do Azure (especificada por scriptLinkedService, chamada **StorageLinkedService**) e em um contêiner chamado **script**.

	A seção **extendedProperties** é usada para especificar as configurações de tempo de execução que serão passadas para o script do hive como valores de configuração de Hive (como ${hiveconf:PartitionedData}).

	As propriedades **start** e **end** do pipeline especificam o período ativo do pipeline.

	Na atividade de JSON, você especifica que o script do Hive deve ser executado na computação especificada pelo serviço vinculado –**HDInsightOnDemandLinkedService**.
3. Salve o arquivo **HiveActivity1.json**. 

### Publicar/implantar entidades de data factory
  
1. Na área da barra de ferramentas, clique com o botão direito e selecione **Data Factory** para habilitar a barra de ferramentas de Data Factory se ela ainda não estiver habilitada. 
19. Na **barra de ferramentas de Data Factory**, clique na **caixa suspensa** para ver todas as fábricas de dados na sua assinatura do Azure. Se você vir a caixa de diálogo **Entrar no Visual Studio**: 
	20. Insira a **conta de email** associada à assinatura do Azure na qual você deseja criar a fábrica de dados, digite **Senha** e clique em **Entrar**.
	21. Quando a entrada for bem-sucedida, você deverá ver todas as data factories na assinatura do Azure. Neste tutorial, você criará uma nova data factory.       
22. Na lista suspensa, selecione **DataFactoryMyFirstPipeline** e clique no botão **Publicar** para implantar/publicar os serviços vinculados, conjuntos de dados e o pipeline.    

	![Botão Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

23. Você deve ver o status de publicação na janela da **Lista de Tarefas do Data Factory** que é mostrada na imagem anterior. Confirme que a publicação foi bem-sucedida.


## Use o Gerenciador de Servidores para examinar as entidades de data factory

1. Em **Visual Studio**, clique em **Exibição** no menu e clique em **Gerenciador de Servidores**.
2. Na janela Gerenciador de Servidores, expanda **Azure** e expanda **Data Factory**. Se você vir **Entrar no Visual Studio**, digite a **conta** associada à sua assinatura do Azure e clique em **Continuar**. Digite sua **Senha** e clique em **Entrar**. O Visual Studio tenta obter informações sobre todas as data factories do Azure em sua assinatura. Você verá o status da operação na janela **Lista de Tarefas do Data Factory**.

	![Gerenciador de Servidores](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Clique com o botão direito em uma fábrica de dados e selecione **Exportar Data Factory para novo projeto** para criar um projeto do Visual Studio com base em uma fábrica de dados existente.

	![Exportar data factory](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Atualizar ferramentas de data factory para o Visual Studio

Para atualizar as ferramentas da Azure Data Factory para o Visual Studio, faça o seguinte:

1. Clique em **Ferramentas** no menu e selecione **Extensões e atualizações**.
2. Selecione **Atualizações** no painel esquerdo e selecione **Galeria do Visual Studio**.
3. Selecione **Ferramentas do Azure Data Factory para Visual Studio** e clique em **Atualizar**. Se você não vir essa entrada, você já tem a versão mais recente das ferramentas. 

Consulte [Monitorar conjuntos de dados e pipeline](data-factory-monitor-manage-pipelines.md) para obter instruções sobre como usar o Portal de Visualização do Azure para monitorar o pipeline e conjuntos de dados que você criou neste tutorial.
 

## Próximas etapas
Neste artigo, você criou um pipeline com uma atividade de transformação (atividade do HDInsight) que executa um script Hive em um cluster do HDInsight sob demanda. Para ver como usar uma Atividade de cópia para copiar dados de um Blob do Azure para o SQL Azure, consulte [Tutorial: Copiar dados de um blob do Azure para o SQL Azure](data-factory-get-started.md).
  

<!------HONumber=August15_HO6-->