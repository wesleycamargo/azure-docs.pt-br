<properties
	pageTitle="Introdução ao Azure Data Factory (Visual Studio)"
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
	ms.topic="hero-article" 
	ms.date="12/18/2015"
	ms.author="spelluru"/>

# Criar seu primeiro pipeline do Azure Data Factory usando o Visual Studio
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


Neste artigo, você aprenderá a usar o Microsoft Visual Studio para criar seu primeiro data factory do Azure.

## Pré-requisitos

1. Você **deve** ler o artigo [Visão geral do tutorial](data-factory-build-your-first-pipeline.md) e concluir as etapas de pré-requisito antes de continuar.
2. Este artigo não fornece uma visão geral conceitual do serviço Azure Data Factory. Nós recomendamos que você veja o artigo [Introdução ao Azure Data Factory](data-factory-introduction.md) para obter uma visão geral detalhada do serviço.  

## Passo a passo: Criar e implantar as entidades de data factory usando o Visual Studio 

### Pré-requisitos

Você deve ter os seguintes itens instalados no seu computador:

- Visual Studio 2013 ou Visual Studio 2015
- Baixe o SDK do Azure para Visual Studio 2013 ou Visual Studio de 2015. Navegue até a [Página de Download do Azure](http://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na seção **.NET**.
- Baixe o plug-in Azure Data Factory para o Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Se você estiver usando o Visual Studio 2013, você também pode atualizar o plug-in, fazendo o seguinte: no menu, clique em **Ferramentas** -> **Extensões e atualizações** -> **Online** -> **Galeria do Visual Studio** -> **Ferramentas do Microsoft Azure Data Factory** -> **Atualizar**. 
	
	

### Criar o projeto do Visual Studio 
1. Inicie o **Visual Studio 2013** ou o **Visual Studio 2015**. Clique em **Arquivo**, indique **Novo** e, em seguida, clique em **Projeto**. Você deverá ver a caixa de diálogo **Novo Projeto**.  
2. Na caixa de diálogo **Novo Projeto**, selecione o modelo **DataFactory** e clique em **Projeto Vazio do Data Factory**.   

	![Caixa de diálogo Novo projeto](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Insira um **nome** para o projeto, o **local**, um nome para a **solução** e clique em **OK**.

	![Gerenciador de Soluções](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### Criar serviços vinculados
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline em um data factory pode ter uma ou mais atividades. Por exemplo, uma Atividade de Cópia para copiar dados de um armazenamento de dados de origem para um de destino e uma atividade do Hive do HDInsight para executar o script do Hive para transformar os dados de entrada em dados de saída do produto. Você especificará o nome e as configurações do data factory posteriormente quando publicar sua solução do Data Factory.

Nesta etapa, você vinculará sua conta do Armazenamento do Azure e um cluster do HDInsight do Azure sob demanda ao data factory. A conta do Armazenamento do Azure manterá os dados de entrada e de saída para o pipeline neste exemplo. O serviço vinculado do HDInsight é usado para executar o script do Hive especificado na atividade do pipeline neste exemplo. Você precisa identificar quais serviços de armazenamento/computação de dados serão usados em seu cenário e vincular esses serviços ao data factory criando serviços vinculados.

#### Criar o serviço vinculado do armazenamento do Azure
Nesta etapa, você vinculará a conta do Armazenamento do Azure ao data factory. Para o tutorial, use a mesma conta do Armazenamento do Azure para armazenar dados de entrada/saída e o arquivo do script do HQL.

4. Clique com o botão direito do mouse em **Serviços Vinculados** no gerenciador de soluções, aponte para **Adicionar** e clique em **Novo Item**.      
5. Na caixa de diálogo **Adicionar Novo Item**, selecione **Serviço Vinculado de Armazenamento do Azure** na lista e clique em **Adicionar**. 
3. Substitua **accountname** e **accountkey** pelo nome da sua conta de armazenamento do Azure e sua chave. Para saber como obter sua chave de acesso de armazenamento, confira [Exibir, copiar e regenerar chaves de acesso de armazenamento](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

	![Serviço vinculado de armazenamento do Azure](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Salve o arquivo **AzureStorageLinkedService1.json**.

#### Criar o serviço vinculado do Azure HDInsight
Nesta etapa, você vinculará um cluster do HDInsight sob demanda ao seu data factory. O cluster do HDInsight é automaticamente criado no tempo de execução e excluído após a conclusão do processamento, ficando ocioso durante o período especificado. Você pode usar seu próprio cluster do HDInsight em vez de usar um cluster do HDInsight sob demanda. Veja [Serviços vinculados de computação](data-factory-compute-linked-services.md) para obter detalhes.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Serviços Vinculados**, aponte para **Adicionar** e clique em **Novo Item**.
2. Selecione **Serviço Vinculado sob Demanda do HDInsight** e clique em **Adicionar**. 
3. Substitua o **JSON** pelo seguinte:

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "AzureStorageLinkedService1"
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

4. Salve o arquivo **HDInsightOnDemandLinkedService1.json**.

### Criar conjuntos de dados
Nesta etapa, você criará conjuntos de dados para representar dados de entrada e de saída para o processamento do Hive. Esses conjuntos de dados fazem referência ao **StorageLinkedService1** que você criou anteriormente neste tutorial. Os pontos de serviço apontam para uma conta do Armazenamento do Azure e os conjuntos de dados especificam o contêiner, a pasta, o nome no armazenamento que contém os dados de entrada e de saída.

#### Criar o conjunto de dados de entrada

1. No **Gerenciador de Soluções**, clique com o botão direito em **Tabelas**, aponte para **Adicionar** e clique em **Novo Item**. 
2. Selecione **Blob do Azure** na lista, altere o nome do arquivo para **OutputDataset.json** e clique em **Adicionar**.
3. Substitua o **JSON** no editor pelo seguinte: 

	No trecho JSON, você está criando um conjunto de dados chamado **AzureBlobInput** que representa dados de entrada para uma atividade no pipeline. Além disso, você especifica que os dados de entrada estão localizados no contêiner de blob denominado **adfgetstarted** e na pasta chamada **inputdata**
		
		{
			"name": "AzureBlobInput",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "AzureStorageLinkedService1",
		        "typeProperties": {
		            "fileName": "input.log",
		            "folderPath": "adfgetstarted/inputdata",
		            "format": {
		                "type": "TextFormat",
		                "columnDelimiter": ","
		            }
		        },
		        "availability": {
		            "frequency": "Month",
		            "interval": 1
		        },
		        "external": true,
		        "policy": {}
		    }
		} 

	A tabela a seguir fornece descrições das propriedades de JSON usadas no trecho de código:

	| Propriedade | Descrição |
	| :------- | :---------- |
	| type | A propriedade type é definida como AzureBlob porque os dados residem no armazenamento de blobs do Azure. |  
	| linkedServiceName | refere-se ao AzureStorageLinkedService1 que você criou anteriormente. |
	| fileName | Essa propriedade é opcional. Se você omitir essa propriedade, todos os arquivos de folderPath serão selecionados. Nesse caso, somente o input.log será processado. |
	| type | Os arquivos de log estão em formato de texto, então utilizaremos TextFormat. | 
	| columnDelimiter | as colunas nos arquivos de log são delimitadas por , (vírgula) |
	| frequência/intervalo | a frequência é definida como Mês e o intervalo como 1, o que significa que as fatias de entrada estão disponíveis mensalmente. | 
	| externo | essa propriedade será definida como true se os dados de entrada não forem gerados pelo serviço Data Factory. | 
	  
	
3. Salve o arquivo **InputDataset.json**.

 
#### Criar o conjunto de dados de saída
Agora, você criará o conjunto de dados de saída para representar os dados de saída armazenados no armazenamento de Blobs do Azure.

1. No **Gerenciador de Soluções**, clique com o botão direito em **tabelas**, aponte para **Adicionar** e clique em **Novo Item**. 
2. Selecione **Blob do Azure** na lista, altere o nome do arquivo para **OutputDataset.json** e clique em **Adicionar**. 
3. Substitua o **JSON** no editor pelo seguinte: 

	No trecho JSON, você está criando um conjunto de dados chamado **AzureBlobOutput** e especificando a estrutura dos dados que serão produzidos pelo script do Hive. Além disso, você especifica que os resultados são armazenados no contêiner de blob denominado **adfgetstarted** e na pasta chamada **partitioneddata**. A seção **availability** especifica que o conjunto de dados de saída é produzido mensalmente.
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
		      "folderPath": "adfgetstarted/partitioneddata",
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

	Consulte a seção **Criar o conjunto de dados de entrada** para obter descrições dessas propriedades. Você não define a propriedade externa em um conjunto de dados de saída porque o conjunto de dados é produzido pelo serviço Data Factory.

4. Salve o arquivo **OutputDataset.json**.


### Criação do seu primeiro pipeline
Nesta etapa, você criará seu primeiro pipeline com a atividade **HDInsightHive**. Observe que a fatia de entrada está disponível mensalmente (frequência: Mês, intervalo: 1), a fatia de saída é produzida mensalmente e a propriedade do agendador para a atividade também é definida como mensal (veja abaixo). As configurações para o conjunto de dados de saída e o agendador de atividades devem corresponder. Neste momento, o conjunto de dados de saída é o que aciona a agenda, então você deve criar um conjunto de dados de saída, mesmo que a atividade não produza qualquer saída. Se a atividade não receber entradas, ignore a criação de conjunto de dados de entrada. As propriedades usadas no JSON a seguir são explicadas no final desta seção.

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
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "AzureStorageLinkedService1",
		                    "defines": {
		                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
		                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "inputs": [
		                    {
		                        "name": "AzureBlobInput"
		                    }
		                ],
		                "outputs": [
		                    {
		                        "name": "AzureBlobOutput"
		                    }
		                ],
		                "policy": {
		                    "concurrency": 1,
		                    "retry": 3
		                },
		                "scheduler": {
		                    "frequency": "Month",
		                    "interval": 1
		                },
		                "name": "RunSampleHiveActivity",
		                "linkedServiceName": "HDInsightOnDemandLinkedService"
		            }
		        ],
		        "start": "2014-02-01T00:00:00Z",
		        "end": "2014-02-02T00:00:00Z",
		        "isPaused": false
		    }
		}

 	No trecho de JSON, você cria um pipeline que consiste de uma única atividade que usa o Hive para processar dados em um cluster HDInsight.
	
	No trecho de JSON, você cria um pipeline que consiste de uma única atividade que usa o Hive para processar dados em um cluster HDInsight.
	
	O arquivo de script do Hive, **partitionweblogs.hql**, é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService chamado **AzureStorageLinkedService1**) e na pasta **script** no contêiner **adfgetstarted**.

	A seção **defines** é usada para especificar as configurações de tempo de execução que serão passadas para o script do hive como valores de configuração do Hive (por exemplo, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

	As propriedades **start** e **end** do pipeline especificam o período ativo do pipeline.

	Na atividade de JSON, você especifica que o script do Hive deve ser executado na computação especificada pelo **linkedServiceName** – **HDInsightOnDemandLinkedService**.

3. Salve o arquivo **HiveActivity1.json**.

### Adicionar partitionweblogs.hql e input.log como uma dependência 

1. Clique com o botão direito do mouse em **Dependências** na janela do **Gerenciador de Soluções**, aponte para **Adicionar** e clique em **Item Existente**.  
2. Navegue até **C:\\ADFGettingStarted**, selecione os arquivos **partitionweblogs.hql**, **input.log** e clique em **Adicionar**. Você criou esses dois arquivos como parte dos pré-requisitos da [Visão Geral do Tutorial](data-factory-build-your-first-pipeline.md).

Quando você publicar a solução na próxima etapa, o arquivo **partitionweblogs.hql** será carregado na pasta de scripts no contêiner de blob **adfgetstarted**.

### Publicar/implantar entidades de data factory

18. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e clique em **Publicar**. 
19. Se a caixa de diálogo **Entrar na sua conta da Microsoft** for exibida, insira as credenciais da conta com a assinatura do Azure e clique em **entrar**.
20. Você deve ver a caixa de diálogo a seguir:

	![Caixa de diálogo Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. Na página Configurar data factory, faça o seguinte:
	1. Selecione a opção **Criar Nova Fábrica de Dados**.
	2. Insira **FirstDataFactoryUsingVS** em **Nome**. 
	
		> [AZURE.IMPORTANT]O nome do Azure Data Factory deve ser globalmente exclusivo. Se você receber o erro **O nome da fábrica de dados "FirstDataFactoryUsingVS" não está disponível** durante a publicação, altere o nome (por exemplo, seunomeFirstDataFactoryUsingVS). Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos de Data Factory.
		> 
		> O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se publicamente visível.
	3. Selecione a assinatura correta para o campo **Assinatura**. 
	4. Selecione o **grupo de recursos** para o data factory a ser criado. 
	5. Selecione a **região** do data factory. 
	6. Clique em **Avançar** para alternar para a página **Publicar Itens**. (Pressione **TAB** para sair do campo Nome se o botão **Avançar** estiver desabilitado). 
23. Na página **Publicar Itens**, verifique se todas as entidades do Data Factory estão selecionadas e clique em **Avançar** para alternar para a página **Resumo**.     
24. Examine o resumo e clique em **Avançar** para iniciar o processo de implantação e exibir o **Status da Implantação**.
25. Na página **Status da Implantação**, você deverá ver o status do processo de implantação. Clique em Concluir depois que a implantação tiver terminado. 
 
## Etapa 4: Monitorar o pipeline

6. Faça logon no [Portal do Azure](http://portal.azure.com/), faça o seguinte:
	1. Clique em **Procurar** e selecione **Data factories**. ![Procurar data factories](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png) 
	2. Escolha **FirstDataFactoryUsingVS** na lista de data factories. 
7. Na home page do seu data factory, clique em **Diagrama**.
  
	![Bloco do diagrama](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
7. Na Exibição de diagrama, você terá uma visão geral dos pipelines e conjuntos de dados usados neste tutorial.
	
	![Exibição de diagrama](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png) 
8. Para exibir todas as atividades no pipeline, clique com o botão direito do mouse no pipeline no diagrama e clique em Abrir Pipeline. 

	![Menu do pipeline aberto](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
9. Confirme que você vê a atividade HDInsightHive no pipeline. 
  
	![Abrir a exibição do pipeline](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

	Para navegar de volta ao modo de exibição anterior, clique em **Data factory** no menu de atalho na parte superior. 
10. Na **Exibição de Diagrama**, clique duas vezes no conjunto de dados **AzureBlobInput**. Confirme se a fatia está no estado **Pronto**. Pode levar alguns minutos para que a fatia apareça no estado Pronto. Se isso não acontecer depois de algum tempo, veja se o arquivo de entrada (input.log) está posicionado no contêiner à direita (adfgetstarted) e na pasta (inputdata).

	![Fatia de entrada no estado pronto](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
11. Clique em **X** para fechar a folha **AzureBlobInput**. 
12. Na **Exibição de Diagrama**, clique duas vezes no conjunto de dados **AzureBlobOutput**. Você verá que a fatia que está sendo processada.

	![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. Quando o processamento for concluído, você verá a fatia no estado **Pronto**.
	>[AZURE.IMPORTANT]A criação de um cluster do HDInsight sob demanda geralmente leva algum tempo (20 minutos, aproximadamente).  

	![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png)
	
10. Quando a fatia estiver no estado **Pronto**, verifique a pasta **partitioneddata** no contêiner **adfgetstarted** em seu armazenamento de blobs para os dados de saída.
 
	![dados de saída](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)

## Use o Gerenciador de Servidores para examinar as entidades de data factory

1. No **Visual Studio**, clique em **Exibir** no menu e em **Gerenciador de Servidores**.
2. Na janela Gerenciador de Servidores, expanda **Azure** e expanda **Data Factory**. Se **Entrar no Visual Studio** for exibido, insira a **conta** associada à sua assinatura do Azure e clique em **Continuar**. Insira a **senha** e clique em **Entrar**. O Visual Studio tenta obter informações sobre todas as data factories do Azure em sua assinatura. Você verá o status da operação na janela **Lista de Tarefas do Data Factory**.

	![Gerenciador de Servidores](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Clique com o botão direito em um data factory e selecione **Exportar Data Factory para Novo Projeto** para criar um projeto do Visual Studio com base em um data factory existente.

	![Exportar data factory](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Atualizar ferramentas de data factory para o Visual Studio

Para atualizar as ferramentas da Azure Data Factory para o Visual Studio, faça o seguinte:

1. Clique em **Ferramentas** no menu e selecione **Extensões e Atualizações**.
2. Selecione **Atualizações** no painel esquerdo e selecione **Galeria do Visual Studio**.
3. Selecione **Ferramentas do Azure Data Factory para Visual Studio** e clique em **Atualizar**. Se você não vir essa entrada, você já tem a versão mais recente das ferramentas. 

Confira [Monitorar os conjuntos de dados e o pipeline](data-factory-monitor-manage-pipelines.md) para obter instruções sobre como usar o Portal do Azure para monitorar o pipeline e os conjuntos de dados que você criou neste tutorial.
 

## Próximas etapas
Neste artigo, você criou um pipeline com uma atividade de transformação (atividade do HDInsight) que executa um script Hive em um cluster do HDInsight sob demanda. Para saber como usar uma Atividade de Cópia para copiar dados de um Blob do Azure para o SQL do Azure, confira [Tutorial: Copiar dados de um blob do Azure para o SQL do Azure](data-factory-get-started.md).
  

<!---HONumber=AcomDC_1223_2015-->