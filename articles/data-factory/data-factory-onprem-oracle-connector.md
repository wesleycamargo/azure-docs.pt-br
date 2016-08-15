<properties 
	pageTitle="Mover dados para dentro e fora do Oracle usando o Data Factory | Microsoft Azure" 
	description="Aprenda a mover dados de/para o banco de dados da Oracle que está no local usando o Azure Data Factory." 
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
	ms.date="07/05/2016" 
	ms.author="spelluru"/>

# Mover dados para dentro e fora do Oracle local usando o Azure Data Factory 

Este artigo descreve como você pode usar a atividade de cópia de data factory para mover dados do Oracle para outro repositório de dados e de outro repositório de dados para o Oracle. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

## Instalação 
Para o serviço de fábrica de dados do Azure poder se conectar ao banco de dados Oracle no local, você deve instalar o seguinte:

- Gateway de Gerenciamento de Dados na mesma máquina que hospeda o banco de dados ou em uma máquina separada para evitar competência por recursos com o banco de dados. O Gateway de Gerenciamento de Dados é um software que conecta fontes de dados locais a serviços de nuvem de maneira segura e gerenciada. Consulte o artigo [Mover dados entre local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre o Gateway de Gerenciamento de Dados.
- Provedor de dados Oracle para .NET. Está incluído nos [Componentes de Acesso a Dados do Oracle para Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Instale a versão adequada (32/64 bits) no computador host em que o gateway está instalado. [O Oracle Data Provider .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) pode acessar o Oracle Database 10g Release 2 ou posterior.

	Se você escolher "Instalação de XCopy", siga as etapas no readme.htm. É recomendável escolher o instalador com interface de usuário (um que não seja XCopy).
 
	Depois de instalar o provedor, reinicie o serviço de Host do Gateway de Gerenciamento de Dados no seu computador usando o miniaplicativo Serviços (ou) o Gerenciador de Configuração do Gateway de Gerenciamento de Dados.

> [AZURE.NOTE] Consulte [Solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para ver dicas sobre como solucionar os problemas relacionados à conexão/gateway.

## Exemplo: copiar dados do Oracle para o Blob do Azure
Este exemplo mostra como copiar dados de um banco de dados Oracle local para um Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer uma das fontes declaradas [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a Atividade de Cópia no Azure Data Factory.
 
O exemplo tem as seguintes entidades de data factory:

1.	Um serviço vinculado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2.	Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties).
4.	Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5.	O [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) como fonte e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) como coletor.

O exemplo copia dados de uma tabela em um banco de dados Oracle local para um blob a cada hora. Para obter mais informações sobre várias propriedades usadas no exemplo abaixo, consulte a documentação sobre as diferentes propriedades nas seções após os exemplos.

**Serviço vinculado do Oracle:**

	{
	  "name": "OnPremisesOracleLinkedService",
	  "properties": {
	    "type": "OnPremisesOracle",
	    "typeProperties": {
	      "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
	      "gatewayName": "<gateway name>"
	    }
	  }
	}

**Serviço vinculado do armazenamento de Blob do Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

**Conjunto de dados de entrada do Oracle:**

O exemplo supõe que você criou uma tabela "MyTable" no Oracle e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

Definir “external”: “true” e especificar a política externalData informa à data factory que essa é uma tabela externa à data factory e não é produzida por uma atividade nessa data factory.

	{
	    "name": "OracleInput",
	    "properties": {
	        "type": "OracleTable",
	        "linkedServiceName": "OnPremisesOracleLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	           "external": true,
	        "availability": {
	            "offset": "01:00:00",
	            "interval": "1",
	            "anchorDateTime": "2014-02-27T12:00:00",
	            "frequency": "Hour"
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


**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.
	
	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


**Pipeline com Atividade de cópia:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **OracleSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada com a propriedade **oracleReaderQuery** seleciona os dados na última hora para copiar.

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "OracletoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": " OracleInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "OracleSource",
	            "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}


Você precisará ajustar a cadeia de caracteres de consulta com base na maneira como as datas são configuradas no seu banco de dados Oracle. Se você vir a seguinte mensagem de erro:

	Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

Pode ser necessário alterar a consulta como mostrado a seguir (usando a função to\_date):

	"oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"

## Exemplo: Copiar dados do Blob do Azure para o Oracle
Este exemplo mostra como copiar dados de um Armazenamento de Blobs do Azure para um banco de dados Oracle local. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes declaradas [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a Atividade de Cópia no Azure Data Factory.
 
O exemplo tem as seguintes entidades de data factory:

1.	Um serviço vinculado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2.	Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties).
5.	Um [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) como fonte e [OracleSink](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) como coletor.

O exemplo copia dados de um blob para uma tabela em um banco de dados Oracle local de hora em hora. Para obter mais informações sobre várias propriedades usadas no exemplo abaixo, consulte a documentação sobre as diferentes propriedades nas seções após os exemplos.

**Serviço vinculado do Oracle:**

	{
	  "name": "OnPremisesOracleLinkedService",
	  "properties": {
	    "type": "OnPremisesOracle",
	    "typeProperties": {
	      "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
	      "gatewayName": "<gateway name>"
	    }
	  }
	}

**Serviço vinculado do armazenamento de Blob do Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

**Conjunto de dados de entrada de Blob do Azure**

Os dados são coletados de um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa parte da hora de início do dia, mês e ano e nome de arquivo usa a parte de hora da hora de início. A configuração “external”: ”true” informa o serviço Data Factory que essa é uma tabela externa à data factory e não é produzida por uma atividade na data factory.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "fileName": "{Hour}.csv",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
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

**Conjunto de dados de saída Oracle:**

O exemplo pressupõe que você tenha criado uma tabela "MyTable" no Oracle. Você deve criar a tabela no Oracle com o mesmo número de colunas que espera que o arquivo CSV de Blob contenha. Novas linhas são adicionadas à tabela a cada hora.

	{
	    "name": "OracleOutput",
	    "properties": {
	        "type": "OracleTable",
	        "linkedServiceName": "OnPremisesOracleLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": "1"
	        }
	    }
	}


**Pipeline com Atividade de cópia:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **BlobSource** e o tipo **sink** está definido como **OracleSink**.

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoOracle",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "OracleOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "OracleSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}


## Propriedades do serviço vinculado do Oracle

A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do Oracle.

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
type | A propriedade do tipo deve ser definida como: **OnPremisesOracle** | Sim
connectionString | Especifique as informações necessárias para se conectar à instância do Banco de Dados Oracle para a propriedade connectionString. | Sim 
gatewayName | Nome do gateway que será usado para se conectar ao servidor Oracle local | Sim

Consulte [Definir credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) para obter detalhes sobre como definir credenciais para uma fonte de dados do Oracle local.
## Propriedades de tipo do conjunto de dados do Oracle

Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (Oracle, blob do Azure, tabela do Azure etc.).
 
A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo OracleTable tem as propriedades a seguir.

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
tableName | Nome da tabela no Banco de Dados Oracle à qual o serviço vinculado se refere. | Não (se **oracleReaderQuery** de **OracleSource** for especificado)

## Propriedades de tipo da atividade de cópia do Oracle

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, veja o artigo [Criando pipelines](data-factory-create-pipelines.md). Propriedades, como nome, descrição, tabelas de entrada e saída, várias políticas, etc. estão disponíveis para todos os tipos de atividades.

**Observação:** a Atividade de Cópia usa apenas uma entrada e produz apenas uma saída.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade e, no caso de Atividade de cópia, variam dependendo dos tipos de fontes e coletores.

### OracleSource
No caso da atividade de Cópia, quando a fonte é do tipo **OracleSource**, as seguintes propriedades estão disponíveis na seção **typeProperties**:

Propriedade | Descrição |Valores permitidos | Obrigatório
-------- | ----------- | ------------- | --------
oracleReaderQuery | Utiliza a consulta personalizada para ler os dados. | Cadeia de caracteres de consulta SQL. Por exemplo: select * from MyTable <br/><br/>Se não for especificada, a instrução SQL que é executada será: select * from MyTable | Não (se **tableName** de **dataset** for especificado)

### OracleSink
O **OracleSink** é compatível com as seguintes propriedades:

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
writeBatchTimeout | Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite. | timespan<br/><br/> Exemplo 00:30:00 (30 minutos). | Não
writeBatchSize | Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. | Inteiro (número de linhas)| Não (padrão: 10000)  
sqlWriterCleanupScript | A consulta especificada pelo usuário para a Atividade de cópia ser executada para assegurar que os dados de uma fatia específica serão limpos. | Uma instrução de consulta. | Não
sliceIdentifierColumnName | Nome de coluna especificado pelo usuário para a Atividade de cópia preencher com o identificador de fatia gerado automaticamente, que será usado para limpar os dados de uma fatia específica quando executado novamente. | Nome de uma coluna com tipo de dados de binário (32). | Não


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapeamento de tipo para Oracle

Como mencionado no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), a atividade de Cópia executa conversões automáticas de tipos de fonte para tipos de coletor, com a seguinte abordagem de duas etapas:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados do Oracle, os seguintes mapeamentos serão usados do tipo de dados do Oracle para o tipo .NET e vice-versa.

Tipo de dados do Oracle | Tipo de dados do .NET Framework
---------------- | ------------------------
BFILE | Byte
BLOB | Byte
CHAR | Cadeia de caracteres
CLOB | Cadeia de caracteres
DATE | DateTime
FLOAT | Decimal
INTEGER | Decimal
INTERVAL YEAR TO MONTH | Int32
INTERVAL DAY TO SECOND | TimeSpan
LONG | Cadeia de caracteres
LONG RAW | Byte
NCHAR | Cadeia de caracteres
NCLOB | Cadeia de caracteres
NUMBER | Decimal
NVARCHAR2 | Cadeia de caracteres
RAW | Byte
ROWID | Cadeia de caracteres
TIMESTAMP | DateTime
TIMESTAMP WITH LOCAL TIME ZONE | DateTime
TIMESTAMP WITH TIME ZONE | DateTime
UNSIGNED INTEGER | Número
VARCHAR2 | Cadeia de caracteres
XML | Cadeia de caracteres

## Dicas de solução de problemas

**Problema:** você vê a seguinte **mensagem de erro**: A atividade de cópia encontrou parâmetros inválidos: 'UnknownParameterName', Mensagem detalhada: não é possível localizar o Provedor de Dados do .Net Framework solicitado. Ele pode não estar instalado".

**Possíveis causas**

1. O provedor de dados .NET Framework para Oracle não foi instalado.
2. O provedor de dados .NET Framework para Oracle foi instalado no .NET Framework 2.0 e não foi encontrado nas pastas .NET Framework 4.0.

**Solução/solução alternativa**

1. Se você não instalou o Provedor do .NET para o Oracle, [instale-o](http://www.oracle.com/technetwork/topics/dotnet/downloads/) e repita o cenário.
2. Se você receber a mensagem de erro mesmo depois de instalar o provedor, faça o seguinte:
	1. Abra a configuração do computador do .NET 2.0 na pasta: <disco do sistema>: \\Windows\\Microsoft.NET\\Framework64\\v2.0.50727\\CONFIG\\machine.config.
	2. Procure por **Provedor de dados Oracle para .NET** que você deverá conseguir encontrar uma entrada como abaixo em **system.data** -> **DbProviderFactories**: “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
2.	Copie esta entrada no arquivo machine.config na seguinte pasta v4.0: <disco do sistema>: \\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config e altere a versão para 4.xxx.x.x.
3.	Instale “<Caminho Instalado do ODP.NET> \\11.2.0\\client\_1\\odp.net\\bin\\4\\Oracle.DataAccess.dll” no GAC (cache de assembly global), executando “gacutil /i [provider path]”.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


## Desempenho e Ajuste  
Confira o [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md) para aprender sobre os principais fatores que afetam o desempenho do movimento de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.

<!---HONumber=AcomDC_0803_2016-->