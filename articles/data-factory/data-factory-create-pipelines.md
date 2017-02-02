---
title: Criar/agendar pipelines, atividades em cadeia no Data Factory | Microsoft Docs
description: "Aprenda a criar um pipeline de dados no Azure Data Factory para mover e transformar dados. Crie um fluxo de trabalho orientado por dados para produção pronto para usar informações."
keywords: pipeline de dados, fluxo de trabalho controlados por dados
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 13b137c7-1033-406f-aea7-b66f25b313c0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d841c57dc736f7d690a6dc97863b7568365fd01a


---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipelines e atividades no Azure Data Factory
Este artigo o ajuda a compreender pipelines e atividades no Azure Data Factory e a usá-los para construir fluxos de trabalho orientados a dados de ponta a ponta para seus cenários de movimentação e processamento de dados.  

> [!NOTE]
> Esse artigo pressupõe que você tenha lido os artigos [Introdução ao Azure Data Factory](data-factory-introduction.md). Se você não tiver experiência prática com a criação de data factories, o tutorial [Compilar sua primeira data factory](data-factory-build-your-first-pipeline.md) o ajudará a entender melhor este artigo.  
> 
> 

## <a name="what-is-a-data-pipeline"></a>O que é um pipeline de dados?
**Pipeline** é um agrupamento de **atividades** relacionadas logicamente. Ele é usado para agrupar atividades em uma unidade que executa uma tarefa. Para entender melhor os pipelines, você precisa entender uma atividade primeiro. 

## <a name="what-is-an-activity"></a>O que é uma atividade?
As Atividades definem as ações a serem realizadas em seus dados. Cada atividade leva zero ou mais [conjuntos de dados](data-factory-create-datasets.md) como entrada e produz um ou mais conjuntos de dados como saída. 

Por exemplo, você pode usar uma atividade de Cópia para orquestrar a cópia de dados de um repositório de dados para outro. Da mesma forma, você pode usar uma atividade do HDInsight Hive para executar uma consulta de Hive em um cluster do Azure HDInsight para transformar seus dados. O Azure Data Factory fornece uma ampla gama de atividades de [movimentação de dados](data-factory-data-movement-activities.md) e [transformação de dados](data-factory-data-transformation-activities.md). Você também pode optar por criar uma atividade personalizada do .NET para executar seu próprio código. 

## <a name="sample-copy-pipeline"></a>Pipeline de cópia de exemplo
No pipeline de exemplo a seguir, há uma atividade do tipo **Cópia** in the **atividades** . Neste exemplo, a [Atividade de cópia](data-factory-data-movement-activities.md) copia dados de um Armazenamento de Blobs do Azure para um banco de dados SQL do Azure. 

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
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

Observe os seguintes pontos:

* Na seção de atividades, há apenas uma atividade cujo **tipo** é definido como **Copy**.
* A entrada da atividade é definida como **InputDataset** e a saída da atividade é definida como **OutputDataset**.
* Na seção **typeProperties**, **BlobSource** é especificado como o tipo de origem e **SqlSink** é especificado como o tipo de coletor.

Para obter uma explicação completa da criação desse pipeline, confira [Tutorial: copiar dados de Armazenamento de Blobs para o Banco de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

## <a name="sample-transformation-pipeline"></a>Pipeline de transformação de exemplo
No pipeline de exemplo a seguir, há uma atividade do tipo **HDInsightHive** in the **atividades** . Neste exemplo, a [atividade de Hive do HDInsight](data-factory-hive-activity.md) transforma os dados de um Armazenamento de Blobs do Azure executando um arquivo de script do Hive em um cluster Hadoop do HDInsight do Azure. 

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
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
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

Observe os seguintes pontos: 

* Na seção de atividades, há apenas uma atividade cujo **tipo** é definido como **HDInsightHive**.
* O arquivo de script do Hive, **partitionweblogs.hql**, é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService chamado **AzureStorageLinkedService**) e na pasta **script** no contêiner **adfgetstarted**.
* A seção **defines** é usada para especificar as configurações de tempo de execução passadas para o script do hive como valores de configuração do Hive (por exemplo, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

Para obter uma explicação completa da criação desse pipeline, confira [Tutorial: criar seu primeiro pipeline para processar dados usando o cluster Hadoop](data-factory-build-your-first-pipeline.md). 

## <a name="chaining-activities"></a>Encadeando atividades
Se você tiver várias atividades em um pipeline e se a saída de uma atividade não for uma entrada de outra, as atividades poderão ser executadas em paralelo se as fatias de dados de entrada das atividades estiverem prontas. 

É possível encadear duas atividades fazendo com que o conjunto de dados de saída de uma atividade seja o conjunto de dados de entrada da outra atividade. As atividades podem estar no mesmo pipeline ou em pipelines diferentes. A segunda atividade é executada apenas quando a primeira é concluída com êxito. 

Por exemplo, considere o seguinte caso:

1. O pipeline P1 contém a Atividade A1, que exige o conjunto de dados de entrada externa D1, e produz o conjunto de dados de **saída** **D2**.
2. O pipeline P2 contém a Atividade A2, que exige a **entrada** do conjunto de dados **D2**, e produz o conjunto de dados de saída D3.

Nesse cenário, a atividade A1 é executada quando os dados externos estão disponíveis e a frequência de disponibilidade agendada é alcançada.  A atividade A2 é executada quando as fatias agendadas de D2 ficam disponíveis e a frequência de disponibilidade agendada é alcançada. Se houver um erro em uma das fatias no conjunto de dados D2, A2 não será executada para essa fatia até que fique disponível.

Exibição de Diagrama:

![Encadeando atividades em dois pipelines](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Exibição de Diagrama com ambas as atividades no mesmo pipeline: 

![Encadeando atividades no mesmo pipeline](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

Para saber mais, confira [agendamento e execução](#chaining-activities). 

## <a name="scheduling-and-execution"></a>agendamento e execução
Até agora você entendeu o que são pipelines e atividades. Você também viu como eles são definidos e uma exibição de alto nível das atividades no Azure Data Factory. Agora, vamos dar uma olhada em como eles são executados. 

Um pipeline está ativo somente entre sua hora de início e de término. Ele não é executado antes da hora de início ou após a hora de término. Se o pipeline for pausado, ele não será executado independentemente da sua hora de início e término. Para um pipeline ser executado, ele não deve estar pausado. Na verdade, não é o pipeline que é executado. São as atividades no pipeline que são executadas. Entretanto, elas fazem isso no contexto geral do pipeline. 

Confira [Agendamento e Execução](data-factory-scheduling-and-execution.md) para saber como funciona o agendamento e a execução no Azure Data Factory.

## <a name="create-pipelines"></a>Criar pipelines
O Azure Data Factory fornece vários mecanismos para criar e implantar pipelines (que, por sua vez, contêm uma ou mais atividades neles). 

### <a name="using-azure-portal"></a>Usando o Portal do Azure
Você pode usar o editor do Data Factory no portal do Azure para criar um pipeline. Confira [Introdução ao Azure Data Factory (Editor do Data Factory)](data-factory-build-your-first-pipeline-using-editor.md) para obter uma explicação de ponta a ponta. 

### <a name="using-visual-studio"></a>Como usar o Visual Studio
Você pode usar o Visual Studio para criar e implantar pipelines no Azure Data Factory. Confira [Introdução ao Azure Data Factory (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) para obter uma explicação de ponta a ponta. 

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure
Você pode usar o Azure PowerShell para criar pipelines no Azure Data Factory. Digamos que você definiu o pipeline JSON em um arquivo em c:\DPWikisample.json. Você pode carregá-lo na sua instância do Azure Data Factory, conforme mostrado no seguinte exemplo:

```PowerShell
New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json
```

Confira a [Introdução ao Azure Data Factory (Azure PowerShell)](data-factory-build-your-first-pipeline-using-powershell.md) para obter um passo a passo de ponta a ponta para criar uma data factory com um pipeline. 

### <a name="using-net-sdk"></a>Usando o SDK .NET
Você também pode criar e implantar o pipeline usando SDK .NET. Esse mecanismo pode ser utilizado para criar pipelines programaticamente. Para saber mais, confira [Criar, gerenciar e monitorar data factories programaticamente](data-factory-create-data-factories-programmatically.md). 

### <a name="using-azure-resource-manager-template"></a>Usar o modelo do Azure Resource Manager
É possível criar e implantar o pipeline usando um modelo do Azure Resource Manager. Para saber mais, confira a [Introdução ao Azure Data Factory (Azure Resource Manager)](data-factory-build-your-first-pipeline-using-arm.md). 

### <a name="using-rest-api"></a>Usando a API REST
Você também pode criar e implantar o pipeline usando APIs REST. Esse mecanismo pode ser utilizado para criar pipelines programaticamente. Para saber mais, confira [Criar ou atualizar um pipeline](https://msdn.microsoft.com/library/azure/dn906741.aspx). 

## <a name="monitor-and-manage-pipelines"></a>Monitorar e gerenciar pipelines
Quando um pipeline é implantado, você pode gerenciar e monitorar seu pipeline, divisões e execuções. Leia mais sobre isso aqui: [Monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md).

## <a name="pipeline-json"></a>Pipeline de JSON
Vamos dar uma olhada mais próxima em como um pipeline é definido no formato JSON. A estrutura genérica para um pipeline será semelhante ao seguinte:

```json
{
    "name": "PipelineName",
    "properties": 
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>"
    }
}
```

A seção **Atividades** pode ter uma ou mais atividades definidas dentro dela. Cada atividade tem a seguinte estrutura de nível superior:

```json
{
    "name": "ActivityName",
    "description": "description", 
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    }
    "scheduler":
    {
    }
}
```

A tabela a seguir descreve as propriedades nas definições de JSON de atividade e pipeline:

| Marca | Descrição | Obrigatório |
| --- | --- | --- |
| name |Nome da atividade ou pipeline. Especifique um nome que representa a ação que a atividade ou o pipeline é configurado para executar<br/><ul><li>Número máximo de caracteres: 260</li><li>Deve começar com uma letra, um número ou um sublinhado (_)</li><li>Os seguintes caracteres não são permitidos: “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\\”</li></ul> |Sim |
| Descrição |Texto que descreve para que a atividade ou o pipeline é usado |Sim |
| type |Especifica o tipo da atividade. Confira os artigos [Atividades de movimentação de dados](data-factory-data-movement-activities.md) e [Atividades de transformação de dados](data-factory-data-transformation-activities.md) para diferentes tipos de atividade. |Sim |
| inputs |Tabelas de entrada utilizadas pela atividade<br/><br/>// uma tabela de entrada<br/>"inputs":  [ { "name": "inputtable1"  } ],<br/><br/>// duas tabelas de entrada <br/>"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ], |Sim |
| outputs |Tabelas de saída usadas pela atividade.// Uma tabela de saída.<br/>"outputs":  [ { "name": “outputtable1” } ],<br/><br/>//duas tabelas de saída<br/>"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ], |Sim |
| linkedServiceName |Nome do serviço vinculado usado pela atividade. <br/><br/>Uma atividade pode exigir que você especifique o serviço vinculado que é vinculado ao ambiente de computação necessário. |Sim para Atividade do HDInsight e Atividade de Pontuação de Lote do Aprendizado de Máquina do Azure  <br/><br/>Não para todas as outros |
| typeProperties |As propriedades na seção typeProperties dependem do tipo de atividade. |Não |
| policy |Políticas que afetam o comportamento de tempo de execução da atividade. Se não for especificado, as políticas padrão serão utilizadas. |Não |
| iniciar |Data e hora de início para o pipeline. Deve estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. <br/><br/>É possível especificar uma hora local, por exemplo, EST. Aqui está um exemplo: "2016-02-27T06:00:00**-05:00**", que é 6 AM EST.<br/><br/>As propriedades de início e término especificam o período ativo para o pipeline. Fatias de saída são produzidas somente nesse período ativo. |Não<br/><br/>Se você especificar um valor para a propriedade final, será necessário especificar um valor para a propriedade inicial.<br/><br/>Os horários de início e fim podem estar vazios para criar um pipeline. Você deve especificar ambos os valores para definir um período ativo de execução do pipeline. Se não especificar os horários de início e fim ao criar um pipeline, você poderá defini-los depois usando o cmdlet Set-AzureRmDataFactoryPipelineActivePeriod. |
| end |Data e hora de término para o pipeline. Se especificado, deve estar no formato ISO. Por exemplo: 2014-10-14T17:32:41Z  <br/><br/>É possível especificar uma hora local, por exemplo, EST. Aqui está um exemplo: "2016-02-27T06:00:00**-05:00**", que é 6 AM EST.<br/><br/>Para executar o pipeline indefinidamente, especifique 9999-09-09 como o valor da propriedade end. |Não <br/><br/>Se você especificar um valor para a propriedade inicial, será necessário especificar um valor para a propriedade final.<br/><br/>Confira as observações para a propriedade **iniciar** . |
| isPaused |Se definido como verdadeiro, o pipeline não é executado. Valor padrão = falso. Você pode usar essa propriedade para habilitar ou desabilitar. |Não |
| agendador |A propriedade "scheduler" é usada para definir o agendamento desejado para a atividade. Suas sub-propriedades são aquelas na [propriedade de disponibilidade em um conjunto de dados](data-factory-create-datasets.md#Availability). |Não |
| pipelineMode |O método de agendamento é executado para o pipeline. Os valores permitidos são: scheduled (padrão), onetime.<br/><br/>'Scheduled' indica que o pipeline será executado em um intervalo de tempo especificado de acordo com seu período ativo (hora de início e término). “Onetime” indica que o pipeline é executado apenas uma vez. Pipelines Onetime não podem ser modificados e atualizados depois de criados atualmente. Confira [Pipeline avulso](data-factory-scheduling-and-execution.md#onetime-pipeline) para obter detalhes sobre a configuração única. |Não |
| expirationTime |Duração de tempo após a criação pela qual o pipeline é válido e deve permanecer provisionado. Se não houver execuções ativas, com falha ou pendentes, o pipeline será excluído automaticamente depois de atingir o tempo de expiração. |Não |
| conjuntos de dados |Lista de conjuntos de dados a serem usados por atividades definidas no pipeline. Essa propriedade pode ser usado para definir conjuntos de dados que são específicos para este pipeline e não definido dentro da data factory. Conjuntos de dados definidos dentro este pipeline só podem ser usados por este pipeline e não podem ser compartilhados. Confira [Conjuntos de dados com escopo](data-factory-create-datasets.md#scoped-datasets) para obter detalhes. |Não |

### <a name="policies"></a>Políticas
As políticas afetam o comportamento de tempo de execução de uma atividade, especialmente quando a divisão de uma tabela é processada. A tabela a seguir fornece os detalhes.

| Propriedade | Valores permitidos | Valor Padrão | Descrição |
| --- | --- | --- | --- |
| simultaneidade |Inteiro  <br/><br/>Valor máximo: 10 |1 |Número de execuções simultâneas da atividade.<br/><br/>Determina o número de execuções de atividade paralela que podem ocorrer em divisões diferentes. Por exemplo, se uma atividade precisa passar por um grande conjunto de dados disponíveis, ter um valor de concorrência maior acelera o processamento de dados. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Determina a ordem das divisões de dados que estão sendo processadas.<br/><br/>Por exemplo, se houver duas fatias (uma ocorre às 16h e a outra às 17h),e ambas estiverem com a execução pendente. Se você definir executionPriorityOrder como NewestFirst, a divisão às 17h será processada primeiro. De modo semelhante, se você definir executionPriorityORder como OldestFIrst, a fatia às 16h será processada. |
| tentar novamente |Número inteiro<br/><br/>O valor máximo pode ser 10 |0 |Número de novas tentativas antes do processamento de dados da divisão ser marcado como Com falha. A execução da atividade para uma divisão de dados é repetida até a contagem de repetição especificada. A nova tentativa é feita logo após a falha. |
| Tempo limite |TimeSpan |00:00:00 |Tempo limite para a atividade. Exemplo: 00:10:00 (implica o tempo limite de 10 minutos)<br/><br/>Se um valor não for especificado ou for 0, o tempo limite será infinito.<br/><br/>Se o tempo de processamento de dados em uma divisão exceder o valor de tempo limite, ele será cancelado e o sistema tentará repetir o processamento. O número de repetições depende da propriedade de repetição. Quando atingir o tempo limite, o status será TimedOut. |
| atrasar |TimeSpan |00:00:00 |Especifique o atraso antes do processamento de dados da divisão começar.<br/><br/>A execução da atividade de uma fatia de dados será iniciada após o atraso passar do tempo de execução esperado.<br/><br/>Exemplo: 00:10:00 (implica um atraso de 10 minutos) |
| longRetry |Inteiro <br/><br/>Valor máximo: 10 |1 |O número de tentativas repetidas longas antes que a execução da divisão falhe.<br/><br/>Tentativas de longRetry são espaçadas por longRetryInterval. Portanto, se você precisar especificar um tempo entre tentativas de repetição, use longRetry. Se Retry e longRetry forem especificados, cada tentativa de longRetry incluirá tentativas de Retry, e o número máximo de tentativas será Retry * longRetry.<br/><br/>Por exemplo, se tivermos as seguintes configurações na política de atividade:<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Presumindo que haja apenas uma fatia para execução (o status é Aguardando) e a execução da atividade sempre falhe. Inicialmente haveria três tentativas consecutivas de execução. Após cada tentativa, o status de divisão seria Retry. Depois das três primeiras tentativas, o status da divisão seria LongRetry.<br/><br/>Depois de uma hora (ou seja, valor de longRetryInteval), deve haver outro conjunto de três tentativas consecutivas de execução. Depois disso, o status da divisão seria Com falha e não haveria nova tentativa. Portanto, em geral, foram feitas seis tentativas.<br/><br/>Se qualquer execução for bem-sucedida, o status da fatia seria Ready e não haverá mais nenhuma tentativa.<br/><br/>longRetry pode ser usado em situações em que dados dependentes chegam em horários não determinísticos ou o ambiente geral está instável onde o processamento de dados ocorre. Nesses casos, fazer novas tentativas uma após a outra pode não ajudar e fazer isso após um intervalo de tempo resulta na saída desejada.<br/><br/>Advertência: não defina valores altos para longRetry ou longRetryInterval. Normalmente, os valores mais altos implicam outros problemas sistêmicos. |
| longRetryInterval |TimeSpan |00:00:00 |O intervalo entre tentativas de repetição longa |

## <a name="next-steps"></a>Próximas etapas
* Conheça o [agendamento e execução no Azure Data Factory](data-factory-scheduling-and-execution.md).  
* Leia sobre o [movimento de dados](data-factory-data-movement-activities.md) e [recursos de transformação de dados](data-factory-data-transformation-activities.md) no Azure Data Factory
* Conheça o [gerenciamento e monitoramento no Azure Data Factory](data-factory-monitor-manage-pipelines.md).
* [Criar e implantar seu primeiro pipeline](data-factory-build-your-first-pipeline.md). 




<!--HONumber=Nov16_HO3-->


