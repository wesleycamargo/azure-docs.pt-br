<properties title="Copy data with Azure Data Factory" pageTitle="Copiar dados com o Data Factory do Azure" description="Saiba como usar a atividade de cópia no Azure Data Factory para copiar dados a partir de uma fonte de dados para outra fonte de dados." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# Copiar dados com o Azure Data Factory (atividade de cópia)
É possível utilizar a **Atividade de Cópia** em uma pipeline para copiar dados de uma fonte para um coletor (destino) em um lote. A atividade de cópia pode ser utilizada nos seguintes cenários:

- **Ingresso no Azure**. Nesse cenário, os dados são copiados de uma fonte de dados local (ex: SQL Server) para um armazenamento de dados do Azure (ex: blob do Azure, tabela do Azure ou banco de dados SQL do Azure) para os seguintes subcenários:
	- Coletar dados em um local centralizado no Azure para processamento adicional.
	- Migrar dados de plataformas de nuvem não pertencentes ao Azure ou locais para o Azure.
	- Arquivar ou fazer backup de dados do Azure para armazenamento econômico em camadas.
- **Saída do Azure**. Nesse cenário, os dados são copiados do Azure (ex: Blob do Azure, tabela do Azure ou banco de dados SQL do Azure) para o armazém de dados local e o data warehouse (ex: SQL Server) para os seguintes cenários de subcenários:
	- Transferir dados para o local devido à falta de suporte para o data warehouse de nuvem.
	- Transferir dados para o local para aproveitar a solução local existente ou a infraestrutura de relatório.
	- Arquivar ou fazer backup de dados para os locais de armazenamento em camadas
- **cópia do Azure para o Azure**. Nesse cenário, os dados distribuídos por meio de fontes de dados do Azure são agregados em um repositório centralizado de dados do Azure. Exemplos: tabela do Azure para blobs do Azure, blobs do Azure para tabela do Azure, tabela do Azure para SQL Azure, blob do Azure para SQL Azure.

Consulte [Introdução ao Data Factory do Azure][adfgetstarted] para obter um tutorial que mostra como copiar dados de um armazenamento de blobs do Azure para um banco de dados SQL do Azure utilizando a Atividade de Cópia. Consulte [Habilitar pipelines para trabalhar com dados locais][use-onpremises-datasources]para uma explicação passo a passo que mostra como copiar dados de um banco de dados SQL Server local para um armazenamento de blobs do Azure utilizando a Atividade de Cópia.


## Atividade de cópia - componentes
A atividade de cópia contém os seguintes componentes: 

- **Tabela de entrada**. Uma tabela é um conjunto de dados retangular que tem um esquema. O componente da tabela de entrada 
- descreve os dados de entrada para a atividade que incluem o seguinte: nome da tabela, tipo de tabela e serviço vinculado que faz referência a uma fonte de dados que contém os dados de entrada.
- **Tabela de saída**. A tabela de saída descreve dados de saída para a atividade que incluem o seguinte: nome da tabela, tipo de tabela e o serviço vinculado que faz referência a uma fonte de dados que contém os dados de saída.
- **Regras de transformação**. As regras de transformação especificam como os dados de entrada são extraídos da fonte e como os dados de saída são carregados no coletor etc...
 
Uma atividade de cópia pode ter uma **tabela de entrada** e uma **tabela de saída**.

## JSON para Atividade de cópia
Uma pipeline consiste em uma ou mais atividades. As atividades nas pipelines são definidas na seção de **atividades []**. O JSON para uma pipeline é o seguinte:
         
	{
		"name": "PipelineName",
		"properties": 
    	{
        	"description" : "pipeline description",
        	"activities":
        	[
	
    		]
		}
	}

Cada atividade dentro da seção de **atividades** tem a seguinte estrutura de nível superior. A propriedade **type** deve ser definida como **CopyActivity**. A Atividade de Cópia pode ter apenas uma tabela de entrada e uma de saída.
         

	{
		"name": "ActivityName",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [],
		"outputs":  [],
		"transformation":
		{

		},
		"policy":
		{
		
		}
	}

A tabela a seguir descreve as marcas utilizadas com uma seção de atividade. 

<table border="1">	
	<tr>
		<th align="left">Marca</th>
		<th align="left">Descrição</th>
		<th align="left">Obrigatório</th>
	</tr>	

	<tr>
		<td>name</td>
		<td>Nome da atividade.</td>
		<td>S</td>
	</tr>	

	<tr>
		<td>description</td>
		<td>Texto que descreve qual a utilidade da atividade.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>type</td>
		<td>Especifica o tipo da atividade. <br/><br/>O <b>type</b> deve ser definido como <b>CopyActivity</b>.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>inputs</td>
		<td>Tabelas de entrada usadas pela atividade.  Especifique uma única tabela de entrada para a Atividade de Cópia.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>outputs</td>
		<td>Tabelas de saída usadas pela atividade.  Especifique uma única tabela de saída para a Atividade de Cópia.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>transformation</td>
		<td>Propriedades da transformação depende do tipo.  O <b>A Atividade de Cópia</b> exige que você especifique uma <b>fonte</b> e um <b>coletor</b> na seção de <b>transformation</b> . Mais detalhes serão fornecidos posteriormente neste artigo. </td>
		<td>S</td>
	</tr>

	<tr>
		<td>policy</td>
		<td>Diretivas que afetam o comportamento de tempo de execução da atividade. Se não for especificado, os valores padrão serão utilizados.</td>
		<td>N</td>
	</tr>


</table>

Consulte [Referência de Script JSON][json-script-reference] para obter informações detalhadas sobre propriedades/marcas JSON.

## Atividade de cópia - exemplo
Neste exemplo, uma tabela de entrada e uma tabela de saída são definidas e as tabelas são utilizadas em uma Atividade de cópia em uma pipeline que copia dados de um banco de dados SQL Server local para um blob do Azure.

**Suposições**
Os seguintes artefatos do Azure Data Factory são referenciados em exemplos de scripts JSON que se segue:

* Grupo de recursos denominado **ADF**.
* Um data factory do Azure denominado **CopyFactory**.
* Um serviço vinculado denominado **MyOnPremisesSQLDB** que aponta para um banco de dados SQL Server local.
* Um serviço vinculado denominado **MyAzureStorage** que aponta para um armazenamento de blobs do Azure.

### Tabela de entrada JSON
O script JSON a seguir define uma tabela de entrada que se refere a uma tabela SQL: **MyTable** em um banco de dados SQL Server local que o serviço vinculado **MyOnPremisesSQLDB** define. Observe que **name** é o nome da tabela do Data Factory do Azure e **tableName** é o nome da tabela SQL em um banco de dados SQL Server.

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
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

O seguinte exemplo de comando do PowerShell do Azure utiliza a **New-AzureDataFactoryTable** que utiliza um arquivo JSON que contém o script acima para criar uma tabela (**MyOnPremTable**) em uma data factory do Azure: **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -Name MyOnPremTable -DataFactoryName CopyFactory -File <Filepath>\MyOnPremTable.json.

Consulte [Referência do Cmdlet][cmdlet-reference] para obter detalhes sobre os cmdlets da Data Factory. 

### Tabela de saída JSON
O script JSON a seguir define uma tabela de saída: **MyDemoBlob**, que se refere a um blob do Azure: **MyBlob** na pasta blob: **MySubFolder** no contêiner de blob: **MyContainer**.
         
	{
   		"name": "MyDemoBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
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

O seguinte exemplo de comando do PowerShell do Azure utiliza a **New-AzureDataFactoryTable** que utiliza um arquivo JSON que contém o script acima para criar uma tabela (**MyDemoBlob**) em uma data factory do Azure: **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>


### Pipeline (com a Atividade de cópia) JSON
Neste exemplo, uma pipeline: **CopyActivityPipeline** é definida com as seguintes propriedades: 

- A propriedade **type** é definida como **CopyActivity**.
- **MyOnPremTable** é especificado como a entrada (marca de **entradas**).
- **MyAzureBlob** é especificado como a saída (marca de **saídas**)
- A seção **Transformação** contém duas subseções: **fonte** e **coletor**. O tipo de fonte é definido como **SqlSource** e o tipo de coletor é definido como **BlobSink**. O **sqlReaderQuery** define a transformação (projeção) a ser realizada na fonte. Para obter detalhes sobre todas as propriedades, consulte a [Referência de Script JSON][json-script-reference].

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
						"transformation":
	    				{
							"source":
							{
								"type": "SqlSource",
                    			"sqlReaderQuery": "select * from MyTable"
							},
							"sink":
							{
                        		"type": "BlobSink"
							}
	    				}
      				}
        		]
    		}
		}


 O seguinte exemplo de comando do PowerShell do Azure utiliza a **New-AzureDataFactoryPipeline** que utiliza um arquivo JSON que contém o script acima para criar uma pipeline (**CopyActivityPipeline**) em uma data factory do Azure: **CopyFactory**.
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>

## Saídas e entradas com suporte
O exemplo acima utilizando SqlSource como fonte e BlobSink como o coletor na seção de transformação. A tabela a seguir lista as fontes e coletores com suporte pela Atividade de Cópia. 

<table border="1">	
	<tr>
		<th><i>Coletor/fonte<i></th>
		<th>Blob do Azure</th>
		<th>Tabela do Azure</th>
		<th>Banco de Dados SQL Azure</th>
		<th>SQL Server local</th>
		<th>SQL Server na IaaS</th>
	</tr>	

	<tr>
		<td><b>Blob do Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
	</tr>

	<tr>
		<td><b>Tabela do Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>	

	<tr>
		<td><b>Banco de Dados SQL Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>


	<tr>
		<td><b>SQL Server local</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>SQL Server na IaaS</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

</table>


A tabela a seguir lista os tipos de fonte e coletor que podem ser utilizados em um arquivo JSON para uma pipeline que contém uma Atividade de cópia.


<table border="1">	
	<tr>
		<th></th>
		<th align="left">Tipo de fonte</th>
		<th align="left">Tipo de coletor</th>
	</tr>	

	<tr>
		<td><b>Blob do Azure</b></td>
		<td>BlobSource</td>
		<td>BlobSink</td>
	</tr>

	<tr>
		<td><b>Tabela do Azure</b></td>
		<td>AzureTableSource</td>
		<td>AzureTableSink</td>
	</tr>

	<tr>
		<td><b>Banco de Dados SQL Azure</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>SQL Server local</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>SQL na IaaS</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>
</table>

A tabela a seguir lista as propriedades com suporte por essas fontes e coletores.

<table border="1">

	<tr>
	<th align="left">Fonte/coletor</th>
	<th align="left">Propriedade com suporte</th>
	<th align="left">Descrição</th>
	<th align="left">Valores permitidos</th>
	<th align="left">Obrigatório</th>
	</tr>

	<tr>
		<tr>
		<td><b>BlobSource</b></td>
		<td>BlobSourceTreatEmptyAsNull</td>
		<td>Especifica se deve-se tratar a cadeia de caracteres nula ou vazia como valor nulo.</td>
		<td>TRUE<br/>FALSE</td>
		<td>N</td>
		</tr>
	</tr>

	<tr>
		<td></td>
		<td>BlobSourceSkipHeaderLineCount</td>
		<td>Indica quantas linhas precisam ser ignoradas.</td>
		<td>Inteiro de 0 ao máximo.</td>
		<td>N</td>
	</tr>

	<tr>
		<td><b>AzureTableSource</b></td>
		<td>AzureTableSourceQuery</td>
		<td>Utiliza a consulta personalizada para ler os dados.</td>
		<td>Cadeia de caracteres de consulta de tabela do Azure.<br/>Exemplo: "ColumnA eq ValueA"</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>AzureTableSourceIgnoreTableNotFound</td>
		<td>Indica se deseja ignorar a exceção: "a tabela não existe".</td>
		<td>TRUE<br/>FALSE</td>
		<td>N</td>
	</tr>


	<tr>
		<td><b>SqlSource</b></td>
		<td>sqlReaderQuery</td>
		<td>Utiliza a consulta personalizada para ler os dados.</td>
		<td>Cadeia de caracteres de consulta SQL.<br/><br/> Por exemplo: "Selecionar * de MyTable".<br/><br/> Se não especificado, selecione <columns defined in structure> (colunas definidas na estrutura) da consulta MyTable".</td>
		<td>N</td>
	</tr>


	<tr>
		<td><b>AzureTableSink</b></td>
		<td>azureTableDefaultPartitionKeyValue</td>
		<td>Valor de chave de partição padrão que pode ser utilizado pelo coletor.</td>
		<td>Um valor de cadeia de caracteres.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTablePartitionKeyName</td>
		<td>Nome de coluna do usuário especificado, cujos valores de coluna são utilizados como chave de partição.<br/><br/> Se não especificado, AzureTableDefaultPartitionKeyValue será utilizado como a chave da partição.</td>
		<td>Um nome de coluna.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableRowKeyName</td>
		<td>Valores de coluna de nome de coluna especificado a ser usado como uma chave de linha.<br/><br/>Se não especificado, um GUID é usado para cada linha.</td>
		<td>Um nome de coluna.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableInsertType</td>
		<td>O modo para inserir dados na tabela do Azure.</td>
		<td>"mesclar"<br/><br/>"substituir"</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchSize</td>
		<td>Insere dados na tabela do Azure quando o writeBatchSize ou writeBatchTimeout for atingido.</td>
		<td>Inteiro de 1 a 100 (unidade = contagem de linhas)</td>
		<td>N<br/><br/>(Padrão = 100)</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchTimeout</td>
		<td>Insere dados na tabela do Azure quando o writeBatchSize ou writeBatchTimeout for atingido</td>
		<td>(Unidade = timespan)<br/><br/>Exemplo: "00:20:00" (20 minutos)</td>
		<td>N<br/><br/>(Padrão para 90 seg. de valor de tempo padrão de cliente de armazenamento)</td>
	</tr>

	<tr>
		<td><b>SqlSink</b></td>
		<td>SqlWriterStoredProcedureName</td>
		<td>Especifica o nome do procedimento armazenado para dados de inserção (atualização/inserção) na tabela de destino.</td>
		<td>Um nome de procedimento armazenado.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>SqlWriterTableType</td>
		<td>Especifica o tipo de tabela a ser utilizado no procedimento armazenado acima.</td>
		<td>Um nome de tipo de tabela.</td>
		<td>N</td>
	</tr>
</table>

### SQL na IaaS(Infraestrutura como um serviço)
Para SQL na IaaS, há suporte para o Azure como provedor da IaaS. Há suporte para a seguinte rede e topologias VPN. Observe que o Gateway de Gerenciamento de Dados é necessário para o caso #2 e 3 #, quanto não for necessário para o caso #1. Para obter detalhes sobre o Gateway de Gerenciamento de Dados, consulte [Ativar suas pipelines para acessar dados locais][use-onpremises-datasources].

1.	Máquina virtual com o nome DNS público e porta estática pública: mapeamento de porta privada
2.	Máquina virtual com o nome DNS público sem ponto de extremidade SQL exposto
3.	Rede virtual
	<ol type='a'>
	<li>Azure Cloud VPN with following topology at the end of the list. </li>	
	<li>VM with onpremises-to-cloud site-to-site VPN using Azure Virtual Network.</li>	
	</ol>  
	![Data Factory with Copy Activity][image-data-factory-copy-actvity]

## Column filtering using structure definition
Depending on the type of Table, it is possible to specify a subset of the columns from the source by specifying fewer columns in the **Structure** definition of the table definition than the ones that exist in the underlying data source. The following table provides information about column filtering logic for different types of table. 

<table>

	<tr>
		<th align="left">Tipo da tabela</th>
		<th align="left">Lógica de filtragem de coluna</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>O <b>Estrutura</b> a definição da estrutura da tabela JSON deve corresponder à estrutura do blob.  Para selecionar um subconjunto das colunas, use o recurso de mapeamento de coluna descrito na próxima seção: Regras de transformação - mapeamento de coluna.</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation e OnPremisesSqlServerTableLocation</td>
		<td align="left">
			<p>Se a propriedade <b>SqlReaderQuery</b> for especificada como parte da definição de Atividade de Cópia, <b>Estrutura</b> a definição da estrutura da tabela deve estar alinhada com as colunas selecionadas na consulta.</p>
			<p>Se a propriedade <b>SqlReaderQuery</b> não for especificada, a Atividade de Cópia criará automaticamente uma consulta SELECIONAR, com base nas colunas especificadas na <b>Estrutura</b> a definição da estrutura da definição de tabela.</p>
		</td>
	<tr>

	<tr>
		<td>AzureTableLocation</td>
		<td>
			O <b>Estrutura</b> a seção estrutura na definição da tabela pode conter o conjunto completo ou um subconjunto das colunas na tabela subjacente do Azure.
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

#### Exemplo 1 - mapeamento de coluna do SQL Server para blobs do Azure
Nesse exemplo, a **tabela de entrada** é definida da seguinte maneira. A tabela de entrada tem uma estrutura e ela aponta para uma tabela do SQL em um banco de dados SQL Server.
         
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

Nesse exemplo, a **tabela de saída** é definida da seguinte maneira. A tabela de saída tem uma estrutura e ela aponta para um blob em um armazenamento de blobs do Azure.
         
		
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

se você não especificar um **fileName** para uma **tabela de entrada**, todos os arquivos/blobs da pasta de entrada (**folderPath**) serão considerados como entradas. Se você especificar um nome de arquivo em JSON, apenas arquivo/blob especificado será considerado como entrada de asn. Consulte os arquivos do [tutorial][adf-tutorial] para obter exemplos.

Se você não especificar um **fileName** para uma **tabela de saída**, os arquivos gerados no **folderPath** serão nomeados no seguinte formato: Data.<Guid>.txt (por exemplo: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

Para definir **folderPath** e **fileName** dinamicamente com base na hora **SliceStart**, use a propriedade **partitionedBy**. No exemplo a seguir, **folderPath** usa o ano, mês e dia de SliceStart (hora de início da fatia que está sendo processada) e fileName usa a hora de SliceStart. Por exemplo, se uma fatia é produzida para 2014-10-20T08:00:00, o folderName é definido como wikidatagateway/wikisampledataout/2014/10/20 e o fileName é definido como 08.csv. 

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

Neste exemplo, uma atividade em uma pipeline é definida da seguinte maneira. As colunas da fonte são mapeadas para colunas no coletor (**columnMappings**) utilizando a propriedade **Translator**.

##### Exemplo - definir o mapeamento de coluna

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

![Column Mapping][image-data-factory-column-mapping-1]

##### Exemplo 2- mapeamento de coluna com a consulta SQL do SQL Server para blobs do Azure
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
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', Time.AddHours(SliceStart, 0))"
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

![Column Mapping 2][image-data-factory-column-mapping-2]

#### Manipulação de tipo de dados pela Atividade de cópia

Os tipos de dados especificados na seção Estrutura da definição de Tabela serão considerados somente para **BlobSource**.  A tabela a seguir descreve como os tipos de dados são tratados para outros tipos de fonte e coletor.

<table>	
	<tr>
		<th align="left">Fonte/coletor</th>
		<th align="left">Lógica de manipulação de tipo de dados</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>Tipos de dados definidos na <b>Estrutura</b> seção da definição da tabela são ignorados.  Os tipos de dados definidos no banco de dados SQL subjacente serão usados para a extração de dados durante a atividade da cópia.</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>Tipos de dados definidos na <b>Estrutura</b> seção da definição da tabela são ignorados.  Os tipos de dados na origem e destino subjacente serão comparados e conversão implícita de tipo será feito quando há incompatibilidades de tipo.</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td><p>Durante a transferência de <b>BlobSource</b> para <b>BlobSink</b>, não há nenhuma transformação de tipo. Tipos definidos em <b>Estrutura</b> seção da definição da tabela são ignorados.  Para destinos diferentes de <b>BlobSink</b>, tipos de dados definidos na <b>Estrutura</b> seção da definição da Tabela será respeitada.</p>
		<p>
		Se o <b>Estrutura</b> não foi especificada na definição de tabela, a manipulação de tipo depende do <b>formato</b> da propriedade de <b>BlobSink</b>:
		</p>
		<ul>
			<li> <b>TextFormat:</b> todos os tipos de coluna são tratados como cadeia de caracteres e todos os nomes de coluna são definidos como "Prop_<0-N>"</li> 
			<li><b>AvroFormat:</b> utiliza os nomes e tipos de coluna internos no arquivo Avro.</li> 
			<li><b>JsonFormat:</b> todos os tipos de coluna são tratados como cadeia de caracteres e utilizam os nomes de coluna interno no arquivo Json.</li>
		</ul>
		</td>
	</tr>

	<tr>
		<td>BlobSink</td>
		<td>Tipos de dados definidos na <b>Estrutura</b> seção da definição de Tabela de entrada são ignorados.  Os tipos de dados definidos no repositório de dados de entrada subjacente serão usados.  As colunas serão especificadas como anulável para a serialização de Avro.</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>Tipos de dados definidos na <b>Estrutura</b> seção da definição da Tabela são ignorados.  Os tipos de dados definidos na tabela subjacente do Azure serão usados.</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>Tipos de dados definidos na <b>Estrutura</b> seção da definição da Tabela são ignorados.  Os tipos de dados definidos no repositório de dados de entrada subjacente serão usados.</td>
	</tr>

</table>


## Instruções passo a passo
Consulte [Introdução ao Data Factory do Azure][adfgetstarted] para obter um tutorial que mostra como copiar dados de um armazenamento de blobs do Azure para um banco de dados SQL do Azure utilizando a Atividade de Cópia.
 
Consulte [Habilitar pipelines para trabalhar com dados locais][use-onpremises-datasources]para uma explicação passo a passo que mostra como copiar dados de um banco de dados SQL Server local para um armazenamento de blobs do Azure utilizando a Atividade de Cópia.



[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png

<!--HONumber=35.2-->
