<properties 
	pageTitle="Atividade de procedimento armazenado do SQL Server" 
	description="Saiba como é possível usar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado em um banco de dados SQL do Azure ou SQL Data Warehouse do Azure de um pipeline de Data Factory." 
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
	ms.date="01/19/2016" 
	ms.author="spelluru"/>

# Atividade de procedimento armazenado do SQL Server

Você pode usar a atividade Procedimento Armazenado do SQL Server em um [pipeline](data-factory-create-pipelines.md) do Data Factory para invocar um procedimento armazenado em um dos armazenamentos de dados a seguir.


- Banco de Dados SQL do Azure 
- SQL Data Warehouse do Azure  
- Banco de Dados do SQL Server em sua empresa ou em uma VM do Azure. Você precisa instalar o Gateway de Gerenciamento de Dados no mesmo computador que hospeda o banco de dados ou em um computador separado para evitar a concorrência por recursos com o banco de dados. O Gateway de Gerenciamento de Dados é um software que conecta fontes de dados local/fontes de dados hospedadas em VMs do Azure para serviços de nuvem de maneira segura e gerenciada. Consulte o artigo [Mover dados entre local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre o Gateway de Gerenciamento de Dados. 

Este artigo se baseia no artigo [atividades de transformação de dados](data-factory-data-transformation-activities.md) que apresenta uma visão geral de transformação de dados e as atividades de transformação para as quais há suporte.

## Sintaxe
	{
    	"name": "SQLSPROCActivity",
    	"description": "description", 
    	"type": "SqlServerStoredProcedure",
    	"inputs":  [ { "name": "inputtable"  } ],
    	"outputs":  [ { "name": "outputtable" } ],
    	"typeProperties":
    	{
        	"storedProcedureName": "<name of the stored procedure>",
        	"storedProcedureParameters":  
        	{
				"param1": "param1Value"
				…
        	}
    	}
	}

## Detalhes da sintaxe

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
name | Nome da atividade | Sim
description | Texto que descreve qual a utilidade da atividade | Não
type | SqlServerStoredProcedure | Sim
inputs | Conjunto(s) de dados de entrada que devem estar disponíveis (no status "Pronto") para que a atividade de procedimento armazenado seja executada A entrada(s) para a atividade de procedimento armazenado somente serve como gerenciamento de dependência ao encadear esta atividade com outras. O(s) conjunto(s) de dados de entrada não podem ser consumidos no procedimento armazenado como um parâmetro. | Não
outputs | Conjunto(s) de dado(s) de saída produzidos pela atividade de procedimento armazenado. Verifique se a tabela de saída usa um serviço vinculado que vincula um Banco de Dados SQL do Azure ou um SQL Data Warehouse do Azure ou um Banco de Dados do SQL Server ao data factory. As saída(s) na atividade de procedimento armazenado podem servir como uma maneira de passar o resultado da atividade de procedimento armazenado para realizar o processamento subsequentemente e/ou pode servir como o gerenciamento de dependência ao encadear essa atividade com outras | Sim
storedProcedureName | Especifique o nome do procedimento armazenado no banco de dados SQL do Azure ou SQL Data Warehouse do Azure que é representado pelo serviço vinculado utilizado pela tabela de saída. | Sim
storedProcedureParameters | Especificar valores para parâmetros de procedimento armazenado | Não

## Exemplo passo a passo

### Tabela de exemplo e procedimento armazenado
> [AZURE.NOTE] Este exemplo usa o Banco de Dados SQL do Azure, mas funciona da mesma maneira para o SQL Data Warehouse do Azure e para o Banco de Dados do SQL Server.

1. Crie a seguinte **tabela** em seu Banco de Dados SQL do Azure usando o Estúdio de Gerenciamento do SQL Server ou qualquer outra ferramenta com a qual você estiver confortável. A coluna datetimestamp é a data e a hora em que a ID correspondente é gerada. 

		CREATE TABLE dbo.sampletable
		(
			Id uniqueidentifier,
			datetimestamp nvarchar(127)
		)
		GO

		CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
		GO

	ID é a única identificada e a coluna datetimestamp é a data e a hora em que a ID correspondente é gerada. ![Dados de amostra](./media/data-factory-stored-proc-activity/sample-data.png)

2. Crie o seguinte **procedimento armazenado** que insere dados no **sampletable**.

		CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
		AS
		
		BEGIN
		    INSERT INTO [sampletable]
		    VALUES (newid(), @DateTime)
		END

	> [AZURE.IMPORTANT] O **nome** e o **uso de maiúsculas** do parâmetro (DateTime, neste exemplo) devem corresponder àqueles do parâmetro especificado no JSON do pipeline/atividade. Na definição do procedimento armazenado, certifique-se de que **@** seja usado como um prefixo para o parâmetro.
	
### Criar uma data factory  
4. Depois de fazer logon no [Portal do Azure](https://portal.azure.com/), faça o seguinte:
	1.	Clique em **NOVO** no menu à esquerda. 
	2.	Clique em **Análise de dados** na folha **Criar**.
	3.	Clique em **Data Factory** na folha **Análise de dados**.
4.	Na folha **Nova data factory**, insira **SProcDF** como o Nome. Nomes de Azure Data Factory são globalmente exclusivos. Será necessário prefixar o nome da fábrica de dados com seu nome, para habilitar a criação bem-sucedida de fábrica. 
3.	Se não criou nenhum grupo de recursos, você precisará criar um grupo de recursos. Para fazer isso:
	1.	Clique em **NOME DO GRUPO DE RECURSOS**.
	2.	Selecione **Criar um novo grupo de recursos** na folha **Grupo de recursos**.
	3.	Digite **ADFTutorialResourceGroup** como o **Nome** na folha **Criar grupo de recursos**.
	4.	Clique em **OK**.
4.	Depois de selecionar o grupo de recursos, verifique se que você está usando a assinatura correta na qual deseja que a data factory seja criada.
5.	Clique em **Criar** na folha **Nova data factory**.
6.	Você verá o data factory que está sendo criado no **Quadro Inicial** do Portal do Azure. Após a data factory ter sido criada com êxito, você verá a página da data factory, que exibe seu conteúdo.

### Criar um serviço vinculado do SQL do Azure  
Depois de criar a data factory, você deve criar um serviço do SQL Azure vinculado que vincula seu banco de dados SQL na data factory. Isso é o banco de dados que contém a tabela sampletable e o procedimento armazenado sp\_sample.

7.	Clique em **Criar e implantar** na folha **DATA FACTORY** para **SProcDF**. Isso inicia o Data Factory Editor. 
2.	Clique em **Novo armazenamento de dados** no comando barra e escolha **SQL Azure**. Você deve ver o script JSON para criar um serviço vinculado do SQL Azure no editor. 
4. Substitua o **servername** com o nome do seu servidor de banco de dados SQL, **databasename** com o banco de dados em que você criou a tabela e o procedimento armazenado, * ***username@servername** com a conta de usuário que tenha acesso ao banco de dados, e **senha** com a senha da conta de usuário.
5. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.

### Criar um conjunto de dados de saída
6. Clique em **Novo conjunto de dados** na barra de comando e selecione **SQL Azure**.
7. Copiar/colar o script JSON a seguir no editor de JSON.

		{			    
			"name": "sprocsampleout",
			"properties": {
				"type": "AzureSqlTable",
				"linkedServiceName": "AzureSqlLinkedService",
				"typeProperties": {
					"tableName": "sampletable"
				},
				"availability": {
					"frequency": "Hour",
					"interval": 1
				}
			}
		}
7. Clique em **Implantar** na barra de comando para implantar o conjunto de dados. 

### Criar um pipeline com atividade de SqlServerStoredProcedure
AGora, crie um pipeline com atividade de SqlServerStoredProcedure.
 
9. Clique em **... (reticências)** na barra de comandos e clique em **Novo pipeline**. 
9. Copiar/colar o seguinte trecho JSON. O **storedProcedureName** definido como **sp\_sample**. O nome e o uso de maiúsculas e minúsculas do parâmetro **DateTime** deve corresponder ao nome e o uso de maiúsculas e minúsculas do parâmetro na definição do procedimento armazenado.  

		{
		    "name": "SprocActivitySamplePipeline",
		    "properties": {
		        "activities": [
		            {
		                "type": "SqlServerStoredProcedure",
		                "typeProperties": {
		                    "storedProcedureName": "sp_sample",
		                    "storedProcedureParameters": {
		                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
		                    }
		                },
		                "outputs": [
		                    {
		                        "name": "sprocsampleout"
		                    }
		                ],
		                "scheduler": {
		                    "frequency": "Hour",
		                    "interval": 1
		                },
		                "name": "SprocActivitySample"
		            }
		        ],
		        "start": "2015-01-02T00:00:00Z",
		        "end": "2015-01-03T00:00:00Z",
		        "isPaused": false
		    }
		}
9. Clique em **Implantar** na barra de ferramentas para implantar o pipeline.  

### Monitorar o Pipeline

6. Clique em **X** para fechar as folhas do Data Factory Editor e para navegar de volta para a folha do Data Factory, e clique em **Diagrama**.
7. Na Exibição de diagrama, você terá uma visão geral dos pipelines e conjuntos de dados usados neste tutorial. 
8. Na Exibição de Diagrama, clique duas vezes no conjunto de dados **sprocsampleout**. Você verá as partes no estado pronto. Deve haver 24 partes porque uma parte é produzida para cada hora entre 01/02/2015 e 01/03/2015. 
10. Quando uma parte estiver no estado **pronto**, executar **Selecionar * de sampledata** consultar o banco de dados SQL Azure para verificar que os dados foram inseridos na tabela pelo procedimento armazenado.

	![Dados de saída](./media/data-factory-stored-proc-activity/output.png)

	Consulte [Monitorar o pipeline](data-factory-monitor-manage-pipelines.md) para obter informações detalhadas sobre o monitoramento de pipelines da Azure Data Factory.

> [AZURE.NOTE] No exemplo acima, SprocActivitySample não tem entradas. Se você deseja encadear isso com uma atividade upstream (ou seja, antes do processamento), a saída(s) de atividade upstream pode ser usada como entrada(s) nessa atividade. Nesse caso, essa atividade não será executada até que a atividade de upstream seja concluída e a saída(s) das atividades de upstream esteja disponível (com status Pronta). A entrada(s) não pode ser usada diretamente como um parâmetro para a atividade de procedimento armazenado

## Passando um valor estático 
Agora, vamos considerar adicionar outra coluna denominada 'Cenário' na tabela que contém um valor estático chamado 'Exemplo de documento'.

![Dados de exemplo 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
	
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime, @Scenario)
	END

Para fazer isso, passe o parâmetro Cenário e o valor da atividade de procedimento armazenado. A seção typeProperties no exemplo acima tem a aparência a seguir:

	"typeProperties":
	{
		"storedProcedureName": "sp_sample",
	    "storedProcedureParameters": 
	    {
	    	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
			"Scenario": "Document sample"
		}
	}

<!---HONumber=AcomDC_0128_2016-->