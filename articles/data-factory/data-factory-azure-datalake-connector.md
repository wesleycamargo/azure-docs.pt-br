---
title: Mover dados de/para o Azure Data Lake Store | Microsoft Docs
description: "Saiba como mover dados para/do Repositório Data Lake do Azure usando o Azure Data Factory"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: b2d1a740782a20a7c6b7b8cec8335a41f16231f5
ms.openlocfilehash: 5a6a14e5fc8f6915b34f9667c4294a46c8591633


---
# <a name="move-data-to-and-from-azure-data-lake-store-using-azure-data-factory"></a>Mover dados para e do Repositório Data Lake do Azure usando o Azure Data Factory
Este artigo descreve como você pode usar a Atividade de Cópia no Azure Data Factory para mover dados para fora/dentro do Azure Data Lake Store para fora/dentro de outro repositório de dados. Este artigo se baseia no artigo sobre as [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia e combinações de repositório de dados permitidas.

> [!NOTE]
> Crie uma conta do Azure Data Lake Store antes de criar um pipeline com uma Atividade de Cópia para mover dados para dentro/fora de um Azure Data Lake Store. Para saber mais sobre o Azure Data Lake Store, confira [Introdução ao Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md).
>

## <a name="supported-authentication-types"></a>Tipos de autenticação com suporte
Suporte ao conector da autenticação de **entidade de serviço** e de **credencial do usuário** do Azure Data Lake Store. É recomendável usar o primeiro especialmente para cópia de dados agendada, para evitar comportamento de expiração do token com o último. Consulte [propriedades do serviço vinculado do Azure Data Lake Store](#azure-data-lake-store-linked-service-properties) seção com detalhes de configuração.

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil de criar um pipeline que copia dados para fora/dentro do Azure Data Lake Store é usar o Assistente de cópia de dados. Confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Os exemplos a seguir fornecem amostras de definições de JSON que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tais exemplos mostram como copiar dados de/para o Azure Data Lake Store e o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores com suporte. Para obter mais informações, consulte a seção "Formatos e armazenamentos de dados com suporte" em [Mover dados usando a Atividade de Cópia](data-factory-data-movement-activities.md).  

## <a name="sample-copy-data-from-azure-blob-to-azure-data-lake-store"></a>Exemplo: copiar dados do Blob do Azure para o Repositório Data Lake do Azure
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [AzureStorage](#azure-storage-linked-service-properties).
2. Um serviço vinculado do tipo [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](#azure-blob-dataset-type-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Atividade de cópia que usa [BlobSource](#azure-blob-copy-activity-type-properties) e [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties).

O exemplo copia dados de série temporal de um Armazenamento de Blobs do Azure para um Azure Data Lake Store de hora em hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado de armazenamento do Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Serviço vinculado do Azure Data Lake:**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Veja as etapas da seção [Propriedades do serviço vinculado do Azure Data Lake Store](#azure-data-lake-store-linked-service-properties) com detalhes de configuração.
>

**Conjunto de dados de entrada de Blob do Azure:**

Os dados são coletados de um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa parte da hora de início do dia, mês e ano e o nome de arquivo usa a parte da hora de início. A configuração "external": "true" informa o serviço Data Factory que a tabela é externa ao Data Factory e não é produzida por uma atividade no Data Factory.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
```

**Conjunto de dados de saída do Azure Data Lake:**

O exemplo copia dados para um repositório do Azure Data Lake. Os novos dados são copiados para o repositório Data Lake a cada hora.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
      "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
              "frequency": "Hour",
              "interval": 1
        }
      }
}
```


**Pipeline com a Atividade de cópia:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **BlobSource** e o tipo **sink** está definido como **AzureDataLakeStoreSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":
    {  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [  
              {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureBlobInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureDataLakeStoreOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                      },
                      "sink": {
                        "type": "AzureDataLakeStoreSink"
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
```

## <a name="sample-copy-data-from-azure-data-lake-store-to-azure-blob"></a>Exemplo: copiar dados do Repositório Data Lake do Azure para o Blob do Azure
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](#azure-storage-linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](#azure-blob-dataset-type-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com uma Atividade de cópia que usa [AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) e [BlobSink](#azure-blob-copy-activity-type-properties)

O exemplo copia dados de série temporal de um Azure Data Lake Store para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Exemplo de serviço vinculado do Repositório Data Lake do Azure:**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Veja as etapas da seção [Propriedades do serviço vinculado do Azure Data Lake Store](#azure-data-lake-store-linked-service-properties) com detalhes de configuração.
>

**Serviço vinculado de armazenamento do Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada do Azure Data Lake:**

Configurar **"external": true** informa ao serviço Data Factory que a tabela é externa ao Data Factory e não é produzida por uma atividade no Data Factory.

```JSON
{
    "name": "AzureDataLakeStoreInput",
      "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
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
```
**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```JSON
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
```

**Pipeline com Atividade de cópia:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **AzureDataLakeStoreSource** e o tipo **sink** está definido como **BlobSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureDataLakeStoreInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
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
```

## <a name="azure-data-lake-store-linked-service-properties"></a>Propriedades do Serviço Vinculado do Repositório Data Lake do Azure
A tabela a seguir fornece uma descrição para elementos JSON específicas para o serviço vinculado do Azure Data Lake Store, e você pode escolher entre **entidade de serviço** e **credencial do usuário** autenticação.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **AzureDataLakeStore** | Sim |
| dataLakeStoreUri | Especifica informações sobre a conta do Repositório Data Lake do Azure. Ele está no seguinte formato: **https://[accountname].azuredatalakestore.net/webhdfs/v1** ou **adl://[accountname].azuredatalakestore.net/**. | Sim |
| subscriptionId | Id de assinatura do Azure ao qual pertence Data Lake Store. | Obrigatório para coletor |
| resourceGroupName | Nome do grupo de recursos do Azure ao qual pertence Data Lake Store. | Obrigatório para coletor |

### <a name="using-service-principal-authentication-recommended"></a>Usando a autenticação de entidade de serviço (recomendada)
Para usar autenticação de entidade de serviço, primeiramente é necessário registrar uma entidade de aplicativo no Azure Active Directory (AAD) e conceder acesso no Data Lake Store. Posteriormente, você pode especificar abaixo propriedades no Azure Data Factory com a ID de aplicativo correspondente, informações de locatário e a chave de aplicativo para copiar dados de/para o Data Lake Store. Consulte [autenticação de serviços](../data-lake-store/data-lake-store-authenticate-using-active-directory.md) sobre como configurá-lo e recuperar as informações necessárias.

> [!IMPORTANT]
> Ao usar o assistente de cópia, certifique-se de fornecer à entidade de serviço pelo menos a permissão de Leitura para a raiz do ADLS ("/") ou a função de Leitor para a conta ADLS, a fim de navegar com êxito entre as pastas. Caso contrário, você poderá ver o erro "As credenciais fornecidas são inválidas".
>
> Se você acabou de criar/atualizar uma entidade de serviço do AAD, pode levar alguns minutos para ela realmente entrar em vigor. Verifique a entidade de serviço e a configuração de ACL ADLS primeiro, se continuar encontrando o erro "As credenciais fornecidas são inválidas", aguarde alguns instantes e tente novamente.
>

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo. | Sim |
| locatário | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. Você pode recuperá-lo passando o mouse sobre o canto superior direito do Portal do Azure. | Sim |

**Exemplo: usando a autenticação da entidade de serviço**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="using-user-credential-authentication"></a>Usando a autenticação de credenciais de usuário
Como alternativa, você pode usar a autenticação de credenciais de usuário para copiar de/para o Data Lake Store especificando abaixo propriedades.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| authorization | Clique no botão **Autorizar** no **Editor do Data Factory** e insira as suas credenciais, que atribui a URL de autorização gerada automaticamente a essa propriedade. | Sim |
| sessionId | A ID de sessão OAuth da sessão de autorização OAuth. Cada ID da sessão é exclusiva e pode ser usado somente uma vez. Essa configuração é gerada automaticamente quando você usa o Editor do Data Factory. | Sim |

**Exemplo: usando a autenticação de credenciais de usuário**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Expiração do token
O código de autorização gerado usando o botão **Autorizar** expira após algum tempo. Confira a tabela a seguir para ver os tempos de expiração para os diferentes tipos de contas de usuário. Talvez você veja a mensagem de erro a seguir quando o **token de autenticação expirar**: "Erro de operação de credencial: invalid_grant - AADSTS70002: erro ao validar as credenciais. AADSTS70008: a concessão de acesso fornecida expirou ou foi revogada. ID do rastreamento: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID da correlação: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Carimbo de data/hora: 2015-12-15 21-09-31Z".

| Tipo de usuário | Expira após |
|:--- |:--- |
| Contas de usuário NÃO gerenciadas pelo Azure Active Directory (@hotmail.com, @live.com,, etc.). |12 horas |
| Contas de usuários gerenciadas pelo AAD (Azure Active Directory) |14 dias após a última execução da fatia. <br/><br/>90 dias, se uma fatia com base em serviços vinculados do OAuth for executada pelo menos uma vez a cada 14 dias. |

Se você alterar sua senha antes do momento de expiração do token, o token expirará imediatamente e você verá o erro mencionado nesta seção.

Para evitar/resolver este erro, reautorize usando o botão **Autorizar** quando o **token expirar** e reimplante o serviço vinculado. Você também pode gerar valores para as propriedades **sessionId** e **authorization** programaticamente usando o código na seção a seguir:

#### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Para gerar valores sessionId e authorization programaticamente

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
Veja os tópicos [Classe AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) e [Classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) para obter detalhes sobre as classes do Data Factory usadas no código. Adicione uma referência à versão **2.9.10826.1824** de **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll** para a classe WindowsFormsWebAuthenticationDialog usada no código.

## <a name="azure-data-lake-dataset-type-properties"></a>Propriedades de tipo de Conjunto de Dados do Azure Data Lake
Para obter uma lista completa das seções e propriedades JSON disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização, o formato etc. dos dados no armazenamento de dados. A seção typeProperties do conjunto de dados do tipo **AzureDataLakeStore** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| folderPath |Caminho para o contêiner e a pasta no repositório do Azure Data Lake. |Sim |
| fileName |O nome do arquivo no repositório Azure Data Lake. fileName é opcional e diferencia maiúsculas de minúsculas. <br/><br/>Caso você especifique um nome de arquivo, a atividade (incluindo Cópia) funcionará no arquivo específico.<br/><br/>Quando fileName não for especificado, a Cópia incluirá todos os arquivos do folderPath para o conjunto de dados de entrada.<br/><br/>Quando fileName não for especificado para um conjunto de dados de saída, o nome do arquivo gerado estaria no seguinte formato: Data.<Guid>.txt (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| partitionedBy |partitionedBy é uma propriedade opcional. Você pode usá-lo para especificar um folderPath dinâmico e o nome de arquivo para dados de série temporal. Por exemplo, folderPath pode ser parametrizado para cada hora dos dados. Confira a seção [Usando a propriedade partitionedBy](#using-partitionedby-property) para obter detalhes e exemplos. |Não |
| formato | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](#specifying-textformat), [Formato Json](#specifying-jsonformat), [Formato Avro](#specifying-avroformat), [Formato Orc](#specifying-orcformat), e [Formato Parquet](#specifying-parquetformat). <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. |Não |
| compactação | Especifique o tipo e o nível de compactação para os dados. Os tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**; e os níveis permitidos são: **Ideal** e **Mais rápido**. Para saber mais, veja a seção [Especificação de compactação](#specifying-compression). |Não |

### <a name="using-partitionedby-property"></a>Usando a propriedade partitionedBy
Você pode especificar um folderPath dinâmico e o nome de arquivo para dados de série temporal com a seção **partitionedBy** , macros de Data Factory e variáveis do sistema: SliceStart e SliceEnd, que indicam as horas de início e término para uma fatia de dados determinada.

Confira os artigos [Criando conjuntos de dados](data-factory-create-datasets.md) e [Agendamento e execução](data-factory-scheduling-and-execution.md) para saber mais detalhes sobre os conjuntos de dados de série temporal, agendamento e fatias.

#### <a name="sample-1"></a>Exemplo 1

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Nesse exemplo, {Slice} é substituído pelo valor da variável de sistema SliceStart do Data Factory no formato (AAAAMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. O folderPath é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>Exemplo 2
```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades folderPath e fileName.

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="azure-data-lake-copy-activity-type-properties"></a>Propriedades de tipo de Atividade de Cópia do Azure Data Lake
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e política, estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de Cópia, elas variam de acordo com os tipos de fonte e coletor

**AzureDataLakeStoreSource** dá suporte à seção **typeProperties** das seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente por meio de subpastas ou somente da pasta especificada. |True (valor padrão), False |Não |

**AzureDataLakeStoreSink** dá suporte à seção **typeProperties** das seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| copyBehavior |Especifica o comportamento da cópia. |**PreserveHierarchy:** preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><br/>**FlattenHierarchy:** todos os arquivos da pasta de origem são criados no primeiro nível da pasta de destino. Os arquivos de destino são criados com o nome gerado automaticamente.<br/><br/>**MergeFiles:** mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/blob for especificado, o nome do arquivo mesclado será o nome especificado; caso contrário, será o nome de arquivo gerado automaticamente. |Não |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Desempenho e Ajuste

Dependendo se a movimentação de dados inicial é planejada com um grande volume de dados históricos ou carregamento de dados de produção incremental, o Azure Data Factory tem opções para melhorar o desempenho dessas tarefas. O parâmetro de simultaneidade faz parte da **Atividade de Cópia** e define quantas janelas de atividade diferentes serão processadas em paralelo. O parâmetro **parallelCopies** define o paralelismo para a execução de uma única atividade. É importante considerar o uso desses parâmetros durante a criação de pipelines de movimentação de dados com o Azure Data Factory para obter a melhor taxa de transferência possível.

Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.



<!--HONumber=Feb17_HO2-->


