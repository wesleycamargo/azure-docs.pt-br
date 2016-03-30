<properties 
	pageTitle="Mover dados do DB2 | Azure Data Factory" 
	description="Saiba mais sobre como mover dados do banco de dados DB2 usando o Azure Data Factory" 
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
	ms.date="02/01/2016" 
	ms.author="spelluru"/>

# Mover dados do DB2 usando o Azure Data Factory
Este artigo descreve como você pode usar a atividade de cópia em uma Azure Data Factory para mover dados do DB2 para outro armazenamento de dados. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

A data factory dá suporte à conexão com fontes DB2 locais usando o Gateway de Gerenciamento de Dados. Consulte o artigo [movendo dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Gateway de gerenciamento de dados e obter instruções passo a passo de como configurar o gateway.

**Observação:** é necessário utilizar o gateway para se conectar ao DB2, mesmo se ele estiver hospedado em VMs IaaS do Azure. Se estiver tentando se conectar a uma instância do DB2 hospedada na nuvem, você também pode instalar a instância do gateway na VM de IaaS.

Atualmente, a data factory dá suporte apenas para a movimentação de dados do DB2 para outros armazenamentos de dados, não de outros armazenamentos de dados para o DB2.

## Instalação 

Para o Gateway de Gerenciamento de Dados para se conectar ao banco de dados DB2, você precisa instalar o [Driver do servidor de dados IBM DB2](http://go.microsoft.com/fwlink/p/?LinkID=274911) no mesmo sistema que o Gateway de Gerenciamento de Dados.

Há problemas conhecidos relatados pela IBM sobre a instalação do Driver do servidor de dados IBM DB2 no Windows 8, em que são necessárias etapas adicionais de instalação conhecidos. Para obter mais informações sobre o Driver do servidor de dados IBM DB2 no Windows 8, consulte [http://www-01.ibm.com/support/docview.wss?uid=swg21618434](http://www-01.ibm.com/support/docview.wss?uid=swg21618434).

> [AZURE.NOTE] Confira [Solução de problemas de gateway](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting) para ver dicas sobre como solucionar problemas de conexão/gateway.


## Exemplo: copiar dados do DB2 para o Blob do Azure

Este exemplo mostra como copiar dados de um banco de dados DB2 local para um Armazenamento de Blob do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer uma das fontes declaradas [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia no Azure Data Factory.
 
O exemplo tem as seguintes entidades de data factory:

1.	Um serviço vinculado do tipo [OnPremisesDb2](data-factory-onprem-db2-connector.md#db2-linked-service-properties).
2.	Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3.	Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [RelationalTable](data-factory-onprem-db2-connector.md#db2-dataset-type-properties).
4.	Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
5.	Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [RelationalSource](data-factory-onprem-db2-connector.md#db2-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

O exemplo copia dados de um resultado de consulta no banco de dados DB2 para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Na primeira etapa, configure o gateway de gerenciamento de dados de acordo com as instruções no artigo [Movendo dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md).

**Serviço vinculado do DB2:**

	{
	    "name": "OnPremDb2LinkedService",
	    "properties": {
	        "type": "OnPremisesDb2",
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
	        "type": "AzureStorageLinkedService",
			"typeProperties": {
	        	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
			}
	    }
	}

**Conjunto de dados de entrada do DB2:**

O exemplo supõe que você criou uma tabela "MyTable" no DB2 e que ela contém uma coluna chamada "timestamp" para dados de série temporal.

Definir “external”: true e especificar a política externalData informa à data factory que essa é uma tabela externa à data factory e não é produzida por uma atividade nessa data factory. Observe que o **type** é definido como **RelationalTable**.

	{
	    "name": "Db2DataSet",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremDb2LinkedService",
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

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

	{
	    "name": "AzureBlobDb2DataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline com Atividade de cópia:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. No definição JSON do pipeline, o tipo de **source** está definido como **RelationalSource** e o tipo de **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **query** seleciona os dados da tabela Orders.

	{
	    "name": "CopyDb2ToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "select * from "Orders""
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "Db2DataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobDb2DataSet"
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
	                "name": "Db2ToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}


## Propriedades do serviço vinculado do DB2

A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do DB2.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- | 
| type | A propriedade type deve ser definida como: **OnPremisesDB2** | Sim |
| server | Nome do servidor DB2. | Sim |
| database | Nome do banco de dados DB2. | Sim |
| schema | Nome do esquema no banco de dados. | Não |
| authenticationType | Tipo de autenticação usado para se conectar ao banco de dados DB2. Os valores possíveis são: Anonymous, Basic e Windows. | Sim |
| Nome de Usuário | Especifique o nome de usuário se você estiver usando a autenticação Basic ou Windows. | Não |
| Senha | Especifique a senha da conta de usuário que você especificou para o nome de usuário. | Não |
| gatewayName | O nome do gateway que o serviço Data Factory deve usar para se conectar ao banco de dados DB2 local. | Sim |

Consulte [Definir credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) para obter detalhes sobre como definir credenciais para uma fonte de dados do DB2 local.


## Propriedades de tipo do conjunto de dados do DB2

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties de um conjunto de dados do tipo RelationalTable (que inclui o conjunto de dados do DB2) tem as propriedades a seguir.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- | 
| tableName | Nome da tabela na instância do Banco de Dados DB2 à qual o serviço vinculado se refere. | Não (se **query** de **RelationalSource** for especificado) |

## Propriedades de tipo da atividade de cópia do DB2

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, veja o artigo [Criando pipelines](data-factory-create-pipelines.md). Propriedades como nome, descrição, tabelas de entrada e saída, diversas políticas, etc. estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade e, no caso de Atividade de cópia, variam dependendo dos tipos de fontes e coletores.

No caso da atividade de Cópia, quando a fonte é do tipo **RelationalSource** (que inclui o DB2), as seguintes propriedades estão disponíveis na seção typeProperties:


| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------- | -------------- |
| query | Utiliza a consulta personalizada para ler os dados. | Cadeia de caracteres de consulta SQL. Por exemplo: select * from MyTable. | Não (se **tableName** de **dataset** for especificado)|

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Mapeamento de tipo para DB2
Conforme mencionado no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), A atividade de cópia executa conversões automáticas de tipos de fontes para tipos de coletor, com a abordagem em 2 etapas descritas a seguir:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados para o DB2 os seguintes mapeamentos serão usados do tipo do DB2 para o tipo do .NET.

Tipo do Banco de Dados DB2 | Tipo .NET Framework 
----------------- | ------------------- 
SmallInt | Int16
Número inteiro | Int32
BigInt | Int64
Real | Single
Duplo | Duplo
Float | Duplo
Decimal | Decimal
DecimalFloat | Decimal
Numérico | Decimal
Data | Datetime
Hora | TimeSpan
Timestamp | DateTime
Xml | Byte
Char | Cadeia de caracteres
VarChar | Cadeia de caracteres
LongVarChar | Cadeia de caracteres
DB2DynArray | Cadeia de caracteres
Binário | Byte
VarBinary | Byte
LongVarBinary | Byte
Graphic | Cadeia de caracteres
VarGraphic | Cadeia de caracteres
LongVarGraphic | Cadeia de caracteres
Clob | Cadeia de caracteres
Blob | Byte
DbClob | Cadeia de caracteres
SmallInt | Int16
Número inteiro | Int32
BigInt | Int64
Real | Single
Duplo | Duplo
Float | Duplo
Decimal | Decimal
DecimalFloat | Decimal
Numérico | Decimal
Data | Datetime
Hora | TimeSpan
Timestamp | DateTime
Xml | Byte
Char | Cadeia de caracteres


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=AcomDC_0316_2016-->