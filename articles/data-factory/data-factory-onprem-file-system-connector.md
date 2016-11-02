<properties
    pageTitle="Mover dados de e para um sistema de arquivos | Microsoft Azure"
    description="Aprenda como mover dados de um sistema de arquivos local e para ele usando o Azure Data Factory."
    services="data-factory"
    documentationCenter=""
    authors="linda33wj"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="jingwang"/>


# <a name="move-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Mover dados de e para o sistema de arquivos local usando o Azure Data Factory

Este artigo descreve como você pode usar a Atividade de Cópia do Azure Data Factory para mover dados de e para o sistema de arquivos local. Confira [Supported sources and sinks](data-factory-data-movement-activities.md#supported-data-stores) (Fontes e coletores com suporte) para obter uma lista de armazenamentos de dados que podem ser usados como fontes ou coletores com o sistema de arquivos local. Este artigo se baseia no artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com a Atividade de Cópia e combinações de armazenamentos de dados com suporte.

O Data Factory dá suporte à conexão de e para um sistema de arquivos local por meio do Gateway de Gerenciamento de Dados. Para saber mais sobre o Gateway de Gerenciamento de Dados e para obter instruções passo a passo sobre como configurar o gateway, confira [Mover dados entre fontes locais e a nuvem com o Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md).

> [AZURE.NOTE]
> Com exceção do Gateway de Gerenciamento de Dados, não é necessário instalar nenhum outro binário para se comunicar de e para o sistema de arquivos local.
>
> Confira [Troubleshoot gateway issues](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) (Solucionar problemas de gateway) para ver dicas sobre como solucionar os problemas relacionados à conexão/gateway.

## <a name="linux-file-share"></a>Compartilhamento de arquivos do Linux

Execute as duas etapas a seguir para usar um compartilhamento de arquivos do Linux com o serviço vinculado do servidor de arquivos:

- Instale o [Samba](https://www.samba.org/) no servidor Linux.
- Instale e configure o Gateway de Gerenciamento de Dados em um servidor Windows. Não há suporte para a instalação do Gateway de Gerenciamento de Dados em um servidor Linux.

## <a name="copy-wizard"></a>Assistente de Cópia
A maneira mais fácil de criar um pipeline que copia dados de e para o sistema de arquivos local é usar o Assistente de Cópia. Para obter uma explicação rápida, confira [Tutorial: Create a pipeline using Copy Wizard](data-factory-copy-data-wizard-tutorial.md) (Tutorial: criar um pipeline usando o Assistente de Cópia).

Os exemplos a seguir fornecem amostras de definições de JSON que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados entre um sistema de arquivos local e o Armazenamento de Blobs do Azure. No entanto, você pode copiar dados *diretamente* de qualquer uma das fontes para qualquer um dos receptores listados em [Supported sources and sinks](data-factory-data-movement-activities.md#supported-data-stores) (Fontes e coletores com suporte) usando a Atividade de Cópia no Azure Data Factory.


## <a name="sample:-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Exemplo: copiar dados de um sistema de arquivos local para um Armazenamento de Blobs do Azure

Este exemplo mostra como copiar dados de um sistema de arquivos local para um Armazenamento de Blobs do Azure.

O exemplo tem as seguintes entidades do Data Factory:

- Um serviço vinculado do tipo [OnPremisesFileServer](data-factory-onprem-file-system-connector.md#onpremisesfileserver-linked-service-properties).
- Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [FileShare](data-factory-onprem-file-system-connector.md#on-premises-file-system-dataset-type-properties).
- Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- O [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa [FileSystemSource](data-factory-onprem-file-system-connector.md#file-share-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo a seguir copia dados de uma série temporal do sistema de arquivos local para o Armazenamento de Blobs do Azure a cada hora. As propriedades JSON que são usadas nestes exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o Gateway de Gerenciamento de Dados conforme as instruções em [Mover dados entre fontes locais e a nuvem com o Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md).

**Serviço vinculado do Servidor de Arquivos Local:**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

É recomendável usar a propriedade **encryptedCredential** em vez das propriedades **userid** e **password**. Confira [File Server linked service](#onpremisesfileserver-linked-service-properties) (Serviço vinculado do Servidor de Arquivos) para obter detalhes sobre este serviço vinculado.

**Serviço vinculado de armazenamento do Azure:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Conjunto de dados de entrada do sistema de arquivos local:**

Dados são coletados de um novo arquivo a cada hora. As propriedades folderPath e fileName são determinadas com base na hora de início da fatia.  

A configuração `"external": "true"` informa ao Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade nele.

    {
      "name": "OnpremisesFileSystemInput",
      "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
          "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
          ]
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

**Conjunto de dados do Armazenamento de Blobs do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e hora da hora de início.

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Atividade de cópia:**

O pipeline contém uma atividade de cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo de **fonte** está definido como **FileSystemSource** e o tipo de **coletor** está definido como **BlobSink**.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-06-01T18:00:00",
        "end":"2015-06-01T19:00:00",
        "description":"Pipeline for copy activity",
        "activities":[  
          {
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "OnpremisesFileSystemInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "FileSystemSource"
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

## <a name="sample:-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Exemplo: copiar dados do Banco de Dados SQL do Azure pra o sistema de arquivos local

O exemplo a seguir mostra:

- Um serviço vinculado do tipo AzureSqlDatabase.
- Um serviço vinculado do tipo OnPremisesFileServer.
- Um conjunto de dados de entrada do tipo AzureSqlTable.
- Um conjunto de dados de saída do tipo FileShare.
- Um pipeline com a atividade de cópia que usa SqlSource e FileSystemSink.

O exemplo copia os dados de série temporal de uma tabela SQL do Azure para um sistema de arquivos local a cada hora. As propriedades JSON que são usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Azure:**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

**Serviço vinculado do Servidor de Arquivos Local:**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

É recomendável usar a propriedade **encryptedCredential** em vez de usar as propriedades **userid** e **password**. Confira [File System linked service](#onpremisesfileserver-linked-service-properties) (Serviço vinculado do Sistema de Arquivos) para obter detalhes sobre este serviço vinculado.

**Conjunto de dados de entrada do SQL Azure:**

O exemplo supõe que você criou uma tabela "MyTable" no SQL Azure e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

A configuração ``“external”: ”true”`` informa ao Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade nele.

    {
      "name": "AzureSqlInput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
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

**Conjunto de dados de saída do sistema de arquivos local:**

Dados são copiados para um novo arquivo a cada hora. folderPath e fileName para o blob são determinados com base na hora de início da fatia.

    {
      "name": "OnpremisesFileSystemOutput",
      "properties": {
        "type": "FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
          "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
          ]
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

**Pipeline com a Atividade de cópia:**

O pipeline contém uma atividade de cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo de **fonte** está definido como **SqlSource** e o tipo de **coletor** está definido como **FileSystemSink**. A consulta SQL que é especificada para a propriedade **SqlReaderQuery** seleciona os dados da última hora a serem copiados.


    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-06-01T18:00:00",
        "end":"2015-06-01T20:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureSQLInput"
              }
            ],
            "outputs": [
              {
                "name": "OnpremisesFileSystemOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
              },
              "sink": {
                "type": "FileSystemSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 3,
              "timeout": "01:00:00"
            }
          }
         ]
       }
    }

## <a name="on-premises-file-server-linked-service-properties"></a>Propriedades do serviço vinculado de Servidor de Arquivos local

Você pode vincular um sistema de arquivos local ao Azure Data Factory com o serviço vinculado do Servidor de Arquivos local. A tabela a seguir fornece descrições dos elementos JSON específicos para o serviço vinculado do Servidor de Arquivos Local.

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
type | Verifique se a propriedade de tipo foi definida como **OnPremisesFileServer**. | Sim
host | Especifica o caminho raiz da pasta que você deseja copiar. Use o caractere de escape ‘ \ ’ para caracteres especiais na cadeia de caracteres. Confira [Definições de conjunto de dados e serviço vinculado de exemplo](#sample-linked-service-and-dataset-definitions) para obter exemplos. | Sim
userid | Especifique a ID do usuário que tem acesso ao servidor. | Não (se você escolher encryptedcredential)
Senha | Especifique a senha para o usuário (userid). | Não (se você escolher encryptedcredential
encryptedCredential | Especifique as credenciais criptografadas que você pode obter executando o cmdlet New-AzureRmDataFactoryEncryptValue. | Não (se você optar por especificar userid e password em texto sem formatação)
gatewayName | Especifica o nome do gateway que o Data Factory deve usar para se conectar ao servidor de arquivos local. | Sim

Consulte [Definir credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) para obter detalhes sobre como definir credenciais para uma fonte de dados do Sistema de Arquivos  local.

### <a name="sample-linked-service-and-dataset-definitions"></a>Definições de conjunto de dados e serviço vinculado de exemplo
Cenário | Host em definição de serviço vinculado | folderPath em definição de conjunto de dados
-------- | --------------------------------- | --------------------- |
Pasta local no computador do Gateway de Gerenciamento de Dados  <br/><br/>Exemplos: D:\\\* ou D:\pasta\subpasta\\* | D:\\\\ (para o Gateway de Gerenciamento de Dados 2.0 e versões posteriores) <br/><br/> localhost (para versões anteriores do Gateway de Gerenciamento de Dados 2.0) | .\\\\ ou pasta\\\\subpasta (para o Gateway de Gerenciamento de Dados 2.0 e versões posteriores) <br/><br/>D:\\\\ ou D:\\\\pasta\\\\subpasta (para a versão de gateway abaixo de 2.0)
Pasta compartilhada remota:  <br/><br/>Exemplos: \\\\meuservidor\\compartilhar\\\* ou \\\\meuservidor\\compartilhar\\pasta\\subpasta\\* | \\\\\\\\meuservidor\\\\compartilhar | .\\\\ ou pasta\\\\subpasta


**Para localizar a versão do gateway:**

1. Inicie o [Gerenciador de Configurações do Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md#data-management-gateway-configuration-manager) no computador.
2. Mude para a guia **Ajuda** .

> [AZURE.NOTE] É recomendável que você [atualize seu gateway para o Gateway de Gerenciamento de Dados 2.0 ou posterior](data-factory-data-management-gateway.md#update-data-management-gateway) para aproveitar os recursos e correções mais recentes.

**Exemplo: usando username e password em texto sem formatação**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

**Exemplo: usando encryptedcredential**

    {
      "Name": " OnPremisesFileServerLinkedService ",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "D:\\",
          "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
          "gatewayName": "mygateway"
        }
      }
    }

## <a name="on-premises-file-system-dataset-type-properties"></a>Propriedades type do conjunto de dados de sistema de arquivos local

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados.

A seção typeProperties é diferente para cada tipo de conjunto de dados. Ela fornece informações, como o local e o formato dos dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo **FileShare** tem as propriedades a seguir:

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
folderPath | Especifica o subcaminho para a pasta. Use o caractere de escape ‘\’ para caracteres especiais na cadeia de caracteres. Confira [Definições de conjunto de dados e serviço vinculado de exemplo](#sample-linked-service-and-dataset-definitions) para obter exemplos.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pastas com base na fatia de data/hora de início/término. | Sim
fileName | Especifique o nome do arquivo no **folderPath** se quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar algum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando o fileName não for especificado para um conjunto de dados de saída, o nome do arquivo gerado será no seguinte formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) | Não
partitionedBy | Você pode usar partitionedBy para especificar um folderPath/fileName dinâmico para dados de série temporal. Um exemplo é folderPath parametrizado para cada hora dos dados. | Não
Formatar | Há suporte para os seguintes tipos de formato: **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **tipo** em Formato como um desses valores. Confira [Especificando TextFormat](#specifying-textformat), [Especificando AvroFormat](#specifying-avroformat), [Especificando JsonFormat](#specifying-jsonformat), [Especificando OrcFormat](#specifying-orcformat) e [Especificando ParquetFormat](#specifying-parquetformat) para obter detalhes. Se você quiser copiar arquivos no estado em que se encontram entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. | Não
fileFilter | Especifique um filtro a ser usado para selecionar um subconjunto de arquivos no folderPath em vez de todos os arquivos. <br/><br/>Os valores permitidos são: `*` (vários caracteres) e `?` (um único caractere).<br/><br/>Exemplo 1: "fileFilter": "*.log"<br/>Exemplo 2: "fileFilter": 2014-1-?.txt"<br/><br/>Observe que fileFilter é aplicável a um conjunto de dados FileShare de entrada. | Não
| compactação | Especifique o tipo e o nível de compactação para os dados. Os tipos com suporte são **GZip**, **Deflate** e **BZip2**. Os níveis de suporte são **Ideal** e **Mais rápido**. Atualmente, as configurações de compactação não têm suporte para dados em **AvroFormat** ou **OrcFormat**. Para saber mais, confira a seção [Suporte à compactação](#compression-support). | Não |


> [AZURE.NOTE] Você não pode usar fileName e fileFilter simultaneamente.

### <a name="using-partitionedby-property"></a>Usando a propriedade partitionedBy

Conforme mencionado na seção anterior, você pode especificar um folderPath e um fileName dinâmico para dados de série temporal com partitionedBy. Você pode fazer isso com as macros de Data Factory e as variáveis de sistema SliceStart e SliceEnd, que indicam o período de tempo lógico para uma determinada fatia de dados.

Confira [Criando conjuntos de dados](data-factory-create-datasets.md), [Agendamento e execução](data-factory-scheduling-and-execution.md) e [Criando pipelines](data-factory-create-pipelines.md) para saber mais detalhes sobre conjuntos de dados de série temporal, agendamentos e fatias.

#### <a name="sample-1:"></a>Exemplo 1:

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy":
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

Neste exemplo, {Slice} é substituído pelo valor da variável de sistema SliceStart do Data Factory no formato (AAAAMMDDHH). SliceStart se refere à hora de início da fatia. O folderPath é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2:"></a>Exemplo 2:

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],

Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas, que são usadas pelas propriedades folderPath e fileName.

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]   
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="file-share-copy-activity-type-properties"></a>Propriedades de tipo da atividade de cópia do compartilhamento de arquivos

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. | True, False (padrão)| Não |

**FileSystemSink** dá suporte às seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Define o comportamento de cópia quando a origem é BlobSource ou FileSystem. | **PreserveHierarchy:** preserva a hierarquia de arquivos na pasta de destino. Ou seja, o caminho relativo do arquivo de origem para a pasta de origem é o mesmo que o caminho relativo do arquivo de destino para a pasta de destino.<br/><br/>**FlattenHierarchy:** todos os arquivos da pasta de origem estarão no primeiro nível da pasta de destino. Os arquivos de destino são criados com um nome gerado automaticamente.<br/><br/>**MergeFiles**: mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/nome do blob for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não |

### <a name="recursive-and-copybehavior-examples"></a>exemplos de recursive e copyBehavior
Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores nas propriedades recursive e copyBehavior.

valor de recursive | valor de copyBehavior | Comportamento resultante
--------- | ------------ | --------
verdadeiro | preserveHierarchy | Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a mesma estrutura de origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5  
verdadeiro | flattenHierarchy | Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5
verdadeiro | mergeFiles | Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 + Arquivo3 + Arquivo4 + Arquivo5 são mesclados em um arquivo com um nome de arquivo gerado automaticamente.
false | preserveHierarchy | Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada.
false | flattenHierarchy | Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada.
false | mergeFiles | Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 são mesclados em um arquivo com um nome de arquivo gerado automaticamente.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para o Arquivo1<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada.


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Desempenho e ajuste
 Para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory e diversas maneiras para otimizá-la, confira [Copy Activity performance and tuning guide](data-factory-copy-activity-performance.md) (Guia de desempenho e ajuste da Atividade de Cópia).



<!--HONumber=Oct16_HO2-->


