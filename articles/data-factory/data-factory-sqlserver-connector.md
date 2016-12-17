---
title: Mover dados para e do SQL Server | Microsoft Docs
description: Saiba mais sobre como mover dados de/para o banco de dados do SQL Server local ou em uma VM do Azure usando o Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 1b2514e1e6f39bb3ce9d8a46f4af01835284cdcc
ms.openlocfilehash: b473a9e5bc70b9f2b100032b45a0114f8745ab46


---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Mover dados para e do SQL Server local ou em IaaS (VM do Azure) usando o Azure Data Factory
Este artigo descreve como é possível usar a Atividade de Cópia para mover dados do SQL Server para outro repositório de dados, e vice-versa. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados e dos repositórios de dados com suporte como fontes e coletores.

## <a name="supported-versions"></a>Versões com suporte
Este conector do SQL Server dá suporte à cópia de dados de e para versões inferiores da instância hospedada no local ou no IaaS do Azure usando a autenticação do SQL e do Windows:

* SQL Server 2016
* SQL Server 2014
* SQL Server 2012
* SQL Server 2008 R2
* SQL Server 2008
* SQL Server 2005

## <a name="create-pipeline"></a>Criar um pipeline
Você pode criar um pipeline com atividade de cópia que move dados de/para um banco de dados SQL Server local por meio de ferramentas/APIs diferentes.  

* Assistente de Cópia
* Portal do Azure
* Visual Studio
* Azure PowerShell
* API do .NET
* API REST

Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia de diferentes maneiras.

## <a name="enabling-connectivity"></a>Habilitando a conectividade
Os conceitos e as etapas necessários para conectar-se com o SQL Server hospedado localmente ou em máquinas virtuais do Azure IaaS (infraestrutura como serviço) são os mesmos. Em ambos os casos, você precisa usar o Gateway de Gerenciamento de Dados para ter conectividade.

Consulte o artigo [movendo dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Gateway de gerenciamento de dados e obter instruções passo a passo de como configurar o gateway. Configurar uma instância de gateway é um pré-requisito para a conexão com o SQL Server.

Embora você possa instalar o gateway no mesmo computador local ou instância de VM na nuvem que o SQL Server, para obter melhore desempenho, é recomendável instalá-los em computadores separados. Ter o gateway e o SQL Server em computadores distintos reduz a contenção de recursos.

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil de criar um pipeline que copie dados de um banco de dados SQL Server para qualquer um dos repositórios de dados compatíveis é usar o Assistente de cópia de dados. Confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

O exemplo a seguir fornece as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos a seguir mostram como copiar dados entre o SQL Server e o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.     

## <a name="sample-copy-data-from-sql-server-to-azure-blob"></a>Exemplo: copiar dados do SQL Server para Blob do Azure
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. O [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [SqlSource](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de série temporal de uma tabela do SQL Server para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados. As instruções estão no artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço vinculado do SQL Server**

    {
      "Name": "SqlServerLinkedService",
      "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
          "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
          "gatewayName": "<gatewayname>"
        }
      }
    }

**Serviço vinculado do armazenamento de Blob do Azure**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Conjunto de dados de entrada do SQL Server**

O exemplo supõe que você criou uma tabela "MyTable" no SQL Server e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal. Você pode consultar várias tabelas no mesmo banco de dados usando um único conjunto de dados, mas uma única tabela deve ser usada para a typeProperty de tableName do conjunto de dados.

Configurar "external": "true" informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

    {
      "name": "SqlServerInput",
      "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
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

**Conjunto de dados de saída de Blob do Azure**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

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

**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **fonte** está definido como **SqlSource** e o tipo de **coletor** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora a serem copiados.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": " SqlServerInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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


Neste exemplo, **sqlReaderQuery** é especificada para SqlSource. A Atividade de Cópia executa essa consulta na fonte do Banco de Dados do SQL Server para obter os dados. Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usa parâmetros). A sqlReaderQuery pode fazer referência a várias tabelas no banco de dados referenciado pelo conjunto de dados de entrada. A propriedade não se limita apenas à tabela definida como typeProperty de tableName do conjunto de dados.

Se você não especificar sqlReaderQuery nem sqlReaderStoredProcedureName, as colunas definidas na seção de estrutura serão usadas para criar uma consulta seleção a ser executada no Banco de Dados SQL Server. Se a definição de conjunto de dados não tem a estrutura, todas as colunas serão selecionadas da tabela.

Consulte a seção [Sql Source](#sqlsource) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) para obter a lista de propriedades com suporte em SqlSource e BlobSink.

## <a name="sample-copy-data-from-azure-blob-to-sql-server"></a>Exemplo: copiar dados do Blob do Azure para o SQL Server
O exemplo a seguir mostra:

1. O serviço vinculado do tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties).
2. O serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties).
5. O [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [SqlSink](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties).

O exemplo copia dados de série temporal de um Blob do Azure para uma tabela do SQL Server a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Server**

    {
      "Name": "SqlServerLinkedService",
      "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
          "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
          "gatewayName": "<gatewayname>"
        }
      }
    }

**Serviço vinculado do armazenamento de Blob do Azure**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Conjunto de dados de entrada de Blob do Azure**

Os dados são coletados de um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa parte da hora de início do dia, mês e ano e o nome de arquivo usa a parte da hora de início. A configuração "external": "true" informa o serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

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

**Conjunto de dados de saída do SQL Server**

O exemplo copia dados para uma tabela chamada "MyTable" no SQL Server. Crie a tabela no SQL Server com o mesmo número de colunas que você espera que o arquivo CSV de Blob contenha. Novas linhas são adicionadas à tabela a cada hora.

    {
      "name": "SqlServerOutput",
      "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **fonte** está definido como **BlobSource** e o tipo de **coletor** está definido como **SqlSink**.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": " SqlServerOutput "
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource",
                "blobColumnSeparators": ","
              },
              "sink": {
                "type": "SqlSink"
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

## <a name="sql-server-linked-service-properties"></a>Propriedades do serviço vinculado do SQL Server
Nos exemplos, você usou um serviço vinculado do tipo **OnPremisesSqlServer** para vincular um banco de dados SQL Server local a uma fábrica de dados. A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do SQL Server local.

A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do SQL Server.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| type |A propriedade type deve ser definida como: **OnPremisesSqlServer**. |Sim |
| connectionString |Especifique as informações de connectionString necessárias para conexão com o banco de dados do SQL Server local usando a autenticação do SQL ou então a autenticação do Windows. |Sim |
| gatewayName |O nome do gateway que o serviço Data Factory deve usar para se conectar ao banco de dados do SQL Server local. |Sim |
| Nome de Usuário |Especifique o nome de usuário se você estiver usando a Autenticação do Windows. Exemplo: **domainname\\username**. |Não |
| Senha |Especifique a senha da conta de usuário que você especificou para o nome de usuário. |Não |

Criptografe as credenciais usando o cmdlet **New-AzureRmDataFactoryEncryptValue** e use-as na cadeia de conexão, como mostrado no seguinte exemplo (propriedade **EncryptedCredential**):  

    "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

### <a name="samples"></a>Exemplos
**JSON para usar Autenticação SQL**

    {
        "name": "MyOnPremisesSQLDB",
        "properties":
        {
            "type": "OnPremisesSqlLinkedService",
            "typeProperties": {
                "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
                "gatewayName": "<gateway name>"
            }
        }
    }

**JSON para usar Autenticação Windows**

Se o nome de usuário e a senha forem especificados, o gateway os usará para representar a conta de usuário especificada para se conectar ao banco de dados SQL Server local. Caso contrário, o gateway se conectará ao SQL Server diretamente com o contexto de segurança do Gateway (a sua conta de inicialização).

    {
         "Name": " MyOnPremisesSQLDB",
         "Properties":
         {
             "type": "OnPremisesSqlLinkedService",
             "typeProperties": {
                 "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
                 "username": "<domain\\username>",
                 "password": "<password>",
                 "gatewayName": "<gateway name>"
            }
         }
    }


Consulte [Definir credenciais e segurança](data-factory-data-management-gateway.md#encrypting-credentials) para obter detalhes sobre como definir as credenciais para uma fonte de dados do SQL Server.

## <a name="sql-server-dataset-type-properties"></a>Propriedades de tipo de conjunto de dados do SQL Server
Nos exemplos, você usou um conjunto de dados do tipo **SqlServerTable** para representar uma tabela em um banco de dados SQL Server.  

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL Server, blob do Azure, tabela do Azure, etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção **typeProperties** do conjunto de dados do tipo **SqlServerTable** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela ou exibição na instância do Banco de Dados SQL Server à qual o serviço vinculado se refere. |Sim |

## <a name="sql-server-copy-activity-type-properties"></a>Propriedades de tipo de atividade de cópia do SQL Server
Se você estiver movendo dados de um banco de dados SQL Server, defina o tipo de origem na atividade de cópia como **SqlSource**. Da mesma forma você estiver movendo dados para um banco de dados SQL Server, defina o tipo de coletor na atividade de cópia como **SqlSink**. Esta seção fornece uma lista das propriedades com suporte de SqlSource e SqlSink.

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade.

> [!NOTE]
> A Atividade de cópia usa apenas uma entrada e produz apenas uma saída.
>
>

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

### <a name="sqlsource"></a>SqlSource
Quando a fonte em uma atividade de cópia for do tipo **SqlSource**, as seguintes propriedades estarão disponíveis na seção **typeProperties**:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| SqlReaderQuery |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: select * from MyTable. Pode fazer referência a várias tabelas do banco de dados referenciado pelo conjunto de dados de entrada. Se não for especificada, a instrução SQL que é executada é: select from MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |

Se **sqlReaderQuery** for especificado para SqlSource, a Atividade de Cópia executará essa consulta na fonte do Banco do SQL Server para obter os dados.

Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usa parâmetros).

Se você não especificar sqlReaderQuery nem sqlReaderStoredProcedureName, as colunas definidas na seção de estrutura serão usadas para criar uma consulta seleção a ser executada no Banco de Dados SQL Server. Se a definição de conjunto de dados não tem a estrutura, todas as colunas serão selecionadas da tabela.

> [!NOTE]
> Quando você usa **sqlReaderStoredProcedureName**, ainda é necessário especificar um valor para a propriedade **tableName** no JSON do conjunto de dados. Contudo, não há nenhuma validação executada nessa tabela.
>
>

### <a name="sqlsink"></a>SqlSink
**SqlSink** dá suporte às seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite. |timespan<br/><br/>  Exemplo: "00:30:00" (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 10000) |
| sqlWriterCleanupScript |Especifique a consulta para a Atividade de Cópia a ser executada para que os dados de uma fatia especifica sejam removidos. Para saber mais, confira a seção de [repetição](#repeatability-during-copy) . |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome de coluna para a Atividade de Cópia a ser preenchido com o identificador de fatia gerado automaticamente, que é usado para limpar dados de uma fatia específica quando executado novamente. Para saber mais, confira a seção de [repetição](#repeatability-during-copy) . |Nome de uma coluna com tipo de dados de binário (32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que upserts (atualiza/insere) na tabela de destino. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |
| sqlWriterTableType |Especifique o nome do tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia disponibiliza aqueles dados sendo movidos em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados sendo copiados com dados existentes. |Um nome de tipo de tabela. |Não |

## <a name="troubleshooting-connection-issues"></a>Solucionar problemas de conexão
1. Configure seu SQL Server para aceitar conexões remotas. Inicie o **SQL Server Management Studio**, clique com o botão direito do mouse em **servidor** e clique em **Propriedades**. Selecione **Conexões** na lista e marque a opção **Permitir conexões remotas com o servidor**.

    ![Habilitar conexões remotas](.\\media\\data-factory-sqlserver-connector\\AllowRemoteConnections.png)

    Veja [Configurar a Opção de Configuração do Servidor de acesso remoto](https://msdn.microsoft.com/library/ms191464.aspx) para obter as etapas detalhadas.
2. Inicie o **SQL Server Configuration Manager**(Gerenciador de Configuração do SQL Server). Expanda **Configuração de Rede do SQL Server** para a instância desejada e selecione **Protocolos para MSSQLSERVER**. Você deve ver os protocolos no painel à direita. Habilite o TCP/IP clicando com o botão direito do mouse em **TCP/IP** e clicando em **Habilitar**.

    ![Habilitar TCP/IP](.\\media\\data-factory-sqlserver-connector\\EnableTCPProptocol.png)

    Veja [Habilitar ou Desabilitar um Protocolo de Rede do Servidor](https://msdn.microsoft.com/library/ms191294.aspx) para ver detalhes e formas alternativas de habilitar um protocolo TCP/IP.
3. Na mesma janela, clique duas vezes em **TCP/IP** para iniciar a janela **Propriedades de TCP/IP**.
4. Alterne para a guia **Endereços IP** . Role para baixo para ver a seção **IPAll** . Anote a **Porta TCP** (o padrão é **1433**).
5. Crie uma **regra para o Firewall do Windows** no computador para permitir a entrada de tráfego por essa porta.  
6. **Verifique a conexão**: para se conectar ao SQL Server usando nome totalmente qualificado, use o SQL Server Management Studio de um computador diferente. Por exemplo: “<machine>.<domain>.corp.<company>.com,1433”.

   > [!IMPORTANT]

   > Consulte [Mover dados entre fontes locais e a nuvem com o Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md) para obter informações detalhadas.
   >
   > Consulte [Solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para ver dicas sobre como solucionar os problemas relacionados à conexão/gateway.
   >
   >


## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade no banco de dados de destino
Esta seção fornece um exemplo que copia dados de uma tabela de origem sem uma coluna de identidade para uma tabela de destino com uma coluna de identidade.

**Tabela de origem:**

    create table dbo.SourceTbl
    (
           name varchar(100),
           age int
    )

**Tabela de destino:**

    create table dbo.TargetTbl
    (
           id int identity(1,1),
           name varchar(100),
           age int
    )


Observe que a tabela de destino tem uma coluna de identidade.

**Definição de JSON do conjunto de dados de origem**

    {
        "name": "SampleSource",
        "properties": {
            "published": false,
            "type": " SqlServerTable",
            "linkedServiceName": "TestIdentitySQL",
            "typeProperties": {
                "tableName": "SourceTbl"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }

**Definição de JSON do conjunto de dados de destino**

    {
        "name": "SampleTarget",
        "properties": {
            "structure": [
                { "name": "name" },
                { "name": "age" }
            ],
            "published": false,
            "type": "AzureSqlTable",
            "linkedServiceName": "TestIdentitySQLSource",
            "typeProperties": {
                "tableName": "TargetTbl"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": false,
            "policy": {}
        }
    }


Observe que sua tabela de origem e de destino têm um esquema diferente (a de destino tem uma coluna adicional com identidade). Nesse cenário, você precisa especificar a propriedade **structure** na definição de conjunto de dados de destino, que não inclui a coluna de identidade.

Em seguida, você pode mapear colunas de conjunto de dados de origem para colunas no conjunto de dados de destino. Confira a seção [Exemplos de mapeamento de coluna](#column-mapping-samples) para obter um exemplo.

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[!INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-sql-server-azure-sql"></a>Mapeamento de tipo para SQL Server e Azure SQL
Conforme mencionado no artigo sobre [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a Atividade de Cópia executa conversões automáticas de tipos de fonte em tipos de coletor com a abordagem de duas etapas:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados entre o SQL Azure, SQL Server e Sybase, os mapeamentos a seguir são usados do tipo SQL para o tipo .NET, e vice-versa.

O mapeamento é o mesmo que o mapeamento de tipo de dados do SQL Server para o ADO.NET.

| Tipo de mecanismo do Banco de Dados do SQL Server | Tipo .NET Framework |
| --- | --- |
| bigint |Int64 |
| binário |Byte[] |
| bit |Booliano |
| char |String, Char[] |
| data |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Decimal |Decimal |
| Atributo FILESTREAM (varbinary(max)) |Byte[] |
| Float |Duplo |
| imagem |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numérico |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objeto * |
| texto |String, Char[] |
| tempo real |timespan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.



<!--HONumber=Nov16_HO3-->


