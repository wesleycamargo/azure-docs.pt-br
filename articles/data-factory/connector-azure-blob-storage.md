---
title: Copiar dados para ou do Armazenamento de Blobs do Azure usando o Data Factory.| Microsoft Docs
description: Saiba como copiar dados de armazenamentos de dados de origem com suporte para o Armazenamento de Blobs do Azure ou do Armazenamento de Blobs para armazenamentos de dados de coletor com suporte, usando o Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: jingwang
ms.openlocfilehash: ee3dafe55799c46231aa3ca7c19684d905a057de
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815419"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Copiar dados de ou para o Armazenamento de Blobs do Azure usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-azure-blob-connector.md)
> * [Versão atual](connector-azure-blob-storage.md)

Este artigo descreve como usar a Atividade de Cópia no Azure Data Factory para copiar dados de e para o Armazenamento de Blobs do Azure. Ele amplia o artigo [visão geral da Atividade de Cópia](copy-activity-overview.md) que apresenta uma visão geral da Atividade de Cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de qualquer armazenamento de dados fonte compatível para o armazenamento de blob. Você também pode copiar dados de um armazenamento de blob para qualquer armazenamento de dados de coletor compatível. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md).

Especificamente, este conector de armazenamento de Blob dá suporte a:

- Copiar blobs de e para contas de Armazenamento do Azure de uso geral e para armazenamento de blobs Quente/Frio. 
- Copiar blobs usando chave de conta, assinatura de acesso compartilhado de serviço, entidade de serviço ou identidades gerenciadas para autenticações de recursos do Azure.
- Copiar blobs de blocos, acréscimos ou de páginas e copiar dados somente para blobs de blocos. Não há suporte para o Armazenamento Premium do Azure como um coletor porque ele é feito por blobs de página.
- Copiar blobs no estado em que se encontram ou analisando ou gerando blobs com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas para o Armazenamento de Blobs.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

O conector de Blob do Azure suporta os seguintes tipos de autenticação, consulte a seção correspondente sobre detalhes:

- [Autenticação de chave de conta](#account-key-authentication)
- [Autenticação de assinatura de acesso compartilhado](#shared-access-signature-authentication)
- [Autenticação de entidade de serviço](#service-principal-authentication)
- [Identidades gerenciadas para autenticação de recursos do Azure](#managed-identity)

>[!NOTE]
>HDInsights, Azure Machine Learning e carga PolyBase do SQL Data Warehouse do Azure apenas dão suporte à autenticação de chave da conta de armazenamento de Blob do Azure.

### <a name="account-key-authentication"></a>Autenticação de chave de conta

Para usar a autenticação de chave de conta de armazenamento, há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade de tipo deve ser definida como **AzureBlobStorage** (sugerido) ou **AzureStorage** (consulte as observações abaixo). |SIM |
| connectionString | Especifique as informações necessárias para se conectar ao Armazenamento para a propriedade connectionString. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |SIM |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

>[!NOTE]
>Se você estiver usando o serviço vinculado do tipo "AzureStorage", ele ainda terá suporte no estado em que se encontra, enquanto você recebe a sugestão de usar esse novo tipo de serviço vinculado "AzureBlobStorage" no futuro.

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="shared-access-signature-authentication"></a>Autenticação de assinatura de acesso compartilhado

Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos da sua conta de armazenamento. Você pode usar uma assinatura de acesso compartilhado para conceder a um cliente permissões limitadas para objetos em sua conta de armazenamento por determinado tempo. Não é preciso compartilhar as chaves de acesso da conta. A assinatura de acesso compartilhado é um URI que engloba em seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a assinatura de acesso compartilhado, o cliente só precisa passar a assinatura de acesso compartilhado ao construtor ou método apropriado. Para saber mais sobre assinaturas de acesso compartilhado, veja [Assinaturas de Acesso Compartilhado: entendendo o modelo de assinatura de acesso compartilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> O Data Factory agora dá suporte para **assinaturas de acesso compartilhado de serviço** e **assinaturas de acesso compartilhado de conta**. Para obter mais informações sobre esses dois tipos e como construí-los, consulte [Tipos de assinaturas compartilhadas](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). 

> [!TIP]
> Para gerar uma assinatura de acesso compartilhado de serviço para a conta de armazenamento, você pode executar os comandos a seguir do PowerShell. Substitua os espaços reservados e conceda a permissão necessária.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Para usar a autenticação de assinatura de acesso compartilhado, há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade de tipo deve ser definida como **AzureBlobStorage** (sugerido) ou **AzureStorage** (consulte as observações abaixo). |SIM |
| sasUri | Especifique o URI de assinatura de acesso compartilhado para os recursos de Armazenamento, como blob, contêiner ou tabela. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |SIM |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

>[!NOTE]
>Se você estiver usando o serviço vinculado do tipo "AzureStorage", ele ainda terá suporte no estado em que se encontra, enquanto você recebe a sugestão de usar esse novo tipo de serviço vinculado "AzureBlobStorage" no futuro.

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Ao criar um URI de assinatura de acesso compartilhado, considere os seguintes pontos:

- Defina as permissões apropriadas de leitura/gravação em objetos com base em como o serviço vinculado (leitura, gravação, leitura/gravação) será usado no data factory.
- Defina o **Tempo de expiração** adequadamente. Certifique-se de que o acesso aos objetos de Armazenamento não expira dentro do período ativo do pipeline.
- O URI deve ser criado no contêiner/blob à direita ou no nível de tabela com base na necessidade. Um URI de assinatura de acesso compartilhado a um blob permite que o Data Factory acesse o blob específico. Um URI de assinatura de acesso compartilhado a um contêiner de armazenamento de Blob permite que o Data Factory se reitere pelos blobs naquele contêiner. Para fornecer acesso a mais/menos objetos posteriormente, ou para atualizar o URI de assinatura de acesso compartilhado, lembre-se de atualizar o serviço vinculado ao novo URI.

### <a name="service-principal-authentication"></a>Autenticação de entidade de serviço

Para autenticação de entidade de serviço de Armazenamento do Microsoft Azure em geral, consulte [Autenticar o acesso ao Armazenamento do Microsoft Azure usando o Azure Active Directory](../storage/common/storage-auth-aad.md).

Para usar a autenticação de entidade de serviço, siga estas etapas:

1. Registre uma entidade de aplicativo no Azure Active Directory (Azure AD) seguindo [Registre o aplicativo com um locatário do Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anote os seguintes valores, que são usados para definir o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. Conceda a permissão apropriada à entidade de serviço no armazenamento de Blob do Azure. Consulte [Gerenciar os direitos de acesso aos dados do Armazenamento do Microsoft Azure com o RBAC](../storage/common/storage-auth-aad-rbac.md) com mais detalhes sobre as funções.

    - **Como fonte**, no Controle de acesso (IAM), conceda pelo menos a função **Leitor de Dados do Blob de Armazenamento**.
    - **Como coletor**, no Controle de acesso (IAM), conceda pelo menos a função **Colaborador de Dados do Blob de Armazenamento**.

Estas propriedades são suportadas por um serviço vinculado de armazenamento de Blob do Azure:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade de tipo deve ser definida como **AzureBlobStorage**. |SIM |
| serviceEndpoint | Especifique o ponto de extremidade do serviço de armazenamento de Blob do Azure com o padrão de `https://<accountName>.blob.core.windows.net/`. |SIM |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | SIM |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque esse campo como **SecureString** para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | SIM |
| locatário | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. Para recuperá-lo, passe o mouse no canto superior direito do portal do Azure. | SIM |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

>[!NOTE]
>A autenticação da entidade de serviço só é suportada pelo serviço vinculado do tipo "AzureBlobStorage", mas não pelo serviço vinculado do tipo "AzureStorage" anterior.

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Identidades gerenciadas para autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa esse data factory específico. Você pode usar essa identidade de serviço diretamente para a autenticação de armazenamento do Blob da mesma maneira que no uso de sua própria entidade de serviço. Ele permite que este alocador designado acesse e copie dados de/para o seu armazenamento de Blob.

Para autenticação de MSI de Armazenamento do Microsoft Azure em geral, consulte [Autenticar o acesso ao Armazenamento do Microsoft Azure usando o Azure Active Directory](../storage/common/storage-auth-aad.md).

Para usar identidades gerenciadas para autenticação de recursos do Azure, siga estas etapas:

1. [Recuperar a identidade do serviço de data factory](data-factory-service-identity.md#retrieve-service-identity) copiando o valor de "SERVIÇO DE IDENTIDADE ID DO APLICATIVO" gerado junto com seu alocador.

2. Conceda a permissão apropriada à entidade de serviço no armazenamento de Blob do Azure. Consulte [Gerenciar os direitos de acesso aos dados do Armazenamento do Microsoft Azure com o RBAC](../storage/common/storage-auth-aad-rbac.md) com mais detalhes sobre as funções.

    - **Como fonte**, no Controle de acesso (IAM), conceda pelo menos a função **Leitor de Dados do Blob de Armazenamento**.
    - **Como coletor**, no Controle de acesso (IAM), conceda pelo menos a função **Colaborador de Dados do Blob de Armazenamento**.

Estas propriedades são suportadas por um serviço vinculado de armazenamento de Blob do Azure:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade de tipo deve ser definida como **AzureBlobStorage**. |SIM |
| serviceEndpoint | Especifique o ponto de extremidade do serviço de armazenamento de Blob do Azure com o padrão de `https://<accountName>.blob.core.windows.net/`. |SIM |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

> [!NOTE]
> As identidades gerenciadas para a autenticação de recursos do Azure t~e suporte apenas pelo serviço vinculado do tipo "AzureBlobStorage", mas não pelo serviço vinculado do tipo "AzureStorage" anterior. 

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção apresenta uma lista das propriedades com suporte pelo conjunto de dados de armazenamento de blob.

Para copiar dados de e para o armazenamento de blob, defina a propriedade type do conjunto de dados como **AzureBlob**. Há suporte para as seguintes propriedades.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do conjunto de dados deve ser definida como **AzureBlob**. |SIM |
| folderPath | Caminho para o contêiner e a pasta no armazenamento de blob. O filtro curinga não é suportado. Um exemplo é myblobcontainer/myblobfolder/. |SIM |
| fileName | **Filtro de nome ou curinga** para o blob (s) sob o "folderPath" especificado. Se você não especificar algum valor para essa propriedade, o conjunto de dados apontará para todos os blobs na pasta. <br/><br/>Para filtro, os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único).<br/>– Exemplo 1: `"fileName": "*.csv"`<br/>– Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para se seu nome de arquivo real curinga ou esse caractere de escape dentro de escape.<br/><br/>Quando fileName não é especificado para um conjunto de dados de saída e **preserveHierarchy** ão é especificado no coletor de atividade, a atividade de cópia gera automaticamente o nome do arquivo com o seguinte formato: "*Data. [Activity run id GUID]. [GUID se FlattenHierarchy]. [Formato se configurado]. [Compressão se configurado]*". Um exemplo é "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". |Não  |
| formato | Se você quiser copiar arquivos no estado em que se encontram entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quiser analisar ou gerar arquivos com um formato específico, haverá suporte para os seguintes tipos de formatos de arquivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob **format** para um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Os níveis de suporte são **Ideal** e **Mais rápido**. |Não  |

>[!TIP]
>Para copiar todos os blobs em uma pasta, especifique **folderPath** somente.<br>Para copiar um único objeto binário grande com um determinado nome, especifique **folderPath** com parte da pasta e **fileName** com nome de arquivo.<br>Para copiar um subconjunto de blobs em uma pasta, especifique **folderPath** com parte da pasta e **fileName** com filtro curinga. 

**Exemplo:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção apresenta uma lista das propriedades com suporte na origem e no coletor do armazenamento de blob.

### <a name="blob-storage-as-a-source-type"></a>Armazenamento de blob como um tipo de fonte

Para copiar dados do armazenamento de blob, defina o tipo de fonte na atividade de cópia como **BlobSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type da fonte da atividade de cópia deve ser definida como **BlobSource**. |SIM |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observe que quando recursiva é definida como true e o coletor é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor.<br/>Os valores permitidos são **true** (padrão) e **false**. | Não  |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>Armazenamento de blob como um tipo de coletor

Para copiar dados para o armazenamento de blob, defina o tipo de coletor na atividade de cópia como **BlobSink**. As propriedades a seguir têm suporte na seção **coletor**.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do coletor da atividade de cópia deve ser definida como **BlobSink**. |SIM |
| copyBehavior | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>- PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>- FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>- MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo ou do blob for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não  |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="some-recursive-and-copybehavior-examples"></a>Alguns exemplos de recursive e copyBehavior

Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursive e copyBehavior.

| recursiva | copyBehavior | Estrutura de pasta de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| verdadeiro |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a mesma estrutura da origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 |
| verdadeiro |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5 |
| verdadeiro |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 + Arquivo3 + Arquivo4 + Arquivo5 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. |
| falso |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |
| falso |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |
| falso |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. nome gerado automaticamente para o Arquivo1<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
