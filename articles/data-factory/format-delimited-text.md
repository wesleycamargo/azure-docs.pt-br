---
title: Formato de texto delimitado no Azure Data Factory | Microsoft Docs
description: Este tópico descreve como lidar com o formato de texto delimitado no Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 4e365ef01b8c7a89d61efad25bd18943e7b2d1a4
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64877962"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Formato de texto delimitado no Azure Data Factory

Siga este artigo quando quiser **analisar os arquivos de texto delimitado ou gravar os dados no formato de texto delimitado**. 

Formato de texto delimitado é compatível com os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [BLOBs do Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [do Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [o armazenamento de arquivos do Azure](connector-azure-file-storage.md), [Sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [do Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)e [ SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados de texto delimitado.

| Propriedade         | DESCRIÇÃO                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| Tipo             | A propriedade type do conjunto de dados deve ser definida como **DelimitedText**. | Sim      |
| location         | Configurações de local dos arquivos. Cada conector com base em arquivo tem seu próprio tipo de local e suporte para propriedades em `location`. **Consulte detalhes no artigo de conector -> seção de propriedades do conjunto de dados**. | Sim      |
| columnDelimiter  | Os caracteres usados para separar as colunas em um arquivo. Atualmente, o delimitador de char várias tem suporte apenas para mapeamento de fluxo de dados, mas não a atividade de cópia. <br>O valor padrão é **vírgula `,`** , quando o delimitador de coluna é definido como cadeia de caracteres vazia, que significa que nenhum delimitador, a linha inteira será interpretado como uma única coluna. | Não        |
| rowDelimiter     | O único caractere ou "\r\n" usado para separar linhas em um arquivo.<br>O valor padrão é qualquer um dos seguintes valores **na leitura: ["\r\n", "\r", "\n"]**, e **"\n" ou "\r\n" na gravação** pelos dados de mapeamento de fluxo e atividade de cópia, respectivamente. <br>Quando `rowDelimiter` é definido como nenhum delimitador (cadeia de caracteres vazia), o `columnDelimiter` deve ser definida como nenhum delimitador (cadeia de caracteres vazia), como também, que significa que tratar de todo o conteúdo como um único valor. | Não        |
| quoteChar        | O único caractere para citar os valores de coluna se ela contém o delimitador de coluna. <br>O valor padrão é **aspas duplas** `"`. <br>Para o mapeamento de fluxo de dados, `quoteChar` não pode ser uma cadeia de caracteres vazia. <br>Para a atividade de cópia, quando `quoteChar` é definido como cadeia de caracteres vazia, isso significa que não há nenhum caractere de citação e valor da coluna não está entre aspas, e `escapeChar` é usado para escapar o delimitador de coluna e ela mesma. | Não        |
| escapeChar       | O único caractere de escape aspas dentro de um valor entre aspas.<br>O valor padrão é **barra invertida `\`** . <br>Para o mapeamento de fluxo de dados, `escapeChar` não pode ser uma cadeia de caracteres vazia. <br/>Para a atividade de cópia, quando `escapeChar` é definido como cadeia de caracteres vazia, o `quoteChar` deve ser definido como cadeia de caracteres vazia, bem, nesse caso, certifique-se de todos os valores de coluna não contém delimitadores. | Não        |
| firstRowAsHeader | Especifica se deve tratar/fazer a primeira linha como uma linha de cabeçalho com nomes de colunas.<br>Valores permitidos são **verdadeira** e **falso** (padrão). | Não        |
| nullValue        | Especifica a representação de cadeia de caracteres de valor nulo. <br>O valor padrão é **cadeia de caracteres vazia**. | Não        |
| encodingName     | O tipo de codificação usado para ler/gravar arquivos de teste. <br>Valores permitidos são da seguinte maneira: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "COREIA EUC", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", " IBM852 ","IBM855","IBM857","IBM860","IBM861","IBM863","IBM864","IBM865","IBM869","IBM870","IBM01140","IBM01141","IBM01142","IBM01143","IBM01144","IBM01145","IBM01146","IBM01147","IBM01148","IBM01149","ISO-2022-JP","ISO-2022-KR ","ISO-8859-1","ISO-8859-2","ISO-8859-3","ISO-8859-4","ISO-8859-5","ISO-8859-6","ISO-8859-7","ISO-8859-8","ISO-8859-9","ISO-8859-13","ISO-8859-15","WINDOWS-874","WINDOWS-1250","WINDOWS 1251","WINDOWS-1252","WINDOWS-1253"," WINDOWS-1254 ","WINDOWS-1255","WINDOWS-1256","WINDOWS-1257","WINDOWS-1258".<br>Observe o que mapeamento de fluxo de dados não dá suporte a codificação UTF-7. | Não        |
| compressionCodec | O codec de compactação usado para ler/gravar arquivos de texto. <br>Valores permitidos são **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**, ou **lz4**. Para usar ao salvar o arquivo. <br>Observe no momento, atividade de cópia não dá suporte a "interessantes" & "lz4", e o mapeamento de fluxo de dados não dá suporte a "ZipDeflate". | Não        |
| compressionLevel | A taxa de compactação. <br>Valores permitidos são **ideal** ou **mais rápido**.<br>- **Mais rápida:** A operação de compactação deve ser concluída o mais rápido possível, mesmo se o arquivo resultante não for compactado da maneira ideal.<br>- **Ideal**: A operação de compactação deve ser concluída da maneira ideal, mesmo se a operação demorar mais tempo para ser concluída. Para saber mais, veja o tópico [Nível de compactação](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Não        |

Abaixo está um exemplo de conjunto de dados de texto delimitados no armazenamento de BLOBs do Azure:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte de texto delimitados e coletor.

### <a name="delimited-text-as-source"></a>Texto delimitado como fonte 

As propriedades a seguir têm suporte na atividade de cópia ***\*código-fonte\**** seção.

| Propriedade       | DESCRIÇÃO                                                  | Obrigatório |
| -------------- | ------------------------------------------------------------ | -------- |
| Tipo           | A propriedade type da fonte da atividade de cópia deve ser definida como **DelimitedTextSource**. | Sim      |
| formatSettings | Um grupo de propriedades. Consulte a **configurações de leitura de texto delimitado** tabela a seguir. | Não        |
| storeSettings  | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector com base em arquivo tem suas próprias configurações de leitura com suporte em `storeSettings`. **Consulte detalhes no artigo de conector -> seção de propriedades de atividade de cópia**. | Não        |

Com suporte **configurações de leitura de texto delimitado** sob `formatSettings`:

| Propriedade      | DESCRIÇÃO                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| Tipo          | O tipo de formatSettings deve ser definido como **DelimitedTextReadSetting**. | Sim      |
| skipLineCount | Indica o número de linhas **não vazias** a serem ignoradas ao ler dados de arquivos de entrada. <br>Se skipLineCount e firstRowAsHeader forem especificados, as linhas serão ignoradas pela primeira vez e, em seguida, as informações de cabeçalho serão lidas do arquivo de entrada. | Não        |

### <a name="delimited-text-as-sink"></a>Texto delimitado como coletor

As propriedades a seguir têm suporte na atividade de cópia ***\*coletor\**** seção.

| Propriedade       | DESCRIÇÃO                                                  | Obrigatório |
| -------------- | ------------------------------------------------------------ | -------- |
| tipo           | A propriedade type da fonte da atividade de cópia deve ser definida como **DelimitedTextSink**. | Sim      |
| formatSettings | Um grupo de propriedades. Consulte a **texto delimitado gravar configurações** tabela a seguir. |          |
| storeSettings  | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector com base em arquivo tem suas próprias configurações de gravação com suporte em `storeSettings`. **Consulte detalhes no artigo de conector -> seção de propriedades de atividade de cópia**. | Não        |

Com suporte **configurações de gravação de texto delimitado** sob `formatSettings`:

| Propriedade      | DESCRIÇÃO                                                  | Obrigatório                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| Tipo          | O tipo de formatSettings deve ser definido como **DelimitedTextWriteSetting**. | Sim                                                   |
| fileExtension | A extensão de arquivo usada para nomear os arquivos de saída, por exemplo, `.csv`, `.txt`. Ele deve ser especificado quando o `fileName` não for especificado na saída DelimitedText conjunto de dados. | Sim, quando o nome do arquivo não for especificado no conjunto de dados de saída |

## <a name="mapping-data-flow-properties"></a>Propriedades do mapeamento de fluxo de dados

Obtenha detalhes em [transformação de origem](data-flow-source.md) e [transformação do coletor](data-flow-sink.md) no mapeamento de fluxo de dados.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Mapeamento de fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)