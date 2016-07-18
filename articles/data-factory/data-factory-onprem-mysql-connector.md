<properties 
	pageTitle="Mover dados do MySQL | Azure Data Factory" 
	description="Saiba mais sobre como mover dados do banco de dados MySQL usando o Azure Data Factory" 
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

# Mover dados do MySQL usando o Azure Data Factory

Este artigo descreve como você pode usar a atividade de cópia em uma Azure Data Factory para mover dados do MySQL para outro armazenamento de dados. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

O serviço Data Factory dá suporte à conexão com fontes MySQL locais usando o Gateway de Gerenciamento de Dados. Consulte o artigo [movendo dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Gateway de gerenciamento de dados e obter instruções passo a passo de como configurar o gateway.

**Observação:** é necessário utilizar o gateway para se conectar ao MySQL, mesmo se ele estiver hospedado em VMs IaaS do Azure. Se estiver tentando se conectar a uma instância do MySQL hospedada na nuvem, você também pode instalar a instância do gateway na VM de IaaS.

Atualmente, a data factory dá suporte apenas para a movimentação de dados do MySQL para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para o MySQL.

## Instalação 
Para o Gateway de Gerenciamento de Dados para se conectar ao banco de dados MySQL, você precisa instalar o [Conector do MySQL/Net 6.6.5 para Microsoft Windows](http://go.microsoft.com/fwlink/?LinkId=278885) no mesmo sistema que o Gateway de Gerenciamento de Dados.

> [AZURE.NOTE] Confira [Solução de problemas de gateway](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting) para ver dicas sobre como solucionar problemas de conexão/gateway.

## Exemplo: copiar dados do MySQL para o Blob do Azure
Este exemplo mostra como copiar dados de um banco de dados MySQL local para um Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer uma das fontes declaradas [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia no Azure Data Factory.
 
O exemplo tem as seguintes entidades de data factory:

1.	Um serviço vinculado do tipo [OnPremisesMySql](data-factory-onprem-mysql-connector.md#mysql-linked-service-properties).
2.	Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [RelationalTable](data-factory-onprem-mysql-connector.md#mysql-dataset-type-properties).
4.	Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [RelationalSource](data-factory-onprem-mysql-connector.md#mysql-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de um resultado de consulta no banco de dados MySQL para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Na primeira etapa, configure o gateway de gerenciamento de dados de acordo com as instruções no artigo [Movendo dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md).

**Serviço vinculado do MySQL**

	{
	  "name": "OnPremMySqlLinkedService",
	  "properties": {
	    "type": "OnPremisesMySql",
	    "typeProperties": {
	      "server": "<server name>",
	      "database": "<database name>",
	      "schema": "<schema name>",
	      "authenticationType": "<authentication type>",
	      "userName": "<user name>",
	      "password": "<password>",
	      "gatewayName": "<gateway>"
	    }
	  }
	}

**Serviço vinculado de armazenamento do Azure**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Conjunto de dados de entrada do MySQL**

O exemplo supõe que você criou uma tabela "MyTable" no MySQL e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

Definir “external”: “true” e especificar a política externalData informa o serviço Data Factory que essa é uma tabela externa à data factory e não é produzida por uma atividade dessa data factory.
	
	{
	    "name": "MySqlDataSet",
	    "properties": {
	        "published": false,
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremMySqlLinkedService",
	        "typeProperties": {},
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"external": true,
	        "policy": {
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}



**Conjunto de dados de saída de Blob do Azure**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

	{
	    "name": "AzureBlobMySqlDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
	            },
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
	                        "format": "%M"
	                    }
	                },
	                {
	                    "name": "Day",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%d"
	                    }
	                },
	                {
	                    "name": "Hour",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%H"
	                    }
	                }
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}



**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **RelationalSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **query** seleciona os dados na última hora para copiar.
	
	{
	    "name": "CopyMySqlToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "MySqlDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobMySqlDataSet"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "MySqlToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## Propriedades do serviço vinculado do MySQL

A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do MySQL.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- | 
| type | A propriedade do tipo deve ser definida como: **OnPremisesMySql** | Sim |
| server | Nome do servidor MySQL. | Sim |
| database | Nome do banco de dados MySQL. | Sim | 
| schema | Nome do esquema no banco de dados. | Não | 
| authenticationType | Tipo de autenticação usado para se conectar ao banco de dados MySQL. Os valores possíveis são: Anonymous, Basic e Windows. | Sim | 
| Nome de Usuário | Especifique o nome de usuário se você estiver usando a autenticação Basic ou Windows. | Não | 
| Senha | Especifique a senha da conta de usuário que você especificou para o nome de usuário. | Não | 
| gatewayName | O nome do gateway que o serviço Data Factory deve usar para se conectar ao banco de dados MySQL local. | Sim |

Consulte [Definir credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) para obter detalhes sobre como definir credenciais para uma fonte de dados do MySQL no local.

## Propriedades de tipo do conjunto de dados do MySQL

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties de um conjunto de dados do tipo **RelationalTable** (que inclui o conjunto de dados do MySQL) tem as propriedades a seguir

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| tableName | Nome da tabela na instância do Banco de Dados MySQL à qual o serviço vinculado se refere. | Não (se **query** de **RelationalSource** for especificado) | 

## Propriedades de tipo da atividade de cópia do MySQL

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte o artigo [Criando pipelines](data-factory-create-pipelines.md). Propriedades como nome, descrição, tabelas de entrada e saída, diversas políticas, etc. estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade e, no caso de Atividade de cópia, variam dependendo dos tipos de fontes e coletores.

No caso da atividade de cópia, quando a fonte é do tipo **RelationalSource** (que inclui o MySQL), as seguintes propriedades estão disponíveis na seção typeProperties:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| query | Utiliza a consulta personalizada para ler os dados. | Cadeia de caracteres de consulta SQL. Por exemplo: select * from MyTable. | Não (se **tableName** de **dataset** for especificado) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapeamento de tipo para MySQL

Conforme mencionado no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), a Atividade de cópia executa conversões automáticas de tipo de fonte para tipos de coletor, com a abordagem em duas etapas descritas a seguir:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados para o MySQL os seguintes mapeamentos serão usados dos tipos do MySQL para os tipos do .NET.

| Tipo do Banco de Dados MySQL | Tipo .NET Framework |
| ------------------- | ------------------- | 
| bigint unsigned | Decimal |
| bigint | Int64 |
| bit | Decimal |
| blob | Byte |
| bool | Booliano | 
| char | Cadeia de caracteres |
| data | Datetime |
| datetime | Datetime |
| decimal | Decimal |
| double precision | Duplo |
| double | Duplo |
| enum | Cadeia de caracteres |
| flutuante | Single |
| int unsigned | Int64 |
| int | Int32 |
| integer unsigned | Int64 |
| inteiro | Int32 | 
| long varbinary | Byte |
| long varchar | Cadeia de caracteres |
| longblob | Byte |
| longtext | Cadeia de caracteres | 
| mediumblob | 	Byte | 
| mediumint unsigned | Int64 |
| mediumint | Int32 | 
| mediumtext | Cadeia de caracteres |
| numérico | Decimal |
| real | Duplo |
| set | Cadeia de caracteres |
| smallint unsigned | Int32 |
| smallint | Int16 |
| texto | Cadeia de caracteres |
| tempo real | TimeSpan |
| timestamp | Datetime |
| tinyblob | Byte |
| tinyint unsigned | Int16 | 
| tinyint | Int16 |
| tinytext | Cadeia de caracteres | 
| varchar | Cadeia de caracteres | 
| year | Int | 


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## Desempenho e Ajuste  
Confira o [Guia de Desempenho e Ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para aprender sobre os principais fatores que afetam o desempenho e o movimento de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.

<!---HONumber=AcomDC_0706_2016-->