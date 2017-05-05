---
title: Copiar dados de/para o Armazenamento de Blobs do Azure | Microsoft Docs
description: Saiba como copiar dados de blob no Azure Data Factory. O(s) exemplo(s) a seguir mostra(m) como copiar dados de e para o Armazenamento de Blobs do Azure e o Banco de Dados SQL do Azure.
keywords: "dados de blob, cópia de blobs do azure"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: 16a094b8a311c43658aad299e206d79e29bafed0
ms.lasthandoff: 04/12/2017


---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Copie os dados de ou para o Armazenamento de Blobs do Azure usando o Azure Data Factory
Este artigo explica como usar a Atividade de Cópia no Azure Data Factory para copiar dados bidirecionalmente no Armazenamento de Blobs do Azure. Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de qualquer armazenamento de dados de origem com suporte para o armazenamento de Blobs do Azure ou do armazenamento de Blobs do Azure para qualquer armazenamento de dados do coletor com suporte. Para obter uma lista de repositórios de dados com suporte como fontes ou coletores da atividade de cópia, confira a tabela [Repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats).

> [!IMPORTANT]
> A Atividade de Cópia dá suporte à cópia de dados de/para contas de Armazenamento do Azure para fins gerais e para o Armazenamento de Blobs Dinâmico/Estático. A atividade dá suporte à **leitura de blobs de bloco, de acréscimo ou de página**, mas dá suporte à **gravação apenas em blobs de blocos**. Não há suporte para armazenamento Premium do Azure como um coletor porque ele é feito por blobs de página.
>
> A atividade de cópia não exclui os dados de origem depois que eles são copiados com êxito para o destino. Se precisar excluir os dados de origem após uma cópia bem-sucedida, crie uma atividade personalizada para excluir os dados e use a atividade no pipeline.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados de um armazenamento de Blobs do Azure usando ferramentas/APIs diferentes.

A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Você também pode usar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo do Azure Resource Manager**, **API .NET** e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON.  Para obter exemplos com definições de JSON das entidades do Data Factory usadas para copiar dados bidirecionalmente em um Armazenamento de Blobs do Azure, confira a seção [Exemplos de JSON](#json-examples) neste artigo.

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas ao Armazenamento de Blobs do Azure:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
Existem dois tipos de serviço vinculado que você pode usar para vincular um Armazenamento do Azure a um Azure Data Factory. São eles: o serviço vinculado **AzureStorage** e o serviço vinculado **AzureStorageSas**. O serviço vinculado do Armazenamento do Azure fornece o data factory com acesso global ao Armazenamento do Azure. Já o serviço vinculado SAS (Assinatura de Acesso Compartilhado) do Armazenamento do Azure fornece o data factory com acesso restrito/associado ao tempo ao Armazenamento do Azure. Não há outras diferenças entre esses dois serviços vinculados. Escolha o serviço vinculado que atenda às suas necessidades. As seções a seguir fornecem mais detalhes sobre esses dois serviços vinculados.

[!INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para especificar um conjunto de dados de modo a representar dados de entrada ou saída em um Armazenamento de Blobs do Azure, defina a propriedade de tipo do conjunto de dados para: **AzureBlob**. Defina a propriedade **linkedServiceName** do conjunto de dados para o nome do Armazenamento do Azure ou do serviço vinculado de SAS do Armazenamento do Azure.  As propriedades de tipo do conjunto de dados especificam o **contêiner de blob** e a **pasta** no Armazenamento de Blobs.

Para obter uma lista completa das seções e propriedades JSON disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

O Data Factory dá suporte aos valores de tipo baseados em .NET em conformidade com CLS a seguir, para fornecer informações de tipo em "estrutura" para fontes de dados em que o esquema é definido na leitura, assim como o blob do Azure: Int16, Int32, Int64, Single, Double, Decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset, Timespan. O Data Factory executa conversões de tipo automaticamente ao mover dados de um armazenamento de dados de origem para um armazenamento de dados do coletor.

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização, o formato etc. dos dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo **AzureBlob** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| folderPath |Caminho para o contêiner e a pasta no armazenamento de blob. Exemplo: myblobcontainer\myblobfolder\ |Sim |
| fileName |O nome do blob. fileName é opcional e diferencia maiúsculas de minúsculas.<br/><br/>Caso você especifique um nome de arquivo, a atividade (incluindo Cópia) funcionará no Blob específico.<br/><br/>Quando fileName não for especificado, a Cópia incluirá todos os Blobs do folderPath para o conjunto de dados de entrada.<br/><br/>Quando fileName não for especificado para um conjunto de dados de saída, o nome do arquivo gerado estaria no seguinte formato: Data.<Guid>.txt (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| partitionedBy |partitionedBy é uma propriedade opcional. Você pode usá-lo para especificar um folderPath dinâmico e o nome de arquivo para dados de série temporal. Por exemplo, folderPath pode ser parametrizado para cada hora dos dados. Confira a seção [Usando a propriedade partitionedBy](#using-partitionedBy-property) para obter detalhes e exemplos. |Não |
| formato | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. |Não |
| compactação | Especifique o tipo e o nível de compactação para os dados. Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. Os níveis com suporte são **Ideal** e **O mais rápido**. Para saber mais, confira [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support) (Formatos de arquivo e de compactação no Azure Data Factory). |Não |

### <a name="using-partitionedby-property"></a>Usando a propriedade partitionedBy
Conforme mencionado na seção anterior, você pode especificar um nome de arquivo e um folderPath dinâmico para dados de série temporal com a propriedade **partitionedBy**, [funções do Data Factory e as variáveis do sistema](data-factory-functions-variables.md).

Para saber mais sobre conjuntos de dados de série temporal, agendamento e fatias, veja os artigos [Criando conjuntos de dados](data-factory-create-datasets.md) e [Agendamento e execução](data-factory-scheduling-and-execution.md).

#### <a name="sample-1"></a>Exemplo 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Nesse exemplo, {Slice} é substituído pelo valor da variável de sistema SliceStart do Data Factory no formato (AAAAMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. O folderPath é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>Exemplo 2

```json
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

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, conjuntos de dados de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade. Por outro lado, as propriedades disponíveis na seção **typeProperties** da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor. Se você estiver movendo dados de um armazenamento de Blobs do Azure, defina o tipo de origem na atividade de cópia como **BlobSource**. Da mesma forma, se você estiver movendo dados para um armazenamento de Blobs do Azure, defina o tipo de coletor na atividade de cópia como **BlobSink**. Esta seção fornece uma lista das propriedades compatíveis com BlobSource e BlobSink.

O **BlobSource** dá suporte às seguintes propriedades na seção **typeProperties**:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente por meio de subpastas ou somente da pasta especificada. |True (valor padrão), False |Não |

O **BlobSink** dá suporte às seguintes propriedades na seção **typeProperties**:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| copyBehavior |Define o comportamento de cópia quando a origem é BlobSource ou FileSystem. |<b>PreserveHierarchy:</b> preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><br/><b>FlattenHierarchy:</b> todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm o nome gerado automaticamente. <br/><br/><b>MergeFiles:</b> mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/blob for especificado, o nome do arquivo mesclado será o nome especificado; caso contrário, será o nome de arquivo gerado automaticamente. |Não |

**BlobSource** também oferece suporte a essas duas propriedades para compatibilidade com versões anteriores.

* **treatEmptyAsNull**: especifica se deve-se tratar a cadeia de caracteres nula ou vazia como valor nulo.
* **skipHeaderLineCount** - Especifica quantas linhas precisam ser ignoradas. É aplicável somente quando o conjunto de dados de entrada usa TextFormat.

Da mesma forma, **BlobSink** oferece suporte à seguinte propriedade para compatibilidade com versões anteriores.

* **blobWriterAddHeader**: especifica se deve adicionar um cabeçalho de definições de coluna ao gravar em um conjunto de dados de saída.

Agora, os conjuntos de dados dão suporte às seguintes propriedades que implementam a mesma funcionalidade: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

A tabela a seguir fornece orientação sobre como usar as novas propriedades do conjunto de dados em vez de propriedades de fonte/coletor de blob.

| Propriedade de Atividade de Cópia | Propriedade de Conjunto de Dados |
|:--- |:--- |
| skipHeaderLineCount em BlobSource |skipLineCount e firstRowAsHeader. As linhas são ignoradas primeiro e, em seguida, a primeira linha é lida como um cabeçalho. |
| treatEmptyAsNull em BlobSource |treatEmptyAsNull no conjunto de dados de entrada |
| blobWriterAddHeader em BlobSink |firstRowAsHeader no conjunto de dados de saída |

Confira a seção [Especificar TextFormat](data-factory-supported-file-and-compression-formats.md#text-format) para obter informações detalhadas sobre essas propriedades.    

### <a name="recursive-and-copybehavior-examples"></a>exemplos de recursive e copyBehavior
Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursive e copyBehavior.

| recursiva | copyBehavior | Comportamento resultante |
| --- | --- | --- |
| verdadeiro |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura:  <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a mesma estrutura da origem<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. |
| verdadeiro |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura:  <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5 |
| verdadeiro |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura:  <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos dos Arquivo1 + Arquivo2 + Arquivo3 + Arquivo4 + Arquivo5 são mesclados em um único arquivo com um nome de arquivo gerado automaticamente |
| false |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura:  <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados. |
| false |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados. |
| false |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos dos Arquivo1 + Arquivo2 são mesclados em um único arquivo com um nome de arquivo gerado automaticamente. Nome gerado automaticamente para o Arquivo1<br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados. |

## <a name="supported-file-and-compression-formats"></a>Formatos de arquivo e compactação com suporte
Consulte o artigo [Formatos de arquivo e compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md) para obter detalhes.

## <a name="json-examples"></a>Exemplos de JSON
Os exemplos a seguir fornecem amostras de definições de JSON que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tais exemplos mostram como copiar dados de/para o Armazenamento de Blobs do Azure e o Banco de Dados SQL do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.

## <a name="example-copy-data-from-blob-storage-to-sql-database"></a>Exemplo: copiar dados do Armazenamento de Blobs para o Banco de Dados SQL
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Atividade de cópia que usa [BlobSource](#copy-activity-properties) e [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

O exemplo copia os dados de série temporal de um blob do Azure para uma tabela SQL do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Azure:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Serviço vinculado de armazenamento do Azure:**

```json
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
O Azure Data Factory dá suporte a dois tipos de serviços vinculados do Armazenamento do Azure: **AzureStorage** e **AzureStorageSas**. Para o primeiro, você especifica a cadeia de conexão que inclui a chave de conta, e para o mais recente, você especifica o URI de SAS (Assinatura de Acesso Compartilhado). Confira a seção [Serviços vinculados](#linked-service-properties) para obter detalhes.  

**Conjunto de dados de entrada de Blob do Azure:**

Os dados são coletados de um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa parte da hora de início do dia, mês e ano e o nome de arquivo usa a parte da hora de início. A configuração "external": "true" informa o Data Factory que a tabela é externa ao Data Factory e não é produzida por uma atividade no Data Factory.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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
```
**Conjunto de dados de saída do SQL Azure:**

Os dados de cópia do exemplo em uma tabela chamada "MyTable" em um banco de dados SQL do Azure. Crie a tabela no banco de dados SQL Azure com o mesmo número de colunas que você espera que o arquivo CSV de Blob contenha. Novas linhas são adicionadas à tabela a cada hora.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Uma atividade de cópia em um pipeline com origem de Blob e coletor SQL:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **fonte** está definido como **BlobSource** e o tipo de **coletor** está definido como **SqlSink**.

```json
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
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
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
```
## <a name="example-copy-data-from-azure-sql-to-azure-blob"></a>Exemplo: copiar dados do SQL do Azure para o Blob do Azure
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Atividade de Cópia que usa [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) e [BlobSink](#copy-activity-properties).

O exemplo copia os dados de série temporal de uma tabela SQL do Azure para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Azure:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Serviço vinculado de armazenamento do Azure:**

```json
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
O Azure Data Factory dá suporte a dois tipos de serviços vinculados do Armazenamento do Azure: **AzureStorage** e **AzureStorageSas**. Para o primeiro, você especifica a cadeia de conexão que inclui a chave de conta, e para o mais recente, você especifica o URI de SAS (Assinatura de Acesso Compartilhado). Confira a seção [Serviços vinculados](#linked-service-properties) para obter detalhes.  

**Conjunto de dados de entrada do SQL Azure:**

O exemplo supõe que você criou uma tabela "MyTable" no SQL Azure e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

Configurar "external": true informa ao serviço Data Factory que a tabela é externa ao Data Factory e não é produzida por uma atividade no Data Factory.

```json
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
```

**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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

**Uma atividade de cópia em um pipeline com origem SQL e coletor de Blob:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **fonte** está definido como **SqlSource** e o tipo de **coletor** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora a serem copiados.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureSQLInput"
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
```

> [!NOTE]
> Para mapear colunas de conjunto de dados de origem para colunas do conjunto de dados de coletor, confira [Mapeando colunas de conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.

