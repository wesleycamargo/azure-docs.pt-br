<properties 
	pageTitle="Tutorial: Criar um pipeline com a Atividade de Cópia usando o Data Factory Editor" 
	description="Neste tutorial, você criará um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Data Factory Editor no Portal do Azure." 
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
	ms.date="11/02/2015" 
	ms.author="spelluru"/>

# Tutorial: Criar um pipeline com a Atividade de Cópia usando o Data Factory Editor
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)



##Nesse tutorial
Este tutorial contém as seguintes etapas:

Etapa | Descrição
-----| -----------
[Etapa 1: Criar uma Azure Data Factory](#CreateDataFactory) | Nesta etapa, você criará uma Azure Data Factory denominada **ADFTutorialDataFactory**.  
[Etapa 2: Criar serviços vinculados](#CreateLinkedServices) | Nesta etapa, você criará dois serviços vinculados: **StorageLinkedService** e **AzureSqlLinkedService**. O StorageLinkedService vincula o armazenamento do Azure e o AzureSqlLinkedService vincula o banco de dados SQL do Azure à ADFTutorialDataFactory. Os dados de entrada do pipeline residem em um contêiner de blob no armazenamento de blob do Azure e os dados de saída serão armazenados em uma tabela no banco de dados SQL do Azure. Portanto, adicione esses dois repositórios de dados como serviços vinculados à data factory.      
[Etapa 3: Criar tabelas de entrada e saída](#CreateInputAndOutputDataSets) | Na etapa anterior, você criou serviços vinculados que fazem referência a repositórios de dados que contêm dados de entrada/saída. Nesta etapa, você definirá duas tabelas de data factory – **EmpTableFromBlob** e **EmpSQLTable** – que representam os dados de entrada/saída que são armazenados nos repositórios de dados. Na tabela EmpTableFromBlob, você especifica o contêiner de blob que contém um blob com os dados de origem e, na tabela EmpSQLTable, você especifica a tabela SQL que armazenará os dados de saída. Também é possível especificar outras propriedades, como a estrutura dos dados, a disponibilidade dos dados, etc... 
[Etapa 4: Criar e executar um pipeline](#CreateAndRunAPipeline) | Nesta etapa, você criará um pipeline chamado **ADFTutorialPipeline** na ADFTutorialDataFactory. O pipeline terá uma **Atividade de Cópia**, que copia os dados de entrada do blob do Azure na tabela de saída SQL do Azure.
[Etapa 5: Monitorar fatias e pipeline](#MonitorDataSetsAndPipeline) | Nesta etapa, você vai monitorar fatias de tabelas de entrada e saídas usando o Portal de Visualização do Azure.
 

## <a name="CreateDataFactory"></a>Etapa 1: Criar uma Azure Data Factory
Nesta etapa, você usa o Portal de Visualização do Azure para criar uma Azure Data Factory denominada **ADFTutorialDataFactory**.

1.	Depois de fazer logon no [Portal de Visualização do Azure][azure-preview-portal], clique em **NOVO** no canto inferior esquerdo, selecione **Análise de dados** na folha **Criar** e clique em **Data Factory** na folha **Análise de dados**. 

	![Novo -> DataFactory][image-data-factory-new-datafactory-menu]

6. Na folha **Nova data factory**:
	1. Digite **ADFTutorialDataFactory** para o **nome**. 
	
  		![Folha Nova data factory][image-data-factory-getstarted-new-data-factory-blade]
	2. Clique em **NOME DO GRUPO DE RECURSOS** e faça o seguinte:
		1. Clique em **Criar um novo grupo de recursos**.
		2. Na folha **Criar grupo de recursos**, digite **ADFTutorialResourceGroup** para o **nome** do grupo de recursos e clique em **OK**. 

			![Criar grupo de recursos][image-data-factory-create-resource-group]

		Algumas das etapas neste tutorial supõem que você usa o nome: **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](resource-group-overview.md).  
7. Na folha **Nova data factory**, observe que a opção **Adicionar ao Quadro Inicial** está selecionada.
8. Clique em **Criar** na folha **Nova data factory**.

	O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **O nome da data factory "ADFTutorialDataFactory" não está disponível**, altere o nome da data factory (por exemplo, yournameADFTutorialDataFactory) e tente criá-la novamente. Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos de Data Factory.
	 
	![Nome da data factory indisponível][image-data-factory-name-not-available]
	
	> [AZURE.NOTE]O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se publicamente visível.

9. Clique no hub **NOTIFICAÇÕES** à esquerda e procure as notificações do processo de criação. Clique em **X** para fechar a folha **NOTIFICAÇÕES** se ela estiver aberta.
10. Depois que a criação estiver concluída, você verá a folha **DATA FACTORY**, conforme mostrado abaixo.

    ![Página inicial da data factory][image-data-factory-get-stated-factory-home-page]

## <a name="CreateLinkedServices"></a>Etapa 2: Criar serviços vinculados
Serviços vinculados vinculam armazenamentos de dados ou serviços de computação para uma data factory do Azure. Um armazenamento de dados pode ser um armazenamento do Azure, um banco de dados SQL ou um banco de dados do SQL Server local.

Nesta etapa, você criará dois serviços vinculados: **StorageLinkedService** e **AzureSqlLinkedService**. O serviço vinculado StorageLinkedService vincula uma conta de armazenamento do Azure e o AzureSqlLinkedService vincula um banco de dados SQL do Azure à **ADFTutorialDataFactory**. Você criará, posteriormente neste tutorial, um pipeline que copia dados de um contêiner de blob no StorageLinkedService em uma tabela SQL no AzureSqlLinkedService.

### Criar um serviço vinculado para a conta de armazenamento do Azure
1.	Na folha **DATA FACTORY**, clique no bloco **Criar e implantar** para iniciar o **Editor** da data factory.

	![Bloco Criar e implantar][image-author-deploy-tile]

	Consulte o tópico [Editor Data Factory][data-factory-editor] para obter a visão geral detalhada do Editor Data Factory.
	 
5. No **Editor**, clique no botão **Novo armazenamento de dados** na barra de ferramentas e selecione **Armazenamento do Azure** no menu suspenso. Você deve ver o modelo JSON para criar um serviço de armazenamento vinculado do Azure no painel à direita.

	![Botão Novo repositório de dados do editor][image-editor-newdatastore-button]
    
6. Substitua **accountname** e **accountkey** pelos valores de nome e de chave da sua conta de armazenamento do Azure.

	![JSON do armazenamento de Blob do editor][image-editor-blob-storage-json]
	
	Consulte a [Referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.

6. Clique em **Implantar** na barra de ferramentas para implantar o StorageLinkedService. Confirme que você vê a mensagem **SERVIÇO VINCULADO CRIADO COM ÊXITO** na barra de título.

	![Implantar armazenamento de Blob do editor][image-editor-blob-storage-deploy]

### Criar um serviço vinculado para o banco de dados SQL do Azure
1. No **Editor Data Factory**, clique no botão **Novo armazenamento de dados** na barra de ferramentas e selecione **Banco de dados SQL do Azure** no menu suspenso. Você deve ver o modelo JSON para criar o serviço vinculado do SQL do Azure no painel à direita.

	![Configurações do SQL do Azure do editor][image-editor-azure-sql-settings]

2. Substitua **servername**, **databasename**, ****username@servername** e **password** pelos nomes do servidor SQL do Azure, do banco de dados, da conta de usuário e a senha.
3. Clique em **Implantar** na barra de ferramentas para implantar o AzureSqlLinkedService. 
   

## <a name="CreateInputAndOutputDataSets"></a>Etapa 3: Criar tabelas de entrada e saída
Na etapa anterior, você criou os serviços vinculados **StorageLinkedService** e **AzureSqlLinkedService** para vincular uma conta de armazenamento do Azure e o banco de dados SQL do Azure à data factory: **ADFTutorialDataFactory**. Nesta etapa, você vai definir duas tabelas de data factory – **EmpTableFromBlob** e **EmpSQLTable** – que representam os dados de entrada/saída que são armazenados nos repositórios de dados referidos por StorageLinkedService e AzureSqlLinkedService, respectivamente. Em EmpTableFromBlob, você especifica o contêiner de blob que contém um blob com os dados de origem e, em EmpSQLTable, você especifica a tabela SQL que armazenará os dados de saída.

### Criar tabela de entrada 
Uma tabela é um conjunto de dados retangular e tem um esquema. Nesta etapa, você criará uma tabela denominada **EmpBlobTable** que aponta para um contêiner de blob no Armazenamento do Azure representado pelo serviço vinculado **StorageLinkedService**.

1. No **Editor** da Data Factory, clique no botão **Novo conjunto de dados** na barra de ferramentas e clique em **Tabela de blobs** no menu suspenso. 
2. Substitua JSON no painel direito pelo trecho JSON a seguir: 

		{
		  "name": "EmpTableFromBlob",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
			  "fileName": "emp.txt",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		
     Observe o seguinte:
	
	- O **tipo** do conjunto de dados foi definido como **AzureBlob**.
	- **linkedServiceName** é definido como **StorageLinkedService**. Você criou esse serviço vinculado na Etapa 2.
	- **folderPath** é definido como o contêiner **adftutorial**. É possível também especificar o nome de um blob dentro da pasta. Como você não está especificando o nome do blob, dados de todos os blobs no contêiner são considerados como um entrada de dados.  
	- O **type** de formato é definido como **TextFormat**
	- Há dois campos no arquivo de texto, **FirstName** e **LastName**, separados por uma vírgula (**columnDelimiter**)	
	- A **availability** é definida como **hourly** (**frequency** definida como **hour** e **interval** definido como **1** ), de modo que o serviço Data Factory vai procurar dados de entrada a cada hora na pasta raiz do contêiner de blob (**adftutorial**) especificado. 
	

	Se você não especificar um **fileName** para uma **tabela** de **entradas**, todos os arquivos/blobs da pasta de entrada (**folderPath**) serão considerados como entradas. Se você especificar um nome de arquivo em JSON, apenas arquivo/blob especificado será considerado como entrada de asn. Consulte os arquivos do [tutorial][adf-tutorial] para obter exemplos.
 
	Se você não especificar um **fileName** para uma **tabela de saída**, os arquivos gerados no **folderPath** serão nomeados no seguinte formato: Data.&lt;Guid&gt;.txt (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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

	Consulte a [Referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.

2. Clique em **Implantar** na barra de ferramentas para criar e implantar a tabela **EmpTableFromBlob**. Confirme que você vê a mensagem **TABELA CRIADA COM ÊXITO** na barra de título do Editor.

### Criar tabela de saída
Nesta parte da etapa, você criará uma tabela de saída denominada **EmpSQLTable** que aponta para uma tabela SQL no banco de dados SQL do Azure que é representada pelo serviço vinculado **AzureSqlLinkedService**.

1. No **Editor** de Data Factory, clique no botão **Novo conjunto de dados** na barra de ferramentas e clique em **Tabela SQL do Azure** no menu suspenso. 
2. Substitua JSON no painel direito pelo trecho JSON a seguir:

		{
		  "name": "EmpSQLTable",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureSqlTable",
		    "linkedServiceName": "AzureSqlLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		
     Observe o seguinte:
	
	* O **tipo** do conjunto de dados foi definido como **AzureSQLTable**.
	* **linkedServiceName** é definido como **AzureSqlLinkedService** (você criou esse serviço vinculado na Etapa 2).
	* **tablename** está definido como **emp**.
	* Há três colunas – **ID**, **FirstName** e **LastName** – na tabela emp no banco de dados, mas ID é uma coluna de identidade, portanto, você precisa especificar somente **FirstName** e **LastName**.
	* A **availability** é definida como **hourly** (**frequency** definida como **hour** e **interval** definido como **1**). O serviço Data Factory gera uma fatia de dados de saída a cada hora na tabela **emp** no banco de dados SQL do Azure.


3. Clique em **Implantar** na barra de ferramentas para criar e implantar a tabela **EmpSQLTable**.


## <a name="CreateAndRunAPipeline"></a>Etapa 4: Criar e executar um pipeline
Nesta etapa, você cria um pipeline com uma **Atividade de Cópia** que utiliza **EmpTableFromBlob** como entrada e **EmpSQLTable** como saída.

1. No **Editor** da Data Factory, clique no botão **Novo pipeline** na barra de ferramentas. Clique em **... (Elipse)** na barra de ferramentas se você não enxergar o botão. Como alternativa, você pode clicar com o botão direito em **Pipelines** no modo de exibição de árvore e clicar em **Novo pipeline**.

	![Botão Novo Pipeline do editor][image-editor-newpipeline-button]
 
2. Substitua JSON no painel direito pelo trecho JSON a seguir:
		
		{
		  "name": "ADFTutorialPipeline",
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpTableFromBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "EmpSQLTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "BlobSource"
		          },
		          "sink": {
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z"
		  }
		} 

	Observe o seguinte:

	- Na seção de atividades, há apenas uma atividade cujo **type** é definido como **CopyActivity**.
	- A entrada da atividade é definida como **EmpTableFromBlob** e a saída da atividade é definida como **EmpSQLTable**.
	- Na seção **transformation**, **BlobSource** é especificado como o tipo de origem e **SqlSink** é especificado como o tipo de coletor.

	Substitua o valor da propriedade **start** pelo dia atual e o valor de **end** pelo dia seguinte. Você pode especificar apenas a parte da data e ignorar a parte de hora do valor de data/hora. Por exemplo, "2015-02-03", que é equivalente a "2015-02-03T00:00:00Z"
	
	Ambos os valores de data/hora de início e de término devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A hora de **end** é opcional, mas nós o usaremos neste tutorial.
	
	Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9999-09-09** como o valor para a propriedade **end**.
	
	No exemplo acima, como cada fatia de dados é produzida por hora, haverá 24 fatias de dados.
	
	Consulte a [Referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.

4. Clique em **Implantar** na barra de ferramentas para criar e implantar o **ADFTutorialPipeline**. Confirme que você vê a mensagem **PIPELINE CRIADO COM ÊXITO**.
5. Agora, feche a folha **Editor** clicando em **X**. Clique em **X** novamente para fechar a folha ADFTutorialDataFactory com o modo de exibição de árvore e a barra de ferramentas. Caso a mensagem **suas edições não salvas serão descartadas** seja exibida, clique em **OK**.
6. Você deve ser voltar à folha **DATA FACTORY** do **ADFTutorialDataFactory**.

**Parabéns!** Você criou um data factory do Azure, serviços vinculados, tabelas e uma pipeline e a pipeline agendada com êxito.
 
### Exibir a data factory em um Modo de Exibição de Diagrama 
1. Na folha **DATA FACTORY**, clique em **Diagrama**.

	![Folha Data Factory — bloco Diagrama][image-datafactoryblade-diagramtile]

2. Você deverá ver o diagrama semelhante ao seguinte:

	![Modo de Exibição de Diagrama][image-data-factory-get-started-diagram-blade]

	É possível ampliar, reduzir, aplicar zoom de 100%, ajustar nível de zoom, posicionar pipelines e tabelas automaticamente, bem como mostrar informações de linhagem (realça itens upstream e downstream dos itens selecionados). Você pode clicar duas vezes em um objeto (pipeline ou tabela de entrada/saída) para ver as propriedades dele. 
3. Clique com o botão direito do mouse em **ADFTutorialPipeline** no Modo de Exibição de Diagrama e clique **Abrir pipeline**. Você deve ver as atividades no pipeline juntamente com conjuntos de dados de entrada e saída das atividades. Neste tutorial, você tem apenas uma atividade no pipeline (Atividade de Cópia) com EmpTableBlob como conjunto de dados de entrada e EmpSQLTable como conjunto de dados de saída.   

	![Pipeline aberto](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. Clique em **Data factory** na trilha do canto superior esquerdo para voltar para o modo de exibição de diagrama. O modo de exibição de diagrama exibe todos os pipelines. Neste exemplo, você criou somente um pipeline.
 

## <a name="MonitorDataSetsAndPipeline"></a>Etapa 5: Monitorar os conjuntos de dados e o pipeline
Nesta etapa, você utilizará o Portal do Azure para monitorar o que está acontecendo em um data factory do Azure. Você também pode usar os cmdlets do PowerShell para monitorar conjuntos de dados e pipelines. Para obter detalhes sobre como utilizar os cmdlets para monitoramento, consulte [Monitorar e gerenciar a Data Factory utilizando os cmdlets do PowerShell][monitor-manage-using-powershell].

1. Navegue até o [Portal do Azure (Visualização)][azure-preview-portal] se não estiver com ele aberto. 
2. Se a folha **ADFTutorialDataFactory** não estiver aberta, abra-a clicando em **ADFTutorialDataFactory** no **Quadro Inicial**. 
3. Você verá a contagem e os nomes das tabelas e do pipeline criados nessa folha.

	![página inicial com nomes][image-data-factory-get-started-home-page-pipeline-tables]

4. Agora, clique no bloco **Conjuntos de dados**.
5. Na folha **Conjuntos de dados**, clique em **EmpTableFromBlob**. Esta é a tabela de entrada de **ADFTutorialPipeline**.

	![Conjuntos de dados com EmpTableFromBlob selecionado][image-data-factory-get-started-datasets-emptable-selected]   
5. Observe que as fatias de dados até o momento atual já foram produzidas e estão **prontas**, pois o arquivo **emp.txt** sempre existe no contêiner de blob: **adftutorial\\input**. Confirme que nenhuma fatia apareça na seção **Fatias com falha recente**, na parte inferior.

	Ambas as listas, **Fatias atualizadas recentemente** e **Fatias com falha recente**, são classificadas pela **HORA DA ÚLTIMA ATUALIZAÇÃO**. A hora de atualização de uma fatia é alterada nas situações a seguir.
    

	-  Você atualiza o status da fatia manualmente, por exemplo, usando o **Set-AzureDataFactorySliceStatus** (ou) clicando em **EXECUTAR** na folha **FATIA** da fatia.
	-  A fatia é alterada devido a uma execução (por exemplo, uma execução iniciada, uma execução finalizada e com falha, uma execução finalizada e bem-sucedida, etc).
 
	Clique no título das listas ou em **... (reticências)** para ver a lista maior de fatias. Clique em **Filtrar** na barra de ferramentas para filtrar as fatias.
	
	Para exibir as fatias de dados classificadas pelas horas de início/término da fatia, clique no bloco **Fatias de dados (por hora da fatia)**.

	![Fatias de Dados por Hora da Fatia][DataSlicesBySliceTime]

6. Agora, na folha **Conjuntos de dados**, clique em **EmpSQLTable**. Essa é a tabela de saída de **ADFTutorialPipeline**.

	![folha conjuntos de dados][image-data-factory-get-started-datasets-blade]



	 
6. Você deve ver a folha **EmpSQLTable** conforme mostrado abaixo:

	![folha tabela][image-data-factory-get-started-table-blade]
 
7. Observe que as fatias de dados até o momento atual já foram produzidas e estão **Prontas**. Nenhuma fatia aparece na seção **Fatias com problemas** na parte inferior.
8. Clique em **... (Reticências)** para ver todas as fatias.

	![folha fatias de dados][image-data-factory-get-started-dataslices-blade]

9. Clique em qualquer fatia de dados na lista e você deverá ver a folha **FATIA DE DADOS**.

	![folha fatia de dados][image-data-factory-get-started-dataslice-blade]
  
	Quando a fatia não está no estado **Pronto**, você pode ver as fatias upstream que não estão Prontas e estão impedindo a execução da fatia atual na lista **Fatias upstream que não estão prontas**.

11. Na folha **FATIA DE DADOS**, você deve ver que toda a atividade é executada na lista, na parte inferior. Clique em uma **execução de atividade** para ver a folha **DETALHES DE EXECUÇÃO DA ATIVIDADE**.

	![Detalhes da execução da atividade][image-data-factory-get-started-activity-run-details]

	
12. Clique em **X** para fechar todas as folhas até voltar à folha inicial de **ADFTutorialDataFactory**.
14. (opcional) Clique em **Pipelines** na página inicial de **ADFTutorialDataFactory**, clique em **ADFTutorialPipeline** na folha **Pipelines** e faça drill-through nas tabelas de entrada (**Consumed**) ou nas tabelas de saída (**Produced**).
15. Inicie o **SQL Server Management Studio**, conecte-se ao Banco de Dados SQL do Azure e verifique se as linhas estão inseridas na tabela **emp** do banco de dados.

	![resultados da consulta sql][image-data-factory-get-started-sql-query-results]


## Resumo 
Neste tutorial, você criou uma data factory do Azure para copiar dados de um blob do Azure para um banco de dados SQL do Azure. Você utilizou o Portal de Visualização do Azure para criar a data factory, serviços vinculados, tabelas e um pipeline. Aqui estão as etapas de alto nível executadas nesse tutorial:

1.	Criar uma Azure **Data Factory**.
2.	Criar **serviços vinculados** que vinculam repositórios de dados e computações (conhecidos como **Serviços Vinculados**) à data factory.
3.	Criar **tabelas** que descrevem os dados de entrada e de saída para os pipelines.
4.	Criar **pipelines**. Uma pipeline consiste em uma ou mais atividades e processa as entradas e produz as saídas. Defina o período ativo para o pipeline especificando a hora de **Início** e **Término** para o pipeline. O período ativo define a duração de tempo em que serão produzidas as fatias de dados. 


Para obter uma lista de atividades com suporte, consulte o tópico [Pipelines e atividades][msdn-activities] e, para obter uma lista de serviços vinculados com suporte, consulte o tópico [Serviços Vinculados][msdn-linkedservices] na biblioteca MSDN.
 
Para concluir este tutorial usando o PowerShell do Azure, consulte [Criar e monitorar uma data factory usando o PowerShell do Azure][monitor-manage-using-powershell].


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
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
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
 

<!---HONumber=Nov15_HO3-->