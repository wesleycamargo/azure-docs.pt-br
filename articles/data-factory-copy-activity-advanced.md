<properties 
	pageTitle="Cenários avançados para usar a atividade de cópia no Azure Data Factory" 
	description="Descreve cenários avançados para usar a atividade de cópia no Azure Data Factory." 
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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Cenários avançados para usar a atividade de cópia no Azure Data Factory 
## Visão geral
Você pode usar a **Atividade de cópia** em uma pipeline para copiar dados de uma fonte para um coletor (destino) em um lote. Este tópico descreve os cenários avançados com suporte pela atividade de cópia. Para obter uma visão geral detalhada da atividade de cópia e cenários principais aos quais ele dá suporte, consulte [Copiar dados com o Azure Data Factory][adf-copyactivity].


## Filtragem de coluna utilizando a definição de estrutura
Dependendo do tipo de tabela, é possível especificar um subconjunto das colunas da fonte especificando menos colunas na definição de **estrutura** da definição de tabela do que as existentes na fonte de dados subjacente. A tabela a seguir fornece informações sobre a lógica de filtragem de coluna para diferentes tipos de tabela.

<table>

	<tr>
		<th align="left">Tipo da tabela</th>
		<th align="left">Lógica de filtragem de coluna</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>A definição <b>Estrutura</b> na tabela JSON deve corresponder à estrutura do blob. Para selecionar um subconjunto das colunas, use o recurso de mapeamento de coluna descrito na próxima seção: Regras de transformação – mapeamento de coluna.</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation e OnPremisesSqlServerTableLocation</td>
		<td align="left">
			Se a propriedade <b>SqlReaderQuery</b> for especificada como parte da definição da Atividade de Cópia, a definição de <b>Estrutura</b> da tabela deve ser alinhada com as colunas selecionadas na consulta.<br/><br/>
			Se a propriedade <b>SqlReaderQuery</b> não for especificada, a Atividade de cópia automaticamente construirá uma consulta SELECT, com base nas colunas especificadas na definição de <b>Estrutura</b> da definição de tabela.
		</td>
	<tr>

	<tr>
		<td>AzureTableLocation</td>
		<td>
			A seção <b>Estrutura</b> na definição da tabela pode conter o conjunto completo ou um subconjunto das colunas na tabela subjacente do Azure.
		</td>
	<tr>

</table>

## Regras de transformação - mapeamento de coluna
O mapeamento de coluna pode ser utilizado para especificar como colunas na tabela de fonte são mapeados para colunas na tabela de coletor. Ele oferece suporte para os seguintes cenários:

- Mapear todas as colunas na tabela de fonte "estrutura" para a tabela de destino "estrutura".
- Um subconjunto de colunas na da tabela de fonte "estrutura" são mapeados para todas as da tabela de destino "estrutura".

Ele não oferece suporte e abre uma exceção para o seguinte:

- Ou menos colunas ou mais colunas no destino.
- Mapeamento duplicado.
- Resultado da consulta de SQL não tem um nome de coluna

Especialmente, ao copiar dados entre dois Blobs do Azure, a Atividade de Cópia trataria essa atividade principalmente como uma cópia direta de dados binários, a menos que os 3 cenários a seguir sejam atendidos:


1. - se as tabelas de entrada e saída têm formatos diferentes, a Atividade de Cópia fará a conversão de formato;
2. - se a tabela de entrada for especificada como uma pasta que pode conter múltiplos arquivos e tabela de saída é especificada como um arquivo, a Atividade de Cópia mesclará os arquivos na pasta de origem em um único arquivo coletor;
3. - se "columnMapping" estiver especificado, a Atividade de Cópia fará a transformação de dados correspondente.


### Exemplo 1 - mapeamento de coluna do SQL Server para blobs do Azure
Neste exemplo, a **tabela de entrada** é definida do modo a seguir. A tabela de entrada tem uma estrutura e ela aponta para uma tabela do SQL em um banco de dados SQL Server.
         
		{
		    "name": "MyOnPremTable",
    		"properties":
    		{
				"structure": 
				[
            		{ "name": "userid", "type": "String"},
            		{ "name": "name", "type": "String"},
            		{ "name": "group", "type": "Decimal"}
				],
				"location":
				{
					"type": "OnPremisesSqlServerTableLocation",
					"tableName": "MyTable",
					"linkedServiceName": "MyOnPremisesSQLDB"
				},
				"availability":	
				{
    				"frequency": "Hour",
    				"interval": 1
				}
     		}
		}

Neste exemplo, a **tabela de saída** é definida do modo a seguir. A tabela de saída tem uma estrutura e ela aponta para um blob em um armazenamento de blobs do Azure.
         
		
	{
		"name": "MyDemoBlob",
		"properties":
		{
    		"structure":
			[
        	    { "name": "myuserid", "type": "String"},
        	    { "name": "mygroup", "type": "String"},
        	    { "name": "myname", "type": "Decimal"}
			],
			"location":
    		{
    	    	"type": "AzureBlobLocation",
		        "folderPath": "MyContainer/MySubFolder",
				"fileName": "MyBlobName",
    	    	"linkedServiceName": "MyLinkedService",
    	    	"format":
    	    	{
    	        	"type": "TextFormat",
		            "columnDelimiter": ",",
    		        "rowDelimiter": ";",
    		        "EscapeChar": "$",
    		        "NullValue": "NaN"
    		    }
			},
			"availability":
			{
    			"frequency": "Hour",
    			"interval": 1
			}
		}
	}	

Se você não especificar um **fileName** para uma **tabela de saída**, os arquivos gerados no **folderPath** serão nomeados no seguinte formato: Data.<Guid>.txt (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

Para definir **folderPath** e **fileName** dinamicamente com base no horário **SliceStart**, use a propriedade **partitionedBy**. No exemplo a seguir, **folderPath** usa o ano, mês e dia de SliceStart (hora de início da fatia que está sendo processada) e fileName usa a hora de SliceStart. Por exemplo, se uma fatia é produzida para 2014-10-20T08:00:00, o folderName é definido como wikidatagateway/wikisampledataout/2014/10/20 e o fileName é definido como 08.csv.

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

#### Exemplo – definir o mapeamento de coluna
Neste exemplo, uma atividade em uma pipeline é definida da seguinte maneira. As colunas da fonte são mapeadas para colunas no coletor (**columnMappings**) utilizando a propriedade **Translator**.

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "MyOnPremTable"  } ],
		"outputs":  [ { "name": "MyDemoBlob" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource"
    		},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
    		}
		}
	}

![Mapeamento de coluna][image-data-factory-column-mapping-1]

### Exemplo 2- mapeamento de coluna com a consulta SQL do SQL Server para blobs do Azure
Neste exemplo, uma consulta SQL (em vez de tabela no exemplo anterior) é utilizada para extrair dados de um SQL Server local e colunas de resultados de consulta são mapeadas para o artefato de fonte e, em seguida, para o artefato de destino. Para fins desse exemplo, a consulta retorna 5 colunas.

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource",
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', SliceStart)"
			},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
    		}
		}
	}

![Mapeamento de coluna 2][image-data-factory-column-mapping-2]

## Manipulação de tipo de dados pela Atividade de cópia

Os tipos de dados especificados na seção Estrutura da definição de tabela será considerado somente para **BlobSource**. A tabela a seguir descreve como os tipos de dados são tratados para outros tipos de fonte e coletor.

<table>	
	<tr>
		<th align="left">Fonte/coletor</th>
		<th align="left">Lógica de manipulação de tipo de dados</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>Os tipos de dados definidos na seção <b>Estrutura</b> da definição da tabela são ignorados. Os tipos de dados definidos no banco de dados SQL subjacente serão usados para a extração de dados durante a atividade da cópia.</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>Os tipos de dados definidos na seção <b>Estrutura</b> da definição da tabela são ignorados. Os tipos de dados na origem e destino subjacente serão comparados e conversão implícita de tipo será feito quando há incompatibilidades de tipo.</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td>Ao realizar uma transferência de <b>BlobSource</b> para <b>BlobSink</b>, não há nenhuma transformação de tipo; tipos de dados definidos na seção <b>Estrutura</b> da definição da tabela são ignorados. Para destinos diferentes de <b>BlobSink</b>, os tipos de dados definidos na seção <b>Estrutura</b> da definição de tabela serão respeitados.<br/><br/>
		Se a <b>Estrutura</b> não é especificada na definição de tabela, a manipulação de tipo depende da propriedade <b>formato</b> da tabela <b>BlobSource</b>:
		<ul>
			<li> <b>TextFormat:</b> todos os tipos de coluna são tratados como cadeia de caracteres e todos os nomes de coluna são definidos como "Prop_&lt;0-N>"</li> 
			<li><b>AvroFormat:</b> utilize os nomes e tipos de coluna internos no arquivo Avro.</li> 
			<li><b>JsonFormat:</b> todos os tipos de coluna são tratados como cadeia de caracteres e utilizam os nomes de coluna internos no arquivo Json.</li>
		</ul>
		</td>
	</tr>

	<tr>
		<td>BlobSink</td>
		<td>Os tipos de dados definidos na seção <b>Estrutura</b> da definição da Tabela são ignorados. Os tipos de dados definidos no repositório de dados de entrada subjacente serão usados. As colunas serão especificadas como anulável para a serialização de Avro.</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>Os tipos de dados definidos na seção <b>Estrutura</b> da definição da Tabela são ignorados. Os tipos de dados definidos na tabela subjacente do Azure serão usados.</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>Os tipos de dados definidos na seção <b>Estrutura</b> da definição da Tabela são ignorados. Os tipos de dados definidos no repositório de dados de entrada subjacente serão usados.</td>
	</tr>

</table>

**Observação:** a tabela do Azure dá suporte apenas a um conjunto limitado de tipos de dados; consulte [Noções básicas sobre o modelo de dados do serviço Tabela][azure-table-data-type].

## Invocar o procedimento armazenado para o coletor SQL
Ao copiar dados no SQL Server ou no Banco de Dados SQL do Azure, um procedimento armazenado do usuário especificado poderá ser configurado e invocado com parâmetros adicionais.
### Exemplo
1. Defina o JSON de saída de tabela da seguinte maneira (considere a tabela de banco de dados SQL como um exemplo):

    	{
    		"name": "MyAzureSQLTable",
    		"properties":
    		{
    			"location":
    			{
    				"type": "AzureSqlTableLocation",
    				"tableName": "Marketing",
    				"linkedServiceName": "AzureSqlLinkedService"
    			},
    			"availability":
    			{
    				"frequency": "Hour",
    				"interval": 1
    			}
    		}
    	}

2. Defina a seção **SqlSink** na Atividade de Cópia JSON conforme demonstrado a seguir. Para chamar um procedimento armazenado ao inserir dados, ambas as propriedades **SqlWriterStoredProcedureName** e **SqlWriterTableType** são necessárias.

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

3. No banco de dados, defina o procedimento armazenado com o mesmo nome que **SqlWriterStoredProcedureName**. Ele lida com os dados de entrada de origem especificada por você e os insere na tabela de saída. Observe que o nome de parâmetro do procedimento armazenado deve ser igual ao **tableName** definido no arquivo JSON da tabela.

		CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
		AS
		BEGIN
			DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    		INSERT [dbo].[Marketing](ProfileID, State)
    		SELECT * FROM @Marketing
		END


4. No banco de dados, defina o tipo de tabela com o mesmo nome que **SqlWriterTableType**. Observe que o esquema do tipo de tabela deve ser a mesmo que o esquema retornado por seus dados de entrada.

		CREATE TYPE [dbo].[MarketingType] AS TABLE(
    	    [ProfileID] [varchar](256) NOT NULL,
    	    [State] [varchar](256) NOT NULL,
    	)

O recurso de procedimento armazenado se beneficia de [parâmetros com valores de tabela][table-valued-parameters].

## Especificar codificação para arquivos de texto
Embora a codificação UTF-8 seja muito popular, geralmente arquivos de texto de horário no Blob do Azure utilizam outras codificações por razões históricas. A propriedade **encodingName** permite que você especifique a codificação por nome de página de código para tabelas do tipo TextFormat. Para obter a lista de nomes de codificação válidos, consulte: Propriedade Encoding.EncodingName. Por exemplo: windows-1250 ou shift_jis. O valor padrão é UTF-8. Consulte [Classe de codificação](https://msdn.microsoft.com/library/system.text.encoding(v=vs.110).aspx) para nomes de codificação válidos.

## Consulte também

- [Exemplos de uso de Atividade de Cópia][copy-activity-examples]
- [Copiar dados com o Azure Data Factory][adf-copyactivity]
- [Atividade de Cópia - Referência de Scripting JSON](https://msdn.microsoft.com/library/dn835035.aspx)
- [Vídeo: apresentando a atividade de cópia do Azure Data Factory][copy-activity-video]


[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx


[adfgetstarted]: data-factory-get-started.md
[adf-copyactivity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[copy-activity-examples]: data-factory-copy-activity-examples.md

[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[azure-table-data-type]: https://msdn.microsoft.com/pt-br/library/azure/dd179338.aspx

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png

<!--HONumber=54-->