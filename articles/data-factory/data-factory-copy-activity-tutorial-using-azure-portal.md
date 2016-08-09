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
	ms.date="08/01/2016" 
	ms.author="spelluru"/>

# Tutorial: Criar um pipeline com a Atividade de Cópia usando o Data Factory Editor
> [AZURE.SELECTOR]
- [Visão geral do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Como usar o Editor do Data Factory](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Usando o PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Como usar o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)


Este tutorial contém as seguintes etapas:

Etapa | Descrição
-----| -----------
[Criar uma Data Factory do Azure](#create-data-factory) | Nesta etapa, você criará uma Azure Data Factory denominada **ADFTutorialDataFactory**.  
[Criar serviços vinculados](#create-linked-services) | Nesta etapa, você criará dois serviços vinculados: **AzureStorageLinkedService** e **AzureSqlLinkedService**. O AzureStorageLinkedService vincula o armazenamento do Azure e o AzureSqlLinkedService vincula o banco de dados SQL do Azure ao ADFTutorialDataFactory. Os dados de entrada do pipeline residem em um contêiner de blob no armazenamento de blob do Azure e os dados de saída serão armazenados em uma tabela no banco de dados SQL do Azure. Portanto, adicione esses dois repositórios de dados como serviços vinculados à data factory.      
[Criar conjuntos de dados de entrada e saída](#create-datasets) | Na etapa anterior, você criou serviços vinculados que fazem referência a repositórios de dados que contêm dados de entrada/saída. Nesta etapa, você definirá duas tabelas de data factory – **EmpTableFromBlob** e **EmpSQLTable** – que representam os dados de entrada/saída que são armazenados nos repositórios de dados. Na tabela EmpTableFromBlob, você especifica o contêiner de blob que contém um blob com os dados de origem e, na tabela EmpSQLTable, você especifica a tabela SQL que armazenará os dados de saída. Também é possível especificar outras propriedades, como a estrutura dos dados, a disponibilidade dos dados, etc... 
[Criar uma pipeline](#create-pipeline) | Nesta etapa, você criará um pipeline chamado **ADFTutorialPipeline** na ADFTutorialDataFactory. O pipeline terá uma **Atividade de Cópia**, que copia os dados de entrada do blob do Azure na tabela de saída SQL do Azure. A Atividade de Cópia executa a movimentação de dados no Azure Data Factory e é capacitada por um serviço globalmente disponível que pode copiar dados entre vários armazenamentos de dados de forma segura, confiável e escalonável. Veja o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia. 
[Monitorar o pipeline](#monitor-pipeline) | Nesta etapa, você vai monitorar fatias de tabelas de entrada e de saída usando o Portal do Azure.

> [AZURE.IMPORTANT] 
Leia o artigo [Visão geral do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e conclua as etapas de pré-requisito antes de executar este tutorial.

## Criar um data factory
Nesta etapa, você usa o Portal do Azure para criar uma data factory do Azure denominada **ADFTutorialDataFactory**.

1.	Depois de fazer logon no [Portal do Azure][azure-portal], clique em **NOVO** no canto inferior esquerdo, selecione **Análises de dados** na folha **Criar** e clique em **Data Factory** na folha **Análises de dados**.

	![Novo -> DataFactory][image-data-factory-new-datafactory-menu]

6. Na folha **Nova data factory**:
	1. Digite **ADFTutorialDataFactory** para o **nome**.
	
  		![Folha Nova data factory][image-data-factory-getstarted-new-data-factory-blade]
	2. Clique em **NOME DO GRUPO DE RECURSOS** e faça o seguinte:
		1. Clique em **Criar um novo grupo de recursos**.
		2. Na folha **Criar grupo de recursos**, digite **ADFTutorialResourceGroup** para o **nome** do grupo de recursos e clique em **OK**.

			![Criar grupo de recursos][image-data-factory-create-resource-group]

		Algumas das etapas neste tutorial supõem que você usa o nome: **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../resource-group-overview.md).
7. Na folha **Nova data factory**, observe que a opção **Adicionar ao Quadro Inicial** está selecionada.
8. Clique em **Criar** na folha **Nova data factory**.

	O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **O nome da data factory "ADFTutorialDataFactory" não está disponível**, altere o nome da data factory (por exemplo, yournameADFTutorialDataFactory) e tente criá-la novamente. Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos de Data Factory.
	 
	![Nome da data factory indisponível][image-data-factory-name-not-available]
	
	> [AZURE.NOTE] O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se publicamente visível.
	> 
	> Para criar instâncias do Data Factory, você precisa ser um colaborador/administrador da assinatura do Azure

9. Clique no hub **NOTIFICAÇÕES** à esquerda e procure as notificações do processo de criação. Clique em **X** para fechar a folha **NOTIFICAÇÕES** se ela estiver aberta.
10. Depois que a criação estiver concluída, você verá a folha **DATA FACTORY**, conforme mostrado abaixo.

    ![Página inicial da data factory][image-data-factory-get-stated-factory-home-page]

## Criar serviços vinculados
Serviços vinculados vinculam armazenamentos de dados ou serviços de computação para uma data factory do Azure. Um armazenamento de dados pode ser um armazenamento do Azure, um banco de dados SQL ou um banco de dados do SQL Server local.

Nesta etapa, você criará dois serviços vinculados: **AzureStorageLinkedService** e **AzureSqlLinkedService**. O serviço vinculado AzureStorageLinkedService vincula uma conta de armazenamento do Azure e o AzureSqlLinkedService vincula um banco de dados SQL do Azure à **ADFTutorialDataFactory**. Você criará, posteriormente neste tutorial, um pipeline que copia dados de um contêiner de blob no AzureStorageLinkedService em uma tabela SQL no AzureSqlLinkedService.

### Criar um serviço vinculado para a conta de armazenamento do Azure
1.	Na folha **DATA FACTORY**, clique no bloco **Criar e implantar** para iniciar o **Editor** da data factory.

	![Bloco Criar e implantar][image-author-deploy-tile]

	 
5. No **Editor**, clique no botão **Novo armazenamento de dados** na barra de ferramentas e selecione **Armazenamento do Azure** no menu suspenso. Você deve ver o modelo JSON para criar um serviço de armazenamento vinculado do Azure no painel à direita.

	![Botão Novo repositório de dados do editor][image-editor-newdatastore-button]
    
6. Substitua **accountname** e **accountkey** pelos valores de nome e de chave da sua conta de armazenamento do Azure.

	![JSON do armazenamento de Blob do editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)
	
	Consulte a [Referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.

6. Clique em **Implantar** na barra de ferramentas para implantar o AzureStorageLinkedService. Confirme que você vê a mensagem **SERVIÇO VINCULADO CRIADO COM ÊXITO** na barra de título.

	![Implantar armazenamento de Blob do editor][image-editor-blob-storage-deploy]

### Criar um serviço vinculado para o banco de dados SQL do Azure
1. No **Editor Data Factory**, clique no botão **Novo armazenamento de dados** na barra de ferramentas e selecione **Banco de dados SQL do Azure** no menu suspenso. Você deve ver o modelo JSON para criar o serviço vinculado do SQL do Azure no painel à direita.

	![Configurações do SQL do Azure do editor][image-editor-azure-sql-settings]

2. Substitua **servername**, **databasename**, **username@servername** e **password** pelos nomes de seu servidor SQL do Azure, banco de dados, conta de usuário e senha.
3. Clique em **Implantar** na barra de ferramentas para implantar o AzureSqlLinkedService.
   

## Criar conjuntos de dados
Na etapa anterior, você criou os serviços vinculados **AzureStorageLinkedService** e **AzureSqlLinkedService** para vincular uma conta de armazenamento do Azure e o banco de dados SQL do Azure à data factory: **ADFTutorialDataFactory**. Nesta etapa, você definirá duas tabelas de data factory, **EmpTableFromBlob** e **EmpSQLTable**, que representam os dados de entrada/saída que são armazenados nos repositórios de dados referenciados por AzureStorageLinkedService e AzureSqlLinkedService, respectivamente. Em EmpTableFromBlob, você especifica o contêiner de blob que contém um blob com os dados de origem e, em EmpSQLTable, você especifica a tabela SQL que armazenará os dados de saída.

### Criar conjunto de dados de entrada 
Uma tabela é um conjunto de dados retangular e tem um esquema. Nesta etapa, você criará uma tabela denominada **EmpBlobTable** que aponta para um contêiner de blob no Armazenamento do Azure representado pelo serviço vinculado **AzureStorageLinkedService**.

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
		    "linkedServiceName": "AzureStorageLinkedService",
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
	- **linkedServiceName** é definido como **AzureStorageLinkedService**. Você criou esse serviço vinculado na Etapa 2.
	- **folderPath** é definido como o contêiner **adftutorial**. É possível também especificar o nome de um blob dentro da pasta. Como você não está especificando o nome do blob, dados de todos os blobs no contêiner são considerados como um entrada de dados.
	- O **type** de formato é definido como **TextFormat**
	- Há dois campos no arquivo de texto, **FirstName** e **LastName**, separados por uma vírgula (**columnDelimiter**)
	- A **disponibilidade** é definida como **por hora** (a **frequência** é definida como **hora** e o **intervalo** é definido como **1**), de modo que o serviço Data Factory procure dados de entrada a cada hora na pasta raiz do contêiner de blob (**adftutorial**) especificado.
	

	Se você não especificar um **fileName** para uma **tabela** de **entradas**, todos os arquivos/blobs da pasta de entrada (**folderPath**) serão considerados como entradas. Se você especificar um nome de arquivo em JSON, apenas arquivo/blob especificado será considerado como entrada de asn.
 
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

### Criar conjunto de dados de saída
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


## Criar um pipeline
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

	![Pipeline aberto](./media/data-factory-copy-activity-tutorial-using-azure-portal/DiagramView-OpenPipeline.png)

4. Clique em **Data factory** na trilha do canto superior esquerdo para voltar para o modo de exibição de diagrama. O modo de exibição de diagrama exibe todos os pipelines. Neste exemplo, você criou somente um pipeline.
 

## Monitorar o pipeline
Nesta etapa, você utilizará o Portal do Azure para monitorar o que está acontecendo em um data factory do Azure.

1. Navegue até o [Portal do Azure (Visualização)][azure-portal] se não estiver com ele aberto.
2. Se a folha **ADFTutorialDataFactory** não estiver aberta, abra-a clicando em **ADFTutorialDataFactory** no **Quadro Inicial**.
3. Você verá a contagem e os nomes das tabelas e do pipeline criados nessa folha.

	![página inicial com nomes][image-data-factory-get-started-home-page-pipeline-tables]

4. Agora, clique no bloco **Conjuntos de dados**.
5. Na folha **Conjuntos de dados**, clique em **EmpTableFromBlob**. Esta é a tabela de entrada de **ADFTutorialPipeline**.

	![Conjuntos de dados com EmpTableFromBlob selecionado][image-data-factory-get-started-datasets-emptable-selected]
5. Observe que as fatias de dados até o momento atual já foram produzidas e estão **prontas**, pois o arquivo **emp.txt** sempre existe no contêiner de blob: **adftutorial\\input**. Confirme que nenhuma fatia apareça na seção **Fatias com falha recente**, na parte inferior.

	Ambas as listas, **Fatias atualizadas recentemente** e **Fatias com falha recente**, são classificadas pela **HORA DA ÚLTIMA ATUALIZAÇÃO**. A hora de atualização de uma fatia é alterada nas situações a seguir.
    

	-  Você atualiza o status da fatia manualmente, por exemplo, usando o **Set-AzureRmDataFactorySliceStatus** (ou) clicando em **EXECUTAR** na folha **FATIA** da fatia.
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
Neste tutorial, você criou uma data factory do Azure para copiar dados de um blob do Azure para um banco de dados SQL do Azure. Você utilizou o Portal do Azure para criar a data factory, os serviços vinculados, os conjuntos de dados e um pipeline. Aqui estão as etapas de alto nível executadas nesse tutorial:

1.	Foi criada uma **data factory** do Azure.
2.	Foram criados **serviços vinculados**:
	1. Um serviço vinculado do **Armazenamento do Azure** para vincular a conta do Armazenamento do Azure que contém os dados de entrada.
	2. Um serviço vinculado do **Azure SQL** para vincular o banco de dados SQL do Azure que contém os dados de saída.
3.	Foram criados **conjuntos de dados** que descrevem os dados de entrada e de saída para os pipelines.
4.	Foi criado um **pipeline** com uma **Atividade de Cópia** com **BlobSource** como origem e **SqlSink** como coletor.


## Veja também
| Tópico | Descrição |
| :---- | :---- |
| [Atividades de movimentação de dados](data-factory-data-movement-activities.md) | Este artigo fornece informações detalhadas sobre a Atividade de Cópia utilizada neste tutorial. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Este artigo o ajudará a compreender pipelines e atividades no Azure Data Factory e como aproveitá-los para construir fluxos de trabalho orientados a dados de ponta a ponta para seu cenário ou negócio. |
| [Conjunto de dados](data-factory-create-datasets.md) | Este artigo o ajudará a entender os conjuntos de dados no Azure Data Factory.
| [Monitorar e gerenciar pipelines usando o Aplicativo de Monitoramento](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorar, gerenciar e depurar seus pipelines usando o Aplicativo de Monitoramento e Gerenciamento. 

<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account



[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-deploy]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-diagram-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/CreateNewResourceGroup.png


[image-data-factory-new-datafactory-menu]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png


[image-data-factory-name-not-available]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png
 

<!---HONumber=AcomDC_0803_2016-->