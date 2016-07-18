<properties 
	pageTitle="Mover dados do PostgreSQL | Azure Data Factory" 
	description="Saiba mais sobre como mover dados do banco de dados PostgreSQL usando o Azure Data Factory" 
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

# Mover dados do PostgreSQL usando o Azure Data Factory

Este artigo descreve como você pode usar a atividade de cópia em uma Azure Data Factory para mover dados do PostgreSQL para outro armazenamento de dados. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

O serviço Data Factory dá suporte à conexão com fontes PostgreSQL locais usando o Gateway de Gerenciamento de Dados. Consulte o artigo [movendo dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Gateway de gerenciamento de dados e obter instruções passo a passo de como configurar o gateway.

**Observação:** é necessário utilizar o gateway para se conectar ao PostgreSQL, mesmo se ele estiver hospedado em VMs de IaaS do Azure. Se estiver tentando se conectar a uma instância do PostgreSQL hospedada na nuvem, você também pode instalar a instância do gateway na VM de IaaS.

A data factory dá suporte apenas para a movimentação de dados de PostgreSQL para outros armazenamentos de dados, não de outros armazenamentos de dados para PostgreSQL.

## Instalação 

Para o Gateway de Gerenciamento de Dados para se conectar ao banco de dados PostgreSQL, você precisa instalar o [provedor de dados Ngpsql para PostgreSQL](http://go.microsoft.com/fwlink/?linkid=282716) no mesmo sistema que o Gateway de Gerenciamento de Dados.

> [AZURE.NOTE] Confira [Solução de problemas de gateway](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting) para ver dicas sobre como solucionar problemas de conexão/gateway.

## Exemplo: copiar dados do PostgreSQL para o Blob do Azure

Este exemplo mostra como copiar dados de um banco de dados PostgreSQL para um Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer uma das fontes declaradas [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia no Azure Data Factory.
 
O exemplo tem as seguintes entidades de data factory:

1.	Um serviço vinculado do tipo [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#postgresql-linked-service-properties).
2.	Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [RelationalTable](data-factory-onprem-postgresql-connector.md#postgresql-dataset-type-properties).
4.	Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	O [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [RelationalSource](data-factory-onprem-postgresql-connector.md#postgresql-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de um resultado de consulta no banco de dados PostgreSQL para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados de acordo com as instruções no artigo [movendo dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md).

**Serviço vinculado do PostgreSQL:**

	{
	    "name": "OnPremPostgreSqlLinkedService",
	    "properties": {
	        "type": "OnPremisesPostgreSql",
	        "typeProperties": {
	            "server": "<server>",
	            "database": "<database>",
	            "schema": "<schema>",
	            "authenticationType": "<authentication type>",
	            "username": "<username>",
	            "password": "<password>",
	            "gatewayName": "<gatewayName>"
	        }
	    }
	}

**Serviço vinculado do armazenamento de Blob do Azure:**
		
	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
	    }
	  }
	}

**Conjunto de dados de entrada do PostgreSQL:**

O exemplo supõe que você criou uma tabela "MyTable" no PostgreSQL e que ela contém uma coluna chamada "timestamp" para dados de série temporal.

Definir “external”: true e especificar a política externalData informa à data factory que essa é uma tabela externa à data factory e não é produzida por uma atividade nessa data factory.

	{
	    "name": "PostgreSqlDataSet",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremPostgreSqlLinkedService",
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


**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

	{
	    "name": "AzureBlobPostgreSqlDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


**Atividade de cópia:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **RelationalSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **query** seleciona os dados da tabela public.usstates no banco de dados PostgreSQL.
	
	{
	    "name": "CopyPostgreSqlToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "select * from "public"."usstates""
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "PostgreSqlDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobPostgreSqlDataSet"
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
	                "name": "PostgreSqlToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}


## Propriedades do serviço vinculado do PostgreSQL

A tabela a seguir fornece a descrição para elementos JSON específicas para o serviço de vinculado do PostgreSQL.

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
type | A propriedade tipo deve ser definida como: **OnPremisesPostgreSql** | Sim
server | Nome do servidor PostgreSQL. | Sim 
database | Nome do banco de dados PostgreSQL. | Sim 
schema | Nome do esquema no banco de dados. O nome do esquema diferencia maiúsculas de minúsculas. | Não 
authenticationType | Tipo de autenticação usado para se conectar ao banco de dados PostgreSQL. Os valores possíveis são: Anonymous, Basic e Windows. | Sim 
Nome de Usuário | Especifique o nome de usuário se você estiver usando a autenticação Basic ou Windows. | Não 
Senha | Especifique a senha da conta de usuário que você especificou para o nome de usuário. | Não 
gatewayName | O nome do gateway que o serviço Data Factory deve usar para se conectar ao banco de dados PostgreSQL local. | Sim 

Consulte [Definir credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) para obter detalhes sobre como definir credenciais para uma fonte de dados do PostgreSQL local.

## Propriedades de tipo do conjunto de dados do PostgreSQL

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties de um conjunto de dados do tipo **RelationalTable** (que inclui o conjunto de dados do PostgreSQL) tem as seguintes propriedades.

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
tableName | Nome da tabela na instância do banco de dados PostgreSQL à qual o serviço vinculado se refere. O tableName diferencia maiúsculas de minúsculas. | Não (se **query** de **RelationalSource** for especificado) 

## Propriedades de tipo da atividade de cópia do PostgreSQL

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte o artigo [Criando pipelines](data-factory-create-pipelines.md). Propriedades como nome, descrição, tabelas de entrada e saída, diversas políticas, etc. estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade e, no caso de Atividade de cópia, variam dependendo dos tipos de fontes e coletores.

No caso da Atividade de Cópia, quando a fonte é do tipo **RelationalSource** (que inclui o PostgreSQL), as seguintes propriedades estão disponíveis na seção typeProperties:

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
query | Utiliza a consulta personalizada para ler os dados. | Cadeia de caracteres de consulta SQL. Por exemplo: "query": "select * from "MySchema"."MyTable"". | Não (se **tableName** de **dataset** for especificado)

> [AZURE.NOTE] Nomes de esquema e de tabelas diferenciam maiúsculas de minúsculas e precisam estar entre "" (aspas duplas) na consulta.

**Exemplo:**

 "query": "select * from "MySchema"."MyTable""

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Mapeamento de tipo para PostgreSQL

Como mencionado no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), a atividade de Cópia executa conversões automáticas de tipos de fonte para tipos de coletor, com a seguinte abordagem de duas etapas:

1. Converter de tipos de fonte nativos para o tipo .NET
1. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados para PostgreSQL os seguintes mapeamentos serão usados do tipo do PostgreSQL para o tipo do .NET.

Tipo de banco de dados PostgreSQL |	Aliases de PostgresSQL | Tipo .NET Framework
------------------------ | -------------------- | ---------------------
abstime | | Datetime
bigint | int8 | Int64
bigserial | serial8 | Int64
bit [ (n) ] | | Byte, String
bit varying [ (n) ] | varbit | Byte, String
Booliano | bool | Booliano
box | | Byte, String
bytea | | Byte, String
character [ (n) ] | char [ (n) ] | Cadeia de caracteres
character varying [ (n) ] | varchar [ (n) ] | Cadeia de caracteres
cid | | Cadeia de caracteres
cidr | | Cadeia de caracteres
circle | | Byte, String
data | | Datetime
daterange | | Cadeia de caracteres
double precision| float8 | Duplo
inet | | Byte, String
intarry | | Cadeia de caracteres
int4range | | Cadeia de caracteres
int8range | | Cadeia de caracteres
inteiro | int, int4 | Int32
interval [ fields ] [ (p) ] | | Timespan
json | | Cadeia de caracteres
jsonb | | Byte
line | | Byte, String
lseg | | Byte, String
macaddr | | Byte, String
money | | Decimal
numeric [ (p, s) ] | decimal [ (p, s) ] | Decimal
numrange | | Cadeia de caracteres
oid | | Int32
path | | Byte, String
pg\_lsn | | Int64
point | | Byte, String
polygon | | Byte, String
real | float4 | Single
smallint | int2 | Int16
smallserial | serial2 | Int16
serial | serial4 | Int32
texto | | Cadeia de caracteres


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## Desempenho e Ajuste  
Confira o [Guia de Desempenho e Ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para aprender sobre os principais fatores que afetam o desempenho e o movimento de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.

<!---HONumber=AcomDC_0706_2016-->