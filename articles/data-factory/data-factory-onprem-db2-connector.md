---
title: Mover dados do DB2 usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como mover dados do banco de dados DB2 usando o Azure Data Factory
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: 6d54203797ad970d590b853b171b383708dbcb5d
ms.lasthandoff: 04/12/2017


---
# <a name="move-data-from-db2-using-azure-data-factory"></a>Mover dados do DB2 usando o Azure Data Factory
Este artigo descreve como você pode usar a Atividade de Cópia no Azure Data Factory para copiar dados de um banco de dados DB2 local em qualquer armazenamento de dados na coluna Coletores da seção [Fontes de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

Atualmente, o Data Factory permite apenas a movimentação de dados de um banco de dados DB2 para [repositórios de dados de coletores com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats), mas não mover dados de outros repositórios de dados para um banco de dados DB2.

## <a name="prerequisites"></a>Pré-requisitos
A Data Factory dá suporte à conexão com banco de dados DB2 local usando o Gateway de Gerenciamento de Dados. Veja o artigo [Gateway de gerenciamento de dados](data-factory-data-management-gateway.md) para saber mais sobre o Gateway de Gerenciamento de Dados e o artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo sobre como configurar o gateway de um pipeline de dados para mover dados.

O gateway é requerido mesmo que o DB2 esteja hospedado em uma VM IaaS do Azure. Você pode instalar o gateway na mesma VM IaaS do armazenamento de dados ou em uma VM diferente, desde que o gateway possa conectar o banco de dados.

O Gateway de Gerenciamento de Dados fornece um driver DB2 interno, portanto, não é necessário instalar manualmente nenhum driver ao copiar dados do DB2.

> [!NOTE]
> Consulte [Solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para ver dicas sobre como solucionar os problemas relacionados à conexão/gateway.

## <a name="supported-versions"></a>Versões com suporte
Este conector DB2 dá suporte às plataformas e versões do IBM DB2 a seguir com as versões 9, 10 e 11 do SQL Access Manager (SQLAM) da Distributed Relational Database Architecture (DRDA):

* IBM DB2 para z/OS 11.1
* IBM DB2 para z/OS 10.1
* IBM DB2 para i 7.2
* IBM DB2 para i 7.1
* IBM DB2 para LUW 11
* IBM DB2 para LUW 10.5
* IBM DB2 para LUW 10.1

> [!TIP]
> Se você encontrar um erro que indica “O pacote correspondente a uma solicitação de execução de instrução SQL não foi encontrado. SQLSTATE=51002 SQLCODE=-805”, um usuário com uma conta de privilégio alto (usuário avançado ou administrador) deverá executar a atividade de cópia uma vez, e os pacotes necessários serão criados automaticamente durante a cópia. Posteriormente, você poderá voltar para o usuário normal para a execução de cópias subsequentes.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com atividade de cópia que mova dados de um armazenamento de dados local DB2 usando diferentes ferramentas/APIs. 

- A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados. 
- Você também pode usar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo do Azure Resource Manager**, **API .NET** e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia. 

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. 
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída. 

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON.  Para obter um exemplo com definições de JSON para entidades do Data Factory que são usadas para copiar dados de um armazenamento de dados DB2 local, confira a seção [Exemplo de JSON: Copiar dados do DB2 para o Blob do Azure](#json-example-copy-data-from-db2-to-azure-blob) deste artigo. 

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas para um armazenamento de dados DB2:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do DB2.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| type |A propriedade type deve ser definida como: **OnPremisesDB2** |Sim |
| server |Nome do servidor DB2. |Sim |
| database |Nome do banco de dados DB2. |Sim |
| schema |Nome do esquema no banco de dados. O nome do esquema diferencia maiúsculas de minúsculas. |Não |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados DB2. Os valores possíveis são: Anonymous, Basic e Windows. |Sim |
| Nome de Usuário |Especifique o nome de usuário se você estiver usando a autenticação Basic ou Windows. |Não |
| Senha |Especifique a senha da conta de usuário que você especificou para o nome de usuário. |Não |
| gatewayName |O nome do gateway que o serviço Data Factory deve usar para se conectar ao banco de dados DB2 local. |Sim |


## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties de um conjunto de dados do tipo RelationalTable (que inclui o conjunto de dados do DB2) tem as propriedades a seguir.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na instância do Banco de Dados DB2 à qual o serviço vinculado se refere. O tableName diferencia maiúsculas de minúsculas. |Não (se **query** de **RelationalSource** for especificado) |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade.

Por outro lado, as propriedades disponíveis na seção typeProperties da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

Para atividade de Cópia, quando a fonte é do tipo **RelationalSource** (que inclui o DB2), as seguintes propriedades estão disponíveis na seção typeProperties:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `"query": "select * from "MySchema"."MyTable""`. |Não (se **tableName** de **dataset** for especificado) |

> [!NOTE]
> Os nomes de esquema e tabela diferenciam maiúsculas de minúsculas. Coloque os nomes em "" (aspas duplas) na consulta.  

**Exemplo:**

```sql
"query": "select * from "DB2ADMIN"."Customers""
```


## <a name="json-example-copy-data-from-db2-to-azure-blob"></a>Exemplo de JSON: copiar dados do DB2 para o Blob do Azure
Este exemplo fornece as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ele mostra como copiar dados do banco de dados do DB2 e do Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.

O exemplo tem as seguintes entidades de data factory:

1. Um serviço vinculado do tipo [OnPremisesDb2](data-factory-onprem-db2-connector.md#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Atividade de Cópia que usa [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um resultado de consulta no banco de dados DB2 para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Primeiro, instale e configure um gateway de gerenciamento de dados. As instruções estão no artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço vinculado do DB2:**

```json
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
```

**Serviço vinculado do armazenamento de Blob do Azure:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Conjunto de dados de entrada do DB2:**

O exemplo supõe que você criou uma tabela "MyTable" no DB2 e que ela contém uma coluna chamada "timestamp" para dados de série temporal.

Configurar "external": true informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory. Observe que o **type** é definido como **RelationalTable**.

```json
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
```

**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```json
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
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline com Atividade de cópia:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **RelationalSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **query** seleciona os dados da tabela Orders.

```json
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
                        "query": "select * from \"Orders\""
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
```


## <a name="type-mapping-for-db2"></a>Mapeamento de tipo para DB2
Conforme mencionado no artigo sobre [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a Atividade de Cópia executa conversões automáticas de tipos de fonte em tipos de coletor com a abordagem de duas etapas:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados para o DB2 os seguintes mapeamentos serão usados do tipo do DB2 para o tipo do .NET.

| Tipo do Banco de Dados DB2 | Tipo .NET Framework |
| --- | --- |
| SmallInt |Int16 |
| Número inteiro |Int32 |
| BigInt |Int64 |
| Real |Single |
| Duplo |Duplo |
| Float |Duplo |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numérico |Decimal |
| Data |Datetime |
| Hora |TimeSpan |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |Cadeia de caracteres |
| VarChar |Cadeia de caracteres |
| LongVarChar |Cadeia de caracteres |
| DB2DynArray |Cadeia de caracteres |
| Binário |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Graphic |Cadeia de caracteres |
| VarGraphic |Cadeia de caracteres |
| LongVarGraphic |Cadeia de caracteres |
| Clob |Cadeia de caracteres |
| Blob |Byte[] |
| DbClob |Cadeia de caracteres |
| SmallInt |Int16 |
| Número inteiro |Int32 |
| BigInt |Int64 |
| Real |Single |
| Duplo |Duplo |
| Float |Duplo |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numérico |Decimal |
| Data |Datetime |
| Hora |TimeSpan |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |Cadeia de caracteres |

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas de coletor
Para saber mais sobre mapeamento de colunas no conjunto de dados de origem para colunas no conjunto de dados de coletor, confira [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Mapeamento de colunas de conjunto de dados no Azure Data Factory).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, lembre-se da capacidade de repetição para evitar resultados não intencionais. No Azure Data Factory, você pode repetir a execução de uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de dados de modo que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente, seja de que maneira for, você precisa garantir que os mesmos dados sejam lidos não importa quantas vezes uma fatia seja executada. Confira [Leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.

