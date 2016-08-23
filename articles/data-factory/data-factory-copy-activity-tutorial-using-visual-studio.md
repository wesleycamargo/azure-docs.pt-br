<properties 
	pageTitle="Tutorial: Criar um pipeline com a Atividade de Cópia usando o Visual Studio" 
	description="Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Visual Studio." 
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

# Tutorial: Criar um pipeline com a Atividade de Cópia usando o Visual Studio
> [AZURE.SELECTOR]
- [Visão geral do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Como usar o Editor do Data Factory](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Usando o PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Como usar o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Usando a API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [Usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)

Neste tutorial, você faz o seguinte usando o Visual Studio 2013:

1. Criar dois serviços vinculados: **AzureStorageLinkedService1** e **AzureSqlinkedService1**. O AzureStorageLinkedService1 vincula um armazenamento do Azure e AzureSqlLinkedService1 vincula um banco de dados SQL do Azure à data factory: **ADFTutorialDataFactoryVS**. Os dados de entrada do pipeline residem em um contêiner de blob no armazenamento de blobs do Azure e os dados de saída são armazenados em uma tabela no banco de dados SQL do Azure. Portanto, adicione esses dois repositórios de dados como serviços vinculados à data factory.
2. Criar duas tabelas de data factory: **EmpTableFromBlob** e **EmpSQLTable**, que representam os dados de entrada/saída que são armazenados nos repositórios de dados. Na tabela EmpTableFromBlob, você especifica o contêiner de blobs que contém um blob com os dados de origem e, na tabela EmpSQLTable, você especifica a tabela SQL que armazena os dados de saída. Também é possível especificar outras propriedades, como a estrutura dos dados, a disponibilidade dos dados, etc.
3. Criar um pipeline chamado **ADFTutorialPipeline** no ADFTutorialDataFactoryVS. O pipeline tem uma **Atividade de Cópia**, que copia os dados de entrada do blob do Azure na tabela de saída do SQL Azure. A Atividade de Cópia executa a movimentação de dados no Azure Data Factory e é capacitada por um serviço globalmente disponível que pode copiar dados entre vários armazenamentos de dados de forma segura, confiável e escalonável. Veja o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.
4. Criar uma data factory e implantar serviços vinculados, tabelas e o pipeline.

## Pré-requisitos

1. Você **deve** ler o artigo [Visão Geral do Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e concluir as etapas de pré-requisito antes de continuar.
2. Você deve ser um **administrador da assinatura do Azure** para poder publicar entidades de Data Factory no Azure Data Factory. Atualmente, isso é uma limitação.
3. Você deve ter os seguintes itens instalados no seu computador:
	- Visual Studio 2013 ou Visual Studio 2015
	- Baixe o SDK do Azure para Visual Studio 2013 ou Visual Studio de 2015. Navegue até a [Página de Download do Azure](https://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na seção **.NET**.
	- Baixe o plug-in Azure Data Factory para o Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Se você estiver usando o Visual Studio 2013, você também pode atualizar o plug-in, fazendo o seguinte: no menu, clique em **Ferramentas** -> **Extensões e atualizações** -> **Online** -> **Galeria do Visual Studio** -> **Ferramentas do Microsoft Azure Data Factory** -> **Atualizar**.
 


## Criar um projeto do Visual Studio 
1. Inicie o **Visual Studio 2013**. Clique em **Arquivo**, indique **Novo** e, em seguida, clique em **Projeto**. Você deverá ver a caixa de diálogo **Novo Projeto**.
2. Na caixa de diálogo **Novo Projeto**, selecione o modelo **DataFactory** e clique em **Projeto Vazio de Data Factory**. Se você não vir o modelo DataFactory, feche o Visual Studio, instale o SDK do Azure para Visual Studio 2013 e reabra o Visual Studio.

	![Caixa de diálogo Novo projeto](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)

3. Insira um **nome** para o projeto, o **local**, um nome para a **solução** e clique em **OK**.

	![Gerenciador de Soluções](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)

## Criar serviços vinculados
Serviços vinculados vinculam armazenamentos de dados ou serviços de computação para uma data factory do Azure. Um armazenamento de dados pode ser um armazenamento do Azure, um banco de dados SQL ou um banco de dados do SQL Server local.

Nesta etapa, você cria dois serviços vinculados: **AzureStorageLinkedService1** e **AzureSqlLinkedService1**. O serviço vinculado AzureStorageLinkedService1 vincula uma Conta de Armazenamento do Azure e o AzureSqlLinkedService vincula um banco de dados SQL do Azure à data factory: **ADFTutorialDataFactory**.

### Criar o serviço de armazenamento do Azure vinculado

4. Clique com o botão direito do mouse em **Serviços Vinculados** no Gerenciador de Soluções, aponte para **Adicionar** e clique em **Novo Item**.
5. Na caixa de diálogo **Adicionar Novo Item**, selecione **Serviço Vinculado de Armazenamento do Azure** na lista e clique em **Adicionar**.

	![Novo serviço vinculado](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
 
3. Substitua **accountname** e **accountkey** pelo nome da sua conta de armazenamento do Azure e sua chave.

	![Serviço vinculado de armazenamento do Azure](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)

4. Salve o arquivo **AzureStorageLinkedService1.json**.

### Criar um serviço vinculado do SQL do Azure

5. Clique com o botão direito no nó **Serviços Vinculados** no **Gerenciador de Soluções** novamente, aponte para **Adicionar** e clique em **Novo Item**.
6. Desta vez, selecione **Serviço Vinculado SQL do Azure** e clique em **Adicionar**.
7. No **arquivo AzureSqlLinkedService1.json**, substitua **servername**, **databasename**, **username@servername** e **password** pelos nomes do seu servidor SQL do Azure, banco de dados, conta de usuário e senha.
8.  Salve o arquivo **AzureSqlLinkedService1.json**.


## Criar conjuntos de dados
Na etapa anterior, você criou os serviços vinculados **AzureStorageLinkedService1** e **AzureSqlLinkedService1** para vincular uma conta de armazenamento do Azure e o banco de dados SQL do Azure à data factory: **ADFTutorialDataFactory**. Nesta etapa, você define duas tabelas de data factory, **EmpTableFromBlob** e **EmpSQLTable**, que representam os dados de entrada/saída armazenados nos repositórios de dados referidos por AzureStorageLinkedService1 e AzureSqlLinkedService1, respectivamente. Para EmpTableFromBlob, você especifica o contêiner de blobs que contém um blob com os dados de origem e, para EmpSQLTable, você especifica a tabela SQL que armazena os dados de saída.

### Criar conjunto de dados de entrada

9. Clique com o botão direito do mouse em **Tabelas** no **Gerenciador de Soluções**, aponte para **Adicionar** e clique em **Novo Item**.
10. Na caixa de diálogo **Adicionar Novo Item**, selecione **Blob do Azure** e clique em **Adicionar**.
10. Substitua o texto JSON pelo texto a seguir e salve o arquivo **AzureBlobLocation1.json**.

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
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
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

### Criar conjunto de dados de saída

11. Clique com o botão direito do mouse em **Tabelas** no **Gerenciador de Soluções** novamente, aponte para **Adicionar** e clique em **Novo item**.
12. Na caixa de diálogo **Adicionar Novo Item**, selecione **SQL do Azure** e clique em **Adicionar**.
13. Substitua o texto JSON pelo JSON a seguir e salve o arquivo **AzureSqlTableLocation1.json**.

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
		    "linkedServiceName": "AzureSqlLinkedService1",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

## Criar um pipeline 
Você criou tabelas e serviços vinculados de entrada/saída até o momento. Agora, você cria um pipeline com uma **Atividade de Cópia** para copiar dados do blob do Azure ao banco de dados SQL do Azure.


1. Clique com o botão direito do mouse em **Pipelines** no **Gerenciador de Soluções**, aponte para **Adicionar** e clique em **Novo Item**.
15. Selecione **Copiar Pipeline de Dados** na caixa de diálogo **Adicionar Novo Item** e clique em **Adicionar**.
16. Substitua o JSON pelo JSON a seguir e salve o arquivo **CopyActivity1.json**.
			
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
		          "style": "StartOfInterval",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z",
		    "isPaused": false
		  }
		}

## Publicar/implantar entidades de data factory
  
18. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e clique em **Publicar**.
19. Se a caixa de diálogo **Entrar na sua conta da Microsoft** for exibida, insira as credenciais da conta com a assinatura do Azure e clique em **entrar**.
20. Você deve ver a caixa de diálogo a seguir:

	![Caixa de diálogo Publicar](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)

21. Na página Configurar data factory, faça o seguinte:
	1. Selecione a opção **Criar Nova Data Factory**.
	2. Digite **VSTutorialFactory** para **Nome**.
	
		> [AZURE.NOTE]  
		O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber um erro sobre o nome do data factory durante a publicação, altere o nome (por exemplo, seunomeVSTutorialFactory) e tente publicar novamente. Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos de Data Factory.
		
	3. Selecione a assinatura certa para o campo **Assinatura**.
	4. Selecione o **grupo de recursos** para o data factory a ser criado.
	5. Selecione a **região** do data factory.
	6. Clique em **Avançar** para alternar para a página **Publicar Itens**.
23. Na página **Publicar Itens**, verifique se todas as entidades de Data Factories estão selecionadas e clique em **Avançar** para alternar para a página **Resumo**.
24. Examine o resumo e clique em **Avançar** para iniciar o processo de implantação e exibir o **Status da Implantação**.
25. Na página **Status da Implantação**, você deve ver o status do processo de implantação. Clique em Concluir depois que a implantação tiver terminado.

Observe o seguinte:

- Se você receber o erro: "**Esta assinatura não está registrada para usar o namespace Microsoft.DataFactory**", siga um destes procedimentos e tente publicar novamente:

	- No Azure PowerShell, execute o comando a seguir para registrar o provedor do Data Factory.
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		Você pode executar o comando a seguir para confirmar se o provedor do Data Factory está registrado.
	
			Get-AzureRmResourceProvider
	- Faça logon no [portal do Azure](https://portal.azure.com) usando a assinatura do Azure e navegue até uma folha do Data Factory (ou) crie um data factory no portal do Azure. Isso registra automaticamente o provedor para você.
- 	O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se publicamente visível.
- 	Para criar instâncias do Data Factory, você precisa ser um colaborador/administrador da assinatura do Azure

## Resumo
Neste tutorial, você criou uma data factory do Azure para copiar dados de um blob do Azure para um banco de dados SQL do Azure. Você utilizou o Visual Studio para criar a data factory, os serviços vinculados, os conjuntos de dados e um pipeline. Aqui estão as etapas de alto nível executadas nesse tutorial:

1.	Foi criado um **data factory** do Azure.
2.	Foram criados **serviços vinculados**:
	1. Um serviço vinculado do **Armazenamento do Azure** para vincular a conta do Armazenamento do Azure que contém os dados de entrada.
	2. Um serviço vinculado do **SQL Azure** para vincular o banco de dados SQL do Azure que contém os dados de saída.
3.	Foram criados **conjuntos de dados** que descrevem os dados de entrada e de saída para os pipelines.
4.	Foi criado um **pipeline** com uma **Atividade de Cópia** com **BlobSource** como origem e **SqlSink** como coletor.


## Usar o Gerenciador de Servidores para exibir os data factories

1. No **Visual Studio**, clique em **Exibir** no menu e em **Gerenciador de Servidores**.
2. Na janela Gerenciador de Servidores, expanda **Azure** e expanda **Data Factory**. Se **Entrar no Visual Studio** for exibido, insira a **conta** associada à sua assinatura do Azure e clique em **Continuar**. Insira a **senha** e clique em **Entrar**. O Visual Studio tenta obter informações sobre todos os data factories do Azure em sua assinatura. Você verá o status da operação na janela **Lista de Tarefas de Data Factory**. ![Gerenciador de Servidores](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)
3. Clique com o botão direito em uma data factory e selecione Exportar Data Factory para Novo Projeto para criar um projeto do Visual Studio com base em uma data factory existente.![Exportar data factory para um projeto VS](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)

## Atualizar ferramentas de data factory para o Visual Studio
Para atualizar as ferramentas da Azure Data Factory para o Visual Studio, faça o seguinte:

1. Clique em **Ferramentas** no menu e selecione **Extensões e Atualizações**.
2. Selecione **Atualizações** no painel esquerdo e selecione **Galeria do Visual Studio**.
4. Selecione **Ferramentas do Azure Data Factory para Visual Studio** e clique em **Atualizar**. Se você não vir essa entrada, você já tem a versão mais recente das ferramentas.

Confira [Monitorar os conjuntos de dados e o pipeline](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) para obter instruções sobre como usar o portal do Azure para monitorar o pipeline e os conjuntos de dados que você criou neste tutorial.

## Consulte também
| Tópico | Descrição |
| :---- | :---- |
| [Atividades de movimentação de dados](data-factory-data-movement-activities.md) | Este artigo fornece informações detalhadas sobre a Atividade de Cópia utilizada neste tutorial. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Este artigo o ajuda a compreender pipelines e atividades no Azure Data Factory e como aproveitá-los para construir fluxos de trabalho orientados a dados de ponta a ponta para seu cenário ou negócio. |
| [Conjunto de dados](data-factory-create-datasets.md) | Este artigo o ajuda a entender os conjuntos de dados no Azure Data Factory.
| [Monitorar e gerenciar pipelines usando o Aplicativo de Monitoramento](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorar, gerenciar e depurar seus pipelines usando o Aplicativo de Monitoramento e Gerenciamento. 

<!---HONumber=AcomDC_0817_2016-->