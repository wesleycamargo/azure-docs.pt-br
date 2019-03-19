---
title: Criar dependências de gatilho de janela em cascata no Azure Data Factory | Microsoft Docs
description: Saiba como criar dependência em um gatilho de janela em cascata no Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: f3a547f5b953262d7263d1be0897161cf4091a1d
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57574382"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Criar uma dependência de gatilho de janela em cascata

Este artigo fornece etapas para criar uma dependência em um gatilho de janela em cascata. Para obter informações gerais sobre gatilhos de Janela em Cascata, consulte [Como criar um gatilho de janela em cascata](how-to-create-tumbling-window-trigger.md).

Para criar uma cadeia de dependência e certificar-se de que um gatilho executará somente após a execução com êxito de outro gatilho no data factory, use esse recurso avançado para criar uma dependência de janela em cascata.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Criar uma dependência na interface do usuário do Data Factory

Para criar dependência em um gatilho, selecione **Gatilho > Avançado > Novo** e, em seguida, escolha o gatilho para depender do deslocamento e tamanho apropriados. Selecione **Concluir** e publique as alterações do data factory para que as dependências entrem em vigor.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png)

## <a name="tumbling-window-dependency-properties"></a>Propriedades de dependência de janela em cascata

Uma dependência de gatilho de janela em cascata tem as seguintes propriedades:

```json
{
    "name": "DemoTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00.000Z",
            "delay": "00:00:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": "-02:00:00",
                    "offset": "-01:00:00",
                    "referenceTrigger": {
                        "referenceName": "DemoDependency1",
                        "type": "TriggerReference"
                    }
                }
            ]
        }
    }
}
```

A tabela a seguir fornece a lista de atributos necessários para definir uma dependência de Janela em Cascata.

| **Nome da Propriedade** | **Descrição**  | **Tipo** | **Obrigatório** |
|---|---|---|---|
| Gatilho  | Todos os gatilhos da janela em cascata existentes são exibidos neste menu suspenso. Escolha o gatilho para assumir a dependência.  | TumblingWindowTrigger | Sim |
| Deslocamento | Deslocamento do gatilho de dependência. Forneça um valor no formato de período de tempo e ambos os deslocamentos negativos e positivos serão permitidos. Esse parâmetro será obrigatório se o gatilho estiver dependendo de si próprio e, em todos os outros casos, será opcional. A autodependência deverá sempre ser um deslocamento negativo. | Timespan | Autodependência: Sim Outro: Não  |
| Tamanho da janela | Tamanho da janela em cascata de dependência. Forneça um valor no formato de período de tempo. Esse parâmetro é opcional. | Timespan | Não   |
|||||

## <a name="tumbling-window-self-dependency-properties"></a>Propriedades de autodependência de janela em cascata

Nos cenários em que o gatilho não deverá prosseguir para a próxima janela, até que a janela anterior seja concluída com êxito, crie uma autodependência. O gatilho de autodependência terá as propriedades a seguir:

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```

A seguir, estão as ilustrações dos cenários e o uso de propriedades de dependência de janela em cascata.

## <a name="dependency-offset"></a>Deslocamento de dependência

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png)

## <a name="dependency-size"></a>Tamanho da dependência

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png)

## <a name="self-dependency"></a>Autodependência

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png)

## <a name="usage-scenarios"></a>Cenários de uso

### <a name="dependency-on-another-tumbling-window-trigger"></a>Dependência de outro gatilho de janela em cascata

Por exemplo, um trabalho de processamento de telemetria diário, dependendo de outro trabalho diário, agregando os últimos sete dias de saída e gerando fluxos de janela rolantes de sete dias:

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png)

### <a name="dependency-on-itself"></a>Dependência de si próprio

Um trabalho diário sem lacunas nos fluxos de saída do trabalho:

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png)

## <a name="monitor-dependencies"></a>Monitorar dependências

É possível monitorar a cadeia de dependência e as janelas correspondentes na página de monitoramento de execução do gatilho. Navegue até  **Monitoramento > Execuções de Gatilho**.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png)

Clique na lupa para exibir todas as execuções de gatilho dependentes da janela selecionada.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png)

## <a name="next-steps"></a>Próximas etapas

Revise [Como criar um gatilho de janela em cascata](how-to-create-tumbling-window-trigger.md).