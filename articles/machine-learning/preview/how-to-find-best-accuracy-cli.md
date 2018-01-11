---
title: "Localizar execuções com a maior precisão e no menor tempo no Azure Machine Learning Workbench | Microsoft Docs"
description: "Um caso de uso de ponta a ponta para encontrar a maior precisão por meio de CLI usando o Azure Machine Learning Workbench"
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs, garyericson, jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 40e066fe602e8c4680043158f1d401a884e07c19
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2017
---
# <a name="find-runs-with-the-best-accuracy-and-lowest-duration"></a>Localizar execuções com a maior precisão e no menor tempo
Como há várias execuções, um caso de uso deve encontrar execuções com a maior precisão. Uma abordagem é usar a interface de linha de comando (CLI) com uma consulta [JMESPath](http://jmespath.org/). Para saber mais sobre como usar JMESPath na CLI do Azure, veja [Usar consultas JMESPath com a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest). No exemplo a seguir, quatro execuções são criadas com valores de precisão de 0, 0,98 e 1 e 1. Execuções são filtradas se estiverem no intervalo `[MaxAccuracy-Threshold, MaxAccuracy]` em que `Threshold = .03`.

## <a name="sample-data"></a>Dados de amostra
Se você não tiver execuções existentes com um valor `Accuracy`, as etapas a seguir gerarão execuções para consultas.

Primeiro, crie um arquivo Python no Azure Machine Learning Workbench, nomeie-o como `log_accuracy.py` e cole o código a seguir:
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

Em seguida, crie um arquivo `run.py` e cole no código a seguir:
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

Por fim, abra a CLI por meio do Workbench e execute o comando `python run.py` para enviar quatro experimentos. Depois que o script for concluído, você deve ver mais quatro execuções na guia `Run History`.

## <a name="query-the-run-history"></a>Consultar o histórico de execução
O primeiro comando localiza o valor de precisão máximo.
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

Usando o valor de precisão máximo de `1` e um valor de limite de `0.03`, o segundo comando filtrará as execuções usando `Accuracy` e classificará as execuções por `duration` em ordem crescente.
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> Se desejar uma verificação de limite superior rigorosa, o formato da consulta será ``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``

Se você usar o PowerShell, o código a seguir usará variáveis locais para armazenar o limite e a precisão máxima:
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre registros em log, veja [Como usar o histórico de execuções e as métricas do modelo no Azure Machine Learning Workbench](how-to-use-run-history-model-metrics.md).    
