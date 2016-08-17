## Invocando o procedimento armazenado para o coletor SQL

Ao copiar dados no SQL Server ou no SQL Azure/banco de dados do SQL Server, um procedimento armazenado do usuário especificado poderá ser configurado e invocado com parâmetros adicionais.

Um procedimento armazenado pode ser utilizado quando os mecanismos de cópia internos não têm essa finalidade. Normalmente isso ocorre quando é necessário realizar processamento extra (mesclar colunas, pesquisar valores adicionais, inserção em várias tabelas...) antes da inserção final dos dados de origem na tabela de destino.

Você pode invocar o procedimento armazenado da sua escolha. O exemplo a seguir mostra como usar um procedimento armazenado para fazer uma inserção simples em uma tabela no banco de dados.

**Conjunto de dados de saída**

Neste exemplo, o tipo é definido como: SqlServerTable. Defina-o como AzureSqlTable para usá-lo com um banco de dados SQL Azure.

	{
	  "name": "SqlOutput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlLinkedService",
	    "typeProperties": {
	      "tableName": "Marketing"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}
	
Defina a seção SqlSink na atividade de cópia JSON conforme demonstrado a seguir. Para chamar um procedimento armazenado ao inserir dados, as propriedades SqlWriterStoredProcedureName e SqlWriterTableType são necessárias.

	"sink":
	{
	    "type": "SqlSink",
	    "SqlWriterTableType": "MarketingType",
	    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
	    "storedProcedureParameters":
	            {
	                "stringData": 
	                {
	                    "value": "str1"     
	                }
	            }
	}

No banco de dados, defina o procedimento armazenado com o mesmo nome que SqlWriterStoredProcedureName. Ele lida com os dados de entrada de origem especificada por você e os insere na tabela de saída. Observe que o nome de parâmetro do procedimento armazenado deve ser igual ao tableName definido no arquivo JSON da tabela.

	CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
	AS
	BEGIN
	    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
	    INSERT [dbo].[Marketing](ProfileID, State)
	    SELECT * FROM @Marketing
	END

No banco de dados, defina o tipo de tabela com o mesmo nome que SqlWriterTableType. Observe que o esquema do tipo de tabela deve ser a mesmo que o esquema retornado por seus dados de entrada.

	CREATE TYPE [dbo].[MarketingType] AS TABLE(
	    [ProfileID] [varchar](256) NOT NULL,
	    [State] [varchar](256) NOT NULL
	)

O recurso de procedimento armazenado se beneficia de [parâmetros com valores de tabela](https://msdn.microsoft.com/library/bb675163.aspx).

<!---HONumber=AcomDC_0803_2016-->