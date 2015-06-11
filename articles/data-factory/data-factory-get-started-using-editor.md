<properties 
	pageTitle="Introdução ao uso do Azure Data Factory" 
	description="Este tutorial mostra como criar um pipeline de dados de exemplo que copia dados de um blob para uma instância do banco de dados SQL do Azure." 
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
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Tutorial: Criar e monitorar uma fábrica de dados usando o Editor de fábrica de dados
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)


##Nesse tutorial
Este tutorial contém as seguintes etapas:

Etapa | Descrição
-----| -----------
[Etapa 1: Criar uma fábrica de dados do Azure](#CreateDataFactory) | Nesta etapa, você criará uma fábrica de dados do Azure denominada **ADFTutorialDataFactory**.  
[Etapa 2: Criar serviços vinculados](#CreateLinkedServices) | Nesta etapa, você criará dois serviços vinculados: **StorageLinkedService** e **AzureSqlLinkedService**. O StorageLinkedService vincula o armazenamento do Azure e AzureSqlLinkedService vincula o banco de dados do SQL Azure para o ADFTutorialDataFactory. Os dados de entrada do pipeline residem em um contêiner de blob no armazenamento de blob do Azure e os dados de saída serão armazenados em uma tabela no banco de dados do SQL Azure. Portanto, adicione esses dois armazenamentos de dados como serviços vinculados à fábrica de dados.      
[Etapa 3: Criar a entrada e saída de tabelas](#CreateInputAndOutputDataSets) | Na etapa anterior, você criou serviços vinculados que fazem referência a repositórios de dados que contêm dados de entrada/saída. Nesta etapa, você irá definir duas tabelas de fábrica de dados – **EmpTableFromBlob** e **EmpSQLTable** – que representam os dados de entrada/saída que são armazenados nos repositórios de dados. Para EmpTableFromBlob, você especificará o contêiner de blob que contém um blob com a fonte de dados e para o EmpSQLTable, você especificará a tabela SQL que armazenará os dados de saída. Também é possível especificar outras propriedades, como a estrutura dos dados, a disponibilidade dos dados, etc... 
[Etapa 4: Criar e executar um pipeline](#CreateAndRunAPipeline) | Nesta etapa, você criará um pipeline de chamado **ADFTutorialPipeline** no ADFTutorialDataFactory. O pipeline terá um **cópia atividade** que copia de entrada de dados do Azure blob para a tabela do SQL Azure de saída.
[Etapa 5: Monitorar fatias e pipeline](#MonitorDataSetsAndPipeline) | Nesta etapa, você irá monitorar fatias de tabelas de entrada e saídas usando o Portal de visualização do Azure.
 

## <a name="CreateDataFactory"></a>Etapa 1: Criar uma fábrica de dados do Azure
Nesta etapa, você deve usar o Portal de visualização do Azure para criar uma fábrica de dados do Azure denominada **ADFTutorialDataFactory**.

1.	Após fazer logon na [Azure Preview Portal][azure-preview-portal], clique em **novo** no canto inferior esquerdo, selecione **análises de dados** no **criar** blade e clique em **Data Factory** no **análises de dados** blade. 

	![Novo -> DataFactory][image-data-factory-new-datafactory-menu]

6. No **nova fábrica de dados** blade:
	1. Digite **ADFTutorialDataFactory** para o **nome**. 
	
  		![Nova lâmina de fábrica de dados][image-data-factory-getstarted-new-data-factory-blade]
	2. Clique em **nome do grupo de recursos** e faça o seguinte:
		1. Clique em **criar um novo grupo de recursos**.
		2. No **Criar grupo de recursos** lâmina, digite **ADFTutorialResourceGroup** para o **nome** do grupo de recursos e clique em **OK**. 

			![Criar grupo de recursos][image-data-factory-create-resource-group]

		Algumas das etapas neste tutorial supõem que você use o nome: **ADFTutorialResourceGroup** do grupo de recursos. Para saber mais sobre grupos de recursos, consulte [usando grupos de recursos para gerenciar os recursos do Azure](resource-group-overview.md).  
7. No **nova fábrica de dados** lâmina, observe que **Adicionar ao quadro inicial** está selecionado.
8. Clique em **criar** no **nova fábrica de dados** lâmina.

	> [AZURE.NOTE]O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **dados fábrica nome "ADFTutorialDataFactory" não está disponível**, altere o nome da fábrica de dados (por exemplo, yournameADFTutorialDataFactory) e tente criar novamente. Use esse nome em vez de ADFTutorialFactory ao executar as etapas restantes neste tutorial. Consulte [dados Factory - regras de nomenclatura de][data-factory-naming-rules] tópico para regras de nomeação para artefatos de fábrica de dados.
	 
	![Nome da fábrica de dados não está disponível][image-data-factory-name-not-available]

9. Clique em **notificações** hub à esquerda e procure as notificações do processo de criação. Clique em **X** para fechar o **notificações** lâmina se ele estiver aberto.
10. Após a criação for concluída, você verá o **DATA FACTORY** lâmina conforme mostrado abaixo.

    ![Homepage do alocador de dados][image-data-factory-get-stated-factory-home-page]

## <a name="CreateLinkedServices"></a>Etapa 2: Criar serviços vinculados
Serviços vinculados vinculam armazenamentos de dados ou serviços de computação para uma data factory do Azure. Um armazenamento de dados pode ser um armazenamento do Azure, um banco de dados SQL ou um banco de dados do SQL Server local.

Nesta etapa, você criará dois serviços vinculados: **StorageLinkedService** e **AzureSqlLinkedService**. StorageLinkedService vinculadas links de serviço uma conta de armazenamento do Azure e AzureSqlLinkedService vincula um banco de dados do SQL Azure para o **ADFTutorialDataFactory**. Você criará um pipeline posteriormente neste tutorial que copia dados de um contêiner de blob StorageLinkedService em uma tabela SQL em AzureSqlLinkedService.

### Criar um serviço vinculado para a conta de armazenamento do Azure
1.	No **DATA FACTORY** lâmina, clique em **autor e implantar** lado a lado para iniciar o **Editor** da fábrica de dados.

	![Bloco Criar e implantar][image-author-deploy-tile]

	> [AZURE.NOTE]Consulte [dados fábrica de Editor][data-factory-editor] tópico de visão geral detalhada do editor de fábrica de dados.
	 
5. No **Editor**, clique em **novo armazenamento de dados** botão na barra de ferramentas e selecione **armazenamento do Azure** no menu suspenso. Você deve ver o modelo JSON para criar um serviço de armazenamento do Azure vinculado no painel à direita.

	![Botão de repositório de dados novo editor][image-editor-newdatastore-button]
    
6. Substitua **accountname** e **accountkey** com o nome de conta e valores de chave de conta para sua conta de armazenamento do Azure.

	![Armazenamento de Blob do Editor de JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]Consulte [referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.

6. Clique em **Deploy** na barra de ferramentas para implantar o StorageLinkedService. Confirme que você vê a mensagem **VINCULADO serviço criado com êxito** na barra de título.

	![Implantar o armazenamento de Blob do Editor][image-editor-blob-storage-deploy]

### Criar um serviço vinculado para o banco de dados do SQL Azure
1. No **dados fábrica de Editor** , clique em **novo armazenamento de dados** botão na barra de ferramentas e selecione **banco de dados do SQL Azure** no menu suspenso. Você deve ver o modelo JSON para criar o serviço do SQL Azure vinculado no painel à direita.

	![Configurações do SQL Azure Editr][image-editor-azure-sql-settings]

2. Substitua **servername**, **databasename**, **username@servername**, e **senha** com nomes de seu servidor SQL Azure, banco de dados, conta de usuário e senha. 
3. Clique em **Deploy** na barra de ferramentas para criar e implantar o AzureSqlLinkedService. 
   

## <a name="CreateInputAndOutputDataSets"></a>Etapa 3: Criar a entrada e saída de tabelas
Na etapa anterior, você criou serviços vinculados **StorageLinkedService** e **AzureSqlLinkedService** para vincular uma conta de armazenamento do Azure e o banco de dados do SQL Azure para o alocador de dados: **ADFTutorialDataFactory**. Nesta etapa, você irá definir duas tabelas de fábrica de dados – **EmpTableFromBlob** e **EmpSQLTable** – que representam os dados de entrada/saída que são armazenados nos repositórios de dados referenciados por StorageLinkedService e AzureSqlLinkedService, respectivamente. Para EmpTableFromBlob, você especificará o contêiner de blob que contém um blob com a fonte de dados e para EmpSQLTable, você especificará a tabela SQL que armazenará os dados de saída.

### Criar tabela de entrada 
Uma tabela é um conjunto de dados retangular e tem um esquema. Nesta etapa, você criará uma tabela denominada **EmpBlobTable** que aponta para um contêiner de blob no armazenamento do Azure representado pelo **StorageLinkedService** vinculado de serviço.

1. No **Editor** da fábrica de dados, clique em **novo conjunto de dados** botão na barra de ferramentas e clique em **tabela Blob** no menu suspenso. 
2. Substitua o seguinte trecho JSON JSON no painel direito: 

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
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1,
            		"waitOnExternal": {}
       		 	}
    		}
		}

		
     Observe o seguinte:
	
	- local **tipo** é definido como **AzureBlobLocation**.
	- **linkedServiceName** é definido como **StorageLinkedService**. Você tivesse criado esse serviço vinculado na etapa 2.
	- **folderPath** é definido como o **adftutorial** contêiner. É possível também especificar o nome de um blob dentro da pasta. Como você não está especificando o nome do blob, dados de todos os blobs no contêiner são considerados como um entrada de dados.  
	- formato **tipo** é definido como **TextFormat**
	- Há dois campos no arquivo de texto – **FirstName** e **LastName** – separados por um caractere de vírgula (* * columnDelimiter * *)	
	- O **disponibilidade** é definido como **por hora** (* * frequência * * é definido como **hora** e **intervalo** é definido como **1** ), de modo que o serviço de dados Factory irá procurar dados de entrada a cada hora, na pasta raiz no contêiner de blob (* * adftutorial * *) especificado. 
	

	Se você não especificar um **fileName** para um **entrada** **tabela**, todos os arquivos/blobs da pasta entrada (* * folderPath * *) são considerados como entradas. Se você especificar um nome de arquivo em JSON, apenas arquivo/blob especificado será considerado como entrada de asn. Consulte os arquivos de exemplo de [tutorial][adf-tutorial] para obter exemplos.
 
	Se você não especificar um **fileName** para um **tabela de saída**, o gerados arquivos de **folderPath** são nomeados no formato a seguir: dados & lt; GUID & gt;. txt (exemplo: Data.0a405f8a 93ff 4c6f b3be f69616f1df7a.txt.).

	Para definir **folderPath** e **fileName** dinamicamente com base no horário **SliceStart**, use a propriedade **partitionedBy**. No exemplo a seguir, folderPath usa o ano, mês e dia de SliceStart (hora de início da fatia que está sendo processada) e fileName usa a hora de SliceStart. Por exemplo, se uma fatia é produzida para 2014-10-20T08:00:00, o folderName é definido como wikidatagateway/wikisampledataout/2014/10/20 e o fileName é definido como 08.csv.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

	> [AZURE.NOTE]Consulte [referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.

2. Clique em **Deploy** na barra de ferramentas para criar e implantar o **EmpTableFromBlob** tabela. Confirme que você vê o **tabela CRIADA com êxito** mensagem na barra de título do Editor.

### Criar tabela de saída
Nesta parte da etapa, você criará uma tabela de saída denominada **EmpSQLTable** que aponta para uma tabela do SQL no SQL Azure banco de dados que é representado pelo **AzureSqlLinkedService** vinculado de serviço.

1. No **Editor** da fábrica de dados, clique em **novo conjunto de dados** botão na barra de ferramentas e clique em **tabela SQL Azure** no menu suspenso. 
2. Substitua o seguinte trecho JSON JSON no painel direito:

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
            		"type": "AzureSqlTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "AzureSqlLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1            
        		}
    		}
		}


		
     Observe o seguinte:
	
	* local **tipo** é definido como **AzureSQLTableLocation**.
	* **linkedServiceName** é definido como **AzureSqlLinkedService** (você tivesse criado esse serviço vinculado na etapa 2).
	* **tablename** é definido como **emp**.
	* Há três colunas – **ID**, **FirstName**, e **LastName** – na tabela emp no banco de dados, mas a ID é uma coluna de identidade, então você precisa apenas especificar **FirstName** e **LastName** aqui.
	* O **disponibilidade** é definido como **por hora** (* * frequência * * definido como **hora** e **intervalo** definida como **1**). O serviço de dados Factory irá gerar uma fatia de dados de saída cada hora o **emp** tabela no banco de dados do SQL Azure.


3. Clique em **Deploy** na barra de ferramentas para criar e implantar o **EmpSQLTable** tabela.


## <a name="CreateAndRunAPipeline"></a>Etapa 4: Criar e executar um pipeline
Nesta etapa, você criará um pipeline com um **cópia atividade** que usa **EmpTableFromBlob** como entrada e **EmpSQLTable** como saída.

1. No **Editor** da fábrica de dados, clique em **novo pipeline** na barra de ferramentas. Clique em **... (Elipse)** na barra de ferramentas se você não enxergar o botão. Como alternativa, você pode clicar com o botão direito em **Pipelines** no modo de exibição de árvore e clicar em **Novo pipeline**.

	![Novo botão de Pipeline Editor][image-editor-newpipeline-button]
 
2. Substitua o seguinte trecho JSON JSON no painel direito:

         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Azure SQL database",
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
        		],

				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
      		}
		} 

	Observe o seguinte:

	- Na seção de atividades, há apenas uma atividade cuja **tipo** é definido como **CopyActivity**.
	- A atividade é definida como de entrada **EmpTableFromBlob** e de saída para a atividade é definida como **EmpSQLTable**.
	- No **transformação** seção, **BlobSource** é especificado como o tipo de fonte e **SqlSink** é especificado como o tipo de coletor.

	> [AZURE.NOTE]Substitua o valor da **Iniciar** propriedade com o dia atual e **final** valor com o dia seguinte. Você pode especificar apenas a parte da data e ignorar a parte de hora do tempo Data. Por exemplo, "2015-02-03", que é equivalente a "2015-02-03T00:00:00Z" ambos começam e datetimes final deve estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O **final** tempo é opcional, mas usaremos neste tutorial. Se você não especificar o valor para o **final** propriedade, ele é calculado como "* * início + 48 horas * *". Para executar o pipeline indefinidamente, especifique **9999-09-09** como o valor para o **final** propriedade. No exemplo acima, como cada fatia de dados é produzida por hora, haverá 24 fatias de dados.
	
	> [AZURE.NOTE]Consulte [referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.

4. Clique em **Deploy** na barra de ferramentas para criar e implantar o **ADFTutorialPipeline**. Confirme que você vê o **PIPELINE criado com êxito** mensagem.
5. Agora, feche o **Editor** lâmina clicando **X**. Clique em **X** novamente para fechar a lâmina de ADFTutorialDataFactory com o modo de exibição de árvore e barra de ferramentas. Se você vir **suas edições não salvas serão descartadas** clique em **OK**.
6. Seja para o **DATA FACTORY** lâmina para o **ADFTutorialDataFactory**.

**Parabéns!** Você criou um data factory do Azure, serviços vinculados, tabelas e uma pipeline e a pipeline agendada com êxito.
 
### Exibir a fábrica de dados em um modo de exibição de diagrama 
1. No **DATA FACTORY** lâmina, clique em **diagrama**.

	![Lâmina de fábrica de dados - diagrama de bloco][image-datafactoryblade-diagramtile]

2. Você deverá ver o diagrama semelhante ao seguinte:

	![Exibição de diagrama][image-data-factory-get-started-diagram-blade]

	Você pode aumentar o zoom, zoom, aplicar zoom de 100%, zoom ajustar automaticamente posicionar pipelines e tabelas e mostrar informações de linhagem (realça itens upstream e downstream dos itens selecionados). Você pode blick de duplo em um objeto (tabela de entrada/saída ou pipeline) para ver as propriedades para ele. 
3. Clique em **ADFTutorialPipeline** na exibição de diagrama e clique **Abrir pipeline**. Você deve ver as atividades no pipeline com conjuntos de dados de entrada e saídos para as atividades. Neste tutorial, você pode ter apenas uma atividade no pipeline (atividade de cópia) com EmpTableBlob como dataset de entrada e EmpSQLTable como dataset de saída.   

	![Pipeline aberto](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. Clique em **Data factory** na navegação estrutural no canto superior esquerdo para voltar para o modo de exibição de diagrama. O modo de exibição de diagrama exibe todos os pipelines. Neste exemplo, somente você ter criado um pipeline.
 

## <a name="MonitorDataSetsAndPipeline"></a>Etapa 5: Monitorar a conjuntos de dados e o pipeline
Nesta etapa, você utilizará o Portal do Azure para monitorar o que está acontecendo em um data factory do Azure. Você também pode usar os cmdlets do PowerShell para monitorar conjuntos de dados e pipelines. Para obter detalhes sobre como usar os cmdlets para monitoramento, consulte [monitorar e gerenciar dados fábrica usando Cmdlets do PowerShell][monitor-manage-using-powershell].

1. Navegue até [Portal do Azure (visualização)][azure-preview-portal] se você não estiver com ele aberto. 
2. Se a lâmina de **ADFTutorialDataFactory** não estiver aberto, abra-o clicando em **ADFTutorialDataFactory** sobre o **quadro inicial**. 
3. Você verá a contagem e os nomes das tabelas e do pipeline criados nessa folha.

	![página inicial com nomes][image-data-factory-get-started-home-page-pipeline-tables]

4. Agora, clique em **conjuntos de dados** lado a lado.
5. No **conjuntos de dados** lâmina, clique em **EmpTableFromBlob**. Esta é a tabela de entrada para o **ADFTutorialPipeline**.

	![Conjuntos de dados com EmpTableFromBlob selecionado][image-data-factory-get-started-datasets-emptable-selected]   
5. Observe que as fatias de dados até a hora atual já foram produzidas e são **pronto** porque o **emp.txt** arquivo existe sempre no contêiner de blob: **adftutorial\input**. Confirme que nenhuma fatia aparecerão no **recentemente falha fatias** seção na parte inferior.

	Ambos **atualizado recentemente fatias** e **recentemente falha fatias** listas são classificadas pelo **hora da última atualização**. O tempo de atualização de uma fatia é alterado nas seguintes situações.
    

	-  Você atualiza o status da fatia manualmente, por exemplo, usando o **conjunto AzureDataFactorySliceStatus** (ou) clicando em **executar** no **FATIA** blade da fatia.
	-  A fatia é alterado devido a uma execução (por exemplo, uma execução de Introdução, uma execução finalizada e falha, uma execução terminou e foi bem-sucedida, etc).
 
	Clique no título da lista ou **... (reticências)** Para ver a lista de intervalos de maior. Clique em **filtro** na barra de ferramentas para filtrar as fatias.
	
	Para exibir as fatias de dados classificadas pelas horas de início/término fatia em vez disso, clique em **fatias de dados (por hora fatia)** lado a lado.

	![Fatias de dados por intervalo de tempo][DataSlicesBySliceTime]

6. Agora, no **conjuntos de dados** lâmina, clique o **EmpSQLTable**. Esta é a tabela de saída para o **ADFTutorialPipeline**.

	![blade de conjuntos de dados][image-data-factory-get-started-datasets-blade]



	 
6. Você deve ver o **EmpSQLTable** lâmina conforme mostrado abaixo:

	![blade de tabela][image-data-factory-get-started-table-blade]
 
7. Observe que as fatias de dados até a hora atual já foram produzidas e são **pronto**. Nenhuma fatia aparecerá no **fatias problema** seção na parte inferior.
8. Clique em **... (Reticências)** Para ver todas as fatias.

	![blade de fatias de dados][image-data-factory-get-started-dataslices-blade]

9. Clique em qualquer fatia de dados na lista e você deverá ver o **FATIA de dados** lâmina.

	![blade de fatias de dados][image-data-factory-get-started-dataslice-blade]
  
	Se a fatia não está no **pronto** estado, você pode ver as fatias upstream que não estão prontos e estão bloqueando a fatia atual da execução no **fatias Upstream que não estão prontas** lista.

11. No **FATIA de dados** lâmina, você deverá ver toda a atividade é executada na lista na parte inferior. Clique em uma **atividade execute** para ver o **detalhes de execução da atividade** blade.

	![Detalhes da execução de atividade][image-data-factory-get-started-activity-run-details]

	
12. Clique em **X** para fechar todos os blades até voltar na lâmina inicial para o **ADFTutorialDataFactory**.
14. (opcional) Clique em **Pipelines** na home page do **ADFTutorialDataFactory**, clique em **ADFTutorialPipeline** no **Pipelines** blade e drill-through tabelas de entrada (* * consumido * *) ou tabelas de saída (* * Produced * *).
15. Iniciar **SQL Server Management Studio**, conecte-se ao banco de dados do SQL Azure e verifique se as linhas são inseridas a **emp** tabela no banco de dados.

	![resultados da consulta SQL][image-data-factory-get-started-sql-query-results]


## Resumo 
Neste tutorial, você criou uma data factory do Azure para copiar dados de um blob do Azure para um banco de dados SQL do Azure. Você usou o Portal de visualização do Azure para criar a fábrica de dados, serviços vinculados, tabelas e um pipeline. Aqui estão as etapas de alto nível executadas nesse tutorial:

1.	Criar um Azure **fábrica dados**.
2.	Criar **vinculado serviços** que vinculam os armazenamentos de dados e computa (conhecido como **Serviços vinculados**) para o alocador de dados.
3.	Criar **tabelas** que descrevem entrada de dados e os dados de saída para pipelines.
4.	Criar **pipelines**. Uma pipeline consiste em uma ou mais atividades e processa as entradas e produz as saídas. Definir o período ativo para o pipeline especificando **Iniciar** tempo e **final** tempo para o pipeline. O período ativo define a duração de tempo em que serão produzidas as fatias de dados. 


> [AZURE.NOTE]Para obter uma lista de atividades com suporte, consulte [Pipelines e atividades][msdn-activities] tópico e para obter uma lista de serviços vinculados com suporte, consulte [Serviços vinculados][msdn-linkedservices] tópico na biblioteca MSDN.
> 
> Para fazer este tutorial usando o PowerShell do Azure, consulte [criar e monitorar uma fábrica de dados usando o PowerShell Azure][monitor-manage-using-powershell].

## Próximas etapas

Artigo | Descrição
------ | ---------------
[Copiar dados com o Azure dados Factory - atividade de cópia][copy-activity] | Este artigo fornece uma descrição detalhada do **cópia atividade** usado neste tutorial. 
[Ativar pipelines de trabalhar com dados no local][use-onpremises-datasources] | Este artigo possui um passo a passo que mostra como copiar dados de um **banco de dados do SQL Server local** para um blob do Azure. 
[Tutorial: Mover e processar os arquivos de log usando o alocador de dados][adf-tutorial] | Este artigo fornece uma **passo a passo de ponta a ponta** que mostra como implementar um **cenário do mundo real** usando a fábrica de dados do Azure para transformar dados de arquivos de log em ideias.
[Solucionar problemas de fábrica de dados][troubleshoot] | Este artigo descreve como **solucionar** problemas de fábrica de dados do Azure. Você pode testar o passo a passo neste artigo no ADFTutorialDataFactory introduzindo um erro (excluindo a tabela no Banco de Dados SQL do Azure). 
[Referência do desenvolvedor de fábrica de dados do Azure][developer-reference] | A referência do desenvolvedor tem o conteúdo de referência abrangente de cmdlets, o script JSON, funções, etc... 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started-using-editor/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started-using-editor/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started-using-editor/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started-using-editor/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started-using-editor/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started-using-editor/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started-using-editor/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started-using-editor/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started-using-editor/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started-using-editor/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started-using-editor/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started-using-editor/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started-using-editor/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started-using-editor/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started-using-editor/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started-using-editor/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started-using-editor/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started-using-editor/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started-using-editor/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started-using-editor/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started-using-editor/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started-using-editor/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-not-available.png

<!---HONumber=GIT-SubDir--> 