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
ms.date: 12/05/2016
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: 2ed6b838608f0f2249ef16b62ff2fb0159fc6e7f


---
# <a name="datasets-in-azure-data-factory"></a>Conjuntos de dados no Azure Data Factory
Este artigo descreve os conjuntos de dados na Azure Data Factory e inclui exemplos como bancos de dados de deslocamento, anchorDateTime e deslocamento/estilo.

Quando cria um conjunto de dados, você está criando um ponteiro para os dados que deseja processar. Os dados são processados (entrada/saída) em uma atividade e a atividade está contida em um pipeline. Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline e um conjunto de dados de saída representa a saída da atividade.

Conjuntos de dados identificam dados em armazenamentos de dados diferentes, como tabelas, arquivos, pastas e documentos. Depois de criar um conjunto de dados, você pode usá-lo com atividades no pipeline. Por exemplo, um conjunto de dados pode ser o conjunto de dados de entrada/saída de uma atividade de Cópia ou uma atividade do HDInsightHive. O portal do Azure oferece um layout visual de todos os seus pipelines e entradas e saídas de dados. Em um relance, você verá todas as relações e dependências de seus pipelines em todas as suas fontes, de modo que sempre pode saber de onde os dados são provenientes e para onde estão indo.

No Azure Data Factory, você pode obter dados de um conjunto de dados usando a atividade de cópia em um pipeline.

> [!NOTE]
> Se você ainda não conhece o Azure Data Factory, veja [Introdução ao Azure Data Factory](data-factory-introduction.md) para obter uma visão geral do serviço Azure Data Factory. Veja [Criar sua primeira data factory](data-factory-build-your-first-pipeline.md) para obter um tutorial para criar sua primeira data factory. Esses dois artigos fornecem as informações básicas necessárias para entender melhor este artigo.
>
>

## <a name="define-datasets"></a>Definir conjuntos de dados
Um conjunto de dados no Azure Data Factory é definido da seguinte maneira:

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
| estrutura |Esquema do conjunto de dados<br/><br/>Para obter detalhes, consulte a seção [Estrutura do Conjunto de Dados](#Structure) . |Nº |ND |
| typeProperties |Propriedades que correspondem ao tipo selecionado. Confira a seção [Tipo de conjunto de dados](#Type) para obter detalhes sobre os tipos com suporte e suas propriedades. |Sim |ND |
| externo |Sinalizador booliano para especificar se um conjunto de dados é explicitamente produzido por um pipeline de data factory ou não. |Não |false |
| disponibilidade |Define a janela de processamento ou o modelo de fatiamento para a produção de conjunto de dados. <br/><br/>Para obter detalhes, consulte a seção [Disponibilidade do Conjunto de Dados](#Availability) . <br/><br/>Para obter detalhes sobre o modelo de divisão do conjunto de dados, consulte o artigo [Agendamento e Execução](data-factory-scheduling-and-execution.md) . |Sim |ND |
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
* linkedServiceName refere-se a um serviço vinculado do tipo AzureSqlDatabase. Veja a definição do serviço vinculado a seguir.
* A frequência de disponibilidade foi definida como Dia e o intervalo foi definido como 1, o que significa que a fatia é produzida diariamente.  

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

Como você pode ver, o serviço vinculado define como se conectar a um banco de dados SQL do Azure. O conjunto de dados define qual tabela é usada como uma entrada/saída para a atividade em um pipeline. A seção de atividade no JSON do seu [pipeline](data-factory-create-pipelines.md) especifica se o conjunto de dados é usado como um conjunto de dados de entrada ou de saída.

> [!IMPORTANT]
> A menos que um conjunto de dados seja produzido pela Azure Data Factory, ele deverá ser marcado como **externo**. Essa configuração geralmente se aplica às entradas da primeira atividade em um pipeline.   
>
>

## <a name="a-nametypea-dataset-type"></a><a name="Type"></a> Tipo de conjunto de dados
As fontes de dados com suporte e os tipos de conjunto de dados são alinhados. Confira os tópicos citados no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para obter informações sobre os tipos e a configuração dos conjuntos de dados. Por exemplo, se você estiver usando dados de um Banco de Dados SQL do Azure, clique em Banco de Dados SQL do Azure na lista de repositórios de dados com suporte para ver informações detalhadas.  

## <a name="a-namestructureadataset-structure"></a><a name="Structure"></a>Estrutura do conjunto de dados
A seção **estrutura** define o esquema do conjunto de dados. Ela contém uma coleção de nomes e tipos de dados das colunas.  No exemplo a seguir, o conjunto de dados tem três colunas: slicetimestamp, projectname e pageviews, sendo dos seguintes tipos: String, String e Decimal, respectivamente.

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

## <a name="a-nameavailabilitya-dataset-availability"></a><a name="Availability"></a> Disponibilidade do conjunto de dados
A seção **disponibilidade** em um conjunto de dados define a janela de processamento (horário, diário, semanal etc.) ou o modelo de divisão do conjunto de dados. Consulte o artigo [Cronograma e Execução](data-factory-scheduling-and-execution.md) para obter mais detalhes sobre o modelo de divisão e dependência de conjunto de dados.

A seção de disponibilidade a seguir especifica que o conjunto de dados de saída é produzido por hora (ou) o conjunto de dados de entrada está disponível por hora:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

A tabela a seguir descreve as propriedades que você pode usar na seção de disponibilidade:

| Propriedade | Descrição | Obrigatório | Padrão |
| --- | --- | --- | --- |
| frequência |Especifica a unidade de tempo para a produção da fatia de conjunto de dados.<br/><br/>**Frequência com suporte**: Minuto, Hora, Dia, Semana, Mês |Sim |ND |
| intervalo |Especifica um multiplicador para frequência<br/><br/>"Intervalo de frequência x" determina a frequência com que a fatia é produzida.<br/><br/>Se você precisa que o conjunto de dados seja dividido por hora, defina **Frequência** como **Hora** e **intervalo** como **1**.<br/><br/>**Observação:** caso você especifique a frequência como minuto, recomendamos que defina o intervalo como não inferior a 15 |Sim |ND |
| estilo |Especifica se a fatia deve ser produzida no início/término do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se a frequência for definida como Mês e o estilo como EndOfInterval, a fatia será produzida no último dia do mês. Se o estilo for definido como StartOfInterval, a fatia será produzida no primeiro dia do mês.<br/><br/>Se a frequência for definida como Dia e o estilo como EndOfInterval, a fatia será produzida na última hora do dia.<br/><br/>Se a Frequência for definida como Hora e o estilo como EndOfInterval, a fatia será produzida ao final da hora. Por exemplo, para uma fatia de período 13h – 14h, a fatia é produzida às 14h. |Não |EndOfInterval |
| anchorDateTime |Define a posição absoluta no tempo usada pelo agendador para computar limites de fatia do conjunto de dados. <br/><br/>**Observação:** se AnchorDateTime tiver partes de datas mais granulares do que a frequência, as partes mais granulares serão ignoradas. <br/><br/>Por exemplo, se o **intervalo** for **por hora** (frequência: hora e intervalo: 1) e o **AnchorDateTime** contiver **minutos e segundos**, as partes **minutos e segundos** do AnchorDateTime serão ignoradas. |Não |01/01/0001 |
| deslocamento |O período de tempo no qual o início e o término de todas as fatias de conjunto de dados são deslocados. <br/><br/>**Observação:** se anchorDateTime e o deslocamento forem especificados, o resultado será um deslocamento combinado. |Não |ND |

### <a name="offset-example"></a>exemplo de deslocamento
Divisões diárias que iniciam às 6h, em vez da meia-noite do padrão.

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```

A **frequência** é definida para **Dia** e o **intervalo** é definido para **1** (uma vez por dia): se você quiser, a fatia será produzida às 6:00, em vez da hora padrão: 12:00. Lembre-se de que esse horário é UTC.

## <a name="anchordatetime-example"></a>Exemplo de anchorDateTime
**Exemplo:** fatias de conjuntos de dados de 23 horas que começam em 2007-04-19T08:00:00

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2007-04-19T08:00:00"    
}
```

## <a name="offsetstyle-example"></a>Exemplo de deslocamento/estilo
Se precisar de um conjunto de dados mensalmente em uma data e hora específicas (digamos que seja no dia 3 de cada mês às 8h), use a marcação **offset** para definir a data e a hora de execução.

```json
{
  "name": "MyDataset",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "availability": {
      "frequency": "Month",
      "interval": 1,
      "offset": "3.08:10:00",
      "style": "StartOfInterval"
    }
  }
}
```

## <a name="a-namepolicyadataset-policy"></a><a name="Policy"></a>Política de conjunto de dados
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
| dataDelay |Tempo para esperar a verificação na disponibilidade dos dados externos de uma determinada divisão. Por exemplo, se os dados tiverem de estar disponíveis por hora, a verificação para ver se os dados externos estão disponíveis e se a fatia correspondente está Pronta pode ser atrasada usando dataDelay.<br/><br/>Aplica-se apenas à hora atual.  Por exemplo, se agora forem 13hs e se esse valor for 10 minutos, a validação começará às 13:10hs.<br/><br/>Essa configuração não afeta fatias no passado (fatias com hora de término da fatia + dataDelay < Agora são processadas sem demora).<br/><br/>Um horário superior a 23:59 horas precisa ser especificado usando o formato dia.horas:minutos:segundos. Por exemplo, para especificar 24 horas, não use 24:00:00; em vez disso, use 1.00:00:00. Se você usar 24:00:00, isso será tratado como 24 dias (24.00:00:00). Para 1 dia e 4 horas, especifique 1:04:00:00. |Não |0 |
| retryInterval |O tempo de espera entre uma falha e a próxima tentativa de repetição. Aplica-se a hora atual. Se o anterior falhou, podemos esperar muito tempo após a última tentativa. <br/><br/>Se agora for 13h, iniciaremos a primeira tentativa. Se a duração para concluir a primeira verificação de validação for 1 minuto e a operação tiver falhado, a próxima repetição será 1:00 + 1 min (duração) + 1min (intervalo de repetição) = 13h02. <br/><br/>Para fatias no passado, não haverá nenhum atraso. A repetição acontece imediatamente. |Não |00:01:00 (1 minuto) |
| retryTimeout |O tempo limite para cada tentativa de repetição.<br/><br/>Se essa propriedade for definida para 10 minutos, a validação precisará ser concluída em 10 minutos. Se demorar mais de 10 minutos para executar a validação, a repetição atingirá o tempo limite.<br/><br/>Se todas as tentativas para a validação excederem o tempo limite, a fatia será marcada como TimedOut. |Não |00:10:00 (10 minutos) |
| maximumRetry |Número de vezes para verificar a disponibilidade dos dados externos. O valor máximo permitido é 10. |Não |3 |

## <a name="scoped-datasets"></a>Conjuntos de dados com escopo
Você pode criar conjuntos de dados que estão no escopo de um pipeline usando a propriedade **datasets** . Esses conjuntos de dados só podem ser usados por atividades dentro deste pipeline, e não por atividades em outros pipelines. O exemplo a seguir define um pipeline com dois conjuntos de dados - InputDataset-rdc e OutputDataset-rdc - a serem usados no pipeline:  

> [!IMPORTANT]
> Há suporte apenas para conjuntos de dados com escopo com pipelines avulsos (**pipelineMode** definido como **OneTime**). Confira [Pipeline avulso](data-factory-scheduling-and-execution.md#onetime-pipeline) para obter detalhes.
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


<!--HONumber=Nov16_HO3-->


