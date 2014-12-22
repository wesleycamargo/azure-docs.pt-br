<properties title="Copy data with Azure Data Factory" pageTitle="Copiar dados com o Data Factory do Azure" description="Learn how to use Copy Activity in Azure Data Factory to copy data from a data source to another data source." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Copiar dados com o Azure Data Factory (atividade de cópia)
É possível utilizar a **atividade de cópia** em uma pipeline para copiar dados de uma fonte para um coletor (destino) em um lote. A atividade de cópia pode ser utilizada nos seguintes cenários:

- **Ingresso no Azure**. Nesse cenário, os dados são copiados de uma fonte de dados local (ex: SQL Server) para um armazenamento de dados do Azure (ex: Blob do Azure, tabela do Azure ou banco de dados SQL do Azure) para os seguintes subcenários:
	- Coletar dados em um local centralizado no Azure para processamento adicional.
	- Migrar dados de plataformas de nuvem não pertencentes ao Azure ou locais para o Azure.
	- Arquivar ou fazer backup de dados do Azure para armazenamento econômico em camadas.
- **Saída do Azure**. Nesse cenário, os dados são copiados do Azure (ex: Blob do Azure, tabela do Azure ou banco de dados SQL do Azure) para o armazém de dados local e o data warehouse (ex: SQL Server) para os seguintes cenários de subcenários:
	- Transferir dados para o local devido à falta de suporte para o data warehouse de nuvem.
	- Transferir dados para o local para aproveitar a solução local existente ou a infraestrutura de relatório.
	- Arquivar ou fazer backup de dados para os locais de armazenamento em camadas
- **Cópia do Azure para o Azure**. Nesse cenário, os dados distribuídos por meio de fontes de dados do Azure são agregados em um repositório centralizado de dados do Azure. Exemplos: tabela do Azure para blobs do Azure, blobs do Azure para tabela do Azure, tabela do Azure para SQL Azure, blob do Azure para SQL Azure.

Consulte [Introdução ao Azure Data Factory][adfgetstarted] para obter um tutorial que mostra como copiar dados de um armazenamento de blobs do Azure para um banco de dados SQL do Azure utilizando a Atividade de cópia. Consulte [Habilitar pipelines para trabalhar com dados locais][utilizando fontes de dados locais] para obter uma explicação passo a passo que mostra como copiar dados de um banco de dados SQL Server local para um armazenamento de blobs do Azure utilizando a Atividade de cópia.


## Atividade de cópia - componentes
A atividade de cópia contém os seguintes componentes: 

- **Tabela de entrada**. Uma tabela é um conjunto de dados retangular que tem um esquema. O componente da tabela de entrada 
- descreve os dados de entrada para a atividade que incluí o seguinte: nome da tabela, tipo da tabela e serviço vinculado que faz referência a uma fonte de dados que contém os dados de entrada.
- **Tabela de saída**. A tabela de saída descreve os dados de saída da atividade que incluem o seguinte: nome da tabela, tipo da tabela e serviço vinculado que faz referência a uma fonte de dados que mantém os dados de saída.
- **Regras de transformação**. As regras de transformação especificam como os dados de entrada são extraídos da fonte e como os dados de saída são carregados no coletor etc...
 
Uma atividade de cópia pode ter uma **tabela de entrada** e uma **tabela de saída**.

## JSON para Atividade de cópia
Uma pipeline consiste em uma ou mais atividades. A atividades nas pipelines são definidas na seção de **atividades []**. O JSON para uma pipeline é o seguinte:
         
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

Cada atividade dentro da seção de **atividades** tem a seguinte estrutura de nível superior. A propriedade **type** (tipo) deve ser definida como **CopyActivity**. A Atividade de cópia pode ter apenas uma tabela de entrada e uma de saída.
         

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
		<td>Especifica o tipo da atividade. <br/><br/>O <b>tipo</b> deve ser definido como <b>CopyActivity</b>.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>inputs</td>
		<td>Tabelas de entrada utilizadas pela atividade.  Especifica uma única tabela de entrada para a Atividade de cópia.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>outputs</td>
		<td>Tabelas de saída utilizadas pela atividade.  Especifica uma única tabela de saída para a Atividade de cópia.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>transformation</td>
		<td>As propriedades da transformação dependem do tipo.  A <b>atividade de cópia</b> requer que você especifique uma <b>fonte</b> e uma seção <b>coletor</b> dentro da seção <b>transformação</b>. Mais detalhes serão fornecidos posteriormente neste artigo. </td>
		<td>S</td>
	</tr>

	<tr>
		<td>policy</td>
		<td>Diretivas que afetam o comportamento de tempo de execução da atividade. Se não for especificado, os valores padrão serão utilizados.</td>
		<td>N</td>
	</tr>


</table>

Consulte a [Referência de script JSON][json-script-reference] para obter informações detalhadas sobre propriedades/marcas JSON.

## Atividade de cópia - exemplo
Neste exemplo, uma tabela de entrada e uma tabela de saída são definidas e as tabelas são utilizadas em uma Atividade de cópia em uma pipeline que copia dados de um banco de dados SQL Server local para um blob do Azure.

**Suposições**
Os seguintes artefatos do Azure Data Factory são referenciados em exemplos de scripts JSON que se segue:

* Grupo de recursos denominado **ADF**.
* Um data factory do Azure denominado **CopyFactory**.
* Um serviço vinculado denominado **MyOnPremisesSQLDB** que aponta para um banco de dados SQL Server local.
* Um serviço vinculado denominado **MyAzureStorage** que aponta para um armazenamento de blobs do Azure.

### Tabela de entrada JSON
O script JSON a seguir define uma tabela de entrada que se refere a uma tabela SQL: **MyTable** em um banco de dados SQL Server local que o serviço vinculado **MyOnPremisesSQLDB** define. Observe que o **name** é o nome da tabela do Azure Data Factory e **tableName** é o nome da tabela SQL em um banco de dados SQL Server.

         
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

Consulte a [Referência de cmdlet][cmdlet-reference] para obter detalhes sobre os cmdlets do Data Factory. 

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

- A propriedade **type** (tipo) é definida como **CopyActivity**.
- **MyOnPremTable** é especificado como a entrada (marca de **entradas**).
- **MyAzureBlob** é especificado como a saída (marca de **saídas**)
A seção - **Transformação** contém duas subseções: **fonte** e **coletor**. O tipo de fonte é definido como **SqlSource** e o tipo de coletor é definido como **BlobSink**. O **sqlReaderQuery** define a transformação (projeção) a ser realizada na fonte. Para obter detalhes sobre todas as propriedades, consulte a [Referência de script JSON][json-script-reference].

         
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
O exemplo acima utilizando SqlSource como fonte e BlobSink como o coletor na seção de transformação. A tabela a seguir lista as fontes e coletores com suporte pela Atividade de cópia. 

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
		<td>Cadeia de caracteres de consulta de tabela do Azure.<br/>Exemplo: "ColumnA eq ValueA"</td> ("coluna A equivale a valor A)
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>AzureTableSourceIgnoreTableNotFound</td>
		<td>Indica se deseja ignorar a exceção: "table does not exist".</td> ("a tabela não existe")
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
		<td>Valores de coluna de nome de coluna especificado que serão utilizados como uma chave de linha.<br/><br/>Se não especificado, é utilizado um GUID para cada linha.</td>
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
		<td>N<br/><br/>(padrão = 100)</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchTimeout</td>
		<td>Insere dados na tabela do Azure quando o writeBatchSize ou writeBatchTimeout for atingido</td>
		<td>(Unidade = timespan)<br/><br/>Exemplo: "00:20:00" (20 minutos)</td>
		<td>N<br/><br/>(padrão para 90 seg. de valor de tempo padrão de cliente de armazenamento)</td>
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
Para SQL na IaaS, há suporte para o Azure como provedor da IaaS. Há suporte para a seguinte rede e topologias VPN. Observe que o Gateway de gerenciamento de dados é necessário para o caso #2 e #3, enquanto não é necessário caso #1. Para obter detalhes sobre o Gateway de gerenciamento de dados, consulte [Habilitar suas pipelines para acessar os dados locais][use-onpremises-datasources].

1.	Máquina virtual com o nome DNS e a porta estática pública: mapeamento de porta privada
2.	Máquina virtual com o nome DNS público sem ponto de extremidade SQL exposto
3.	Rede virtual
	<ol type='a'>
	<li>VPN de nuvem do Azure com topologia a seguir no final da lista. </li>	
	<li>Máquina virtual com VPN de site a site local para nuvem utilizando a Rede virtual do Azure.</li>	
	</ol>  
	![Data Factory with Copy Activity][image-data-factory-copy-actvity]

## Filtragem de coluna utilizando a definição de estrutura
Dependendo do tipo de tabela, é possível especificar um subconjunto das colunas da fonte especificando menos colunas na definição de **estrutura** da definição de tabela que existe na fonte de dados subjacente. A tabela a seguir fornece informações sobre a lógica de filtragem de coluna para diferentes tipos de tabela. 

<table>

	<tr>
		<th align="left">Tipo da tabela</th>
		<th align="left">Lógica de filtragem de coluna</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>A definição da <b>estrutura</b> da tabela JSON deve corresponder à estrutura do blob.  Para selecionar um subconjunto das colunas, utilize o recurso de mapeamento de coluna descrito na próxima seção: Regras de transformação - mapeamento de coluna.</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation e OnPremisesSqlServerTableLocation</td>
		<td align="left">
			<p>Se a propriedade <b>SqlReaderQuery</b> for especificada como parte da definição da Atividade de cópia, a definição de <b>estrutura</b> da tabela deve ser alinhada com as colunas selecionadas na consulta.</p>
			<p>Se a propriedade <b>SqlReaderQuery</b> não for especificada, a Atividade de cópia automaticamente construirá uma consulta SELECT, com base nas colunas especificadas na definição de <b>estrutura</b> da definição de tabela.</p>
		</td>
	<tr>

	<tr>
		<td>AzureTableLocation</td>
		<td>
			A seção <b>estrutura</b> na definição da tabela pode conter o conjunto completo ou um subconjunto das colunas na tabela subjacente do Azure.
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

Se você não especificar um **fileName** para uma **tabela de entrada**, todos os arquivos/blobs da pasta de entrada (**folderPath**) serão considerados como entradas. Se você especificar um nome de arquivo em JSON, apenas arquivo/blob especificado será considerado como entrada de asn. Consulte os arquivos de exemplo no [tutorial][adf-tutorial] para ver exemplos.

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

Os tipos de dados especificados na seção Estrutura da definição de tabela será considerado somente para **BlobSource**.  A tabela a seguir descreve como os tipos de dados são tratados para outros tipos de fonte e coletor.

<table>	
	<tr>
		<th align="left">Fonte/coletor</th>
		<th align="left">Lógica de manipulação de tipo de dados</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>Tipos de dados definidos na seção <b>estrutura</b> da definição de tabela são ignorados.  Os tipos de dados definidos no banco de dados SQL subjacente serão utilizados para a extração de dados durante a atividade de cópia.</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>Tipos de dados definidos na seção <b>estrutura</b> da definição de tabela são ignorados.  Os tipos de dados na fonte e destino subjacente serão comparados e a conversão implícita de tipo será feita se houver a incompatibilidades de tipo.</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td><p>Durante a transferência de <b>BlobSource</b> para <b>BlobSink</b>, não há nenhuma transformação do tipo. Os tipos definidos na seção <b>estrutura</b> da definição de tabela são ignorados.  Para destinos que não sejam <b>BlobSink</b>, os tipos de dados definidos na seção <b>estrutura</b> da definição de tabela serão respeitados.</p>
		<p>
		Se a <b>estrutura</b> não é especificada na definição de tabela, a manipulação de tipo depende da propriedade <b>format</b> de <b>BlobSink</b>:
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
		<td>Os tipos de dados definidos na seção <b>estrutura</b> da definição de tabela de entrada são ignorados.  Os tipos de dados definidos no armazenamento de dados de entrada subjacente serão utilizados.  As colunas serão especificadas como anulável para a serialização Avro.</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>Os tipos de dados definidos na seção <b>estrutura</b> da definição de tabela são ignorados.  Os tipos de dados definidos na tabela subjacente do Azure serão utilizados.</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>Os tipos de dados definidos na seção <b>estrutura</b> da definição de tabela são ignorados.  Os tipos de dados definidos no armazenamento de dados de entrada subjacente serão utilizados.</td>
	</tr>

</table>

## Limitações

> [WACOM.NOTE] Quando uma pipeline é suspensa ou excluída, ou quando um data factory é excluído, a operação de cópia em andamento não é suspensa. Ela continuará a executar até a conclusão. No entanto, a operação de cópia que envolve uma fonte de dados local pode ser interrompida ao reiniciar o serviço de Gateway de gerenciamento de dados utilizando o gerenciador de configuração de Gateway de gerenciamento de dados ou o miniaplicativo de serviços. 



## Instruções passo a passo
Consulte [Introdução ao Azure Data Factory][adfgetstarted] para obter um tutorial que mostra como copiar dados de um armazenamento de blobs do Azure para um banco de dados SQL do Azure utilizando a Atividade de cópia.
 
Consulte [Habilitar pipelines para trabalhar com dados locais][utilizando fontes de dados locais] para uma explicação passo a passo que mostra como copiar dados de um banco de dados SQL Server local para um armazenamento de blobs do Azure utilizando a Atividade de cópia.



[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png
