<properties 
	pageTitle="Tutorial: Criar um pipeline com a Atividade de Cópia usando o Visual Studio"
	description="Neste tutorial, você criará um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Visual Studio."
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
	ms.date="07/27/2015"
	ms.author="spelluru"/>

# Tutorial: Criar e monitorar uma data factory usando o Visual Studio
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)


##Nesse tutorial
Neste tutorial, você vai criar primeiro uma Azure Data Factory denominada **ADFTutorialDataFactoryVS** usando o Portal de visualização do Azure e, em seguida, faça o seguinte usando o Visual Studio 2013:

1. Crie dois serviços vinculados: **AzureStorageLinkedService1** e **AzureSqlinkedService1**. O AzureStorageLinkedService1 vincula um armazenamento do Azure e AzureSqlLinkedService1 vincula um Banco de Dados SQL do Azure para a data factory: **ADFTutorialDataFactoryVS**. Os dados de entrada do pipeline residem em um contêiner de blob no armazenamento de blob do Azure e os dados de saída serão armazenados em uma tabela no banco de dados SQL do Azure. Portanto, adicione esses dois repositórios de dados como serviços vinculados à data factory.
2. Crie duas tabelas de data factory: **EmpTableFromBlob** e **EmpSQLTable**, que representam os dados de entrada/saída que são armazenados nos repositórios de dados. Na tabela EmpTableFromBlob, você especifica o contêiner de blob que contém um blob com os dados de origem e, na tabela EmpSQLTable, você especifica a tabela SQL que armazenará os dados de saída. Também é possível especificar outras propriedades, como a estrutura dos dados, a disponibilidade dos dados, etc...
3. Crie um pipeline chamado **ADFTutorialPipeline** no ADFTutorialDataFactoryVS. O pipeline terá uma **Atividade de Cópia**, que copia os dados de entrada do blob do Azure na tabela de saída SQL do Azure. 


## <a name="CreateDataFactory"></a>Criar uma Azure Data Factory
Nesta etapa, você usa o Portal de Visualização do Azure para criar uma Azure Data Factory denominada **ADFTutorialDataFactoryVS**.

1.	Depois de fazer logon no [Portal de Visualização do Azure](http://portal.azure.com), clique em **NOVO** no canto inferior esquerdo, selecione **Análise de dados** na folha **Criar** e clique em **Data Factory** na folha **Análise de dados**. 

	![Novo -> DataFactory](./media/data-factory-get-started-using-vs/NewDataFactoryMenu.png)

6. Na folha **Nova data factory**:
	1. Digite **ADFTutorialDataFactoryVS** para o **nome**. 
	
  		![Folha Nova data factory](./media/data-factory-get-started-using-vs/getstarted-new-data-factory.png)
	2. Clique em **NOME DO GRUPO DE RECURSOS** e faça o seguinte:
		1. Clique em **Criar um novo grupo de recursos**.
		2. Na folha **Criar grupo de recursos**, digite **ADFTutorialResourceGroup** para o **nome** do grupo de recursos e clique em **OK**. 

			![Criar grupo de recursos](./media/data-factory-get-started-using-vs/CreateNewResourceGroup.png)

		Algumas das etapas neste tutorial supõem que você usa o nome: **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](resource-group-overview.md).  
7. Na folha **Nova data factory**, observe que a opção **Adicionar ao Quadro Inicial** está selecionada.
8. Clique em **Criar** na folha **Nova data factory**.

	O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **O nome da data factory "ADFTutorialDataFactoryVS" não está disponível**, altere o nome da data factory (por exemplo, yournameADFTutorialDataFactoryVS) e tente criá-la novamente. Use esse nome em vez de ADFTutorialFactory ao executar as etapas restantes neste tutorial. Consulte o tópico [Data Factory - regras de nomenclatura] [data-factory-naming-rules] para ver as regras de nomenclatura para artefatos de Data Factory.
	 
	![Nome da data factory indisponível](./media/data-factory-get-started-using-vs/getstarted-data-factory-not-available.png)

9. Clique no hub **NOTIFICAÇÕES** à esquerda e procure as notificações do processo de criação. Clique em **X** para fechar a folha **NOTIFICAÇÕES** se ela estiver aberta.
10. Depois que a criação estiver concluída, você verá a folha **DATA FACTORY**, conforme mostrado abaixo.

    ![Página inicial da data factory](./media/data-factory-get-started-using-vs/getstarted-data-factory-home-page.png)

## Criar e implantar as entidades de data factory usando o Visual Studio 

### Pré-requisitos
Você deve ter os seguintes itens instalados no computador: - Visual Studio 2013 - Baixe o SDK do Azure para Visual Studio 2013. Navegue até a [página de download do Azure](http://azure.microsoft.com/downloads/) e clique em **instalar VS 2013** na seção **.NET**.

### Passo a passo

#### Criar o projeto do Visual Studio 
1. Inicie o **Visual Studio 2013**. Clique em **Arquivo**, indique **Novo** e, em seguida, clique em **Projeto**. Você deve ver a caixa de diálogo **Novo projeto**.  
2. Na caixa de diálogo **Novo projeto**, selecione o modelo **DataFactory** e clique em **Projeto vazio de data factory**. Se você não vir o modelo DataFactory, feche o Visual Studio, instale o SDK do Azure para Visual Studio 2013 e reabra o Visual Studio.  

	![Caixa de diálogo Novo projeto](./media/data-factory-get-started-using-vs/new-project-dialog.png)

3. Insira um **nome** para o projeto, **local** e um nome para a **solução** e clique em **OK**.

	![Gerenciador de Soluções](./media/data-factory-get-started-using-vs/solution-explorer.png)

#### Criar serviços vinculados
Serviços vinculados vinculam armazenamentos de dados ou serviços de computação para uma data factory do Azure. Um armazenamento de dados pode ser um armazenamento do Azure, um banco de dados SQL ou um banco de dados do SQL Server local.

Nesta etapa, você criará dois serviços vinculados: **AzureStorageLinkedService1** e **AzureSqlLinkedService1**. O serviço vinculado AzureStorageLinkedService1 vincula uma conta de armazenamento do Azure e o AzureSqlLinkedService vincula um banco de dados SQL do Azure à data factory: **ADFTutorialDataFactory**.

##### Criar o serviço de armazenamento do Azure vinculado

4. Clique em **Serviços vinculados** no Gerenciador de Soluções, aponte para **Adicionar** e clique em **Novo item**.      
5. Na caixa de diálogo **Adicionar novo item**, selecione **Serviço vinculado de armazenamento do Azure** na lista e clique em **Adicionar**. 

	![Novo serviço vinculado](./media/data-factory-get-started-using-vs/new-linked-service-dialog.png)
 
3. Substitua **accountname** e **accountkey** pelo nome da sua conta de armazenamento do Azure e sua chave.

	![Serviço vinculado de armazenamento do Azure](./media/data-factory-get-started-using-vs/azure-storage-linked-service.png)

4. Salve o arquivo **AzureStorageLinkedService1.json**.

#### Criar um serviço vinculado do SQL do Azure

5. Clique com o botão direito no nó **Serviços vinculados** no **Gerenciador de Soluções** novamente, aponte para **Adicionar** e clique em **Novo Item**. 
6. Desta vez, selecione **Serviço vinculado do SQL Azure** e clique em **Adicionar**. 
7. No **arquivo AzureSqlLinkedService1.json**, substitua **servername**, **nomedobancodedados**, ****username@servername** e **password** pelos nomes do servidor SQL do Azure, do banco de dados, da conta de usuário e a senha.
8.  Salve o arquivo **AzureSqlLinkedService1.json**. 


### Criar tabelas de entrada e saída
Na etapa anterior, você criou os serviços vinculados **AzureStorageLinkedService1** e **AzureSqlLinkedService1** para vincular uma conta de armazenamento do Azure e o banco de dados SQL do Azure ao data factory: **ADFTutorialDataFactory**. Nesta etapa, você definirá duas tabelas de data factory – **EmpTableFromBlob** e **EmpSQLTable** – que representam os dados de entrada/saída armazenados nos repositórios de dados referidos por AzureStorageLinkedService1 e AzureSqlLinkedService1, respectivamente. Em EmpTableFromBlob, você especifica o contêiner de blob que contém um blob com os dados de origem e, em EmpSQLTable, você especifica a tabela SQL que armazenará os dados de saída.

#### Criar a tabela de entrada

9. Clique com o botão direito do mouse em **Tabelas** no **Gerenciador de Soluções**, aponte para **Adicionar** e clique em **Novo Item**.
10. Na caixa de diálogo **Adicionar Novo Item**, selecione **Blob do Azure** e clique em **Adicionar**.   
10. Substitua o texto JSON pelo seguinte texto e salve o arquivo **AzureBlobLocation1.json**. 

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
	                "linkedServiceName": "AzureStorageLinkedService1"
	            },
	            "availability": 
	            {
	                "frequency": "Hour",
	                "interval": 1,
	                "waitOnExternal": {}
                }
	        }
		}

#### Criar a tabela de saída

11. Clique com o botão direito do mouse em **Tabelas** no **Gerenciador de Soluções**, aponte para **Adicionar** novamente e clique em **Novo item**.
12. Na caixa de diálogo **Adicionar Novo Item**, selecione **SQL do Azure** e clique em **Adicionar**. 
13. Substitua o texto JSON pelo seguinte JSON e salve o arquivo **AzureSqlTableLocation1.json**.

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
		            "linkedServiceName": "AzureSqlLinkedService1"
		        },
		        "availability": 
		        {
		            "frequency": "Hour",
		            "interval": 1            
		        }
		    }
		}

#### Criar o pipeline 
Você criou tabelas e serviços vinculados de entrada/saída até o momento. Agora, você criará um pipeline com uma **Atividade de Cópia** para copiar dados do blob do Azure ao banco de dados SQL do Azure.


1. Clique com o botão direito do mouse em **Pipelines** no **Gerenciador de Soluções**, aponte para **Adicionar** e clique em **Novo Item**.  
15. Selecione **Pipeline de dados de cópia** na caixa de diálogo **Adicionar Novo Item** e clique em **Adicionar**. 
16. Substitua o JSON pelo JSON a seguir e salve o arquivo **CopyActivity1.json**.
			
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
		                        "type": "SqlSink",
		                        "writeBatchSize": 10000,
		                        "writeBatchTimeout": "60:00:00"
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
		
		        "start": "2015-07-12T00:00:00Z",
		        "end": "2015-07-13T00:00:00Z",
		        "isPaused": false
		    }
		} 

#### Publicar/implantar entidades de data factory
  
18. Na área da barra de ferramentas, clique com botão direito do mouse e selecione **Data Factory** para habilitar a barra de ferramentas de data factory, se ela ainda não estiver habilitada. 
19. Na **barra de ferramentas Data Factory**, clique na **caixa suspensa** para ver todas as data factories na sua assinatura do Azure. Se você vir a caixa de diálogo **Entrar no Visual Studio**: 
	20. Insira a **conta de email** associada à assinatura do Azure na qual você deseja criar a data factory, digite **Senha** e clique em **Entrar**.
	21. Quando a entrada for bem-sucedida, você deverá ver todas as data factories na assinatura do Azure. Neste tutorial, você criará uma nova data factory.       
22. Na lista suspensa, selecione **ADFTutorialFactoryVS** e clique no botão **Publicar** para implantar/publicar os serviços vinculados, os conjuntos de dados e o pipeline.    

	![Botão Publicar](./media/data-factory-get-started-using-vs/publish.png)

23. Você deve ver o status de publicação na janela da lista de tarefas de data factory que é mostrada na imagem anterior. Confirme que a publicação foi bem-sucedida.

## Use o Gerenciador de Servidores para examinar as entidades de data factory

1. Em **Visual Studio**, clique em **Exibição** no menu e clique em **Gerenciador de Servidores**.
2. Na janela Gerenciador de Servidores, expanda **Azure** e expanda **Data Factory**. Se você vir **Entrar no Visual Studio**, digite a **conta** associada à sua assinatura do Azure e clique em **Continuar**. Digite sua **Senha** e clique em **Entrar**. O Visual Studio tenta obter informações sobre todas as data factories do Azure em sua assinatura. Você verá o status da operação na janela **Lista de Tarefas de Data Factory**.![Gerenciador de Servidores](./media/data-factory-get-started-using-vs/server-explorer.png)
3. Clique com o botão direito em uma data factory e selecione Exportar Data Factory para Novo Projeto para criar um projeto do Visual Studio com base em uma data factory existente.![Exportar data factory para um projeto VS](./media/data-factory-get-started-using-vs/export-data-factory-menu.png)  

## Atualizar ferramentas de data factory para o Visual Studio
Para atualizar as ferramentas da Azure Data Factory para o Visual Studio, faça o seguinte:

1. Clique em **Ferramentas** no menu e selecione **Extensões e Atualizações**. 
2. Selecione **Atualizações** no painel esquerdo e selecione **Galeria do Visual Studio**.
4. Selecione **Ferramentas do Azure Data Factory para Visual Studio** e clique em **Atualizar**. Se você não vir essa entrada, você já tem a versão mais recente das ferramentas. 

Consulte [Monitorar conjuntos de dados e pipeline](data-factory-get-started-using-editor.md/#MonitorDataSetsAndPipeline) para obter instruções sobre como usar o Portal de visualização do Azure para monitorar o pipeline e conjuntos de dados que você criou neste tutorial.


## Enviar comentários
Apreciamos muito seus comentários sobre este artigo. Reserve alguns minutos para enviar seus comentários por meio de [email](mailto:adfdocfeedback@microsoft.com?subject=data-factory-get-started-using-vs.md).

<!---HONumber=September15_HO1-->