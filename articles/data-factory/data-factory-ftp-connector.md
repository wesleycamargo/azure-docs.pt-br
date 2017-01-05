---
title: Mover dados a partir do servidor FTP | Microsoft Docs
description: Saiba como mover dados de um servidor FTP usando o Azure Data Factory.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: f27d978eab3aba52ee9a51a5f284b592e3edd6af


---
# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>Mover dados de um servidor FTP usando o Azure Data Factory
Este artigo descreve como usar a Atividade de Cópia no Azure Data Factory para mover dados de um servidor FTP para um armazenamento de dados do coletor com suporte. Este artigo se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e a lista de armazenamentos de dados com suporte, como fontes/coletores.

Atualmente, o data factory dá suporte apenas para a movimentação de dados de um servidor FTP para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para um servidor FTP. Ele dá suporte a servidores FTP locais e em nuvem.

Se você estiver movendo dados de um servidor FTP **local** para um armazenamento de dados em nuvem (exemplo: Armazenamento de Blobs do Azure), instale e use o Gateway de Gerenciamento de Dados. O Gateway de Gerenciamento de Dados é um agente cliente instalado em seu computador local que permite aos serviços de nuvem conectarem-se a recursos locais. Consulte [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) para obter detalhes sobre o gateway. Consulte o artigo [Moving data between on-premises locations and cloud](data-factory-move-data-between-onprem-and-cloud.md) (Mover dados entre pontos locais e na nuvem) para obter instruções detalhadas sobre como configurar o gateway e usá-lo. Use o gateway para conectar-se a um servidor FTP mesmo que o servidor esteja em uma VM (máquina virtual) IaaS do Azure.

Você pode instalar o gateway no mesmo computador local ou na VM IaaS do Azure que o servidor FTP. No entanto, recomendamos instalar o gateway em um computador separado ou em uma VM IaaS do Azure separada para evitar a contenção de recursos e para melhor desempenho. Ao instalar o gateway em um computador separado, o computador deverá ser capaz de acessar o servidor FTP.

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil de criar um pipeline que copie dados de um servidor FTP é usar o Assistente para cópia de dados. Confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Os exemplos a seguir fornecem amostras de definições de JSON que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

## <a name="sample-copy-data-from-ftp-server-to-azure-blob"></a>Exemplo: copiar dados do servidor FTP para o blob do Azure
Este exemplo mostra como copiar dados de um servidor FTP para um Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer uma das fontes declaradas [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.  

O exemplo tem as seguintes entidades de data factory:

* Um serviço vinculado do tipo [FtpServer](#ftp-linked-service-properties).
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
* Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [FileShare](#fileshare-dataset-type-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
* Um [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [FileSystemSource](#ftp-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de um servidor FTP para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço FTP vinculado ** Este exemplo usa autenticação básica com nome de usuário e senha em texto sem formatação. Você também pode usar uma das seguintes maneiras:

* Autenticação anônima
* Autenticação básica com credenciais criptografadas
* FTPS (FTP sobre SSL/TLS)

Confira a seção [Serviço FTP vinculado](#ftp-linked-service-properties) para ver os diferentes tipos de autenticação que você pode usar.

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
    "typeProperties": {
        "host": "myftpserver.com",           
        "authenticationType": "Basic",
        "username": "Admin",
        "password": "123456"
    }
  }
}
```
**Serviço vinculado de armazenamento do Azure**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada do FTP** Esse conjunto de dados refere-se à pasta `mysharedfolder` e ao arquivo `test.csv` FTP. O pipeline copia o arquivo para o destino.

Configurar “external”: “true” informa ao serviço Data Factory que o conjunto de dados é externo ao data factory e não é produzido por uma atividade no data factory.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Conjunto de dados de saída de Blob do Azure**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **FileSystemSource** e o tipo **sink** está definido como **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```

## <a name="ftp-linked-service-properties"></a>Propriedades do Serviço FTP Vinculado
A tabela a seguir apresenta a descrição para elementos JSON específicos do serviço FTP vinculado.

| Propriedade | Descrição | Obrigatório | Padrão |
| --- | --- | --- | --- |
| type |A propriedade de tipo deve ser definida como FtpServer |Sim |&nbsp; |
| host |Nome ou endereço IP do servidor FTP |Sim |&nbsp; |
| authenticationType |Especificar tipo de autenticação |Sim |Básica, Anônima |
| Nome de Usuário |Usuário que tem acesso ao servidor FTP |Não |&nbsp; |
| Senha |Senha do usuário (nome de usuário) |Não |&nbsp; |
| encryptedCredential |Credencial criptografada para acessar o servidor FTP |Não |&nbsp; |
| gatewayName |Nome do Gateway de Gerenciamento de Dados para se conectar a um servidor FTP local |Não |&nbsp; |
| porta |Porta na qual o servidor FTP está escutando |Não |21 |
| enableSsl |Especifique se deseja usar o canal FTP sobre SSL/TLS |Não |verdadeiro |
| enableServerCertificateValidation |Especifique se deseja habilitar a validação do certificado SSL do servidor ao usar o canal FTP sobre SSL/TLS |Não |verdadeiro |

### <a name="using-anonymous-authentication"></a>Usando a autenticação anônima

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {        
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="using-username-and-password-in-plain-text-for-basic-authentication"></a>Usar nome de usuário e senha em texto sem formatação para autenticação básica

```JSON
{
    "name": "FTPLinkedService",
      "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
      }
}
```

### <a name="using-port-enablessl-enableservercertificatevalidation"></a>Usar a porta, enableSsl, enableServerCertificateValidation
 
```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",    
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="using-encryptedcredential-for-authentication-and-gateway"></a>Usar encryptedCredential para autenticação e gateway
    
```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
      }
}
```

Consulte [Mover dados entre fontes locais e a nuvem com o Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre como configurar as credenciais para uma fonte de dados FTP local.

[!INCLUDE [data-factory-file-share-dataset](../../includes/data-factory-file-share-dataset.md)]

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="ftp-copy-activity-type-properties"></a>Propriedades do tipo Atividade de Cópia de FTP
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de Cópia, as propriedades de tipo variam conforme os tipos de fonte e coletor.

[!INCLUDE [data-factory-file-system-source](../../includes/data-factory-file-system-source.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

* [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criação de um pipeline com uma Atividade de cópia.



<!--HONumber=Nov16_HO3-->


