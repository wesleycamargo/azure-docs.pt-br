<properties
	pageTitle="Criar seu primeiro pipeline do Azure Data Factory usando o Visual Studio"
	description="Neste tutorial, você criará um pipeline de exemplo do Azure Data Factory usando o Visual Studio."
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
	ms.date="10/06/2015"
	ms.author="spelluru"/>

# Criar seu primeiro pipeline do Azure Data Factory usando o Visual Studio
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


Neste artigo, você aprenderá a usar o Visual Studio para criar seu primeiro pipeline. Este tutorial consiste nas seguintes etapas:

2.	Criar serviços vinculados (armazenamento de dados, computações).
3.	Criar um conjunto de dados.
3.	Criar um pipeline.
4.	Criar uma data factory e implantar serviços vinculados, conjunto de dados e o pipeline. 

Este artigo não fornece uma visão geral conceitual do serviço Azure Data Factory. Para obter uma visão geral detalhada do serviço, consulte o artigo [Introdução ao Azure Data Factory](data-factory-introduction.md).

> [AZURE.IMPORTANT]Leia o artigo [Visão geral do tutorial](data-factory-build-your-first-pipeline.md) e conclua as etapas de pré-requisito antes de executar este tutorial.

## Passo a passo: Criar e implantar as entidades de data factory usando o Visual Studio 

### Pré-requisitos

Você deve ter os seguintes itens instalados no computador: - Visual Studio 2013 - Baixe o SDK do Azure para Visual Studio 2013. Navegue até a [Página de Download do Azure](http://azure.microsoft.com/downloads/) e clique em **Instalação do VS 2013** na seção **.NET**. - Atualize o "Visual Studio Tools For Azure Data Factory" para a versão mais recente indo até Visual Studio 2013--> Ferramentas --> Atualizações --> Galeria do Visual Studio e clicando em "Atualizar" na entrada do plug-in "Visual Studio Tools For Azure Data Factory”.


### Criar o projeto do Visual Studio 
1. Inicie o **Visual Studio 2013**. Clique em **Arquivo**, indique **Novo** e, em seguida, clique em **Projeto**. Você deverá ver a caixa de diálogo **Novo Projeto**.  
2. Na caixa de diálogo **Novo Projeto**, selecione o modelo **DataFactory** e clique em **Projeto Vazio do Data Factory**. Se você não vir o modelo DataFactory, feche o Visual Studio, instale o SDK do Azure para Visual Studio 2013 e reabra o Visual Studio.  

	![Caixa de diálogo Novo projeto](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Insira um **nome** para o projeto, o **local**, um nome para a **solução** e clique em **OK**.

	![Gerenciador de Soluções](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### Criar serviços vinculados
Nesta etapa, você vinculará sua conta do Armazenamento do Azure e um cluster do Azure HDInsight sob demanda à sua data factory e, em seguida, criará um conjunto de dados para representar os dados de saída do processamento do Hive.


#### Criar o serviço vinculado do armazenamento do Azure


4. Clique com o botão direito do mouse em **Serviços Vinculados** no gerenciador de soluções, aponte para **Adicionar** e clique em **Novo Item**.      
5. Na caixa de diálogo **Adicionar Novo Item**, selecione **Serviço Vinculado de Armazenamento do Azure** na lista e clique em **Adicionar**. 

	![Novo serviço vinculado](./media/data-factory-build-your-first-pipeline-using-vs/new-linked-service-dialog.png)
 
3. Substitua **accountname** e **accountkey** pelo nome da sua conta de armazenamento do Azure e sua chave.

	![Serviço vinculado de armazenamento do Azure](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Salve o arquivo **AzureStorageLinkedService1.json**.

#### Criar o serviço vinculado do Azure HDInsight
Agora, você criará um serviço vinculado para um cluster HDInsight sob demanda que será usado para executar o script Hive.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Serviços Vinculados**, aponte para **Adicionar** e clique em **Novo Item**.
2. Selecione **Serviço Vinculado sob Demanda do HDInsight** e clique em **Adicionar**. 
3. Substitua o **JSON** pelo seguinte:

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "AzureStorageLinkedService1"
		    }
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

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Adicionar** e clique em **Novo Item**. 
2. Selecione **Blob do Azure** na lista e clique em **Adicionar**. 
3. Substitua o **JSON** no editor pelo seguinte: no trecho de JSON, você está criando um conjunto de dados chamado **AzureBlobOutput** e especificando a estrutura dos dados que serão produzidos pelo script do Hive. Além disso, você especifica que os resultados são armazenados no contêiner de blob denominado **data** e na pasta chamada **partitioneddata**. A seção **availability** especifica que o conjunto de dados de saída é produzido mensalmente.
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
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

4. Salve o arquivo **AzureBlobLocation1.json**.


### Criação do seu primeiro pipeline
Nesta etapa, você criará seu primeiro pipelines.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Pipelines**, aponte para **Adicionar** e clique em **Novo Item.** 
2. Selecione **Pipeline de Transformação do Hive** na lista e clique em **Adicionar**. 
3. Substitua o **JSON** pelo trecho a seguir.

	> [AZURE.IMPORTANT]Substitua **storageaccountname** pelo nome da sua conta de armazenamento.

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "AzureStorageLinkedService1",
		          "defines": {
		            "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		          }
		        },
		        "outputs": [
		          {
		            "name": "AzureBlobOutput"
		          }
		        ],
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
	
	O arquivo de script do Hive, **partitionweblogs.hql**, é armazenado na conta de armazenamento do Azure (especificada por scriptLinkedService, chamada **AzureStorageLinkedService1**) e em um contêiner chamado **script**.

	A seção **extendedProperties** é usada para especificar as configurações de tempo de execução que serão passadas para o script hive como valores de configuração do Hive (por exemplo: ${hiveconf:PartitionedData}).

	As propriedades **start** e **end** do pipeline especificam o período ativo do pipeline.

	Na atividade de JSON, você especifica que o script do Hive deve ser executado na computação especificada pelo serviço vinculado – **HDInsightOnDemandLinkedService**.
3. Salve o arquivo **HiveActivity1.json**.

### Adicionar partitionweblogs.hql como uma dependência 

1. Clique com o botão direito do mouse em **Dependências** na janela do **Gerenciador de Soluções**, aponte para **Adicionar** e clique em **Item Existente**.  
2. Navegue até **C:\\ADFGettingStarted**, selecione o arquivo **partitionweblogs.hql** e clique em **Adicionar**. 

Quando você publica a solução na próxima etapa, o arquivo HQL é carregado para o contêiner de scripts no armazenamento de blob.

### Publicar/implantar entidades de data factory

18. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e clique em **Publicar**. 
19. Se a caixa de diálogo **Entrar na sua conta da Microsoft** for exibida, insira as credenciais da conta com a assinatura do Azure e clique em **entrar**.
20. Você deve ver a caixa de diálogo a seguir:

	![Caixa de diálogo Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. Na página Configurar data factory, faça o seguinte:
	1. Selecione a opção **Criar Novo Data Factory**.
	2. Insira **FirstPipelineUsingVS** em **Nome**.
	3. Selecione a assinatura certa para o campo **Assinatura**. 
	4. Selecione o **grupo de recursos** para o data factory a ser criado. 
	5. Selecione a **região** do data factory. 
	6. Clique em **Avançar** para alternar para a página **Publicar Itens**. (Pressione **TAB** para sair do campo Nome se o botão **Avançar** estiver desabilitado). 
23. Na página **Publicar Itens**, verifique se todas as entidades de Data Factories estão selecionadas e clique em **Avançar** para alternar para a página **Resumo**.     
24. Analise o resumo e clique em **Avançar** para iniciar o processo de implantação e exibir o **Status da Implantação**.
25. Na página **Status da Implantação**, você deverá ver o status do processo de implantação. Clique em Concluir depois que a implantação tiver terminado. 
 

## Use o Gerenciador de Servidores para examinar as entidades de data factory

1. No **Visual Studio**, clique em **Exibir** no menu e em **Gerenciador de Servidores**.
2. Na janela Gerenciador de Servidores, expanda **Azure** e expanda **Data Factory**. Se **Entrar no Visual Studio** for exibido, insira a **conta** associada à sua assinatura do Azure e clique em **Continuar**. Insira a **senha** e clique em **Entrar**. O Visual Studio tenta obter informações sobre todas as data factories do Azure em sua assinatura. Você verá o status da operação na janela **Lista de Tarefas de Data Factory**.

	![Gerenciador de Servidores](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Clique com o botão direito do mouse em um data factory e selecione **Exportar Data Factory para Novo Projeto** para criar um projeto do Visual Studio com base em um data factory existente.

	![Exportar data factory](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Atualizar ferramentas de data factory para o Visual Studio

Para atualizar as ferramentas da Azure Data Factory para o Visual Studio, faça o seguinte:

1. Clique em **Ferramentas** no menu e selecione **Extensões e Atualizações**.
2. Selecione **Atualizações** no painel esquerdo e selecione **Galeria do Visual Studio**.
3. Selecione **Ferramentas do Azure Data Factory para Visual Studio** e clique em **Atualizar**. Se você não vir essa entrada, você já tem a versão mais recente das ferramentas. 

Confira [Monitorar conjuntos de dados e pipeline](data-factory-monitor-manage-pipelines.md) para obter instruções sobre como usar o Portal de Visualização do Azure para monitorar o pipeline e os conjuntos de dados que você criou neste tutorial.
 

## Próximas etapas
Neste artigo, você criou um pipeline com uma atividade de transformação (atividade do HDInsight) que executa um script Hive em um cluster do HDInsight sob demanda. Para saber como usar uma Atividade de Cópia para copiar dados de um Blob do Azure para o SQL do Azure, confira [Tutorial: Copiar dados de um blob do Azure para o SQL do Azure](data-factory-get-started.md).
  
## Enviar comentários
Apreciamos muito seus comentários sobre este artigo. Reserve alguns minutos para enviar seus comentários por [email](mailto:adfdocfeedback@microsoft.com?subject=data-factory-build-your-first-pipeline-using-vs.md).

<!---HONumber=Oct15_HO2-->