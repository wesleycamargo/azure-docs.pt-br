<properties 
	pageTitle="Atividade de procedimento armazenado do SQL Server" 
	description="Saiba como é possível usar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado em um banco de dados SQL do Azure de um pipeline de Data Factory." 
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
	ms.date="08/04/2015" 
	ms.author="spelluru"/>

# Atividade de procedimento armazenado do SQL Server

Você pode usar a atividade de procedimento armazenado do SQL Server em um [pipeline](data-factory-create-pipelines.md) de Data Factory para invocar um procedimento armazenado em um banco de dados **SQL do Azure**. Este artigo se baseia no artigo [atividades de transformação de dados](data-factory-data-transformation-activities.md), que apresenta uma visão geral de transformação de dados e as atividades de transformação para as quais há suporte.

## Sintaxe
	{
    	"name": "SQLSPROCActivity",
    	"description": "description", 
    	"type": "SqlServerStoredProcedure",
    	"inputs":  [ { "name": "inputtable"  } ],
    	"outputs":  [ { "name": "outputtable" } ],
    	"typeProperties":
    	{
        	"storedProcedureName": “”,
        	"storedProcedureParameters": “” 
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
inputs | Entrada(s) que deve estar disponível (no status "Pronta") para que a atividade de procedimento armazenado seja executada | Não
outputs | Saída(s) produzida pela atividade de procedimento armazenado. Certifique-se de que a tabela de saída usa um serviço vinculado que vincula um Banco de Dados SQL do Azure ao data factory. | Sim
storedProcedureName | Especifique o nome do procedimento armazenado no banco de dados SQL do Azure que é representado pelo serviço vinculado utilizado pela tabela de saída. | Sim
storedProcedureParameters | Especificar valores para parâmetros de procedimento armazenado | Não

> [AZURE.NOTE]A entrada(s) para a atividade de procedimento armazenado é usada somente para gerenciamento de dependência e encadeamento dessa atividade com outras. A entrada(s) não pode ser consumida no procedimento armazenado como um parâmetro.
 

## Exemplo

Vamos considerar um exemplo em que você deseja criar uma tabela no banco de dados SQL do Azure com duas colunas:

Coluna | Tipo
------ | ----
ID | Um uniqueidentifier
Datetime | Data e hora em que a ID correspondente foi gerada

![Dados de amostra](./media/data-factory-stored-proc-activity/sample-data.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime)
	END

Para executar esse procedimento armazenado em um pipeline de Data Factory, você precisa fazer o seguinte:

1.	Crie um [serviço vinculado](data-factory-azure-sql-connector.md/#azure-sql-linked-service-properties) para registrar a cadeia de conexão do banco de dados SQL do Azure em que o procedimento armazenado deve ser executado.
2.	Crie um [conjunto de dados](data-factory-azure-sql-connector.md/#azure-sql-dataset-type-properties) apontando para a tabela de saída no banco de dados SQL do Azure. Vamos chamar esse conjunto de dados de sprocsampleout. Esse conjunto de dados deve referenciar o serviço vinculado na etapa n.º 1. 
3.	Crie o procedimento armazenado no Banco de Dados SQL do Azure.
4.	Crie o [pipeline](data-factory-azure-sql-connector.md/#azure-sql-copy-activity-type-properties) abaixo com a atividade SqlServerStoredProcedure para invocar o procedimento armazenado no Banco de Dados SQL do Azure.

		{
		    "name": "SprocActivitySamplePipeline",
		    "properties":
		    {
		        "activities":
		        [
		            {
		             "name": "SprocActivitySample",
		             "type": " SqlServerStoredProcedure ",
		             "outputs": [ {"name": "sprocsampleout"} ],
		             "typeproperties":
		              {
		                "storedProcedureName": "sp_sample",
		        		"storedProcedureParameters": 
		        		{
		            	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
		        		}
				}
		            }
		          ]
		     }
		}
5.	Implante o [pipeline](data-factory-create-pipelines.md).
6.	[Monitore o pipeline](data-factory-monitor-manage-pipelines.md) usando as exibições de gerenciamento e monitoramento de data factory.

> [AZURE.NOTE]No exemplo acima, SprocActivitySample não tem entradas. Se você deseja encadear isso com uma atividade upstream, a saída(s) de atividade upstream pode ser usada como entrada(s) nessa atividade. Nesse caso, essa atividade não será executada até que a atividade de upstream seja concluída e a saída(s) esteja disponível (com status Pronta). A entrada(s) não pode ser usada diretamente como um parâmetro para a atividade de procedimento armazenado
> 
> Os nomes e o uso de letras maiúsculas e minúsculas (superior/inferior) de parâmetros de procedimento armazenado no arquivo JSON devem corresponder aos nomes de parâmetros de procedimento armazenado no banco de dados de destino.

Agora, vamos considerar adicionar outra coluna denominada 'Cenário' na tabela que contém um valor estático chamado 'Exemplo de documento'.

![Dados de exemplo 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
	
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime, @Scenario)
	END

Para fazer isso, passe o parâmetro Cenário e o valor da atividade de procedimento armazenado. A seção typeproperties no exemplo acima tem essa aparência:

	"typeproperties":
	{
		"storedProcedureName": "sp_sample",
	    "storedProcedureParameters": 
	    {
	    	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
			"Scenario": "Document sample"
		}
	}

<!---HONumber=August15_HO6-->