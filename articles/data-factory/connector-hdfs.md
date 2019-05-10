---
title: Copiar dados do HDFS usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados de uma fonte HDFS local ou de nuvem para armazenamentos de dados de coletor com suporte, usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: c528f37c8970380678a318ec2d63babd37f89501
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228044"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Copiar dados do HDFS usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-hdfs-connector.md)
> * [Versão atual](connector-hdfs.md)

Este artigo descreve como copiar dados do servidor HDFS. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector HDFS é compatível com as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [suporte para a matriz de origem/coletor](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Especificamente, este conector HDFS dá suporte a:

- Cópia de arquivos usando autenticação **Windows** (Kerberos) ou **Anônima**.
- Cópia de arquivos usando o protocolo **webhdfs** ou suporte à **DistCp interna**.
- Cópia de arquivos no estado em que se encontram ou análise/geração de arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados de um HDFS que não esteja acessível publicamente, você precisará configurar um Integration Runtime auto-hospedado. Consulte o artigo [Integration Runtime auto-hospedado](concepts-integration-runtime.md) para saber mais detalhes.

> [!NOTE]
> Verifique se o Integration Runtime pode acessar **TODOS** os [servidor de nó de nome]: [porta do nó de nome] e [servidores de nó de dados]:[porta do nó de dados] do cluster Hadoop. A [porta do nó de nome] padrão é 50070, e a [porta do nó de dados] padrão é 50075.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao HDFS.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do HDFS:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Hdfs**. | Sim |
| url |URL para o HDFS |Sim |
| authenticationType | Valores permitidos são: **Anônimo** ou **Windows**. <br><br> Para usar **autenticação Kerberos** com o conector HDFS, veja [esta seção](#use-kerberos-authentication-for-hdfs-connector) para configurar seu ambiente local adequadamente. |Sim |
| userName |Nome de usuário para a autenticação do Windows. Para a autenticação Kerberos, especifique `<username>@<domain>.com`. |Sim (para a Autenticação do Windows) |
| Senha |Senha para a autenticação do Windows. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim (para a Autenticação do Windows) |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime auto-hospedado ou o Integration Runtime do Azure (se seu armazenamento de dados estiver publicamente acessível). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

**Exemplo: usando a autenticação anônima**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: usando a autenticação do Windows**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). 

- Para **Parquet e formato de texto delimitado**, consulte [conjunto de dados de formato de Parquet e texto delimitado](#parquet-and-delimited-text-format-dataset) seção.
- Para outros formatos, como **formato ORC/Avro/JSON/binário**, consulte [outro conjunto de dados do formato](#other-format-dataset) seção.

### <a name="parquet-and-delimited-text-format-dataset"></a>Conjunto de dados de formato de parquet e texto delimitado

Para copiar dados do HDFS na **Parquet ou o formato de texto delimitado**, consulte [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigo no conjunto de dados de formato e com suporte Configurações. As propriedades a seguir têm suporte para o HDFS em `location` configurações no conjunto de dados com base no formato:

| Propriedade   | DESCRIÇÃO                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade type sob `location` no conjunto de dados deve ser definida como **HdfsLocation**. | Sim      |
| folderPath | O caminho para a pasta. Se você quiser usar o caractere curinga para a pasta de filtro, ignore essa configuração e especificar nas configurações de fonte da atividade. | Não        |
| fileName   | Nome do arquivo em determinado folderPath. Se você quiser usar o caractere curinga para filtrar arquivos, ignore essa configuração e especificar nas configurações de fonte da atividade. | Não       |

> [!NOTE]
> **Compartilhamento de arquivos** ainda há suporte para o tipo de conjunto de dados com formato de texto/Parquet mencionado na próxima seção como-destina-se a atividade de cópia/pesquisa para compatibilidade com versões anteriores. São sugeridas para usar esse novo modelo no futuro, e o ADF criação da interface do usuário foi alternada para gerar esses novos tipos.

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Outro conjunto de dados de formato

Para copiar dados do HDFS na **formato ORC/Avro/JSON/binário**, as propriedades a seguir têm suporte:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **FileShare** |Sim |
| folderPath | Caminho para a pasta. O filtro curinga é permitido; os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome de arquivo real tiver curinga ou esse caractere interno de escape. <br/><br/>Exemplos: rootfolder/subfolder/; veja mais exemplos em [Exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). |Sim |
| fileName |  **Filtro de nome ou curinga** para os arquivos em "folderPath" especificado. Se você não especificar um valor para essa propriedade, o conjunto de dados apontará para todos os arquivos na pasta. <br/><br/>Para filtro, os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único).<br/>– Exemplo 1: `"fileName": "*.csv"`<br/>– Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` como escape se o nome real da pasta tiver curingas ou esse caractere de escape. |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo: Última Modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você deseja que o filtro de arquivo de enormes quantidades de arquivos. <br/><br/> As propriedades podem ser nulo que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.| Não  |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo: Última Modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você deseja que o filtro de arquivo de enormes quantidades de arquivos. <br/><br/> As propriedades podem ser nulo que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.| Não  |
| format | Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quer analisar arquivos com um formato específico, os seguintes tipos de formato de arquivo têm suporte: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [Formato Json](supported-file-formats-and-compression-codecs.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compression | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Níveis compatíveis são: **Ideal** e **Mais Rápido**. |Não  |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **folderPath** somente.<br>Para copiar um único arquivo com um determinado nome, especifique **folderPath** com parte da pasta e **fileName** com nome de arquivo.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **folderPath** com parte da pasta e **fileName** com filtro curinga.

**Exemplo:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do HDFS.

### <a name="hdfs-as-source"></a>HDFS como fonte

- Para copiar de **Parquet e formato de texto delimitado**, consulte [Parquet e a origem do formato de texto delimitado](#parquet-and-delimited-text-format-source) seção.
- Para cópia de outros formatos, como **formato ORC/Avro/JSON/binário**, consulte [outra fonte de formato](#other-format-source) seção.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet e a origem do formato de texto delimitado

Para copiar dados do HDFS na **Parquet ou o formato de texto delimitado**, consulte [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigo sobre a fonte da atividade de cópia baseada em formato e configurações com suporte. As propriedades a seguir têm suporte para o HDFS em `storeSettings` as configurações na fonte de cópia com base no formato:

| Propriedade                 | DESCRIÇÃO                                                  | Obrigatório                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tipo                     | A propriedade type sob `storeSettings` deve ser definida como **HdfsReadSetting**. | Sim                                           |
| recursive                | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observe que quando recursiva é definida como true e o coletor é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor. Os valores permitidos são **true** (padrão) e **false**. | Não                                             |
| wildcardFolderPath       | O caminho da pasta com caracteres curinga para filtrar as pastas de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome de pasta atual tiver curinga ou esse caractere interno de escape. <br>Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Não                                             |
| wildcardFileName         | O nome de arquivo com caracteres curinga em determinado folderPath/wildcardFolderPath para filtrar arquivos de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome de pasta atual tiver curinga ou esse caractere interno de escape.  Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Sim se `fileName` não for especificado no conjunto de dados |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo: Última Modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora. | Não                                             |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                             |
| maxConcurrentConnections | O número das conexões para se conectar ao repositório de armazenamento simultaneamente. Especifique somente quando você quiser limitar a conexão simultâneo ao armazenamento de dados. | Não                                            |

> [!NOTE]
> Para o formato de texto delimitado por Parquet /, **FileSystemSource** ainda tem suporte como origem de atividade de cópia de tipo mencionada na próxima seção-é para compatibilidade com versões anteriores. São sugeridas para usar esse novo modelo no futuro, e o ADF criação da interface do usuário foi alternada para gerar esses novos tipos.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSetting",
                    "recursive": true
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Outra fonte de formato

Para copiar dados do HDFS na **formato ORC/Avro/JSON/binário**, as propriedades a seguir têm suporte na atividade de cópia **origem** seção:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **HdfsSource** |Sim |
| recursive | Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. Observe que quando o recursivo estiver definido como verdadeiro e o coletor for um armazenamento baseado em arquivo, subpasta/pasta vazia não será copiada/criada no coletor.<br/>Os valores permitidos são: **true** (padrão), **false** | Não |
| distcpSettings | Grupo de propriedades ao usar DistCp de HDFS. | Não |
| resourceManagerEndpoint | O ponto de extremidade do Gerenciador de recursos Yarn | Sim se usando DistCp |
| tempScriptPath | Um caminho de pasta usado para armazenar o script de comando temporário DistCp. O arquivo de script é gerado pelo Data Factory e será removido após a conclusão do trabalho de cópia. | Sim se usando DistCp |
| distcpOptions | Opções adicionais fornecidas ao comando DistCp. | Não |
| maxConcurrentConnections | O número das conexões para se conectar ao repositório de armazenamento simultaneamente. Especifique somente quando você quiser limitar a conexão simultâneo ao armazenamento de dados. | Não |

**Exemplo: Fonte HDFS na atividade de cópia usando DistCp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

Saiba mais sobre como usar o DistCp para copiar dados do HDFS com eficiência na próxima seção.

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e do nome de arquivo com filtros curinga.

| folderPath | fileName             | recursive | Estrutura da pasta de origem e resultado do filtro (os arquivos em **negrito** são recuperados) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (vazio, usar padrão) | falso     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*`  | (vazio, usar padrão) | verdadeiro      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*`  | `*.csv`              | falso     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*`  | `*.csv`              | verdadeiro      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Usar DistCp para copiar dados do HDFS

O [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) é uma ferramenta de linha de comando nativa do Hadoop para fazer cópia distribuída em um cluster Hadoop. Durante a execução de um comando Distcp, primeiro ele listará todos os arquivos a serem copiados, criará vários trabalhos de mapa no cluster Hadoop e cada trabalho de mapa fará a cópia binária da fonte para o coletor.

A atividade de cópia dá suporte ao uso do DistCp para copiar arquivos no estado em que se encontram para o Blob do Azure (incluindo [cópia em etapas](copy-activity-performance.md)) ou para o Azure Data Lake Store, caso em que ele poderá aproveitar totalmente o potencial do seu cluster, em vez de ser executado no Integration Runtime auto-hospedado. Ele proporcionará melhor taxa de transferência de cópia, especialmente se o cluster for muito avançado. Com base em sua configuração no Azure Data Factory, a atividade de cópia criará automaticamente um comando distcp, enviará ao cluster Hadoop e monitorará o status da cópia.

### <a name="prerequisites"></a>Pré-requisitos

Para usar o DistCp para copiar arquivos no estado em que se encontram do HDFS para o Blob do Azure (incluindo cópia em etapas) ou para o Azure Data Lake Store, verifique se seu cluster Hadoop atende aos requisitos abaixo:

1. Os serviços MapReduce e YARN estão habilitados.
2. A versão do YARN é 2.5 ou superior.
3. O servidor do HDFS está integrado com o armazenamento de dados de destino – Blob do Azure ou Azure Data Lake Store:

    - O FileSystem do Blob do Azure tem suporte nativo desde o Hadoop 2.7. Só é necessário especificar o caminho do jar na configuração do Hadoop env.
    - O FileSystem do Azure Data Lake Store está empacotado desde o Hadoop 3.0.0-alpha1. Se o cluster Hadoop for inferior a essa versão, você precisará importar manualmente os pacotes jar relacionados com ADLS (azure-datalake-store.jar) [deste](https://hadoop.apache.org/releases.html) local para o cluster e especificar o caminho do jar na configuração do Hadoop env.

4. Prepare uma pasta temporária no HDFS. Essa pasta temporária é usada para armazenar script de shell do DistCp, portanto ela ocupa espaço em nível de KB.
5. Verifique se a conta de usuário fornecida no serviço vinculado do HDFS tem permissão para: a) enviar aplicativo em YARN; b) criar subpasta e ler/gravar arquivos na pasta temporária acima.

### <a name="configurations"></a>Configurações

Consulte DistCp configurações e exemplos relacionados à [HDFS como fonte](#hdfs-as-source) seção.

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Usar a autenticação Kerberos para o conector HDFS

Há duas opções para configurar o ambiente local para usar a autenticação Kerberos no conector HDFS. Você pode escolher a que melhor se adapta ao seu caso.
* Opção 1: [Ingressar no computador do Integration Runtime auto-hospedado no realm Kerberos](#kerberos-join-realm)
* Opção 2: [Habilitar a confiança mútua entre o domínio do Windows e o realm Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Opção 1: Ingressar no computador do Integration Runtime auto-hospedado no realm Kerberos

#### <a name="requirements"></a>Requisitos

* O computador do Integration Runtime auto-hospedado precisa ingressar no realm Kerberos e não pode ingressar em nenhum domínio do Windows.

#### <a name="how-to-configure"></a>Como configurar

**No computador do Integration Runtime auto-hospedado:**

1.  Execute o utilitário **Ksetup** para configurar o servidor e o realm KDC Kerberos.

    O computador deve ser configurado como um membro de um grupo de trabalho, uma vez que um realm Kerberos é diferente de um domínio do Windows. Para isso, defina o realm Kerberos e adicione um servidor KDC como se segue. Substitua *REALM.COM* pelo seu respectivo realm, conforme a necessidade.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Reinicie** o computador depois de executar esses 2 comandos.

2.  Verifique a configuração com o comando **Ksetup**. A saída deverá ser como a seguinte:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**No Azure Data Factory:**

* Configure o conector HDFS usando a **autenticação do Windows** com o nome da entidade de segurança e a senha Kerberos para se conectar à fonte de dados HDFS. Verifique a seção de [propriedades do Serviço Vinculado HDFS](#linked-service-properties) nos detalhes da configuração.

### <a name="kerberos-mutual-trust"></a>Opção 2: Habilitar a confiança mútua entre o domínio do Windows e o realm Kerberos

#### <a name="requirements"></a>Requisitos

*   O computador do Integration Runtime auto-hospedado deve ingressar em um domínio do Windows.
*   Você precisa de permissão para atualizar as configurações do controlador de domínio.

#### <a name="how-to-configure"></a>Como configurar

> [!NOTE]
> Substitua REALM.COM e AD.COM no tutorial a seguir pelo seu próprio realm e controlador de domínio conforme a necessidade.

**No servidor KDC:**

1. Edite a configuração do KDC no arquivo **krb5.conf** para permitir que o Domínio do Windows de confiança do KDC se refira ao modelo de configuração a seguir. Por padrão, a configuração está localizada em **/etc/krb5.conf**.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log
            
           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true
            
           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }
            
           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM
            
           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   **Reinicie** o serviço KDC após a configuração.

2. Prepare uma entidade de segurança chamada **krbtgt/REALM.COM\@AD.COM** no servidor KDC com o seguinte comando:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. No arquivo de configuração de serviço HDFS **hadoop.security.auth_to_local**, adicione `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**No controlador de domínio:**

1.  Execute os comandos **Ksetup** a seguir para adicionar uma entrada de realm:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Estabeleça uma relação de confiança do Domínio do Windows ao Realm Kerberos. [password] é a senha para a entidade de segurança **krbtgt/REALM.COM\@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Selecione o algoritmo de criptografia usado no Kerberos.

    1. Vá para Gerenciador de Servidores > Gerenciamento de Política de Grupo > Domínio > Objetos de Política de Grupo > Política de Domínio Padrão ou Ativa e Editar.

    2. Na janela pop-up **Editor de Gerenciamento de Política de Grupo**, vá para Configuração do Computador > Políticas > Configurações do Windows > Configurações de Segurança > Políticas Locais > Opções de Segurança e defina **Segurança de rede: Configurar tipos de criptografia permitidos para Kerberos**.

    3. Selecione o algoritmo de criptografia que você deseja usar ao se conectar ao KDC. Normalmente, você pode simplesmente selecionar todas as opções.

        ![Configuração dos tipos de criptografia para Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Use o comando **Ksetup** para especificar o algoritmo de criptografia a ser usado no REALM específico.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Crie o mapeamento entre a conta de domínio e a entidade de segurança Kerberos para usar a entidade de segurança Kerberos no Domínio do Windows.

    1. Inicie as Ferramentas administrativas > **Usuários e Computadores do Active Directory**.

    2. Configure recursos avançados clicando em **Exibir** > **Recursos Avançados**.

    3. Localize a conta para a qual deseja criar mapeamentos e clique com o botão direito do mouse para exibir **Mapeamentos de Nome** > clique na guia **Nomes Kerberos**.

    4. Adicione uma entidade de segurança do realm.

        ![Mapear identidade de segurança](media/connector-hdfs/map-security-identity.png)

**No computador do Integration Runtime auto-hospedado:**

* Execute os comandos **Ksetup** a seguir para adicionar uma entrada de realm.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**No Azure Data Factory:**

* Configure o conector HDFS usando a **autenticação do Windows** com a Conta de Domínio ou a Entidade de Segurança Kerberos para se conectar à fonte de dados HDFS. Verifique a seção de [propriedades do Serviço Vinculado HDFS](#linked-service-properties) nos detalhes da configuração.


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
