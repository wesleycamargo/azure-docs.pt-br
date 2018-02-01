---
title: "Gatilhos e execução de pipeline no Azure Data Factory | Microsoft Docs"
description: "Este artigo fornece informações sobre como executar um pipeline no Azure Data Factory sob demanda ou criando um gatilho."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2018
ms.author: shlo
ms.openlocfilehash: fc34cfbab796c6e1e4cd25ce13dcc63c39c6699d
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2018
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Gatilhos e execução de pipeline no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [Versão 1 – já disponível](v1/data-factory-scheduling-and-execution.md)
> * [Versão 2 – Versão prévia](concepts-pipeline-execution-triggers.md)

Uma _execução de pipeline_ é um termo na versão 2 do Azure Data Factory que define uma instância de uma execução de pipeline. Por exemplo, digamos que você tem um pipeline que é executado às 8h, 9h e 10h. Nesse caso, haveria três execuções de pipeline separadas. Cada execução de pipeline tem uma ID de execução de pipeline exclusiva, que é um GUID que define exclusivamente essa execução do pipeline específica. As execuções de pipeline normalmente são instanciadas por meio da passagem de argumentos para parâmetros que são definidos nos pipelines. Há duas maneiras de executar um pipeline: manualmente ou usando um _gatilho_. Este artigo fornece detalhes sobre as duas maneiras de executar um pipeline.

> [!NOTE]
> Este artigo aplica-se ao Azure Data Factory versão 2, que atualmente está em versão prévia. Se estiver usando o Azure Data Factory versão 1, que está em GA (disponibilidade geral), consulte [Agendamento e execução no Azure Data Factory versão 1](v1/data-factory-scheduling-and-execution.md).

## <a name="manual-execution-on-demand"></a>Execução manual (sob demanda)
A execução manual de um pipeline é também conhecida como execução _sob demanda_.

Por exemplo, digamos que você tem um pipeline simples denominado **copyPipeline** que deseja executar. O pipeline tem uma única atividade que copia de uma pasta de origem no Armazenamento de Blobs do Azure para uma pasta de destino no mesmo armazenamento. A definição de JSON a seguir mostra esse pipeline de exemplo:

```json
{
  "name": "copyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "sourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}
```

Na definição JSON, o pipeline usa dois parâmetros: **sourceBlobContainer** e **sinkBlobContainer**. Você passa valores para esses parâmetros em tempo de execução.

Você pode executar o pipeline manualmente usando os seguintes métodos:
- O SDK do .NET.
- O módulo do Azure PowerShell.
- A API REST.
- O SDK do Python.

### <a name="the-rest-api"></a>A API REST
O comando de exemplo abaixo mostra como executar o pipeline manualmente usando a API REST:  

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```

Para obter um exemplo completo, confira o [Início Rápido: criar um data factory usando a API REST](quickstart-create-data-factory-rest-api.md).

### <a name="azure-powershell"></a>Azure PowerShell
O comando de exemplo abaixo mostra como executar o pipeline manualmente usando o Azure PowerShell:

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

Você passa parâmetros no corpo no conteúdo da solicitação. No SDK do .NET, no Azure Powershell e no SDK do Python, você passa valores em um dicionário que é passado como um argumento para a chamada:

```json
{
  “sourceBlobContainer”: “MySourceFolder”,
  “sinkBlobCountainer”: “MySinkFolder”
}
```

O conteúdo da resposta é uma ID exclusiva do pipeline de execução:

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```

Para obter um exemplo completo, confira o [Início Rápido: criar um data factory usando o Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="the-net-sdk"></a>O SDK do .NET
A chamada de exemplo abaixo mostra como executar o pipeline manualmente usando o SDK do .NET:

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

Para obter um exemplo completo, confira o [Início Rápido: criar um data factory usando o SDK do .NET](quickstart-create-data-factory-dot-net.md).

> [!NOTE]
> Você pode usar a SDK .NET para invocar os pipelines do Azure Data Factory a partir do Azure Functions, de seus próprios serviços Web, etc.

<h2 id="triggers">Execução do gatilho</h2>
Os gatilhos são a segunda maneira de realizar uma execução de pipeline. Os gatilhos representam uma unidade de processamento que determina quando uma execução de pipeline precisa ser inicializada. Atualmente, o Azure Data Factory dá suporte a dois tipos de gatilhos:
- Gatilho de agenda: um gatilho que invoca um pipeline em um agendamento de relógio.
- Gatilho de janela em cascata: um gatilho que opera em um intervalo periódico enquanto também mantém o estado. O Azure Data Factory atualmente não dá suporte a gatilhos com base em eventos. Por exemplo, o gatilho para um pipeline que responde a um evento de entrada de arquivo.

Pipelines e gatilhos têm uma relação de muitos para muitos. Vários gatilhos podem disparar um único pipeline, ou um único gatilho pode disparar vários pipelines. Na definição de gatilho a seguir, a propriedade **pipelines** se refere a uma lista de canais que são disparados pelo gatilho específico. A definição da propriedade inclui valores para os parâmetros de pipeline.

### <a name="basic-trigger-definition"></a>Definição básica do gatilho

```json
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {
            …
        },
        "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                          "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
            }
        ]
    }
```

## <a name="schedule-trigger"></a>Gatilho de agendamento
Um gatilho de agendamento executa pipelines em um agendamento de hora de relógio. Esse gatilho dá suporte às opções de calendário periódicas e avançadas. Por exemplo, os gatilho dá suporte a intervalos como "semanalmente" ou "Segunda-feira às 17h e quinta-feira às 21h”. O gatilho de agendamento é flexível porque o padrão de conjunto de dados é independente e o gatilho não distingue entre os dados de série temporal e não temporal.

Para saber mais sobre os gatilhos de agendamento e obter exemplos, confira [Criar um gatilho de agendamento](how-to-create-schedule-trigger.md).

## <a name="tumbling-window-trigger"></a>Gatilho de janela em cascata
Os gatilhos de janela em cascata são um tipo de gatilho acionado em um intervalo de tempo periódico a partir de uma hora de início especificada, enquanto mantém o estado. As janelas em cascata são uma série de intervalos de tempo de tamanho fixo, não sobrepostos e contíguos. Para saber mais sobre gatilhos de janela em cascata e obter exemplos, confira [Criar um gatilho de janela em cascata](how-to-create-tumbling-window-trigger.md).

## <a name="schedule-trigger-definition"></a>Definição do gatilho de agenda
Quando você cria um gatilho de agenda, especifica o agendamento e a recorrência usando uma definição JSON. 

Para que o gatilho de agendamento dispare uma execução de pipeline, inclua uma referência de pipeline do pipeline específico na definição do gatilho. Pipelines e gatilhos têm uma relação de muitos para muitos. Vários gatilhos podem disparar um único pipeline. Um único gatilho pode disparar vários pipelines.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>,             // How often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": ": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ] 
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
> A propriedade **parameters** é uma propriedade obrigatória do elemento **pipelines**. Se o pipeline não usar nenhum parâmetro, será necessário incluir uma definição de JSON vazia na propriedade **parameters**.

### <a name="schema-overview"></a>Visão geral do esquema
A seguinte tabela fornece uma visão geral de alto nível dos principais elementos de esquema relacionados à recorrência e ao agendamento de um gatilho:

| Propriedade JSON | DESCRIÇÃO |
|:--- |:--- |
| **startTime** | Um valor de Data/Hora. Para agendamentos simples, o valor da propriedade **startTime** aplica-se à primeira ocorrência. Para agendamentos complexos, o gatilho não é iniciado antes do valor de **startTime** especificado. |
| **endTime** | A data e a hora de início do gatilho. O gatilho não é executado após a data e a hora de término especificadas. O valor da propriedade não pode estar no passado. <!-- This property is optional. --> |
| **timeZone** | O fuso horário. Atualmente, há suporte apenas para o fuso horário UTC. |
| **recurrence** | Um objeto de recorrência que especifica as regras de recorrência para o gatilho. O objeto de recorrência dá suporte aos elementos **frequency**, **interval**l, **endTime**, **count** e **schedule**. Quando um objeto de recorrência é definido, o elemento **frequency** é obrigatório. Os outros elementos do objeto de recorrência são opcionais. |
| **frequency** | A unidade de frequência com a qual o gatilho se repete. Os valores com suporte incluem “minute”, “hour”, “day”, “week” e “month”. |
| **interval** | Um inteiro positivo que indica o intervalo para o valor de **frequency**, que determina a frequência com a qual o gatilho é executado. Por exemplo, se **interval** for 3 e **frequency** é "week", o gatilho é repetido a cada 3 semanas. |
| **schedule** | O agendamento de recorrência do gatilho. Um gatilho com um valor de **frequency** especificado altera sua recorrência com base em um agendamento de recorrência. A propriedade **schedule** contém modificações para a recorrência que se baseiam em minutos, horas, dias da semana, dias do mês e número da semana.

### <a name="schedule-trigger-example"></a>Exemplo de gatilho de elemento schedule

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-01T09:00:00-08:00",
                "endTime": "2017-11-02T22:00:00-08:00"
            }
        },
        "pipelines": [{
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToBlobPipeline"
                },
                "parameters": {}
            },
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToAzureSQLPipeline"
                },
                "parameters": {}
            }
        ]
    }
}
```

### <a name="schema-defaults-limits-and-examples"></a>Padrões, limites e exemplos de esquema

| Propriedade JSON | type | Obrigatório | Valor padrão | Valores válidos | Exemplo |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | Cadeia de caracteres | sim | Nenhum | Data e hora ISO 8601 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | Objeto | sim | Nenhum | Objeto de recorrência | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | Número | Não  | 1 | 1 a 1.000 | `"interval":10` |
| **endTime** | Cadeia de caracteres | sim | Nenhum | Um valor de Data/Hora que representa uma hora no futuro. | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | Objeto | Não  | Nenhum | Objeto Agendamento | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>Propriedade startTime
A seguinte tabela mostra como a propriedade **startTime** controla uma execução de gatilho:

| Valor startTime | Recorrência sem agendamento | Recorrência com agendamento |
|:--- |:--- |:--- |
| Hora de início no passado | Calcula a primeira hora de execução futura após a hora de início e executa nessa hora.<br/><br/>Executa as execuções seguintes com base no cálculo do tempo da última execução.<br/><br/>Consulte o exemplo após esta tabela. | O gatilho é iniciado _não antes da_ hora de início especificada. A primeira ocorrência baseia-se no agendamento calculado com base na hora de início.<br/><br/>Executa as execuções seguintes com base no agendamento de recorrência. |
| Hora de início no futuro ou no momento | É executado uma vez na hora de início especificada.<br/><br/>Executa as execuções seguintes com base no cálculo do tempo da última execução. | O gatilho é iniciado _não antes da_ hora de início especificada. A primeira ocorrência baseia-se no agendamento calculado com base na hora de início.<br/><br/>Executa as execuções seguintes com base no agendamento de recorrência. |

Vejamos um exemplo do que acontece quando a hora de início está no passado, com uma recorrência, mas sem agendamento. Suponha que a hora atual seja `2017-04-08 13:00`, a hora de início seja `2017-04-07 14:00` e a recorrência seja a cada dois dias. (O valor de **recurrence** é definido pela configuração da propriedade **frequency** como "day" e a propriedade **interval** como 2.) Observe que o valor de **startTime** está no passado e ocorre antes da hora atual.

Sob essas condições, a primeira execução será em `2017-04-09 at 14:00`. O mecanismo do Agendador calcula as ocorrências de execução desde a hora de início. As instâncias no passado serão descartadas. O mecanismo usa a próxima instância que ocorrer no futuro. Neste cenário, a hora de início é `2017-04-07 at 2:00pm` e, portanto, a próxima instância ocorrerá dois dias depois dessa hora, que será em `2017-04-09 at 2:00pm`.

A primeira hora de execução é igual, mesmo que o valor de **startTime** seja `2017-04-05 14:00` ou `2017-04-01 14:00`. Após a primeira execução, as execuções seguintes são calculadas com o uso do agendamento. Portanto, as execuções seguintes serão em `2017-04-11 at 2:00pm`, depois em `2017-04-13 at 2:00pm`, em `2017-04-15 at 2:00pm` e assim por diante.

Por fim, quando as horas ou os minutos não estiverem definidos no agendamento de um gatilho, as horas ou os minutos da primeira execução serão usados como padrões.

### <a name="schedule-property"></a>Propriedade schedule
Por um lado, o uso de um agendamento pode limitar o número de execuções de gatilho. Por exemplo, se um gatilho com uma frequência mensal é agendado para execução somente no dia 31, ele é executado apenas nos meses que têm o dia 31.

Enquanto que, um elemento schedule também pode expandir o número de execuções do gatilho. Por exemplo, um gatilho com uma frequência mensal agendado para execução nos dias 1 e 2 do mês é executado nos dias 1 e 2 do mês, em vez de uma vez por mês.

Se vários elementos **schedule** forem especificados, a ordem de avaliação será feita da maior configuração de agendamento para a menor. A avaliação começa com o número da semana e, em seguida, dia do mês, dia da semana, hora e, por fim, minuto.

A seguinte tabela descreve elementos **schedule** em detalhes:

| Elemento JSON | DESCRIÇÃO | Valores válidos |
|:--- |:--- |:--- |
| **minutos** | Minutos da hora em que o gatilho será executado. | <ul><li>Número inteiro</li><li>Matriz de inteiros</li></ul>
| **horas** | As horas do dia em que o gatilho será executado. | <ul><li>Número inteiro</li><li>Matriz de inteiros</li></ul> |
| **Dias da semana** | Dias da semana em que o gatilho é executado. O valor pode ser especificado apenas com uma frequência semanal. | <ul><li>Segunda-feira, Terça-feira, Quarta-feira, Quinta-feira, Sexta-feira, Sábado e Domingo</li><li>Matriz de valores de dia (o tamanho máximo da matriz é 7)</li><li>Os valores de dia não diferenciam maiúsculas de minúsculas</li></ul> |
| **monthlyOccurences** | Dias do mês em que o gatilho é executado. O valor pode ser especificado apenas com uma frequência mensal. | <ul><li>Matriz de objetos **monthlyOccurence**: `{ "day": day,  "occurrence": occurence }`.</li><li>O atributo **day** é o dia da semana no qual o gatilho é executado. Por exemplo, uma propriedade **monthlyOccurrences** com um valor de **day** igual a `{Sunday}` significa todo domingo do mês. O atributo **day** é obrigatório.</li><li>O atributo **occurrence** é a ocorrência de **day** especificado durante o mês. Por exemplo, uma propriedade **monthlyOccurrences** com os valores de **day** e **occurrence** iguais a `{Sunday, -1}` significa o último domingo do mês. O atributo **occurrence** é opcional.</li></ul> |
| **Dias do mês** | Dia do mês em que o gatilho é executado. O valor pode ser especificado apenas com uma frequência mensal. | <ul><li>Qualquer valor <= -1 e >= -31</li><li>Qualquer valor >= 1 e <= 31</li><li>Matriz de valores</li></ul> |

## <a name="examples-of-trigger-recurrence-schedules"></a>Exemplos de agendamentos de recorrência de gatilho
Esta seção fornece exemplos de agendamentos de recorrência e se concentra no objeto **schedule** e seus elementos.

Os exemplos pressupõem que o valor de **interval** seja 1 e que o valor de **frequency** esteja correto de acordo com a definição de agendamento. Por exemplo, não é possível ter um valor de **frequency** igual a "day" e também ter uma modificação de "monthDays" no objeto **schedule**. Restrições desse tipo são mencionadas na tabela na seção anterior.

| Exemplo | DESCRIÇÃO |
|:--- |:--- |
| `{"hours":[5]}` | Executar às 5h todos os dias. |
| `{"minutes":[15], "hours":[5]}` | Executar às 5h15 todos os dias. |
| `{"minutes":[15], "hours":[5,17]}` | Executar às 5h15 e 17h15 todos os dias. |
| `{"minutes":[15,45], "hours":[5,17]}` | Executar às 5h15, 5h45, 17h15 e 17h45 todos os dias. |
| `{"minutes":[0,15,30,45]}` | Executar a cada 15 minutos. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Executar a cada hora. Este gatilho é executado a cada hora. Os minutos são controlados pelo valor de **startTime**, quando um valor é especificado. Se um valor não for especificado, os minutos serão controlados pela hora de criação. Por exemplo, se a hora de início ou a hora de criação (o que for aplicável) for 12h25, o gatilho será executado às 24h25, 1h25, 2h25, … e 23h25.<br/><br/>Esse agendamento é equivalente a ter um gatilho com um valor de **frequency** igual a “hour”, um valor de **interval** igual a 1 e nenhum **schedule**. Esse agendamento pode ser usado com valores diferentes de **frequency** e **interval** para criar outros gatilhos. Por exemplo, quando o valor de **frequency** é “month”, o agendamento é executado somente uma vez por mês, em vez de todos os dias, quando o valor de **frequency** é “day”. |
| `{"minutes":[0]}` | Executar a cada hora na hora. Esse gatilho é executado a cada hora na hora, começando às 24h, 1h, 2h e assim por diante.<br/><br/>Esse agendamento é equivalente a um gatilho com um valor de **frequency** igual a “hour” e um valor de **startTime** igual a zero minutos ou nenhum **schedule**, mas um valor de **frequency** igual a “day”. Se o valor de **frequency** é "week" ou "month", o agendamento é executado somente um dia da semana ou um dia do mês, respectivamente. |
| `{"minutes":[15]}` | Executa 15 minutos após cada hora. Esse gatilho é executado a cada hora em 15 minutos após a hora, começando às 24h15, 1h15, 2h15 e assim por diante, terminando em 23h15. |
| `{"hours":[17], "weekDays":["saturday"]}` | Executar às 17h aos sábados toda semana. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Executar às 17h às segundas-feiras, quartas-feiras e sextas-feiras toda semana. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Executar às 17h15 e às 17h45 às segundas-feiras, quartas-feiras e sextas-feiras toda semana. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Executar a cada 15 minutos em dias da semana. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Executar a cada 15 minutos em dias da semana entre 9h e 16h45. |
| `{"weekDays":["tuesday", "thursday"]}` | Executar às terças-feiras e quintas-feiras na hora de início especificada. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Executar às 6h no 28º dia de cada mês (supondo um valor de **frequency** igual a “month”). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Executar às 6h no último dia do mês. Para executar um gatilho no último dia de um mês, use -1, em vez do dia 28, 29, 30 ou 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Executar às 6h no primeiro e último dia de cada mês. |
| `{monthDays":[1,14]}` | Executar no primeiro e no 14º dia de cada mês na hora de início especificada. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Executar na primeira sexta-feira de cada mês às 5h. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Executar na primeira sexta-feira de cada mês na hora de início especificada. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Executar na terceira sexta-feira do final do mês, todo mês, na hora de início especificada. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Executar na primeira e última sexta-feira de cada mês às 5h15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Executar na primeira e última sexta-feira de cada mês na hora de início especificada. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Executar na quinta sexta-feira de cada mês na hora de início especificada. Quando não houver nenhuma quinta sexta-feira em um mês, o pipeline não será executado, uma vez que ele está agendado para ser executado apenas nas quintas sextas-feiras. Para executar o gatilho na última sexta-feira do mês, considere o uso de -1, em vez de 5 para o valor de **occurrence**. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Executar a cada 15 minutos na última sexta-feira do mês. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Executar às 5h15, 5h45, 17h15 e 17h45 na terceira quarta-feira de cada mês. |

## <a name="trigger-type-comparison"></a>Comparação de tipo de gatilho
Tanto o gatilho de janela em cascata quanto o gatilho de agendamento operam sobre pulsações de tempo; logo, qual é a diferença entre eles?

A tabela a seguir fornece uma comparação entre o gatilho de janela em cascata e o gatilho de agenda:

|  | Gatilho de &nbsp;janela&nbsp; em cascata | &nbsp;Gatilho de agenda |
|:--- |:--- |:--- |
| **Cenários de&nbsp;aterramento** | Com suporte. As execuções de pipeline podem ser agendadas para janelas no passado. | Sem suporte. As execuções de pipeline podem ser executadas somente em períodos de tempo atuais e no futuro. |
| **Confiabilidade** | 100% de confiabilidade. As execuções de pipeline podem ser agendadas para todas as janelas de uma data de início especificada sem folgas. | Menos confiável. |
| **Recurso de&nbsp;repetição** | Com suporte. As execuções de pipeline com falha têm o padrão de política de repetição como 0, ou uma política especificada pelo usuário como parte da definição do gatilho. Repete automaticamente quando a execução do pipeline falha devido a limites de simultaneidade/servidor/limitação (ou seja, os códigos de status 400: erro de usuário, 429: muitas solicitações e 500: erro interno do servidor). | Sem suporte. |
| **Simultaneidade** | Com suporte. Os usuários podem definir explicitamente os limites de simultaneidade para o gatilho. Permite uma em um máximo de 50 execuções de pipeline disparadas simultâneas. | Sem suporte. |
| **System&nbsp;variables** | Dá suporte ao uso das variáveis de sistema **WindowStart** e **WindowEnd**. Os usuários podem acessar `triggerOutputs().windowStartTime` e `triggerOutputs().windowEndTime` como variáveis de sistema de gatilho na definição do gatilho. Os valores são usados como hora de início e hora de término da janela, respectivamente. Por exemplo, para um gatilho de janela em cascata que é executado a cada hora, para a janela de 1h a 2h, a definição é `triggerOutputs().WindowStartTime = 2017-09-01T01:00:00Z` e `triggerOutputs().WindowEndTime = 2017-09-01T02:00:00Z`. | Sem suporte. |
| **Relação Pipeline&#x2011;to&#x2011;trigger** | Dá suporte a uma relação um para um. Somente um pipeline pode ser disparado. | Dá suporte a relações muitos para muitos. Vários gatilhos podem disparar um único pipeline. Um único gatilho pode disparar vários pipelines. | 

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes tutoriais:

- [Início Rápido: criar um data factory usando o SDK do .NET](quickstart-create-data-factory-dot-net.md)
- [Criar um gatilho de agenda](how-to-create-schedule-trigger.md)
- [Criar um gatilho de janela em cascata](how-to-create-tumbling-window-trigger.md)
