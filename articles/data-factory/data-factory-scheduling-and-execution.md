---
title: "Agendamento e execução com o Data Factory | Microsoft Docs"
description: "Aprenda sobre os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: febc8fef864f88fa07accf91efc9b87727a48b32
ms.openlocfilehash: 8b1029075178fbc591645a5fd6a112ad0a7f8b86


---
# <a name="data-factory-scheduling-and-execution"></a>Agendamento e execução com o Data Factory
Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory. 

## <a name="prerequisites"></a>Pré-requisitos
Este artigo presume que você compreenda as noções básicas sobre conceitos de modelo de aplicativo do data factory, incluindo atividade, pipelines, serviços vinculados e conjuntos de dados. Para obter conceitos básicos do Azure Data Factory, consulte os seguintes artigos:

* [Introdução ao Data Factory](data-factory-introduction.md)
* [Pipelines](data-factory-create-pipelines.md)
* [Conjunto de dados](data-factory-create-datasets.md) 

## <a name="schedule-an-activity"></a>Agendar uma atividade
Na seção agendador da atividade JSON, você pode especificar um agendamento recorrente para uma atividade. Por exemplo, você pode agendar uma atividade a cada hora, da seguinte maneira:

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},  
```

![Exemplo do Agendador](./media/data-factory-scheduling-and-execution/scheduler-example.png)

Conforme mostrado no diagrama, especificar uma agenda para a atividade cria uma série de janelas em cascata. Janelas em cascata são uma série de intervalos de tempo de tamanho fixo, não sobrepostos e contínuos. Essas janelas lógicas em cascata para a atividade são chamadas de *janelas de atividade*.

Para a janela de atividade atualmente em execução, o intervalo de tempo associado à janela de atividade pode ser acessado com as variáveis de sistema [WindowStart](data-factory-functions-variables.md#data-factory-system-variables) e [WindowEnd](data-factory-functions-variables.md#data-factory-system-variables) na atividade JSON. Você pode usar essas variáveis para finalidades diferentes em sua atividade JSON. Por exemplo, você pode usá-las para selecionar dados em conjuntos de dados de entrada e saída que representem dados de série temporal.

A propriedade **agendador** dá suporte às mesmas subpropriedades que a propriedade **disponibilidade** em um conjunto de dados. Confira [Disponibilidade do conjunto de dados](data-factory-create-datasets.md#Availability) para obter detalhes. Exemplos: agendamento em um deslocamento de tempo específico, ou definir o modo para alinhar o processamento no início ou no fim do intervalo para a janela de atividade.

Você pode especificar propriedades **scheduler para uma** atividade, mas isso é **opcional**. Se você especificar uma propriedade, ela deverá corresponder à cadência que você especificar na definição do conjunto de dados de saída. Atualmente, o conjunto de dados de saída é o que aciona a agenda, então você deve criar um conjunto de dados de saída, mesmo que a atividade não produza qualquer saída. Se a atividade não receber entradas, ignore a criação de conjunto de dados de entrada.

## <a name="time-series-datasets-and-data-slices"></a>Conjuntos de dados de série temporal e fatias de dados
Dados de série temporal são uma sequência contínua de pontos de dados normalmente consistindo em sucessivas medidas feitas durante um intervalo de tempo. Exemplos comuns de dados de série temporal incluem dados de sensor e dados de telemetria de aplicativo.

Com o Data Factory, é possível processar dados de série temporal de maneira em lote com execuções da atividade. Normalmente, há uma cadência recorrentes na qual os dados de entrada chegam e os dados de saída devem ser produzidos. Essa cadência é modelada especificando a **availability** no conjunto de dados da seguinte maneira:

```json
"availability": {
  "frequency": "Hour",
  "interval": 1
},
```

Cada unidade de dados consumida e produzida por uma execução de atividade é chamada de uma fatia de dados. O diagrama a seguir mostra um exemplo de uma atividade com um conjunto de dados de entrada e um conjunto de dados de saída. Esses conjuntos de dados têm a **availability** definida para uma frequência horária.

![Agendador de disponibilidade](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

O diagrama anterior mostra as fatias de dados por hora para o conjunto de dados de entrada e saída. O diagrama mostra três fatias de entrada que estão prontas para processamento. A atividade de 10-11h está em andamento, produzindo a fatia de saída de 10-11h.

O intervalo de tempo associado à fatia atual que está sendo gerada pode ser acessado no conjunto de dados JSON com as variáveis [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) e [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables).

Atualmente, o Data Factory exige que a agenda especificada na atividade corresponda exatamente à agenda especificada na **disponibilidade** do conjunto de dados de saída. Portanto, **WindowStart**, **WindowEnd**, **SliceStart** e **SliceEnd** sempre são mapeados para o mesmo período de tempo e uma única fatia de saída.

Para saber mais sobre propriedades diferentes disponíveis para a seção disponibilidade, confira [Criando conjuntos de dados](data-factory-create-datasets.md).

## <a name="move-data-from-sql-database-to-blob-storage"></a>Mover dados do Banco de Dados SQL para o Armazenamento de Blobs
Vamos colocar tudo em ação criando um pipeline que copia dados de uma tabela do banco de dados SQL do Azure para o armazenamento de Blobs do Azure a cada hora.

**Entrada: conjunto de dados do banco de dados SQL do Azure**

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

A **frequência** é definida como **Hora** e o **intervalo** é definido como **1** na seção disponibilidade.

**Saída: conjunto de dados do armazenamento de Blobs do Azure**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
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
                        "format": "%M"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "%d"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "%H"
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

A **frequência** é definida como **Hora** e o **intervalo** é definido como **1** na seção disponibilidade.

**Atividade: atividade de cópia**

```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
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
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                }
            }
        ],
        "start": "2015-01-01T08:00:00Z",
        "end": "2015-01-01T11:00:00Z"
    }
}
```

O exemplo mostra a agenda de atividades e seções de disponibilidade do conjunto de dados definidas com uma frequência de hora em hora. O exemplo mostra como você pode usar **WindowStart** e **WindowEnd** para selecionar dados relevantes para uma atividade de execução e copiá-los para um blob com o **folderPath** apropriado. O **folderPath** é parametrizado para ter uma pasta separada para cada hora.

Quando três das fatias entre 8– 11h forem executadas e os dados no banco de dados SQL do Azure serão conforme a seguir:

![Entrada de exemplo](./media/data-factory-scheduling-and-execution/sample-input-data.png)

Após o pipeline ser implantado, o blob do Azure é populado da seguinte maneira:

* Arquivo mypath/2015/1/1/8/Data.&lt;Guid&gt;.txt com dados
    ```  
    10002345,334,2,2015-01-01 08:24:00.3130000
    10002345,347,15,2015-01-01 08:24:00.6570000
    10991568,2,7,2015-01-01 08:56:34.5300000
    ```
  
  > [!NOTE]
  > &lt;Guid&gt;; é substituído por um guid real. Exemplo de nome de arquivo: Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
  > 
  > 
* Arquivo mypath/2015/1/1/9/Data.&lt;Guid&gt;.txt com dados:

    ```json  
    10002345,334,1,2015-01-01 09:13:00.3900000
    24379245,569,23,2015-01-01 09:25:00.3130000
    16777799,21,115,2015-01-01 09:47:34.3130000
    ```
* Arquivo mypath/2015/1/1/10/Data.&lt;Guid&gt;.txt sem dados.

## <a name="active-period-for-pipeline"></a>Período ativo do pipeline
[Criando pipelines](data-factory-create-pipelines.md) introduziu o conceito de período ativo para um pipeline especificado definindo as propriedades **start** e **end**.

Você pode definir a data de início para o período ativo do pipeline no passado. O Data Factory calcula automaticamente (faz preenchimento de fundo) todas as fatias de dados no passado e começa a processá-las.

## <a name="parallel-processing-of-data-slices"></a>Processamento paralelo de fatias de dados
Você pode configurar as fatias de dados preenchidas para serem executadas em paralelo, definindo a propriedade **concurrency** na seção de política da atividade JSON. Para saber mais sobre essa propriedade, confira [Criando pipelines](data-factory-create-pipelines.md).

## <a name="rerun-a-failed-data-slice"></a>Executar novamente uma fatia de dados com falha
Você pode monitorar a execução de fatias em um formato visualmente sofisticado. Confira [Monitorar e gerenciar pipelines usando folhas do portal do Azure](data-factory-monitor-manage-pipelines.md) ou [aplicativo de monitoramento e gerenciamento](data-factory-monitor-manage-app.md) para obter detalhes.

Considere o exemplo a seguir, que mostra duas atividades. A Atividade1 produz um conjunto de dados de série temporal com fatias como saída, que é consumida como entrada pela Atividade2 para produzir o conjunto de dados de série temporal final de saída.

![Fatia com falha](./media/data-factory-scheduling-and-execution/failed-slice.png)

O diagrama mostra que, de três fatias recentes, houve uma falha ao produzir a fatia de 9-10h para Dataset2. O Data Factory controla automaticamente a dependência para o conjunto de dados de série temporal. Como resultado, ele não inicia a execução da atividade para a fatia downstream de 9-10h.

Ferramentas de gerenciamento e monitoramento do data factory permitem analisar os logs de diagnóstico da fatia com falha, para que você localize facilmente a causa raiz do problema e corrija-o. Depois de corrigir o problema, você pode iniciar facilmente a execução da atividade para produzir a fatia com falha. Para obter mais detalhes sobre como executar novas execuções e compreender as transições de estado para fatias de dados, confira [Monitorar e gerenciar pipelines usando folhas do portal do Azure](data-factory-monitor-manage-pipelines.md) ou [aplicativo de monitoramento e gerenciamento](data-factory-monitor-manage-app.md).

Após você executar novamente a fatia de 9-10h para o **Dataset2**, o Data Factory iniciará a execução da fatia dependente de 9-10h no conjunto de dados final.

![Executar novamente uma fatia com falha](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="run-activities-in-a-sequence"></a>Executar atividades em uma sequência
É possível encadear duas atividades (executar uma atividade após a outra) definindo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. As atividades podem estar no mesmo pipeline ou em pipelines diferentes. A segunda atividade é executada apenas quando a primeira é concluída com êxito.

Por exemplo, considere o seguinte caso:

1. O pipeline P1 contém a Atividade A1, que exige o conjunto de dados de entrada externa D1 e produz o conjunto de dados de saída D2.
2. O pipeline P2 contém a Atividade A2, que exige a entrada do conjunto de dados D2, e produz o conjunto de dados de saída D3.

Nesse cenário, as atividades A1 e A2 estão em diferentes pipelines. A atividade A1 é executada quando os dados externos estão disponíveis e a frequência de disponibilidade agendada é alcançada. A atividade A2 é executada quando as fatias agendadas de D2 ficam disponíveis e a frequência de disponibilidade agendada é alcançada. Se houver um erro em uma das fatias no conjunto de dados D2, A2 não será executada para essa fatia até que fique disponível.

O modo de exibição de Diagrama seria semelhante ao seguinte diagrama:

![Encadeando atividades em dois pipelines](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Conforme mencionado anteriormente, as atividades podem estar no mesmo pipeline. O modo de exibição de Diagrama com ambas as atividades no mesmo pipeline seria semelhante ao seguinte diagrama:

![Encadeando atividades no mesmo pipeline](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

### <a name="copy-sequentially"></a>Copiar sequencialmente
É possível executar várias operações de cópia, uma após a outra de maneira sequencial/ordenada. Por exemplo, você pode ter duas atividades de cópia em um pipeline (CopyActivity1 e CopyActivity2) com os seguintes conjuntos de dados de saída dos dados de entrada:   

CopyActivity1

Entrada: Dataset. Saída: Dataset2.

CopyActivity2

Entrada: Dataset2.  Saída: Dataset3.

CopyActivity2 seria executado somente se CopyActivity1 tivesse sido executado com êxito e Dataset2 estivesse disponível.

Veja o exemplo de JSON do pipeline:

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

Observe que no exemplo, o conjunto de dados de saída da primeira atividade de cópia (Dataset2) é especificado como entrada para a segunda atividade. Portanto, a segunda atividade será executada somente quando o conjunto de dados de saída da primeira atividade estiver pronto.  

No exemplo, CopyActivity2 pode ter uma entrada diferente como Dataset3, mas você especifica Dataset2 como uma entrada para CopyActivity2 para que a atividade não seja executada até que CopyActivity1 seja concluída. Por exemplo:

CopyActivity1

Entrada: Dataset1. Saída: Dataset2.

CopyActivity2

Entradas: Dataset3, Dataset2. Saída: Dataset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

Observe que no exemplo, dois conjuntos de dados de entrada são especificados para a segunda atividade de cópia. Quando várias entradas forem especificadas, somente o primeiro conjunto de dados de entrada será usado para copiar dados, mas outros conjuntos de dados serão usados como dependências. CopyActivity2 começaria apenas quando as seguintes condições fossem atendidas:

* CopyActivity1 foi concluído com êxito e Dataset2 está disponível. Esse conjunto de dados não é usado ao copiar dados para Dataset4. Ele atua apenas como uma dependência de agendamento de CopyActivity2.   
* Dataset3 está disponível. Esse conjunto de dados representa os dados que são copiados para o destino.  

## <a name="model-datasets-with-different-frequencies"></a>Modelar conjuntos de dados com frequências diferentes
Nos exemplos, as frequências de conjuntos de dados de entrada e saída e a janela de agendamento de atividade foram iguais. Alguns cenários exigem a capacidade de produzir saída em uma frequência diferente de frequências de uma ou mais entradas. O Data Factory dá suporte à modelagem desses cenários.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Exemplo 1: gerar um relatório diário de saída para dados de entrada que estão disponíveis de hora em hora
Considere um cenário em que temos dados de medição de entrada de sensores disponíveis a cada hora no armazenamento de Blobs do Azure. Você deseja produzir um relatório diário de agregação com estatísticas, como média, máximo e mínimo para o dia com a [Atividade hive do Data Factory](data-factory-hive-activity.md).

Veja como você pode modelar esse cenário com o Data Factory:

**Conjunto de dados de entrada**

Os arquivos de entrada por hora são instalados na pasta para o dia determinado. A disponibilidade de entrada é definida por **Hora** (frequência: Hora, intervalo: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Conjunto de dados de saída**

Um arquivo de saída é criado diariamente na pasta do dia. A disponibilidade de saída é definida em **Dia** (frequência: Dia e intervalo: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Atividade: atividade de hive em um pipeline**

O script do hive recebe as informações de *DateTime* apropriadas como parâmetros que usam a variável **WindowStart** conforme mostrado no trecho de código a seguir. O script do hive usa essa variável para carregar os dados da pasta correta para o dia e executar a agregação para gerar a saída.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
            "inputs": [
                {
                    "name": "AzureBlobInput"
                }
            ],
            "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:%M}',WindowStart)",
                    "Day": "$$Text.Format('{0:%d}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

O diagrama a seguir mostra o cenário de um ponto de vista de dependência de dados.

![Dependência de dados](./media/data-factory-scheduling-and-execution/data-dependency.png)

A fatia de saída para cada dia depende de 24 fatias horárias do conjunto de dados de entrada. O Data Factory calcula essas dependências automaticamente descobrindo as fatias de dados de entrada que equivalem ao mesmo período de tempo utilizado para a produção da fatia de saída. Se qualquer uma das 24 fatias de entrada não estiver disponível, o Data Factory aguardará até que a fatia de entrada esteja pronta antes de iniciar a execução da atividade diária.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Exemplo 2: especificar dependência com expressões e funções do Data Factory
Vamos considerar outro cenário. Suponha que você tenha uma atividade de hive que processa dois conjuntos de dados de entrada. Um deles tem novos dados diariamente, mas o outro obtém novos dados toda semana. Suponha que você queira fazer uma associação entre as duas entradas e produzir uma saída diariamente.

A abordagem simples, na qual o Data Factory detecta automaticamente as fatias de entrada certas a serem processadas alinhando-se ao período de tempo da fatia de dados de saída, não funciona.

Você precisa especificar isso para cada execução de atividade, o Data Factory deve usar a fatia de dados da semana passada para o conjunto de dados de entrada semanal. Use as funções do Azure Data Factory conforme mostrado no trecho a seguir para implementar esse comportamento.

**Saída1: blob do Azure**

A primeira entrada é blob do Azure que está sendo atualizado diariamente.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Entrada2: blob do Azure**

Entrada2 é o blob do Azure que está sendo atualizado semanalmente.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Saída: blob do Azure**

Um arquivo de saída é criado diariamente na pasta para o dia. A disponibilidade de saída é definida como **dia** (frequência: Dia, intervalo: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Atividade: atividade de hive em um pipeline**

A atividade de hive usa as duas entradas e produz uma fatia de saída todos os dias. É possível especificar que a fatia de saída de cada dia dependa da fatia de entrada da semana anterior para a entrada semanal, como demonstrado a seguir.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:%M}',WindowStart)",
            "Day": "$$Text.Format('{0:%d}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

## <a name="data-factory-functions-and-system-variables"></a>Funções do Data Factory e variáveis do sistema
Veja [Funções e variáveis do sistema do Data Factory](data-factory-functions-variables.md) para obter uma lista das funções e variáveis às quais o Azure Data Factory dá suporte.

## <a name="data-dependency-deep-dive"></a>Grande aprofundamento em dependência de dados
Para gerar uma fatia do conjunto de dados por uma execução de atividade, o Data Factory usa o *modelo de dependência* a seguir para determinar as relações entre conjuntos de dados consumidos e produzidos por uma atividade.

O intervalo de tempo dos conjuntos de dados de entrada necessários para gerar a fatia do conjunto de dados de saída é chamado de *período de dependência*.

Executar uma atividade gera uma fatia do conjunto de dados somente depois que as fatias de dados nos conjuntos de dados de entrada dentro do período de dependência estão disponíveis. Em outras palavras, todas as fatias de entrada que compõem o período de dependência devem estar com status **Pronto** para que a execução da atividade produza uma fatia do conjunto de dados de saída.

Para gerar a fatia do conjunto de dados [**início**, **término**], uma função deve mapear a fatia do conjunto de dados para seu período de dependência. Essa função é essencialmente uma fórmula que converte o início e fim da fatia do conjunto de dados até o início e fim do período de dependência. Mais formalmente:

```
DatasetSlice = [start, end]
DependecyPeriod = [f(start, end), g(start, end)]
```

**F** e **g** são funções de mapeamento que calculam o início e término do período de dependência para cada entrada de atividade.

Como visto nos exemplos, o período de dependência é igual ao período da fatia de dados produzida. Nesses casos, o Data Factory calcula automaticamente as fatias de entrada que se enquadram no período de dependência.  

Por exemplo: no exemplo de agregação, em que a saída é produzida diariamente e dados de entrada estão disponíveis a cada hora, o período da fatia de dados é de 24 horas. O Data Factory localiza as fatias de entrada de hora em hora relevantes para esse período de tempo e torna a fatia de saída dependente da fatia de entrada.

Você também pode fornecer seu próprio mapeamento para o período de dependência, como mostrado no exemplo em que uma das entradas é semanal e o intervalo de saída é gerado diariamente.

## <a name="data-dependency-and-validation"></a>Validação e dependência de dados
Um conjunto de dados pode ter uma política de validação definida que especifica como os dados gerados pela execução de uma fatia podem ser validados antes de estarem prontos para consumo. Veja [Criando conjuntos de dados](data-factory-create-datasets.md) para obter detalhes.

Nesses casos, quando a execução da fatia tiver terminado, o status da fatia de saída será alterado para **Aguardando** com um substatus de **Validação**. Depois que as fatias são validadas, o status de fatia é alterado para **Pronto**.

Se uma fatia de dados foi produzida mas não passou na validação, as execuções de atividade para fatias downstream que dependem dessa fatia não são processadas.

[Monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md) .

## <a name="external-data"></a>Dados externos
Um conjunto de dados pode ser marcado como externo (como mostrado no trecho de JSON a seguir), indicando que não foi gerado com o Data Factory. Nesse caso, a política de conjunto de dados pode ter um conjunto de parâmetros que descrevem uma política de validação e repetição de tentativas para o conjunto de dados. Veja [Criando pipelines](data-factory-create-pipelines.md) para obter uma descrição de todas as propriedades.

Semelhante a conjuntos de dados que são produzidos pelo Data Factory, as fatias de dados para dados externos precisam estar prontas para que fatias dependentes possam ser processadas.

```json
{
    "name": "AzureSqlInput",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1     
        },
        "external": true,
        "policy":
        {
            "externalData":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }  
    }
}
```
## <a name="onetime-pipeline"></a>Pipeline Onetime
Você pode criar e agendar um pipeline para ser executado periodicamente (por exemplo: horário ou diário) dentro dos horários inicial e final que você especificar na definição do pipeline. Veja [Agendando atividades](#scheduling-and-execution) para obter detalhes. Você também pode criar um pipeline que executa apenas uma vez. Para fazer isso, defina a propriedade **pipelineMode** na definição do pipeline para **onetime** conforme mostrado no exemplo de JSON a seguir. O valor padrão dessa propriedade é **scheduled**.

```json
{
    "name": "CopyPipeline",
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
                        "name": "InputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ]
                "name": "CopyActivity-0"
            }
        ]
        "pipelineMode": "OneTime"
    }
}
```

Observe o seguinte:

* As horas de **início** e **término** para o pipeline não são especificadas.
* A **disponibilidade** de conjuntos de dados de entrada e saída é especificada (**frequência** e **intervalo**), mesmo que os valores não sejam usados pelo Data Factory.  
* A exibição de diagrama não mostra pipelines únicos. Este comportamento ocorre por design.
* Pipelines avulsos não podem ser atualizados. É possível clonar um pipeline único, renomeá-lo, atualizar suas propriedades e implantá-lo para criar outro.




<!--HONumber=Nov16_HO3-->


