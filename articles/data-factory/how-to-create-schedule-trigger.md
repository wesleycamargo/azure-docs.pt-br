---
title: Como criar gatilhos no Azure Data Factory | Microsoft Docs
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
ms.openlocfilehash: eed286c01604ab0e9ac2113d56cbce268503668d
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2017
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Como criar um gatilho que execute um pipeline em um agendamento
Este artigo fornece etapas para criar, iniciar e monitorar um gatilho. Para obter mais informações conceituais sobre gatilhos, consulte [Gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira [introdução ao Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="use-azure-powershell"></a>Usar PowerShell do Azure
Esta seção mostra como usar o Azure PowerShell para criar, iniciar e monitorar um gatilho. Se você quiser ver esse exemplo funcionando, primeiro acesse o [Início rápido: criar um data factory usando o Azure PowerShell](quickstart-create-data-factory-powershell.md). Em seguida, adicione o seguinte código ao método principal, que cria e inicia um gatilho de agenda que é executado a cada 15 minutos. O gatilho está associado a um pipeline (**Adfv2QuickStartPipeline**) que você cria como parte do início rápido.

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