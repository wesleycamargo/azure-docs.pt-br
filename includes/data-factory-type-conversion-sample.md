### Exemplo de conversão de tipo
O exemplo a seguir é para copiar dados de um Blob para SQL Azure com conversões de tipo.

Suponha que o conjunto de dados de Blob está no formato CSV e contém 3 colunas. Uma delas é uma coluna de data e hora com um formato de data e hora personalizado usando nomes abreviados em francês para o dia da semana.

Você definirá o conjunto de dados de origem de Blob como a seguir, juntamente com definições de tipo para as colunas.

	{
	    "name": " AzureBlobTypeSystemInput",
	    "properties":
	    {
	         "structure": 
	          [
	                { "name": "userid", "type": "Int64"},
	                { "name": "name", "type": "String"},
	                { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
	          ],
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/myfolder",
	            "fileName":"myfile.csv",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            }
	        },
	        "external": true,
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1,
	        },
			"policy": {
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
			}
	    }
	}

Considerando a tabela de mapeamento de tipo SQL para tipo .NET acima, você definirá a tabela do SQL Azure com o esquema a seguir.

| Nome da coluna | Tipo SQL |
| ----------- | -------- |
| userid | bigint |
| name | texto |
| lastlogindate | datetime |

Em seguida, você definirá o conjunto de dados do SQL Azure da seguinte maneira. Observação: você não precisa especificar a seção "estrutura" com as informações de tipo, pois o tipo de informação já está especificado no armazenamento de dados subjacente.

	{
	    "name": "AzureSQLOutput",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

Nesse caso, o data factory fará automaticamente as conversões de tipo, inclusive o campo de data e hora, com o formato de data e hora personalizado usando a cultura fr-fr ao mover dados de Blob para o SQL Azure.

<!---HONumber=August15_HO7-->