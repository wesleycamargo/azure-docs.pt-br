---
title: Mover dados a partir do HDFS local | Microsoft Docs
description: Saiba mais sobre como mover dados do HDFS local usando o Azure Data Factory
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: 0f0eaaa927ea73cec845dbb369dc2c4a7a8466ba


---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Mover dados do HDFS local usando o Azure Data Factory
Esse artigo descreve como você pode usar a atividade de cópia em um Azure Data Factory para mover dados do HDFS local para outro armazenamento de dados. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

Atualmente, a data factory dá suporte apenas para a movimentação de dados de um HDFS local para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para um HDFS local.

## <a name="enabling-connectivity"></a>Habilitando a conectividade
O serviço Data Factory dá suporte à conexão com HDFS local usando o Gateway de Gerenciamento de Dados. Consulte o artigo [movendo dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Gateway de gerenciamento de dados e obter instruções passo a passo de como configurar o gateway. Use o gateway para se conectar ao HDFS, mesmo se ele estiver hospedado em uma VM IaaS do Azure.

Embora você possa instalar o gateway no mesmo computador local ou a VM do Azure como o HDFS, recomendamos que você instale o gateway em um computador separado ou em uma VM IaaS do Azure separada. Ter o gateway em um computador separado reduz a contenção de recursos e aprimora o desempenho. Quando você instalar o gateway em um computador separado, o computador deverá ser capaz de acessar o computador com o HDFS.

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil de criar um pipeline que copia dados do HDFS local é usar o Assistente para cópia de dados. Confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Os exemplos a seguir fornecem amostras de definições de JSON que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de um HDFS local para um Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.

## <a name="sample-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Exemplo: copiar dados de um HDFS local para um Blob do Azure
Este exemplo mostra como copiar dados de um HDFS local para o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer uma das fontes declaradas [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.  

O exemplo tem as seguintes entidades de data factory:

1. Um serviço vinculado do tipo [OnPremisesHdfs](#hdfs-linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [FileShare](#hdfs-dataset-type-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [FileSystemSource](#hdfs-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de um HDFS local para o blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados. As instruções estão no artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço vinculado ao HDFS** Esse exemplo usa a autenticação do Windows. Confira a seção [Serviço vinculado ao HDFS](#hdfs-linked-service-properties) para diferentes tipos de autenticação que você pode usar.

    {
        "name": "HDFSLinkedService",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
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

**Conjunto de dados de entrada do HDFS** Esse conjunto de dados refere-se à pasta DataTransfer/UnitTest/ do HDFS. O pipeline copia todos os arquivos dessa pasta para o destino.

Configurar “external”: “true” informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

    {
        "name": "InputDataset",
        "properties": {
            "type": "FileShare",
            "linkedServiceName": "HDFSLinkedService",
            "typeProperties": {
                "folderPath": "DataTransfer/UnitTest/"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval":  1
            }
        }
    }




**Conjunto de dados de saída de Blob do Azure**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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



**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **FileSystemSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **query** seleciona os dados na última hora para copiar.

    {
        "name": "pipeline",
        "properties":
        {
            "activities":
            [
                {
                    "name": "HdfsToBlobCopy",
                    "inputs": [ {"name": "InputDataset"} ],
                    "outputs": [ {"name": "OutputDataset"} ],
                    "type": "Copy",
                    "typeProperties":
                    {
                        "source":
                        {
                            "type": "FileSystemSource"
                        },
                        "sink":
                        {
                            "type": "BlobSink"
                        }
                    },
                    "policy":
                    {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1,
                        "timeout": "00:05:00"
                    }
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="hdfs-linked-service-properties"></a>Propriedades do Serviço Vinculado do HDFS
A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do HDFS.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| type |A propriedade type deve ser definida como: **Hdfs** |Sim |
| Url |URL para o HDFS |Sim |
| encryptedCredential |[New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) da credencial de acesso. |Não |
| userName |Nome de usuário para a autenticação do Windows. |Sim (para a Autenticação do Windows) |
| Senha |Senha para a autenticação do Windows. |Sim (para a Autenticação do Windows) |
| authenticationType |Windows ou Anônima. |Sim |
| gatewayName |O nome do gateway que o serviço Data Factory deve usar para se conectar ao HDFS. |Sim |

Consulte [Mover dados entre fontes locais e a nuvem com o Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre como configurar as credenciais para um HDFS local.

### <a name="using-anonymous-authentication"></a>Usando a autenticação anônima
    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "userName": "hadoop",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }


### <a name="using-windows-authentication"></a>Usando a autenticação do Windows
    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }



## <a name="hdfs-dataset-type-properties"></a>Propriedades do tipo de conjunto de dados do HDFS
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo **FileShare** (que inclui o conjunto de dados do HDFS) tem as propriedades a seguir

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| folderPath |Caminho para a pasta. Exemplo: `myfolder`<br/><br/>Use o caractere de escape ' \ ' para caracteres especiais na cadeia de caracteres. Por exemplo: para pasta\subpasta, especifique a pasta\\\\subpasta e para d:\pastadeexemplo, especifique d:\\\\pastadeexemplo.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pastas com base na fatia de data/hora de início/término. |Sim |
| fileName |Especifique o nome do arquivo no **folderPath** se quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar algum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando o fileName não for especificado para um conjunto de dados de saída, o nome do arquivo gerado será no seguinte formato: <br/><br/>Data.<Guid>.txt (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy pode usado para especificar um filename, folderPath dinâmico para dados de série temporal. Exemplo: folderPath parametrizado para cada hora dos dados. |Não |
| fileFilter |Especifique um filtro a ser usado para selecionar um subconjunto de arquivos no folderPath em vez de todos os arquivos. <br/><br/>Os valores permitidos são: `*` (vários caracteres) e `?` (um único caractere).<br/><br/>Exemplo 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2014-1-?.txt"`<br/><br/>**Observação**: fileFilter é aplicável a um conjunto de dados FileShare de entrada |Não |
| formato |Há suporte para os seguintes tipos de formato: **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Confira as seções [Especificando TextFormat](#specifying-textformat), [Especificando AvroFormat](#specifying-avroformat), [Especificando JsonFormat](#specifying-jsonformat), [Especificando OrcFormat](#specifying-orcformat) e [Especificando ParquetFormat](#specifying-parquetformat) para obter detalhes. Se você quiser copiar arquivos no estado em que se encontram entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. |Não |
| compactação |Especifique o tipo e o nível de compactação para os dados. Os tipos compatíveis são: **GZip**, **Deflate** e **BZip2** e os níveis permitidos são: **Melhor** e **Mais rápido**. Atualmente, as configurações de compactação não têm suporte para dados em **AvroFormat** ou **OrcFormat**. Para saber mais, confira a seção [Suporte à compactação](#compression-support) . |Não |

> [!NOTE]
> filename e fileFilter não podem ser usados simultaneamente.
>
>

### <a name="using-partionedby-property"></a>Usando a propriedade partionedBy
Conforme mencionado na seção anterior, você pode especificar um filename, folderPath dinâmico para dados de série temporal com partitionedBy. Você pode fazer isso com as macros de Data Factory e as variáveis de sistema SliceStart e SliceEnd que indicam o período de tempo lógico para uma determinada fatia de dados.

Confira os artigos [Criando conjuntos de dados](data-factory-create-datasets.md), [Agendamento e execução](data-factory-scheduling-and-execution.md) e [Criando pipelines](data-factory-create-pipelines.md) para saber mais sobre conjuntos de dados de série temporal, agendamentos e fatias.

#### <a name="sample-1"></a>Exemplo 1:
    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy":
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

Nesse exemplo, {Slice} é substituído pelo valor da variável de sistema SliceStart do Data Factory no formato (AAAAMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. O folderPath é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Exemplo 2:
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],

Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades folderPath e fileName.

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="hdfs-copy-activity-type-properties"></a>Propriedades de tipo da Atividade de Cópia do HDFS
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

Para a Atividade de Cópia quando a fonte for do tipo **FileSystemSource** , as propriedades a seguir estarão disponíveis na seção typeProperties:

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. |True, False (padrão) |Não |

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.



<!--HONumber=Nov16_HO3-->


