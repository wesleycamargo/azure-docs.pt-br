---
title: "Azure Service Fabric – monitoramento de desempenho com a extensão do Diagnóstico do Microsoft Azure | Microsoft Docs"
description: "Use o Diagnóstico do Microsoft Azure para coletar contadores de desempenho para os clusters do Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/05/2017
ms.author: dekapur
ms.openlocfilehash: f71c483eba201eae91c15b84a2ed42fcb68ae575
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2017
---
# <a name="performance-monitoring-with-windows-azure-diagnostics-extension"></a>Monitoramento de desempenho com a extensão do Diagnóstico do Microsoft Azure

Este documento aborda as etapas necessárias para configurar a coleta de contadores de desempenho por meio da extensão WAD (Diagnóstico do Microsoft Azure) para clusters do Windows. Para clusters do Linux, configure o [Agente do OMS](service-fabric-diagnostics-oms-agent.md) para coletar contadores de desempenho para seus nós. 

 > [!NOTE]
> A extensão WAD deve ser implantada em seu cluster para que essas etapas funcionem para você. Se ela não estiver configurada, vá para [Coleta e agregação de eventos utilizando o Diagnóstico do Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md) e siga as etapas em *Implantar a extensão de diagnóstico*.

## <a name="collect-performance-counters-via-the-wadcfg"></a>Coletar contadores de desempenho por meio do WadCfg

Para coletar os contadores de desempenho por meio do WAD, você precisa modificar a configuração adequadamente no modelo do Resource Manager do cluster. Siga estas etapas para adicionar um contador de desempenho que você deseja coletar ao modelo e executar uma atualização de recurso do Resource Manager.

1. Localize a configuração WAD no modelo do seu cluster – localize `WadCfg`. Você adicionará contadores de desempenho para coletar sob a `DiagnosticMonitorConfiguration`.

2. Defina sua configuração para coletar os contadores de desempenho adicionando a seção a seguir para sua `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    A `scheduledTransferPeriod` define a frequência com que os valores dos contadores que são coletados são transferidos para a tabela de armazenamento do Azure e para qualquer coletor configurado. 

3. Adicione os contadores de desempenho que você deseja coletar à `PerformanceCounterConfiguration` que foi declarada na etapa anterior. Cada contador que você gostaria de coletar é definido com um `counterSpecifier`, `sampleRate`, `unit`, `annotation` e quaisquer `sinks` relevantes. Aqui está um exemplo de um contador de desempenho para o *Tempo Total de Processador* (o período de tempo que a CPU ficou em uso para operações de processamento):

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
            {
                "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                "sampleRate": "PT1M",
                "unit": "Percent",
                "annotation": [
                ],
                "sinks": ""
            }
        ]
    }
    ```

    A taxa de amostragem do contador pode ser modificada de acordo com suas necessidades. O formato para ela é `PT<time><unit>`, portanto, se você quiser que o contador seja coletado a cada segundo, você deverá definir a `"sampleRate": "PT15S"`.

    De modo similar, você pode coletar vários outros contadores de desempenho, adicionando-as à lista na `PerformanceCounterConfiguration`. Embora você possa usar `*` para especificar grupos de contadores de desempenho que são nomeados de maneira similar, enviar todos os contadores por meio de um coletor (para o Application Insights) exige que eles sejam declarados individualmente. 

4. Uma vez que você tenha adicionado os contadores de desempenho apropriados que precisarem ser coletados, você precisará atualizar o recurso de cluster para que essas alterações sejam refletidas no seu cluster em execução. Salve a `template.json` modificada e abra o PowerShell. Você pode atualizar o cluster usando `New-AzureRmResourceGroupDeployment`. A chamada requer o nome do grupo de recursos, o arquivo de modelo atualizado e o arquivo de parâmetros e solicita que o Resource Manager faça as alterações apropriadas aos recursos que você atualizou. Após você entrar em sua conta e estar na assinatura correta, use o seguinte comando para executar a atualização:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. Quando a distribuição da atualização terminar (demora entre 15 e 45 minutos), o WAD deverá coletar os contadores de desempenho e enviá-los para uma tabela na conta de armazenamento declarada no `WadCfg`.

## <a name="next-steps"></a>Próximas etapas
* Consulte seus contadores de desempenho no Application Insights ao adicionar o coletor do Application Insights ao seu `WadCfg`. Leia a seção *Adicione o coletor do AI ao modelo do Resource Manager* em [Visualização e análise de eventos com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) para configurar o coletor do Application Insights.
* Colete mais contadores de desempenho para o cluster. Consulte [Métricas de desempenho](service-fabric-diagnostics-event-generation-perf.md) para obter uma lista de contadores que você deve coletar.
* [Use monitoramento e diagnóstico com uma VM Windows e modelos do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md) para fazer outras modificações em seu `WadCfg`, incluindo a configuração de contas de armazenamento adicionais para as quais enviar dados de diagnóstico.