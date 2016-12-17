---
title: Mover dados para dentro e fora do Oracle usando o Data Factory | Microsoft Docs
description: "Aprenda a mover dados de/para o banco de dados da Oracle que está no local usando o Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: c3161d1b870936ff51bbe092f1ca8ff320cf956e


---
# <a name="move-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Mover dados para dentro e fora do Oracle local usando o Azure Data Factory
Este artigo descreve como você pode usar a atividade de cópia de data factory para mover dados do Oracle para outro repositório de dados e de outro repositório de dados para o Oracle. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

## <a name="supported-versions"></a>Versões com suporte
Com o Provedor de Dados Oracle para .NET 12.1 instalado no computador do Gateway de Gerenciamento de Dados, é possível usar esse conector do Oracle para copiar dados de e para versões inferiores da instância do Oracle. Consulte a seção [Instalação](#installation) para obter detalhes sobre como configurá-lo.

* Oracle 12c
* Oracle 11g
* Oracle 10g Versão 2

## <a name="installation"></a>Instalação
Para o serviço do Azure Data Factory conseguir conectar o banco de dados Oracle local, você deve instalar os seguintes componentes:

* Gateway de Gerenciamento de Dados na mesma máquina que hospeda o banco de dados ou em uma máquina separada para evitar competência por recursos com o banco de dados. O Gateway de Gerenciamento de Dados é um agente do cliente que conecta as fontes de dados locais aos serviços de nuvem de uma maneira segura e gerenciada. Consulte o artigo [Mover dados entre local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre o Gateway de Gerenciamento de Dados.
* Provedor de dados Oracle para .NET. Esse componente está incluído nos [Componentes de Acesso a Dados do Oracle para Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Instale a versão adequada (32/64 bits) no computador host em que o gateway está instalado. [O Oracle Data Provider .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) pode acessar o Oracle Database 10g Release 2 ou posterior.

    Se você escolher "Instalação de XCopy", siga as etapas no readme.htm. É recomendável escolher o instalador com IU (não XCopy).

    Depois de instalar o provedor, reinicie o serviço de Host do Gateway de Gerenciamento de Dados no seu computador usando o miniaplicativo Serviços (ou) o Gerenciador de Configuração do Gateway de Gerenciamento de Dados.  

> [!NOTE]
> Consulte [Solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para ver dicas sobre como solucionar os problemas relacionados à conexão/gateway.
>
>

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil de criar um pipeline que copie dados de/para um banco de dados Oracle para qualquer um dos repositórios de dados compatíveis é usar o Assistente de cópia de dados. Confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

O exemplo a seguir fornece as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de/para um banco de dados Oracle de/para o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.   

## <a name="sample-copy-data-from-oracle-to-azure-blob"></a>Exemplo: copiar dados do Oracle para o Blob do Azure
Este exemplo mostra como copiar dados de um banco de dados Oracle local para um Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer uma das fontes declaradas [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.  

O exemplo tem as seguintes entidades de data factory:

1. Um serviço vinculado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) como fonte e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) como coletor.

O exemplo copia os dados de uma tabela em um banco de dados Oracle local para um blob por hora. Para obter mais informações sobre as várias propriedades usadas no exemplo, consulte a documentação nas seções após os exemplos.

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

Configurar “external”: “true” informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

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

O pipeline contém uma Atividade de Cópia configurada para usar os conjuntos de dados de entrada e saída, e está agendada para ser executada por hora. Na definição JSON do pipeline, o tipo **source** está definido como **OracleSource** e o tipo **sink** está definido como **BlobSink**.  A consulta SQL especificada com a propriedade **oracleReaderQuery** seleciona os dados na última hora para copiar.

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


Você precisa ajustar a cadeia de caracteres de consulta com base na maneira como as datas são configuradas em seu banco de dados Oracle. Se você vir a seguinte mensagem de erro:

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

pode ser necessário alterar a consulta como mostrado no exemplo a seguir (usando a função to_date):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"

## <a name="sample-copy-data-from-azure-blob-to-oracle"></a>Exemplo: Copiar dados do Blob do Azure para o Oracle
Este exemplo mostra como copiar dados de um Armazenamento de Blobs do Azure para um banco de dados Oracle local. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes indicadas [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.  

O exemplo tem as seguintes entidades de data factory:

1. Um serviço vinculado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) como fonte e [OracleSink](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) como coletor.

O exemplo copia dados de um blob para uma tabela em um banco de dados Oracle local de hora em hora. Para obter mais informações sobre as várias propriedades usadas no exemplo, consulte a documentação nas seções após os exemplos.

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

Os dados são coletados de um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa a parte do ano, mês e dia da hora de início e o nome de arquivo usa a parte da hora de início. A configuração “external”: ”true” informa ao serviço Data Factory que essa é uma tabela externa do data factory e não é produzida por uma atividade no data factory.

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

O exemplo pressupõe que você tenha criado uma tabela "MyTable" no Oracle. Crie a tabela no Oracle com o mesmo número de colunas que você espera que o arquivo CSV de Blob contenha. Novas linhas são adicionadas à tabela a cada hora.

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

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **BlobSource** e o tipo **sink** está definido como **OracleSink**.  

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


## <a name="oracle-linked-service-properties"></a>Propriedades do serviço vinculado do Oracle
A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do Oracle.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| type |A propriedade do tipo deve ser definida como: **OnPremisesOracle** |Sim |
| connectionString |Especifique as informações necessárias para se conectar à instância do Banco de Dados Oracle para a propriedade connectionString. |Sim |
| gatewayName |Nome do gateway usado para conectar o servidor Oracle local |Sim |

Consulte [Mover dados entre fontes locais e a nuvem com o Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre como configurar as credenciais para uma fonte de dados Oracle local.

## <a name="oracle-dataset-type-properties"></a>Propriedades de tipo do conjunto de dados do Oracle
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções, como estrutura, disponibilidade e política de um JSON do conjunto de dados, são parecidas para todos os tipos de conjunto de dados (Oracle, blob do Azure, tabela do Azure etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties do conjunto de dados do tipo OracleTable tem as propriedades a seguir:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela no Banco de Dados Oracle à qual o serviço vinculado se refere. |Não (se **oracleReaderQuery** de **OracleSource** for especificado) |

## <a name="oracle-copy-activity-type-properties"></a>Propriedades de tipo da atividade de cópia do Oracle
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e política, estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A Atividade de cópia usa apenas uma entrada e produz apenas uma saída.
>
>

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

### <a name="oraclesource"></a>OracleSource
Na Atividade de Cópia, quando a fonte é do tipo **OracleSource**, as seguintes propriedades estão disponíveis na seção **typeProperties**:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| oracleReaderQuery |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: select * from MyTable <br/><br/>Se não for especificada, a instrução SQL que é executada será: select * from MyTable |Não (se **tableName** de **dataset** for especificado) |

### <a name="oraclesink"></a>OracleSink
**OracleSink** é compatível com as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite. |timespan<br/><br/>  Exemplo: "00:30:00" (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 100) |
| sqlWriterCleanupScript |Especifique uma consulta da Atividade de Cópia a executar para que os dados de uma fatia específica sejam removidos. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome de coluna para a Atividade de Cópia a ser preenchido com o identificador de fatia gerado automaticamente, que é usado para limpar dados de uma fatia específica quando executado novamente. |Nome de uma coluna com tipo de dados de binário (32). |Não |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-oracle"></a>Mapeamento de tipo para Oracle
Como mencionado no artigo sobre as [atividades da movimentação de dados](data-factory-data-movement-activities.md) , a atividade de Cópia executa conversões automáticas dos tipos de fonte nos tipos de coletor com a seguinte abordagem de duas etapas:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados do Oracle, os seguintes mapeamentos são usados do tipo de dados do Oracle para o tipo .NET e vice-versa.

| Tipo de dados do Oracle | Tipo de dados do .NET Framework |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[] |
| CHAR |Cadeia de caracteres |
| CLOB |Cadeia de caracteres |
| DATE |DateTime |
| FLOAT |Decimal |
| INTEGER |Decimal |
| INTERVAL YEAR TO MONTH |Int32 |
| INTERVAL DAY TO SECOND |timespan |
| LONG |Cadeia de caracteres |
| LONG RAW |Byte[] |
| NCHAR |Cadeia de caracteres |
| NCLOB |Cadeia de caracteres |
| NUMBER |Decimal |
| NVARCHAR2 |Cadeia de caracteres |
| RAW |Byte[] |
| ROWID |Cadeia de caracteres |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |DateTime |
| TIMESTAMP WITH TIME ZONE |DateTime |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |Cadeia de caracteres |
| XML |Cadeia de caracteres |

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas
**Problema:** você vê a seguinte **mensagem de erro**: A atividade de cópia encontrou parâmetros inválidos: 'UnknownParameterName', Mensagem detalhada: Não é possível encontrar o Provedor de Dados .NET Framework solicitado. Ele pode não estar instalado".  

**Possíveis causas:**

1. O provedor de dados .NET Framework para Oracle não foi instalado.
2. O provedor de dados .NET Framework para Oracle foi instalado no .NET Framework 2.0 e não foi encontrado nas pastas .NET Framework 4.0.

**Resolução/Solução Alternativa:**

1. Se você não instalou o Provedor do .NET para o Oracle, [instale-o](http://www.oracle.com/technetwork/topics/dotnet/downloads/) e repita o cenário.
2. Se você receber a mensagem de erro mesmo depois de instalar o provedor, faça o seguinte:
   1. Abra a configuração do computador do .NET 2.0 na pasta: <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Pesquise **Provedor de Dados Oracle para .NET** e você deverá encontrar uma entrada, como mostrado a seguir.der **system.data** -> **DbProviderFactories**:
           “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
3. Copie esta entrada no arquivo machine.config na seguinte pasta v4.0: <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config e altere a versão para 4.xxx.x.x.
4. Instale “<Caminho Instalado do ODP.NET> \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll” no GAC (cache de assembly global), executando `gacutil /i [provider path]`.

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.



<!--HONumber=Nov16_HO3-->


