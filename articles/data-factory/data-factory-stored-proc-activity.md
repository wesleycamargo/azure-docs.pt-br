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
	ms.date="08/18/2016" 
	ms.author="spelluru"/>

# Atividade de procedimento armazenado do SQL Server

Você pode usar a atividade Procedimento Armazenado do SQL Server em um [pipeline](data-factory-create-pipelines.md) do Data Factory para invocar um procedimento armazenado em um dos armazenamentos de dados a seguir.


- Banco de Dados SQL Azure
- SQL Data Warehouse do Azure
- Banco de Dados do SQL Server em sua empresa ou em uma VM do Azure. Você precisa instalar o Gateway de Gerenciamento de Dados no mesmo computador que hospeda o banco de dados ou em um computador separado para evitar a concorrência por recursos com o banco de dados. O Gateway de Gerenciamento de Dados é um software que conecta fontes de dados local/fontes de dados hospedadas em VMs do Azure para serviços de nuvem de maneira segura e gerenciada. Consulte o artigo [Mover dados entre local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre o Gateway de Gerenciamento de Dados.

Este artigo se baseia no artigo sobre [atividades de transformação de dados](data-factory-data-transformation-activities.md) que apresenta uma visão geral da transformação de dados e as atividades de transformação permitidas.

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
inputs | Opcional. Se você especificar um conjunto de dados de entrada, ele deverá estar disponível (no status 'Pronto') para a atividade de procedimento armazenado a ser executada. O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como um parâmetro. Ele só é usado para verificar a dependência antes de iniciar a atividade de procedimento armazenado. | Não
outputs | Você deve especificar um conjunto de dados de saída para uma atividade de procedimento armazenado. O conjunto de dados de saída especifica a **agenda** da atividade de procedimento armazenado (por hora, semana, mês, etc.). <br/><br/>O conjunto de dados de saída deve usar um **serviço vinculado** que se refere a um Banco de Dados SQL do Azure, ou SQL Data Warehouse do Azure, ou um Banco de Dados SQL Server no qual você quer que o procedimento armazenado seja executado. <br/><br/>O conjunto de dados de saída pode servir como uma maneira de passar o resultado do procedimento armazenado para processamento subsequente por outra atividade ([encadeamento de atividades](data-factory-scheduling-and-execution.md#chaining-activities)) no pipeline. No entanto, o Data Factory não trava automaticamente a saída de um procedimento armazenado para esse conjunto de dados. É o procedimento armazenado que grava em uma tabela SQL para a qual o conjunto de dados de saída aponta. <br/><br/>Em alguns casos, o conjunto de dados de saída pode ser um **conjunto de dados fictício**, que é usado apenas para especificar o agendamento para execução da atividade de procedimento armazenado. | Sim
storedProcedureName | Especifique o nome do procedimento armazenado no banco de dados SQL do Azure ou SQL Data Warehouse do Azure que é representado pelo serviço vinculado utilizado pela tabela de saída. | Sim
storedProcedureParameters | Especifique valores para parâmetros de procedimento armazenado. Se você precisar passar null para um parâmetro, use a sintaxe: "param1": null (todas as letras minúsculas). Veja o exemplo a seguir para saber mais sobre como usar essa propriedade.| Não

## Exemplo passo a passo

### Tabela de exemplo e procedimento armazenado
> [AZURE.NOTE] Este exemplo usa o Banco de Dados SQL do Azure, mas funciona da mesma maneira para o SQL Data Warehouse do Azure e para o Banco de Dados SQL Server.

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

2. Crie o seguinte **procedimento armazenado** que insere dados na **tabeladeexemplo**.

		CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
		AS
		
		BEGIN
		    INSERT INTO [sampletable]
		    VALUES (newid(), @DateTime)
		END

	> [AZURE.IMPORTANT] O **nome** e o **uso de maiúsculas** do parâmetro (DateTime, neste exemplo) devem corresponder àqueles do parâmetro especificado no JSON do pipeline/atividade. Na definição do procedimento armazenado, certifique-se de que **@** seja usado como um prefixo para o parâmetro.
	
### Criar uma data factory  
4. Depois de fazer logon no [portal do Azure](https://portal.azure.com/), faça o seguinte:
	1.	Clique em **NOVO** no menu à esquerda.
	2.	Clique em **Análise de dados** na folha **Criar**.
	3.	Clique em **Data Factory** na folha **Análise de dados**.
4.	Na folha **Nova data factory**, insira **SProcDF** como o Nome. Nomes de Azure Data Factory são globalmente exclusivos. É preciso prefixar o nome do data factory com seu nome, para habilitar a criação bem-sucedida de fábrica.
3.	Se você não criou sequer um grupo de recursos, será preciso criar um.
	1.	Clique em **NOME DO GRUPO DE RECURSOS**.
	2.	Selecione **Criar um novo grupo de recursos** na folha **Grupo de recursos**.
	3.	Digite **ADFTutorialResourceGroup** como o **Nome** na folha **Criar grupo de recursos**.
	4.	Clique em **OK**.
4.	Depois de selecionar o grupo de recursos, verifique se você está usando a assinatura correta na qual deseja que o data factory seja criado.
5.	Clique em **Criar** na folha **Novo data factory**.
6.	Você vê o data factory que está sendo criado no **Quadro Inicial** do Portal do Azure. Após o data factory ter sido criado com êxito, você verá a página do data factory, que exibe seu conteúdo.

### Criar um serviço vinculado do SQL do Azure  
Depois de criar a data factory, você deve criar um serviço do SQL Azure vinculado que vincula seu banco de dados SQL na data factory. Esse banco de dados contém a tabela tabeladeexemplo e o procedimento armazenado sp\_sample.

7.	Clique em **Criar e implantar** na folha **DATA FACTORY** para **SProcDF** de modo a iniciar o Editor do Data Factory.
2.	Clique em **Novo armazenamento de dados** no comando barra e escolha **SQL Azure**. Você deve ver o script JSON para criar um serviço vinculado do SQL Azure no editor.
4. Substitua **nomedoservidor** pelo nome do seu servidor de Banco de Dados SQL do Azure, **nomedobancodedados** pelo banco de dados em que você criou a tabela e o procedimento armazenado, **username@servername** com a conta de usuário que tenha acesso ao banco de dados e **senha** pela senha da conta de usuário.
5. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.

### Criar um conjunto de dados de saída
6. Clique em **Novo conjunto de dados** na barra de comando e selecione **SQL Azure**.
7. Copie/cole o script JSON a seguir no editor de JSON.

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
         		"start": "2016-08-02T00:00:00Z",
         		"end": "2016-08-02T05:00:00Z",
		        "isPaused": false
		    }
		}

	Se você precisar passar null para um parâmetro, use a sintaxe: "param1": null (todas as letras minúsculas).
9. Clique em **Implantar** na barra de ferramentas para implantar o pipeline.

### Monitorar o Pipeline

6. Clique em **X** para fechar as folhas do Editor da Fábrica de Dados e para navegar de volta à folha Fábrica de Dados, e clique em **Diagrama**.
7. Na Exibição de Diagrama, você tem uma visão geral dos pipelines e dos conjuntos de dados usados neste tutorial.
8. Na Exibição de Diagrama, clique duas vezes no conjunto de dados **sprocsampleout**. Você vê as partes no estado Pronto. Deve haver cinco partes porque uma fatia é produzida para cada hora entre a hora de início e de término no JSON.
10. Quando uma parte estiver no estado **Pronto**, execute uma consulta **select* from tabeladeexemplo** no banco de dados SQL Azure para verificar se os dados foram inseridos na tabela pelo procedimento armazenado.

	![Dados de saída](./media/data-factory-stored-proc-activity/output.png)

	Consulte [Monitorar o pipeline](data-factory-monitor-manage-pipelines.md) para obter informações detalhadas sobre o monitoramento de pipelines da Azure Data Factory.

> [AZURE.NOTE] No exemplo acima, SprocActivitySample não tem entradas. Se você deseja encadear essa atividade com uma atividade de upstream (ou seja, antes do processamento), as saídas da atividade de upstream poderão ser usadas como entradas nessa atividade. Nesse caso, essa atividade não será executada até que a atividade de upstream seja concluída e as saídas das atividades de upstream estejam disponíveis (com status Pronto). A entrada(s) não pode ser usada diretamente como um parâmetro para a atividade de procedimento armazenado

## Passando um valor estático 
Agora, vamos considerar adicionar outra coluna denominada 'Cenário' na tabela que contém um valor estático chamado 'Exemplo de documento'.

![Dados de exemplo 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
	
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime, @Scenario)
	END

Agora, passe o parâmetro Scenario e o valor da atividade de procedimento armazenado. A seção typeProperties no exemplo acima se parece com o seguinte trecho de código:

	"typeProperties":
	{
		"storedProcedureName": "sp_sample",
	    "storedProcedureParameters": 
	    {
	    	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
			"Scenario": "Document sample"
		}
	}

<!---HONumber=AcomDC_0824_2016-->