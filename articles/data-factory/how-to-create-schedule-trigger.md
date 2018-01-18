---
title: Como criar gatilhos de agendamento no Azure Data Factory | Microsoft Docs
description: Saiba como criar um gatilho no Azure Data Factory que execute um pipeline em um agendamento.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: shlo
ms.openlocfilehash: 6b92e8402d372e29e264dc70b128124973d66bb9
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Como criar um gatilho que execute um pipeline em um agendamento
Este artigo fornece informações sobre o gatilho de agendamento e as etapas para criar, iniciar e monitorar um gatilho. Para outros tipos de gatilhos, consulte [Execução de pipeline e gatilhos](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira [introdução ao Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

### <a name="schedule-trigger-json-definition"></a>Definição JSON de gatilho de agendamento
Quando você cria um gatilho de agendamento, você pode especificar o agendamento e a recorrência usando JSON, conforme mostrado no exemplo nesta seção.

Para que o gatilho de agendamento dispare uma execução de pipeline, inclua uma referência de pipeline do pipeline específico na definição do gatilho. Pipelines e gatilhos têm uma relação de muitos para muitos. Vários gatilhos podem disparar um único pipeline. Um único gatilho pode disparar vários pipelines.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // optional, how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": : [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
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
>  A propriedade **parâmetros** é uma propriedade obrigatória dentro do **pipelines**. Mesmo que o pipeline não use nenhum parâmetro, inclua um json vazio para os parâmetros, já que a propriedade deve existir.


### <a name="overview-schedule-trigger-schema"></a>Visão geral: Esquema do gatilho de agendamento
A tabela a seguir fornece uma visão geral de alto nível dos principais elementos relacionados a recorrência e planejamento em um gatilho:

Propriedade JSON |     DESCRIÇÃO
------------- | -------------
startTime | startTime é uma Data/Hora. Para agendamentos simples, startTime é a primeira ocorrência. Para agendamentos complexos, o gatilho não inicia antes de startTime.
endTime | Especifica a data/hora de término para o gatilho. O gatilho não é executado após esse horário. Não é válido ter um endTime no passado. Este é um parâmetro opcional.
timeZone | No momento, apenas UTC tem suporte.
recurrence | O objeto recurrence especifica regras de recorrência para o gatilho. O objeto recurrence dá suporte aos elementos frequency, interval, endTime, count e schedule. Se recurrence for definido, frequency será necessário; os outros elementos de recurrence serão opcionais.
frequência | Representa a unidade de frequência com que o gatilho se repete. Os valores com suporte são: `minute`, `hour`, `day`, `week` ou `month`.
intervalo | O valor interval é um inteiro positivo. Ele denota o intervalo para a frequência que determina a periodicidade com que o gatilho é executado. Por exemplo, se interval for 3 e frequency for "week", o gatilho se repetirá a cada três semanas.
schedule | Um gatilho com uma frequência especificada altera sua recorrência com base em um agendamento de recorrência. Um elemento schedule contém as modificações com base em minutos, em horas, em dias da semana, em dias do mês e em número da semana.


### <a name="overview-schedule-trigger-schema-defaults-limits-and-examples"></a>Visão geral: Padrões, limites e exemplos de esquema de gatilho de agendador

Nome JSON | Tipo de valor | Obrigatório? | Valor padrão | Valores válidos | Exemplo
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | Cadeia de caracteres | Sim | Nenhum | Data e hora ISO 8601 | ```"startTime" : "2013-01-09T09:30:00-08:00"```
recurrence | Objeto | Sim | Nenhum | Objeto de recorrência | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
intervalo | Número | Não  | 1 | 1 a 1000. | ```"interval":10```
endTime | Cadeia de caracteres | Sim | Nenhum | Valor de data e hora que representa um momento no futuro | `"endTime" : "2013-02-09T09:30:00-08:00"`
schedule | Objeto | Não  | Nenhum | Objeto Agendamento | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

### <a name="deep-dive-starttime"></a>Análise aprofundada: startTime
A tabela a seguir mostra como startTime controla a execução de um gatilho:

Valor startTime | Recorrência sem agendamento | Recorrência com agendamento
--------------- | --------------------------- | ------------------------
Hora de início no passado | Calcula a primeira hora de execução futura após a hora de início e executa nessa hora.<p>Realiza as execuções subsequentes com base no cálculo do tempo da última execução.</p><p>Consulte o exemplo após esta tabela.</p> | O gatilho é iniciado _não antes da_ hora de início especificada. A primeira ocorrência baseia-se no elemento schedule calculado com base na hora de início. <p>Fazer as execuções subsequentes com base no agendamento de recorrência</p>
Hora de início no futuro ou no momento | Executa uma vez na hora de início especificada. <p>Realize as execuções subsequentes com base no cálculo do tempo da última execução.</p> | O gatilho é iniciado _não antes da_ hora de início especificada. A primeira ocorrência baseia-se no elemento schedule calculado com base na hora de início.<p>Realize as execuções subsequentes com base no agendamento de recorrência.</p>

Vejamos um exemplo do que acontece quando startTime está no passado, com recurrence, mas sem elemento schedule. Suponha que a hora atual seja `2017-04-08 13:00`, que startTime seja `2017-04-07 14:00` e que recurrence seja a cada dois dias (definida com frequency: day e interval: 2.) Observe que startTime está no passado e ocorre antes da hora atual.

Sob essas condições, a primeira execução será em `2017-04-09 at 14:00`. O mecanismo do Agendador calcula as ocorrências de execução desde a hora de início. As instâncias no passado serão descartadas. O mecanismo usa a próxima instância que ocorrer no futuro. Nesse caso, startTime é `2017-04-07 at 2:00pm`, portanto, a próxima instância ocorrerá dois dias depois desse momento, o que será em `2017-04-09 at 2:00pm`.

A primeira hora de execução é igual, mesmo que startTime seja `2017-04-05 14:00` ou `2017-04-01 14:00`. Após a primeira execução, as execuções subsequentes são calculadas usando o schedule. Portanto, elas são em `2017-04-11 at 2:00pm`, depois em `2017-04-13 at 2:00pm`, depois em `2017-04-15 at 2:00pm`, etc.

Finalmente, quando um gatilho tiver um agendamento, se as horas e/ou minutos não estiverem definidos no agendamento, serão adotadas as horas e/ou minutos da primeira execução, respectivamente.

### <a name="deep-dive-schedule"></a>Análise aprofundada: schedule
Por um lado, um elemento schedule pode limitar o número de execuções do gatilho. Por exemplo, se um gatilho com o elemento frequency definido como "month" tiver um schedule para ser executado somente no dia 31, o gatilho será executado apenas nos meses que têm o dia 31.

Enquanto que, um elemento schedule também pode expandir o número de execuções do gatilho. Por exemplo, se um gatilho com o elemento frequency definido como "month" tiver um agendamento para ser executado nos dias 1 e 2 do mês, o gatilho será executado no 1º e 2º dias do mês, em vez de apenas uma vez por mês.

Se forem especificados vários elementos schedule, a ordem de avaliação será do maior para o menor – número da semana, dia do mês, dia da semana, hora e minuto.

A tabela a seguir descreve elementos schedule em detalhes:


Nome JSON | DESCRIÇÃO | Valores Válidos
--------- | ----------- | ------------
minutes | Minutos da hora em que o gatilho será executado. | <ul><li>Número inteiro</li><li>Matriz de inteiros</li></ul>
hours | As horas do dia em que o gatilho será executado. | <ul><li>Número inteiro</li><li>Matriz de inteiros</li></ul>
weekDays | Dias da semana em que o gatilho será executado. Só pode ser especificado com uma frequência semanal. | <ul><li>Monday, Tuesday, Wednesday, Thursday, Friday, Saturday ou Sunday</li><li>Matriz de qualquer um dos valores (tamanho máximo da matriz: 7)</li></p>Não diferencia maiúsculas de minúsculas</p>
monthlyOccurrences | Determina em quais dias do mês o gatilho será executado. Só pode ser especificado com uma frequência mensal. | Matriz de objetos monthlyOccurence: `{ "day": day,  "occurrence": occurence }`. <p> O valor do elemento day é o dia da semana no qual o gatilho será executado, por exemplo, `{Sunday}` representa todos os domingos do mês. Obrigatório.<p>O valor do elemento ocurrence é a ocorrência do dia em questão no mês, por exemplo, `{Sunday, -1}` é o último domingo do mês. Opcional.
monthDays | Dia do mês em que o gatilho será executado. Só pode ser especificado com uma frequência mensal. | <ul><li>Qualquer valor <= -1 e >= -31</li><li>Qualquer valor >= 1 e <= 31</li><li>Uma matriz de valores</li>


## <a name="examples-recurrence-schedules"></a>Exemplos: agendamentos de recorrência
Esta seção fornece exemplos de agendamentos de recorrência focados no objeto schedule e seus subelementos.

Os agendamentos de exemplo pressupõem que o elemento interval esteja definido como 1. Além disso, pressuponha a frequência correta de acordo com o valor do elemento schedule (por exemplo, não é possível usar um valor "day" para o elemento frequency e realizar uma modificação a "monthDays" no elemento schedule). Essas restrições são mencionadas na tabela na seção anterior.

Exemplo | DESCRIÇÃO
------- | -----------
`{"hours":[5]}` | Executa às 5h todos os dias
`{"minutes":[15], "hours":[5]}` | Executa às 5:15 todos os dias
`{"minutes":[15], "hours":[5,17]}` | Executa às 5:15 e 17:15 todos os dias
`{"minutes":[15,45], "hours":[5,17]}` | Executa às 5h15, 5h45, 17h15 e 17h45 todos os dias
`{"minutes":[0,15,30,45]}` | Executa a cada 15 minutos
`{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Executa a cada hora. Este gatilho é executado a cada hora. O minuto será controlado por startTime se ele for especificado ou, se nenhum for especificado, será controlado pela hora de criação. Por exemplo, se a hora de início ou a hora de criação (o que for aplicável) for 12h25, o gatilho será executado às 0:25, 1:25, 2:25, …, 23:25. O schedule em questão é equivalente a ter um gatilho com frequency de "hour", interval de 1 e sem schedule. A diferença é que esse schedule também pode ser usado com valores diferentes de frequency e de interval para criar outros gatilhos. Por exemplo, se frequency fosse "month", o schedule seria executado somente uma vez por mês, em vez de todos os dias como ocorreria se frequency fosse "day".
`{"minutes":[0]}` | Executa a cada hora em hora exata. Esse gatilho também é executado a cada hora, mas na hora exata (por exemplo, 0h, 1h, 2h, etc.). Essa configuração equivale a um gatilho com frequency igual a "hour", startTime com zero minutos e sem schedule se frequency é "day", mas, sendo frequency igual a "week" ou "month", o schedule deve executar apenas uma vez por semana ou por mês, respectivamente.
`{"minutes":[15]}` | Executa 15 minutos após cada hora exata. É executado a cada hora, começando em 00:15, 1:15, 2:15, etc. e terminando às 22:15 e 23:15.
`{"hours":[17], "weekDays":["saturday"]}` | Executa às 17h aos sábados toda semana
`{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Executa às 17h às segundas-feiras, quartas-feiras e sextas-feiras toda semana
`{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Executa às 17h15 e 17h45 às segundas-feiras, quartas-feiras e sextas-feiras toda semana
`{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Executar a cada 15 minutos em dias da semana
`{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Executa a cada 15 minutos nos dias úteis entre 9h e 16h45
`{"weekDays":["tuesday", "thursday"]}` | Executa às terças e quintas-feiras na hora de início especificada.
`{"minutes":[0], "hours":[6], "monthDays":[28]}` | Executa às 6h no 28º dia de cada mês (supondo que o valor de frequency seja month)
`{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Executa às 6h no último dia do mês. Se você quiser executar um gatilho no último dia de um mês, use -1 em vez de dia 28, 29, 30 ou 31.
`{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Executa às 6h no primeiro e no último dias de cada mês
`{monthDays":[1,14]}` | Executa no primeiro e no décimo quarto dia de cada mês na hora de início especificada.
`{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Executa na primeira sexta-feira de cada mês às 5h
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Executa na primeira sexta-feira de cada mês na hora de início especificada.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Executa na terceira sexta-feira desde o final do mês, todo mês, na hora de início
`{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Executa na primeira e na última sextas-feiras de cada mês às 5h15
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Executa na primeira e na última sextas-feiras de cada mês na hora de início especificada
`{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Executa na quinta sexta-feira de cada mês na hora de início. Se não houver nenhuma quinta sexta-feira em um determinado mês, o pipeline não será executado, uma vez que está agendado para executar apenas nas quintas sextas-feiras.  Se você quiser executar o gatilho na última sexta-feira do mês, considere o uso de -1 em vez de 5 para a ocorrência.
`{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Executa a cada 15 minutos na última sexta-feira do mês.
`{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Executa às 5h15, 5h45, 17h15 e 17h45 na terceira quarta-feira de cada mês.


## <a name="use-azure-powershell"></a>Usar PowerShell do Azure
Esta seção mostra como usar o Azure PowerShell para criar, iniciar e monitorar um gatilho de agendador. Se você quiser ver esse exemplo funcionando, primeiro acesse o [Início rápido: criar um data factory usando o Azure PowerShell](quickstart-create-data-factory-powershell.md). Em seguida, adicione o seguinte código ao método principal, que cria e inicia um gatilho de agenda que é executado a cada 15 minutos. O gatilho está associado a um pipeline (**Adfv2QuickStartPipeline**) que você cria como parte do início rápido.

1. Crie um arquivo JSON denominado MyTrigger.json na pasta C:\ADFv2QuickStartPSH\ com o seguinte conteúdo:

    > [!IMPORTANT]
    > Definir **startTime** para a hora UTC atual e **endTime** para uma hora após a hora UTC atual antes de salvar o arquivo JSON.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    No trecho JSON:
    - O **tipo** do gatilho é definido como **ScheduleTrigger**.
    - A **frequência** é definida como **Minuto** e o **intervalo** é definido como **15**. Portanto, o gatilho executa o pipeline a cada 15 minutos entre os horários de início e término.
    - O **endTime** é de uma hora após o **startTime**, portanto, o gatilho executa o pipeline 15 minutos, 30 minutos e 45 minutos após o startTime. Não se esqueça de atualizar o startTime para a hora UTC atual e o endTime para uma hora após o startTime.  
    - O gatilho está associado ao pipeline **Adfv2QuickStartPipeline**. Para associar vários pipelines a um gatilho, adicione mais seções **pipelineReference**.
    - O pipeline no início rápido assume dois **parâmetros**. Portanto, você passa os valores para esses parâmetros a partir do gatilho.
2. Criar um gatilho usando o cmdlet **Set-AzureRmDataFactoryV2Trigger**.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
3. Confirme se o status do gatilho é **Parado** usando o cmdlet **Get-AzureRmDataFactoryV2Trigger**.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
4. Inicie o gatilho usando o cmdlet **Start-AzureRmDataFactoryV2Trigger**:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
5. Confirme se o status do gatilho é **Iniciado** usando o cmdlet **Get-AzureRmDataFactoryV2Trigger**.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
6.  Obtenha as execuções do gatilho usando o PowerShell, utiliznado o cmdlet **Get-AzureRmDataFactoryV2TriggerRun**. Para obter informações sobre a execução do gatilho, execute o seguinte comando periodicamente: valores de atualização **TriggerRunStartedAfter** e **TriggerRunStartedBefore** para corresponder aos valores na definição do gatilho.

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```

    Para monitorar execuções do gatilho/pipeline no portal do Azure, consulte [Monitorar execuções de pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-net-sdk"></a>Usar o SDK do .NET
Esta seção mostra como usar o SDK do .NET para criar, iniciar e monitorar um gatilho. Se você quiser ver esse código funcionando, primeiro acesse o [Início rápido para a criação do data factory usando o SDK do .NET](quickstart-create-data-factory-dot-net.md). Em seguida, adicione o seguinte código ao método principal, que cria e inicia um gatilho de agenda que é executado a cada 15 minutos. O gatilho está associado a um pipeline (**Adfv2QuickStartPipeline**) que você cria como parte do início rápido.

```csharp
            //create the trigger
            Console.WriteLine("Creating the trigger");

            // set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // set the start time to current UTC time and the end time to be one hour after.
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // now, create the trigger by invoking CreateOrUpdate method.
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            //start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);

```

Para monitorar uma execução de gatilho, adicione o seguinte código antes do último demonstrativo `Console.WriteLine`:

```csharp
            // Check the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Para monitorar execuções do gatilho/pipeline no portal do Azure, consulte [Monitorar execuções de pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-python-sdk"></a>Usar o SDK do Python
Esta seção mostra como usar o SDK do Python para criar, iniciar e monitorar um gatilho. Se você quiser ver esse código funcionando, primeiro acesse o [Início rápido para a criação do data factory usando o SDK do Python](quickstart-create-data-factory-python.md). Em seguida, adicione o bloco de código a seguir após o bloco de código "monitorar a execução do pipeline" no script de python. Esse código cria um gatilho de agenda que é executado a cada 15 minutos entre os horários de início e término especificados. Atualize o start_time para a hora UTC atual e o end_time para uma hora após a hora UTC atual.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```
Para monitorar execuções do gatilho/pipeline no portal do Azure, consulte [Monitorar execuções de pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-resource-manager-template"></a>Usar modelo do Resource Manager
Você pode usar um modelo do Azure Resource Manager para criar um gatilho. Para obter instruções detalhadas, consulte [Criar um data factory do Azure usando modelo do Gerenciador de Recursos](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Passe a hora de início do gatilho para um pipeline
Na versão 1, com suporte do Azure Data Factory ler ou gravar dados particionados usando variáveis de sistema SliceStart/SliceEnd/WindowStart/WindowEnd. Na versão 2, você pode obter esse comportamento usando um parâmetro de pipeline e de tempo/agendado a hora de início do gatilho como um valor do parâmetro. No exemplo a seguir, o horário agendado para o gatilho é passado como um valor para o parâmetro de pipeline scheduledRunTime.

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    
Para obter mais informações, consulte [Como ler e gravar dados de partição](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações detalhadas sobre gatilhos, consulte [Gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md#triggers).
