<properties 
	pageTitle="Editor do Azure Data Factory" 
	description="Descreve o Editor do Azure Data Factory, que permite criar, editar e implantar definições de JSON de serviços vinculados, tabelas e pipelines usando uma interface do usuário leve baseada na Web." 
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
	ms.date="07/07/2015" 
	ms.author="spelluru"/>

# Editor do Azure Data Factory
O Editor do Azure Data Factory é um editor Web leve que faz parte do Portal de Visualização do Azure, o qual permite a você criar, editar e implantar arquivos JSON de todas as entidades do Azure Data Factory. Isso permite que você crie serviços vinculados, conjuntos de dados e pipelines, usando os modelos JSON que vêm de fábrica com o serviço Data Factory. Se você for novo na utilização do PowerShell, isso elimina a necessidade de instalar, aumentando a capacidade do Azure PowerShell de criar fábricas de dados do Azure.

## Iniciando o Editor Data Factory
Para iniciar o Editor Data Factory, clique no bloco **Criar e implantar** na folha **Data Factory** para o seu Azure Data Factory.

![Bloco Criar e implantar][author-and-deploy-tile]

Você verá o Editor Data Factory, conforme mostrado na imagem a seguir:
 
![Editor Data Factory][data=factory-editor]
 
## Criando novos serviços vinculados, conjuntos de dados e pipelines
Existem quatro botões na barra de ferramentas que você pode usar para criar entidades do Azure Data Factory.
 
- **Novo armazenamento de dados** para criar um serviço de armazenamento de dados vinculado. Quando clicar nesse botão, você verá um menu com as seguintes opções: Armazenamento do Azure, Banco de dados SQL do Azure, Banco de dados SQL Server local.
- **Nova computação** para criar um serviço de computação vinculado. Quando você clica nesse botão, você verá um menu com as seguintes opções: cluster HDInsight sob demanda, cluster HDInsight, Serviço do AzureML vinculado.      
- **Novo conjunto de dados** para criar um conjunto de dados. Quando clicar nesse botão, você verá as seguintes opções: Tabela do Blob, Tabela do SQL do Azure, Tabela local.  
- **Novo pipeline** para criar um pipeline. Clique em **... (reticências)** na barra de ferramentas se você não enxergar este botão nessa barra.
 
### Como criar um serviço de armazenamento vinculado
1. Clique em **Novo armazenamento de dados**, e clique em uma das opções no menu.
 
	![Menu Novo armazenamento de dados][new-data-store-menu] 
2. Você verá o modelo JSON para criar um serviço de armazenamento vinculado na **Tela do editor** à direita. Você verá também que um nó de rascunho é exibido em **Rascunhos**. Faça o seguinte:
	1. Para **Armazenamento do Azure**: substitua **<nome da conta>** e **<chave da conta>** pelo nome e a chave da sua conta de armazenamento do Azure.
	2. Para **Banco de dados SQL do Azure**: substitua **<nome do servidor>** pelo nome do seu servidor SQL do Azure, **<nome do banco de dados>** pelo nome do banco de dados, **<nome de usuário>@<nome do servidor>** pelo nome do usuário e **<senha>** pela senha da conta de usuário. 
	3. Para **Banco de dados SQL Server local**: substitua **<nome do servidor>** pelo nome do seu SQL Server local, **<nome do banco de dados>** pelo nome do banco de dados, **<nome de usuário>** pelo nome do usuário e **<senha>** pela senha da conta de usuário.
4. Clique em **Implantar** na barra de ferramentas para implantar o serviço vinculado. Você pode clicar em **Descartar** para descartar o rascunho JSON criado.
 
	![Botão Implantar][deploy-button]

1. Você deve ver o status da operação de implantação na barra de título.

	![Implantar mensagem de êxito][deploy-success-message]
2. Se a operação de implantação for bem-sucedida, você verá o serviço vinculado criado no modo de exibição de árvore à esquerda.
  
	![StorageLinkedService em modo de exibição de árvore][storagelinkedservice-in-treview]

### Como criar um serviço de computação vinculado
1. Clique em **Nova computação** e clique em uma das opções no menu.
 
	![Menu Nova computação][new-compute-menu] 
2. Você verá o modelo JSON para criar um serviço de computação vinculado na Tela do editor à direita. Faça o seguinte:
	1. Para **Cluster HDInsight sob demanda**, especifique valores para as seguintes propriedades: 
		1. Para a propriedade **clusterSize**, especifique o tamanho do cluster do HDInsight que você deseja que o serviço Data Factory crie em tempo de execução. 
		2. Para a propriedade **jobsContainer**, especifique o nome do contêiner de blob padrão onde você deseja que os logs de cluster sejam armazenados.
		3. Para a propriedade **timeToLive**, especifique o tempo ocioso permitido antes de o cluster HDInsight ser excluído. Por exemplo: 00:05:00 indica que o cluster deve ser excluído após 5 minutos de tempo ocioso.
		4. Para a propriedade **versão**, especifique a versão do HDInsight para o cluster (padrão: versão 3.1).
		5. Para a propriedade **linkedServiceName**, especifique o serviço de armazenamento do Azure vinculado a ser associado com o cluster HDInsight. 
	6. Para **Cluster HDInsight** (traga o seu próprio), especifique valores para as seguintes propriedades:
		1. Para a propriedade **clusterUri**, especifique a URL para seu cluster HDInsight. 
		2. Para a propriedade **userName**, especifique a conta de usuário que o serviço Data Factory deve usar para se conectar ao cluster HDInsight. 
		3. Para a propriedade **password**, especifique a senha para a conta de usuário. 
		4. Para a propriedade **linkedServiceName**, especifique o serviço de armazenamento do Azure vinculado que é associado ao cluster HDInsight.
	5. Para **Serviço AzureML vinculado**, especifique as seguintes propriedades:
		1. Para a propriedade **mlEndPoint**, especifique a URL de pontuação do lote de Aprendizado de Máquina do Azure.
		2. Para a propriedade **apiKey**, especifique a chave de API para o modelo de espaço de trabalho publicado.
3. Clique em **Implantar** na barra de ferramentas para implantar o serviço vinculado.

> [AZURE.NOTE]Consulte o tópico [Serviços Vinculados][msdn-linkedservices-reference] na biblioteca MSDN para obter descrições dos elementos JSON que são usados para definir um serviço vinculado do Azure Data Factory.

### Para criar um novo conjunto de dados
1. Clique em **Novo conjunto de dados** e clique em uma das opções no menu.
2. Você verá o modelo JSON para criar um conjunto de dados na Tela do editor, à direita. Faça o seguinte: 
	1. Para **Tabela de Blob**, especifique valores para as seguintes propriedades:
	2. Para **Tabela SQL do Azure** ou **Tabela local**, especifique valores para as seguintes propriedades: 
		1. Na seção **local**: 
			2. Para a propriedade **linkedServiceName**, especifique o nome do serviço vinculado que se refere ao banco de dados SQL do Azure/SQL Server local.
			2. Para a propriedade **tableName**, especifique o nome da tabela na instância do Banco de Dados SQL do Azure/SQL Server local ao qual o serviço vinculado se refere.
		3. Na seção **disponibilidade**:
			1. Para a propriedade **frequência**, especifique a unidade de tempo para a produção de fatias de dados. Valores de frequência para os quais há suporte: Minuto, Hora, Dia, Semana, Mês.
			2. Para a propriedade **intervalo**, especifique o intervalo na frequência definida. **frequência** definida como **Hora** e **intervalo** definido como **1** indica que novas fatias de dados devem ser produzidas a cada hora. 
		3. Na seção **estrutura**: 
			1. especifique os nomes e tipos de colunas, conforme mostrado no exemplo a seguir:
				
				    "structure":
	        		[
	                	{ "name": "FirstName", "type": "String"},
	                	{ "name": "LastName", "type": "String"}
	        		],
	     
> [AZURE.NOTE]Consulte o tópico [Tabelas][msdn-tables-reference] na biblioteca MSDN para obter descrições dos elementos JSON que são usados para definir uma tabela do Azure Data Factory.
 		           
### Para criar e ativar um pipeline 
1. Clique em **Novo pipeline** na barra de ferramentas. Se você não puder ver o botão **Novo pipeline**, clique em **... (reticências)** Para vê-lo.   
2. Você verá o modelo JSON para criação de um pipeline na Tela do editor, à direita. Faça o seguinte: 
	1. Para a propriedade **descrição**, especifique a descrição para o pipeline.
	2. Para a seção **atividades**, adicione atividades ao pipeline. Exemplo:
	 
			"activities":	
			[
				{
					"name": "CopyFromBlobToSQL",
					"description": "Push Regional Effectiveness Campaign data to Azure SQL",
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
	3. Para a propriedade **início**, especifique quando o processamento de dados é iniciado ou quando as fatias de dados de serão processadas. Exemplo: 2014-05-01T00:00:00Z.
	4. Para a propriedade **fim**, especifique quando termina o processamento de dados. Exemplo: 2014-05-01T00:00:00Z.       

> [AZURE.NOTE]Consulte o tópico [Pipelines e Atividades][msdn-pipelines-reference] na biblioteca MSDN para obter descrições dos elementos JSON que são usados para definir um pipeline do Azure Data Factory.

## Para adicionar uma definição de atividade a um pipeline JSON
Você pode adicionar uma definição de atividade a um pipeline JSON clicando em **Adicionar Atividade** na barra de ferramentas. Quando você clica nesse botão, pode escolher o tipo de atividade que você deseja que seja adicionada ao pipeline.

![Opções de Adicionar Atividade][add-activity-options]

Se você deseja copiar dados de um banco de dados SQL do Azure para armazenamento de blob do Azure e processar os dados no armazenamento de blob usando o script de Pig em um cluster HDInsight, primeiro você adiciona uma **Atividade de Cópia** e, em seguida, adiciona uma **Atividade Pig** ao pipeline. Isso cria duas seções dentro da seção atividades do pipeline JSON. Atividade de pig é apenas a atividade de HDInsight com a transformação do Pig.

	"activities": [
    	{
    		"name": "CopyFromTabletoBlob",
	        "type": "CopyActivity",
			...
		}
		{
			"name": "ProcessBlobDataWithPigScript",
            "type": "HDInsightActivity",
			...
			"transformation": {
            	"type": "Pig",
				...
			}
		}
	]

## Iniciando um pipeline
Você pode especificar a data e hora de início e data e hora de término para um pipeline especificando valores para as propriedades de início e fim no JSON.

 	{
	    "name": "ADFTutorialPipeline",
	    "properties":
	    {   
	        "description" : "Copy data from a blob to Azure SQL table",
	        "activities":   
	        [
				...
	        ],
	
	        "start": "2015-02-13T00:00:00Z",
	        "end": "2015-02-14T00:00:00Z",
	        "isPaused": false
	    }
	} 
  
## Rascunhos no editor
A função Rascunhos permite que você salve seu trabalho temporariamente quando alternar entre diferente contextos ou navegar para uma entidade diferente no Data Factory. O tempo de vida dos Rascunhos está associado com a sessão do navegador. Se você fechar o navegador ou usar outro computador, os rascunhos deixarão de ficar disponíveis.

## Para descartar um rascunho JSON de uma entidade Data Factory
Você pode descartar a definição de JSON de uma entidade do Azure Data Factory clicando no botão **Descartar** na barra de ferramentas.

## Para clonar uma entidade Data Factory
Você pode clonar uma entidade do Azure Data Factory existente (serviço vinculado, tabela ou pipeline) selecionando a entidade no modo de exibição de árvore e clicando em **Clonar** na barra de ferramentas.

![Clonar uma entidade Data Factory][clone-datafactory-entity]

Você verá um novo rascunho criado sob o nó **Rascunhos** no modo de exibição de árvore.

## Para excluir uma entidade Data Factory
Você pode excluir uma entidade do Azure Data Factory (serviço vinculado, tabela ou pipeline) selecionando a entidade no modo de exibição de árvore e clicando em **Excluir** na barra de ferramentas, ou clicando com o botão direito na entidade e depois clicando em **Excluir**.

![Excluir uma entidade Data Factory][delete-datafactory-entity]

## Consulte também
Consulte o tópico [Introdução ao Azure Data Factory][data-factory-get-started] para obter instruções passo a passo de como criar uma fábrica de dados do Azure usando o Editor Data Factory.

[msdn-tables-reference]: https://msdn.microsoft.com/library/dn835002.aspx
[msdn-linkedservices-reference]: https://msdn.microsoft.com/library/dn834986.aspx
[msdn-pipelines-reference]: https://msdn.microsoft.com/library/dn834988.aspx

[data-factory-get-started]: data-factory-get-started.md

[author-and-deploy-tile]: ./media/data-factory-editor/author-and-deploy-tile.png
[data=factory-editor]: ./media/data-factory-editor/data-factory-editor.png
[new-data-store-menu]: ./media/data-factory-editor/new-data-store-menu.png
[new-compute-menu]: ./media/data-factory-editor/new-compute-menu.png
[deploy-button]: ./media/data-factory-editor/deploy-button.png
[deploy-success-message]: ./media/data-factory-editor/deploy-success-message.png
[storagelinkedservice-in-treview]: ./media/data-factory-editor/storagelinkedservice-in-treeview.png
[delete-datafactory-entity]: ./media/data-factory-editor/delete-datafactory-entity.png
[clone-datafactory-entity]: ./media/data-factory-editor/clone-datafactory-entity.png
[add-activity-options]: ./media/data-factory-editor/add-activity-options.png

<!---HONumber=July15_HO4-->