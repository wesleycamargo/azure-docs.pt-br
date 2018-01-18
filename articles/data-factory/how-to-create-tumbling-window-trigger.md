---
title: Como criar gatilhos de janela em cascata no Azure Data Factory | Microsoft Docs
description: Saiba como criar um gatilho no Azure Data Factory que execute um pipeline em uma janela em cascata.
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
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: a3b056ae4bb4eda26fec58ca3b6bed7f0744e36e
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Como criar um gatilho que execute um pipeline em uma janela em cascata
Este artigo fornece etapas para criar, iniciar e monitorar um gatilho de janela em cascata. Para obter informações gerais sobre gatilhos e os tipos com suporte, consulte [Gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira [introdução ao Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Os gatilhos de janela em cascata são um tipo de gatilho acionado em um intervalo de tempo periódico a partir de uma hora de início especificada, enquanto mantém o estado. As janelas em cascata são uma série de intervalos de tempo de tamanho fixo, não sobrepostos e contíguos. Um gatilho de janela em cascata tem uma relação de 1:1 com um pipeline, e só pode fazer referência a um único pipeline.

## <a name="tumbling-window-trigger-type-properties"></a>Propriedades do tipo de gatilho da janela em cascata
```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count":  <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "parameter1": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter2": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
                }
            }
      }    
}
```  

A tabela a seguir fornece uma visão geral de alto nível dos principais elementos relacionados a recorrência e planejamento em um gatilho de janela em cascata.

| **Nome JSON** | **Descrição** | **Valores permitidos** | **Obrigatório** |
|:--- |:--- |:--- |:--- |
| **tipo** | Tipo do gatilho. Isso é fixo como "TumblingWindowTrigger". | Cadeia de caracteres | Sim |
| **runtimeState** | <readOnly> Os valores possíveis: Iniciado, Interrompido, Desabilitado | Cadeia de caracteres | Sim, readOnly |
| **frequency** |A cadeia de caracteres *frequency* que representa a unidade de frequência com a qual o gatilho se repete. Os valores com suporte são "minutos" e "hora". Se a hora de início tiver partes de data mais granulares do que a frequência, elas serão levadas em consideração para calcular os limites da janela. Por exemplo: se a frequência for por hora, e a hora de início for 2016-04-01T10:10:10Z, a primeira janela será (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z.)  | Cadeia de caracteres. Os tipos com suporte são "minutos", "hora" | Sim |
| **interval** |O *interval* é um inteiro positivo e denota o intervalo para *frequency*, que determina a frequência de execução do gatilho. Por exemplo, se *interval* for três e *frequency* for "hour", o gatilho se repetirá a cada três horas. | Número inteiro | Sim |
| **startTime**|*startTime* é uma Data/Hora. *startTime* é a primeira ocorrência e pode estar no passado. O primeiro intervalo do gatilho será (startTime, startTime + intervalo). | Datetime | Sim |
| **endTime**|*endTime* é uma Data/Hora. *endTime* é a última ocorrência e pode estar no passado. | Datetime | Sim |
| **delay** | Especifique o atraso antes do processamento de dados da janela começar. A execução do pipeline é iniciada após o tempo de execução esperado + atraso. O atraso define quanto tempo o gatilho espera antes de disparar a nova execução. Ele não altera a hora de início da janela. | Intervalo de tempo: (Exemplo: 00:10:00, implica um atraso de 10 minutos) |  Nº O padrão é "00:00:00" |
| **simultaneidade máx.** | Número de execuções do gatilho simultâneas que são acionadas para janelas prontas. Exemplo: se estivermos tentando compensar por hora para ontem, seriam 24 janelas. Se simultaneidade = 10, os eventos de gatilho serão disparados somente para as 10 primeiras janelas (00:00-01:00 - 09:00-10:00). Após a conclusão das primeiras 10 execuções de pipeline disparada, as execuções de gatilho serão disparadas para as próximas 10 (10:00-11:00-19:00 às 20:00). Continuando com o exemplo de simultaneidade = 10, se houver 10 janelas prontas, haverá 10 execuções de pipeline. Se houver apenas 1 janela pronta, haverá apenas 1 execução de pipeline. | Número inteiro | Sim. Os valores possíveis são 1 a 50 |
| **retryPolicy: Count** | O número de repetições antes da execução do pipeline ser marcada como "Failed"  | Número inteiro |  Nº O padrão é de 0 tentativas |
| **retryPolicy: intervalInSeconds** | O intervalo entre tentativas de repetição em segundos | Número inteiro |  Nº O padrão é de 30 segundos |

### <a name="using-system-variables-windowstart-and-windowend"></a>Usar as variáveis do sistema: WindowStart e WindowEnd

Se você quiser usar WindowStart e WindowEnd do gatilho de janela em cascata em sua definição de **pipeline** (ou seja, para parte de uma consulta), você deverá passar as variáveis como parâmetros para o pipeline na definição de **gatilho**, da seguinte forma:
```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "MyWindowStart": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "MyWindowEnd": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    }
                }
            }
      }    
}
```  

Em seguida, na definição do pipeline, para usar os valores de WindowStart e WindowEnd, use adequadamente os parâmetros de "MyWindowStart" e "MyWindowEnd"

### <a name="notes-on-backfill"></a>Observações sobre a compensação
Quando há várias janelas para execução (principalmente no cenário de compensação), a ordem de execução das janelas será determinante, e será dos intervalos mais antigos para os mais recentes. A partir de agora, não é possível alterar esse comportamento.

### <a name="updating-an-existing-triggerresource"></a>Atualizar um TriggerResource existente
* Se a frequência (ou o tamanho da janela) do gatilho for alterada, o estado das janelas já processadas *não* será redefinido. O gatilho continuará acionando para as janelas, desde o último executado usando o novo tamanho de janela.
* Se a hora de término do gatilho mudar (for adicionada ou atualizada), o estado das janelas já processadas *não* será redefinido. O gatilho simplesmente aceitará a nova hora de término. Se a hora de término for anterior às janelas já executadas, o gatilho será interrompido. Caso contrário, ele será interrompido quando a nova hora de término for encontrada.

## <a name="sample-using-azure-powershell"></a>Exemplo do uso do Azure PowerShell
Esta seção mostra como usar o Azure PowerShell para criar, iniciar e monitorar um gatilho.

1. Crie um arquivo JSON denominado MyTrigger.json na pasta C:\ADFv2QuickStartPSH\ com o seguinte conteúdo:

   > [!IMPORTANT]
   > Definir **startTime** para a hora UTC atual e **endTime** para uma hora após a hora UTC atual antes de salvar o arquivo JSON.

    ```json   
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```  
2. Criar um gatilho usando o cmdlet **Set-AzureRmDataFactoryV2Trigger**.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    
3. Confirm that the status of the trigger is **Stopped** by using the **Get-AzureRmDataFactoryV2Trigger** cmdlet.

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

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações detalhadas sobre gatilhos, consulte [Gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md#triggers).
