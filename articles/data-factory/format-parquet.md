---
title: Formato parquet no Azure Data Factory | Microsoft Docs
description: Este tópico descreve como lidar com o formato de Parquet no Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 360b794f0d8ba9c145a92f015f264eb624fbb0f1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65144889"
---
# <a name="parquet-format-in-azure-data-factory"></a>Formato parquet no Azure Data Factory

Siga este artigo quando quiser **analisar os arquivos Parquet ou gravar os dados no formato Parquet**. 

Formato parquet é compatível com os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [BLOBs do Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [do Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [o armazenamento de arquivos do Azure](connector-azure-file-storage.md), [Sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [do Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)e [ SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados Parquet.

| Propriedade         | DESCRIÇÃO                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| Tipo             | A propriedade type do conjunto de dados deve ser definida como **Parquet**. | Sim      |
| location         | Configurações de local dos arquivos. Cada conector com base em arquivo tem seu próprio tipo de local e suporte para propriedades em `location`. **Consulte detalhes no artigo de conector -> seção de propriedades do conjunto de dados**. | Sim      |
| compressionCodec | O codec de compactação a ser usado ao gravar em arquivos Parquet. Ao ler de arquivos Parquet, o Data Factory determine automaticamente o codec de compactação com base nos metadados do arquivo.<br>Tipos com suporte são "**none**","**gzip**","**snappy**" (padrão), e "**lzo**". Observe, no momento, atividade de cópia não dá suporte a LZO. | Não        |

> [!NOTE]
> Não há suporte para o espaço em branco no nome da coluna para arquivos Parquet.

Abaixo está um exemplo de conjunto de dados Parquet no armazenamento de BLOBs do Azure:

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do Parquet e coletor.

### <a name="parquet-as-source"></a>Parquet como fonte

As propriedades a seguir têm suporte na atividade de cópia ***\*código-fonte\**** seção.

| Propriedade      | DESCRIÇÃO                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| Tipo          | A propriedade type da fonte da atividade de cópia deve ser definida como **ParquetSource**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector com base em arquivo tem suas próprias configurações de leitura com suporte em `storeSettings`. **Consulte detalhes no artigo de conector -> seção de propriedades de atividade de cópia**. | Não        |

### <a name="parquet-as-sink"></a>Parquet como coletor

As propriedades a seguir têm suporte na atividade de cópia ***\*coletor\**** seção.

| Propriedade      | DESCRIÇÃO                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| Tipo          | A propriedade type da fonte da atividade de cópia deve ser definida como **ParquetSink**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector com base em arquivo tem suas próprias configurações de gravação com suporte em `storeSettings`. **Consulte detalhes no artigo de conector -> seção de propriedades de atividade de cópia**. | Não        |

## <a name="mapping-data-flow-properties"></a>Propriedades do mapeamento de fluxo de dados

Obtenha detalhes em [transformação de origem](data-flow-source.md) e [transformação do coletor](data-flow-sink.md) no mapeamento de fluxo de dados.

## <a name="data-type-support"></a>Suporte ao tipo de dados

Parquet tipos de dados complexos são atualmente não tem suporte (por exemplo, mapa, listar, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Usando o Integration Runtime auto-hospedado

> [!IMPORTANT]
> Para cópias autorizadas pelo Integration Runtime (auto-hospedado), por exemplo, entre repositórios de dados locais e na nuvem, se você não estiver copiando arquivos Parquet **como são**, precisará instalar o **JRE (Java Runtime Environment) 8 de 64 bits ou o OpenJDK** no IR de seu computador. Confira o próximo parágrafo para obter mais detalhes.

Para cópia em execução no IR auto-hospedado com serialização/desserialização de arquivo Parquet, o ADF localiza o tempo de execução do Java verificando o registro *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* para JRE, em primeiro lugar e, se não encontrado, realiza, em segundo lugar, a verificação na variável do sistema *`JAVA_HOME`* para o OpenJDK.

- **Para usar o JRE**: O IR de 64 bits requer um JRE de 64 bits. É possível encontrá-lo [aqui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Para usar o OpenJDK**: ele tem suporte desde a versão do IR 3.13. Empacote o jvm.dll com todos os outros assemblies necessários do OpenJDK no IR auto-hospedado do computador e defina a variável de ambiente JAVA_HOME adequadamente.

> [!TIP]
> Se você copiar os dados para/do formato Parquet usando o IR auto-hospedado e ocorrências de erro informando que "Ocorreu um erro ao chamar o java, mensagem: **espaço de heap java.lang.OutOfMemoryError:Java**", poderá adicionar uma variável de ambiente `_JAVA_OPTIONS` na máquina que hospeda o IR auto-hospedado para ajustar o tamanho mín/máx do heap para JVM para capacitar a cópia e executar novamente o pipeline.

![Definir o tamanho do heap da JVM no IR auto-hospedado](C:/AzureContent/azure-docs-pr/articles/data-factory/media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemplo: defina a variável `_JAVA_OPTIONS` com o valor `-Xms256m -Xmx16g`. O sinalizador `Xms` especifica o pool de alocação de memória inicial para uma JVM (Máquina Virtual Java), enquanto `Xmx` especifica o pool de alocação de memória máxima. Isso significa que a JVM será iniciada com `Xms` quantidade de memória e será capaz de usar um máximo de `Xmx` quantidade de memória. Por padrão, o ADF usa um mínimo de 64MB e um máximo de 1G.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Mapeamento de fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)