---
title: Propriedades padrão nos registros do Log Analytics do Azure Monitor | Microsoft Docs
description: Descreve as propriedades que são comuns a vários tipos de dados no Log Analytics do Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 663f0b04c528c180e4130c1c157441cbc0ceb98b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955860"
---
# <a name="standard-properties-in-log-analytics-records"></a>As propriedades padrão nos registros do Log Analytics
Os dados no [Log Analytics](../log-analytics/log-analytics-queries.md) são armazenados como um conjunto de registros, cada um com um determinado tipo de dados, com um conjunto exclusivo de propriedades. Muitos tipos de dados terão propriedades padrão comuns a vários tipos. Este artigo descreve essas propriedades e fornece exemplos de como você pode usá-las em consultas.

Algumas dessas propriedades ainda estão no processo de implementação, portanto, você poderá vê-las em alguns tipos de dados, mas não em outros.


## <a name="resourceid"></a>_ResourceId
A propriedade **_ResourceId** contém um identificador exclusivo para o recurso ao qual o registro está associado. Isso lhe dá uma propriedade padrão a ser usada para definir o escopo de sua consulta apenas aos registros de um recurso específico, ou para unir dados relacionados em várias tabelas.

Para recursos do Azure, o valor de **_ResourceId** é a [URL de ID de recurso do Azure](../azure-resource-manager/resource-group-template-functions-resource.md). Atualmente, a propriedade está limitada aos recursos do Azure, mas será estendida a recursos fora do Azure, como em computadores locais. 

### <a name="examples"></a>Exemplos
O exemplo a seguir mostra uma consulta que une dados de desempenho e de eventos para cada computador. Ele mostra todos os eventos com a ID _101_ e utilização do processador acima de 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

O exemplo a seguir mostra uma consulta que une registros de _AzureActivity_ com registros de _SecurityEvent_. Ele mostra todas as operações de atividade com usuários que foram registradas nesses computadores.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como os [Dados do Log Analytics](../log-analytics/log-analytics-queries.md) são armazenados.
- Aprenda a [escrever consultas no Log Analytics](../log-analytics/query-language/get-started-queries.md).
- Aprenda a [unir tabelas em consultas do Log Analytics](../log-analytics/query-language/joins.md).