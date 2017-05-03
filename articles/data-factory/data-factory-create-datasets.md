---
title: Criar conjuntos de dados no Azure Data Factory | Microsoft Docs
description: Aprenda a criar conjuntos de dados na Azure Data Factory com exemplos que usam propriedades como offset e anchorDateTime.
keywords: criar conjunto de dados, exemplo de conjunto de dados, exemplo de deslocamento
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: e0c999b2bf1dd38d8a0c99c6cdd4976cc896dd99
ms.openlocfilehash: 88e653f6e46f3e8eb72e620b495d1769f17bdfbf
ms.lasthandoff: 04/20/2017


---
# <a name="datasets-in-azure-data-factory"></a>Conjuntos de dados no Azure Data Factory
Este artigo descreve os conjuntos de dados na Azure Data Factory e inclui exemplos como bancos de dados de deslocamento, anchorDateTime e deslocamento/estilo.

> [!NOTE]
> Se você ainda não conhece o Azure Data Factory, veja [Introdução ao Azure Data Factory](data-factory-introduction.md) para obter uma visão geral do serviço Azure Data Factory. Se você não tiver experiência prática com a criação de data factories, ler o [tutorial de transformação de dados](data-factory-build-your-first-pipeline.md) e/ou [tutorial de movimentação de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ajudará a entender melhor este artigo. 

## <a name="overview"></a>Visão geral
Uma fábrica de dados pode ter um ou mais pipelines. Um **pipeline** é um agrupamento lógico de **atividades** que juntas executam uma tarefa. As atividades em um pipeline definem ações para executar em seus dados. Por exemplo, você pode usar uma atividade de cópia para copiar dados de um SQL Server local para um Armazenamento de Blobs do Azure. Em seguida, usar uma atividade Hive que executa um script Hive em um cluster HDInsight do Azure a fim de processar/transformar dados do armazenamento de blobs para gerar dados de saída. Por fim, usar uma segunda atividade de cópia para copiar os dados de saída em um SQL Data Warehouse do Azure sobre o qual as soluções de relatório de BI (business intelligence) são criadas. Para obter mais informações sobre pipelines e atividades, consulte [Pipelines e atividades no artigo Azure Data Factory](data-factory-create-pipelines.md).

Uma atividade pode não usar ou usar vários **conjuntos de dados** de entrada e gerar um ou mais conjuntos de dados de saída. Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline e um conjunto de dados de saída representa a saída da atividade. Conjuntos de dados identificam dados em armazenamentos de dados diferentes, como tabelas, arquivos, pastas e documentos. Por exemplo, um conjunto de dados de Blob do Azure especifica o contêiner de blobs e a pasta no Armazenamento de Blobs do Azure, de onde o pipeline deve ler os dados. 

Antes de criar um conjunto de dados, você precisa criar um **serviço vinculado** para vincular seu armazenamento de dados para o data factory. Serviços vinculados são como cadeias de conexão, que definem as informações de conexão necessárias para o Data Factory para se conectar a recursos externos. Conjuntos de dados identificam dados em armazenamentos de dados vinculados, como tabelas SQL, arquivos, pastas e documentos. Por exemplo, um serviço vinculado ao Armazenamento do Azure vincula uma conta de armazenamento do Azure ao data factory. Um conjunto de dados de Blob do Azure representa o contêiner de blob e a pasta que contém os blobs de entrada a ser processado. 

Aqui está um exemplo de cenário: para copiar dados de um armazenamento de BLOB do Azure para um banco de dados do SQL Azure, você cria dois serviços vinculados: banco de dados SQL e armazenamento do Azure. Em seguida, crie dois conjuntos de dados: conjunto de dados de Blob do Azure (refere-se ao serviço de armazenamento do Azure vinculado), o conjunto de dados de tabela do SQL Azure (refere-se ao serviço de banco de dados do SQL Azure vinculado). O armazenamento do Azure e serviços de banco de dados do SQL Azure vinculado contêm cadeias de caracteres de conexão que usa o Data Factory em tempo de execução para se conectar ao armazenamento do Azure e banco de dados do SQL Azure respectivamente. O conjunto de dados de Blob do Azure especifica o contêiner de blob e a pasta de blob que contém os blobs de entrada em seu armazenamento de BLOBs do Azure. O conjunto de dados de tabela do SQL Azure especifica a tabela do SQL no banco de dados SQL Azure para o qual os dados serão copiados.

O seguinte diagrama mostra a relação entre pipeline, atividade e conjunto de dados no Data Factory: 

![Relação entre pipeline, atividade e conjunto de dados, serviços vinculados](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Conjunto de dados do JSON
Um conjunto de dados no Azure Data Factory é definido no formato JSON, da seguinte maneira:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
       "policy":
        {      
        }
    }
}
```

A tabela a seguir descreve as propriedades no JSON acima:   

| Propriedade | Descrição | Obrigatório | Padrão |
| --- | --- | --- | --- |
| name |Nome do conjunto de dados. Confira [Azure Data Factory - Regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura. |Sim |ND |
| type |Tipo de conjunto de dados. Especifique um dos tipos com suporte no Azure Data Factory (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Confira [Tipo de conjunto de dados](#Type) para obter detalhes. |Sim |ND |
| estrutura |Esquema do conjunto de dados<br/><br/>Para obter detalhes, consulte a seção [Estrutura do Conjunto de Dados](#Structure) . |Não. |ND |
| typeProperties | As propriedades de tipo são diferentes para cada tipo (por exemplo: Blob do Azure, tabela do SQL Azure). Confira a seção [Tipo de conjunto de dados](#Type) para obter detalhes sobre os tipos com suporte e suas propriedades. |Sim |ND |
| externo | Sinalizador booliano para especificar se um conjunto de dados é explicitamente produzido por um pipeline de data factory ou não. Se o conjunto de dados de entrada para uma atividade não é produzido pelo pipeline atual, defina esse sinalizador como true. Defina esse sinalizador como true para o conjunto de dados de entrada da primeira atividade no pipeline.  |Não |false |
| disponibilidade | Define a janela de processamento (por hora, diária, etc.) ou o modelo de fatiamento para a produção de conjunto de dados. Cada unidade de dados consumida e produzida por uma execução de atividade é chamada de uma fatia de dados. Se a disponibilidade de um conjunto de dados de saída é definida como diária (frequência - Dia, intervalo - 1), uma fatia é produzida diariamente. <br/><br/>Confira [Disponibilidade do conjunto de dados](#Availability) para obter detalhes. <br/><br/>Para obter detalhes sobre o modelo de divisão do conjunto de dados, consulte o artigo [Agendamento e Execução](data-factory-scheduling-and-execution.md) . |Sim |ND |
| policy |Define os critérios ou a condição que as fatias de conjunto de dados devem atender. <br/><br/>Para obter detalhes, consulte a seção [Política do Conjunto de Dados](#Policy) . |Não |ND |

## <a name="dataset-example"></a>Exemplo de conjunto de dados
No exemplo a seguir, o conjunto de dados representa uma tabela chamada **MyTable** em um **banco de dados SQL do Azure**.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Observe os seguintes pontos:

* O tipo foi definido como AzureSqlTable.
* A propriedade de tipo tableName (específica do tipo AzureSqlTable) foi definida como MyTable.
* linkedServiceName se refere a um serviço vinculado do tipo AzureSqlDatabase, que é definido no seguinte trecho de JSON. 
* A frequência de disponibilidade foi definida como Dia e o intervalo foi definido como 1, o que significa que a fatia do conjunto de dados é produzida diariamente.  

AzureSqlLinkedService é definido da seguinte maneira:

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

No JSON acima:

* O tipo foi definido como AzureSqlDatabase
* A propriedade de tipo connectionString especifica informações para se conectar a um banco de dados SQL do Azure.  

Como você pode ver, o serviço vinculado define como se conectar a um banco de dados SQL do Azure. O conjunto de dados define qual tabela é usada como uma entrada/saída para a atividade em um pipeline.   

> [!IMPORTANT]
> A menos que um conjunto de dados seja produzido pela pipeline, ele deverá ser marcado como **externo**. Essa configuração geralmente se aplica às entradas da primeira atividade em um pipeline.   


## <a name="Type"></a> Tipo de conjunto de dados
O tipo do conjunto de dados depende do armazenamento de dados que você usa. Consulte a tabela a seguir para obter uma lista de armazenamentos de dados com suporte pelo Data Factory. Clique em um armazenamento de dados para saber como criar um serviço vinculado e um conjunto de dados para esse armazenamento de dados.

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Os repositórios de dados com * podem estar no local ou no Azure IaaS e exigem a instalação do [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) em um computador Azure IaaS/local.

No exemplo na seção anterior, o tipo do conjunto de dados é definido como **AzureSqlTable**. Da mesma forma, um conjunto de dados de Blob do Azure, o tipo do conjunto de dados é definido como **AzureBlob** conforme mostrado no JSON a seguir:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

## <a name="Structure"></a>Estrutura do conjunto de dados
A seção **estrutura** é uma seção **opcional** que define o esquema do conjunto de dados. Ela contém uma coleção de nomes e tipos de dados das colunas. Você pode usar a seção de estrutura para fornecer informações do tipo que é usado para converter tipos e mapear colunas de origem para destino. No exemplo a seguir, o conjunto de dados tem três colunas: `slicetimestamp`, `projectname` e `pageviews`, sendo dos seguintes tipos: String, String e Decimal, respectivamente.

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Cada coluna da seção Estrutura contém as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| name |Nome da coluna. |Sim |
| type |Tipo de dados da coluna.  |Não |
| culture |Cultura baseada em .NET a ser usada quando o tipo é especificado e é o tipo .NET `Datetime` ou `Datetimeoffset`. O padrão é `en-us`. |Não |
| formato |O formato de cadeia de caracteres a ser usado quando o tipo é especificado e é o tipo .NET: `Datetime` ou `Datetimeoffset`. |Não |

Use as diretrizes a seguir referentes a quando incluir informações de "estrutura" e o que incluir na seção **estrutura**.

* **Para fontes de dados estruturados** que armazenam as informações sobre o esquema e o tipo junto com os dados em si (origens como SQL Server, Oracle, tabela do Azure etc.), especifique a seção "estrutura" apenas se quiser mapear colunas de origem para colunas no coletor e seus nomes não forem iguais. 
  
    Como as informações de tipo já estão disponíveis para fontes de dados estruturadas, não inclua informações de tipo quando você incluir a seção "estrutura".
* **Para esquema de fontes de dados de leitura (especificamente o blob do Azure)**, você pode optar por armazenar os dados sem armazenar nenhuma informação de tipo ou esquema juntamente com esses dados. Para esses tipos de fontes de dados, inclua "estrutura" quando quiser mapear colunas de origem para colunas do coletor (ou) quando o conjunto de dados for um conjunto de dados de entrada para uma atividade de cópia e os tipos de dados do conjunto de dados de origem precisar ser convertido em tipos nativos para o coletor. 
    
    O Data Factory dá suporte aos valores de tipo baseados em .NET compatíveis com CLS a seguir, para fornecer informações de tipo em "estrutura" para fontes de dados em que o esquema é definido na leitura, assim como o blob do Azure: Int16, Int32, Int64, Single, Double, Decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset, Timespan.

O Data Factory executa conversões de tipo automaticamente ao mover dados de um armazenamento de dados de origem para um armazenamento de dados do coletor. 
  

## <a name="Availability"></a> Disponibilidade do conjunto de dados
A seção **disponibilidade** em um conjunto de dados define a janela de processamento (por hora, diária, semanal etc.) para o conjunto de dados. Consulte o artigo [Agendamento e Execução](data-factory-scheduling-and-execution.md) para obter mais informações sobre a janela de atividades.

A seção de disponibilidade a seguir especifica que o conjunto de dados de saída é produzido por hora (ou) o conjunto de dados de entrada está disponível por hora:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

Se o pipeline tem os seguintes horários de início e término:  

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

O conjunto de dados de saída é gerado de hora em hora, dentro dos horários de início e término do pipeline. Portanto, cinco fatias de conjunto de dados são geradas por este pipeline, uma para cada janela de atividade (12:00 - 1:00, 1:00 - 2:00, 2:00 - 3:00, 3:00 - 4:00, 4:00 - 5:00). 

A tabela a seguir descreve as propriedades que você pode usar na seção de disponibilidade:

| Propriedade | Descrição | Obrigatório | Padrão |
| --- | --- | --- | --- |
| frequência |Especifica a unidade de tempo para a produção da fatia de conjunto de dados.<br/><br/><b>Frequência com suporte</b>: Minuto, Hora, Dia, Semana, Mês |Sim |ND |
| intervalo |Especifica um multiplicador para frequência<br/><br/>"Intervalo de frequência x" determina a frequência com que a fatia é produzida.<br/><br/>Se você precisa que o conjunto de dados seja dividido por hora, defina <b>Frequência</b> como <b>Hora</b> e <b>intervalo</b> como <b>1</b>.<br/><br/><b>Observação:</b>: caso você especifique a frequência como minuto, recomendamos que defina o intervalo como não inferior a 15 |Sim |ND |
| estilo |Especifica se a fatia deve ser produzida no início/término do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se a frequência for definida como Mês e o estilo como EndOfInterval, a fatia será produzida no último dia do mês. Se o estilo for definido como StartOfInterval, a fatia será produzida no primeiro dia do mês.<br/><br/>Se a frequência for definida como Dia e o estilo como EndOfInterval, a fatia será produzida na última hora do dia.<br/><br/>Se a Frequência for definida como Hora e o estilo como EndOfInterval, a fatia será produzida ao final da hora. Por exemplo, para uma fatia de período 13h – 14h, a fatia é produzida às 14h. |Não |EndOfInterval |
| anchorDateTime |Define a posição absoluta no tempo usada pelo agendador para computar limites de fatia do conjunto de dados. <br/><br/><b>Observação:</b> se AnchorDateTime tiver partes de datas mais granulares do que a frequência, as partes mais granulares serão ignoradas. <br/><br/>Por exemplo, se o <b>intervalo</b> for <b>por hora</b> (frequência: hora e intervalo: 1) e o <b>AnchorDateTime</b> contiver <b>minutos e segundos</b>, as partes <b>minutos e segundos</b> do AnchorDateTime serão ignoradas. |Não |01/01/0001 |
| deslocamento |O período de tempo no qual o início e o término de todas as fatias de conjunto de dados são deslocados. <br/><br/><b>Observação:</b> se anchorDateTime e o deslocamento forem especificados, o resultado será um deslocamento combinado. |Não |ND |

### <a name="offset-example"></a>exemplo de deslocamento
Por padrão, fatias (`"frequency": "Day", "interval": 1`) diárias começam em hora UTC de 12: 00 (meia-noite). Se desejar que a hora de início para hora UTC de 6 horas em vez disso, define o deslocamento, conforme mostrado no trecho a seguir: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>Exemplo de anchorDateTime
No exemplo a seguir, o conjunto de dados é gerado uma vez a cada 23 horas. Primeira fatia começa no momento especificado pelo anchorDateTime, que é definido como `2017-04-19T08:00:00` (hora UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Exemplo de deslocamento/estilo
O conjunto de dados a seguir é um conjunto de dados mensal e é produzido no dia 3 de cada mês, às 8:00 h (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00",    
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>Política de conjunto de dados
A seção **política** na definição do conjunto de dados define os critérios ou a condição que as divisões de conjunto de dados devem atender.

### <a name="validation-policies"></a>Políticas de validação
| Nome da política | Descrição | Aplicado a | Obrigatório | Padrão |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Valida que os dados em um **blob do Azure** atendem aos requisitos de tamanho mínimo (em megabytes). |blob do Azure |Não |ND |
| minimumRows |Valida que os dados em um **Banco de Dados SQL do Azure** ou uma **tabela do Azure** contêm o número mínimo de linhas. |<ul><li>Banco de Dados SQL do Azure</li><li>tabela do Azure</li></ul> |Não |ND |

#### <a name="examples"></a>Exemplos
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Conjuntos de dados externos
Conjuntos de dados externos são aqueles que não são produzidos por um pipeline em execução na data factory. Se o conjunto de dados estiver marcado como **external**, a política **ExternalData** poderá ser definida para influenciar o comportamento da disponibilidade da divisão do conjunto de dados.

A menos que um conjunto de dados seja produzido pela Azure Data Factory, ele deverá ser marcado como **externo**. Essa configuração geralmente se aplica às entradas da primeira atividade em um pipeline, a menos que uma atividade ou pipeline encadeamento esteja sendo usado.

| name | Descrição | Obrigatório | Valor Padrão |
| --- | --- | --- | --- |
| dataDelay |Tempo para esperar a verificação na disponibilidade dos dados externos de uma determinada divisão. Por exemplo, se os dados estiverem disponíveis por hora, a verificação para ver se os dados externos estão disponíveis e se a fatia correspondente está Pronta pode ser atrasada usando dataDelay.<br/><br/>Aplica-se apenas à hora atual.  Por exemplo, se agora forem 13hs e se esse valor for 10 minutos, a validação começará às 13:10hs.<br/><br/>Essa configuração não afeta fatias no passado (fatias com hora de término da fatia + dataDelay < Agora são processadas sem demora).<br/><br/>Um horário superior a 23:59 horas precisa ser especificado usando o formato `day.hours:minutes:seconds`. Por exemplo, para especificar 24 horas, não use 24:00:00; em vez disso, use 1.00:00:00. Se você usar 24:00:00, isso será tratado como 24 dias (24.00:00:00). Para 1 dia e 4 horas, especifique 1:04:00:00. |Não |0 |
| retryInterval |O tempo de espera entre uma falha e a próxima tentativa de repetição. Aplica-se a hora atual. Se o anterior falhou, a próxima tentativa ocorre após o período de retryInterval. <br/><br/>Se agora for 13h, iniciaremos a primeira tentativa. Se a duração para concluir a primeira verificação de validação for 1 minuto e a operação tiver falhado, a próxima repetição será 1:00 + 1 min (duração) + 1min (intervalo de repetição) = 13h02. <br/><br/>Para fatias no passado, não haverá nenhum atraso. A repetição acontece imediatamente. |Não |00:01:00 (1 minuto) |
| retryTimeout |O tempo limite para cada tentativa de repetição.<br/><br/>Se essa propriedade for definida para 10 minutos, a validação precisará ser concluída em 10 minutos. Se demorar mais de 10 minutos para executar a validação, a repetição atingirá o tempo limite.<br/><br/>Se todas as tentativas para a validação excederem o tempo limite, a fatia será marcada como TimedOut. |Não |00:10:00 (10 minutos) |
| maximumRetry |Número de vezes para verificar a disponibilidade dos dados externos. O valor máximo permitido é 10. |Não |3 |


## <a name="create-datasets"></a>Criar conjuntos de dados
Você pode criar conjuntos de dados usando uma destas ferramentas ou SDKs. 

- Assistente de Cópia. 
- Portal do Azure
- Visual Studio
- Azure PowerShell
- Modelo do Azure Resource Manager
- API REST
- API do .NET

Confira os tutoriais a seguir para obter instruções passo a passo para criar pipelines e conjuntos de dados usando uma destas ferramentas ou SDKs.
 
- [Crie um pipeline com uma atividade de transformação de dados](data-factory-build-your-first-pipeline.md)
- [Crie um pipeline com uma atividade de movimentação de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Quando um pipeline é criado/implantado, você pode gerenciar e monitorar seus pipelines usando as folhas do portal do Azure ou Monitorar e Gerenciar Aplicativos. Confira os tópicos a seguir para obter instruções passo a passo. 

- [Monitorar e gerenciar os pipelines usando as folhas do portal do Azure](data-factory-monitor-manage-pipelines.md).
- [Monitorar e gerenciar pipelines usando Monitorar e Gerenciar Aplicativos](data-factory-monitor-manage-app.md)


## <a name="scoped-datasets"></a>Conjuntos de dados com escopo
Você pode criar conjuntos de dados que estão no escopo de um pipeline usando a propriedade **datasets** . Esses conjuntos de dados só podem ser usados por atividades dentro deste pipeline, e não por atividades em outros pipelines. O exemplo a seguir define um pipeline com dois conjuntos de dados - InputDataset-rdc e OutputDataset-rdc - a serem usados no pipeline:  

> [!IMPORTANT]
> Há suporte apenas para conjuntos de dados com escopo com pipelines avulsos (pipelineMode definido como OneTime). Confira [Pipeline avulso](data-factory-scheduling-and-execution.md#onetime-pipeline) para obter detalhes.
>
>

```json
{
    "name": "CopyPipeline-rdc",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações sobre pipelines, consulte o artigo [Criar pipelines](data-factory-create-pipelines.md). 
- Para obter mais informações sobre como os pipelines são agendados e executados, confira o artigo [Agendamento e execução no Azure Data Factory](data-factory-scheduling-and-execution.md). 
