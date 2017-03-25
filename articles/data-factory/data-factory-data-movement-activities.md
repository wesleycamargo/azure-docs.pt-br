---
title: "Mover dados usando a Atividade de Cópia | Microsoft Docs"
description: "Saiba mais sobre a movimentação de dados em pipelines do Data Factory: migração de dados entre repositórios na nuvem e entre um repositório local e um repositório na nuvem. Use a Atividade de Cópia."
keywords: "copiar dados, movimentação de dados, migração de dados, transferir dados"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: f4c225c97ac997c412704b278c033c519d4424ed
ms.lasthandoff: 03/22/2017


---
# <a name="move-data-by-using-copy-activity"></a>Mover dados usando a Atividade de Cópia
## <a name="overview"></a>Visão geral
No Azure Data Factory, você pode usar a Atividade de Cópia para copiar dados de diferentes formas de vários repositórios de dados locais e na nuvem para o Azure. Depois que os dados são copiados, eles ainda podem ser transformados e analisados. Também é possível usar a Atividade de Cópia a fim de publicar resultados de análise e transformação para consumo do aplicativo e BI (business intelligence).

![Função da Atividade de Cópia](media/data-factory-data-movement-activities/copy-activity.png)

A Atividade de Cópia é alimentada por um [serviço disponível globalmente](#global)seguro, confiável e escalonável. Este artigo fornece detalhes sobre a movimentação de dados no Data Factory e na Atividade de Cópia.

Primeiramente, vejamos como a migração de dados ocorre entre dois repositórios de dados na nuvem e entre um repositório de dados local e um repositório de dados na nuvem.

> [!NOTE]
> Para saber mais sobre atividades em geral, confira o artigo sobre [noções básicas de pipelines e atividades](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Copiando dados entre dois armazenamentos de dados em nuvem
Quando os repositórios de dados de origem e coletor estiverem na nuvem, a Atividade de Cópia passará pelas fases a seguir para copiar os dados da origem para o coletor. O serviço que capacita a Atividade de Cópia:

1. Lê dados do repositório de dados de origem.
2. Executa a serialização/desserialização, a compactação/descompactação, o mapeamento de coluna e a conversão de tipo. Ele realiza essas operações com base nas configurações do conjunto de dados de entrada, no conjunto de dados de saída e na Atividade de Cópia.
3. Grava dados no repositório de dados de destino.

O serviço escolhe automaticamente a região ideal para realizar a movimentação de dados. Essa região geralmente é a mais próxima do repositório de dados do coletor.

![Cópia de nuvem para nuvem](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiar dados entre um armazenamento de dados local e um armazenamento de dados em nuvem
Para mover dados com segurança entre o armazenamento de dados local e um armazenamento de dados na nuvem, instale o Gateway de Gerenciamento de Dados no computador local. O Gateway de Gerenciamento de Dados é um agente que possibilita a movimentação e o processamento de dados híbridos. Você pode instalá-lo no mesmo computador que o repositório de dados em si ou em outro computador que tenha acesso ao repositório de dados.

Neste cenário, o Gateway de Gerenciamento de Dados executa a serialização/desserialização, a compactação/descompactação, o mapeamento de coluna e a conversão de tipo. Os dados não fluem pelo serviço Azure Data Factory. Em vez disso, o Gateway de Gerenciamento de Dados grava os dados diretamente no repositório de destino.

![Cópia do local para a nuvem](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Confira [Mover dados entre repositórios de dados locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para ver uma introdução e instruções passo a passo. Confira o artigo [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) para obter informações detalhadas sobre esse agente.

Você também pode mover dados entre repositórios de dados compatíveis hospedados nas VMs (máquinas virtuais) de IaaS do Azure usando o Gateway de Gerenciamento de Dados. Nesse caso, é possível instalar o Gateway de Gerenciamento de Dados na mesma VM do repositório de dados em si ou em uma VM separada que tenha acesso ao repositório de dados.

## <a name="supported-data-stores-and-formats"></a>Fontes de dados e formatos com suporte
[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Se precisar mover dados para dentro e fora de um armazenamento de dados ao qual a Atividade de Cópia não dá suporte, use uma **atividade personalizada** no Data Factory com sua própria lógica para copiar/mover dados. Para obter detalhes sobre como criar e usar uma atividade personalizada, confira [Usar atividades personalizadas em um pipeline do Azure Data Factory](data-factory-use-custom-activities.md).

### <a name="supported-file-formats"></a>Formatos de arquivo com suporte
Você pode usar a Atividade de Cópia para **copiar arquivos no estado em que se encontram** entre dois repositórios de dados baseados em arquivo, como Blob do Azure, Azure Data Lake Store, Amazon S3, FTP, Sistema de Arquivos e HDFS. Para fazer isso, você pode pular a [seção de formato](data-factory-create-datasets.md) nas definições de conjunto de dados de entrada e saída. Os dados são copiados com eficiência sem qualquer serialização/desserialização.

A Atividade de Cópia também lê e grava em arquivos em formatos especificados: há suporte para **texto, Avro, ORC, Parquet e JSON** e para o codec de compactação **GZip, Deflate, BZip2 e ZipDeflate**. Você pode fazer as seguintes atividades de cópia, por exemplo:

* Copiar dados em formato de texto (CSV) compactado por GZip do Blob do Azure e gravá-los no Banco de Dados SQL Azure.
* Copiar arquivos no formato de texto (CSV) do Sistema de Arquivos local e gravá-los no Blob do Azure no formato Avro.
* Copiar dados no SQL Server local e gravar ao no Azure Data Lake Store no formato ORC.
* Copiar os arquivos compactados do Sistema de Arquivos local e, em seguida, descompactá-los no Azure Data Lake Store.

## <a name="global"></a>Movimentação de dados globalmente disponível
O Azure Data Factory está disponível apenas nas regiões Oeste dos EUA, Leste dos EUA e Europa Setentrional. No entanto, o serviço que possibilita a Atividade de cópia está disponível globalmente nas seguintes regiões e regiões geográficas. A topologia globalmente disponível garante a movimentação de dados eficiente, o que geralmente evita saltos entre regiões. Confira [Serviços por região](https://azure.microsoft.com/regions/#services) para ver a disponibilidade do Data Factory e da Movimentação de Dados em uma região.

### <a name="copy-data-between-cloud-data-stores"></a>Copiar dados entre armazenamentos de dados em nuvem
Quando os armazenamentos de dados de origem e de coletor residem na nuvem, o Data Factory usa uma implantação de serviço na região que está mais perto do local do coletor na mesma região geográfica para realizar a movimentação de dados. Consulte a tabela a seguir para ver o mapeamento:

| Geografia do repositório de dados de destino | Região do armazenamento de dados de destino | Região usada para movimentação de dados |
|:--- |:--- |:--- |
| Estados Unidos | Leste dos EUA | Leste dos EUA |
| &nbsp; | Leste dos EUA 2 | Leste dos EUA 2 |
| &nbsp; | Centro dos EUA | Centro dos EUA |
| &nbsp; | Centro-Norte dos EUA | Centro-Norte dos EUA |
| &nbsp; | Centro-Sul dos Estados Unidos | Centro-Sul dos Estados Unidos |
| &nbsp; | Centro-Oeste dos EUA | Centro-Oeste dos EUA |
| &nbsp; | Oeste dos EUA | Oeste dos EUA |
| &nbsp; | Oeste dos EUA 2 | Oeste dos EUA |
| Canadá | Leste do Canadá | Canadá Central |
| &nbsp; | Canadá Central | Canadá Central |
| Brasil | Sul do Brasil | Sul do Brasil |
| Europa | Norte da Europa | Norte da Europa |
| &nbsp; | Europa Ocidental | Europa Ocidental |
| Pacífico Asiático | Sudeste Asiático | Sudeste Asiático |
| &nbsp; | Ásia Oriental | Sudeste Asiático |
| Austrália | Leste da Austrália | Leste da Austrália |
| &nbsp; | Sudeste da Austrália | Sudeste da Austrália |
| Japão | Leste do Japão | Leste do Japão |
| &nbsp; | Oeste do Japão | Leste do Japão |
| Índia | Índia Central | Índia Central |
| &nbsp; | Índia Ocidental | Índia Central |
| &nbsp; | Sul da Índia | Índia Central |

Como alternativa, você pode indicar explicitamente a região do serviço Data Factory a ser usada para realizar a cópia especificando a propriedade `executionLocation` em Atividade de Cópia `typeProperties`. Os valores com suporte para essa propriedade estão listados acima da coluna **Região usada para movimentação de dados**. Observe que seus dados passarão para essa região durante a cópia. Por exemplo, para copiar entre os repositórios do Azure no Reino Unido, especifique `"executionLocation": "North Europe"` para rotear pelo Norte da Europa (consulte [exemplo de JSON](#by-using-json-scripts) como referência).

> [!NOTE]
> Se a região do repositório de dados de destino não estiver na lista anterior ou não puder ser detectada, a Atividade de Cópia falhará em vez de passar por uma região alternativa, a menos que `executionLocation` seja especificado. A lista de regiões com suporte será expandida ao longo do tempo.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiar dados entre um armazenamento de dados local e um armazenamento de dados em nuvem
Quando os dados estiverem sendo copiados entre repositórios locais (ou IaaS/máquinas virtuais do Azure) e na nuvem, o [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) executa a movimentação de dados em um computador local ou máquina virtual. Os dados não fluem por meio do serviço na nuvem, a menos que você use o recurso [cópia em etapas](data-factory-copy-activity-performance.md#staged-copy) . Nesse caso, os dados fluem por meio de Armazenamento de Blobs do Azure de preparo antes de serem gravados no repositório de dados do coletor.

## <a name="create-a-pipeline-with-copy-activity"></a>Criar um pipeline com Atividade de Cópia
Você pode criar um pipeline com uma Atividade de Cópia de duas maneiras:

### <a name="by-using-the-copy-wizard"></a>Usando o Assistente de cópia
O Assistente de Cópia do Data Factory ajuda você a criar um pipeline com a Atividade de Cópia. Esse pipeline permite copiar dados de fontes compatíveis para destinos *sem gravar definições JSON* para serviços vinculados, conjuntos de dados e pipelines. Veja [Assistente de Cópia do Data Factory](data-factory-copy-wizard.md) para obter detalhes sobre o assistente.  

### <a name="by-using-json-scripts"></a>Usando scripts JSON
Você pode usar o Editor do Data Factory no portal do Azure, no Visual Studio ou no Azure PowerShell para criar uma definição JSON para um pipeline (usando Atividade de Cópia). Em seguida, você pode implantá-la para criar o pipeline no Data Factory. Confira [Tutorial: Use Copy Activity in an Azure Data Factory pipeline (Tutorial: Usar Atividade de Cópia em um pipeline do Azure Data Factory)](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para ver um tutorial com instruções detalhadas.    

As propriedades JSON (como nome, descrição, tabelas de entrada e saída, e políticas) estão disponíveis para todos os tipos de atividade. As propriedades que estão disponíveis na seção `typeProperties` da atividade variam de acordo com cada tipo de atividade.

Para a Atividade de Cópia, a seção `typeProperties` varia de acordo com os tipos de fonte e coletor. Clique em uma fonte/coletor na seção [Fontes e coletores com suporte](#supported-data-stores-and-formats) para saber mais sobre as propriedades do tipo às quais a Atividade de Cópia dá suporte para esse repositório de dados.

Veja um exemplo de definição JSON:

```json
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from Azure blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputBlobTable"
          }
        ],
        "outputs": [
          {
            "name": "OutputSQLTable"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          },
          "executionLocation": "North Europe"          
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```
A agenda que é definida no conjunto de dados de saída determina quando a atividade é executada (por exemplo, **diariamente**, frequência como **dia** e intervalo como **1**). A atividade copia dados de um conjunto de dados de entrada (**origem**) para um conjunto de dados de saída (**coletor**).

Você pode especificar mais de um conjunto de dados de entrada para a Atividade de Cópia. Eles são usados para verificar as dependências antes que a atividade seja executada. No entanto, somente os dados do primeiro conjunto de dados são copiados para o conjunto de dados de destino. Para saber mais, confira [Agendamento e execução](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Confira o artigo [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md)que descreve os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory. Ele também lista o desempenho observado durante os testes internos e discute várias maneiras de otimizar o desempenho da Atividade de Cópia.

## <a name="scheduling-and-sequential-copy"></a>Agendamento e cópia sequencial
Confira [Agendamento e execução](data-factory-scheduling-and-execution.md) para obter informações detalhadas sobre como funcionam o agendamento e a execução no Data Factory. É possível executar várias operações de cópia, uma após a outra de maneira sequencial/ordenada. Veja a seção [Copiar sequencialmente](data-factory-scheduling-and-execution.md#run-activities-in-a-sequence).

## <a name="type-conversions"></a>Conversões de tipo
Armazenamentos de dados diferentes têm sistemas de tipo nativo diferentes. A Atividade de Cópia executa conversões de tipo automáticas dos tipos de origem para os tipos de coletor a seguinte abordagem de duas etapas:

1. Converter de tipos de origem nativos em um tipo .NET.
2. Converter de um tipo .NET em um tipo de coletor nativo.

O mapeamento de um sistema de tipo nativo para o tipo .NET do armazenamento de dados está no respectivo artigo sobre armazenamento de dados. (Clique no link específico da tabela [Repositórios de dados com suporte](#supported-data-stores) ). Você pode usar esses mapeamentos para determinar os tipos apropriados ao criar tabelas para que a Atividade de Cópia execute as conversões certas.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a Atividade de Cópia, consulte [Copiar dados de armazenamento de Blobs do Azure para o Banco de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Para saber mais sobre como mover dados de um repositório de dados local para um repositório de dados na nuvem, confira [Mover dados de repositórios de dados locais para repositórios de dados na nuvem](data-factory-move-data-between-onprem-and-cloud.md).

