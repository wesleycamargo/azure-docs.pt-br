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
ms.date: 09/27/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: c43f8acf7f9278ca412bee9ad15f2d51541cd27b
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044324"
---
# <a name="standard-properties-in-log-analytics-records"></a>As propriedades padrão nos registros do Log Analytics
Os dados no [Log Analytics](../log-analytics/log-analytics-queries.md) são armazenados como um conjunto de registros, cada um com um determinado tipo de dados, com um conjunto exclusivo de propriedades. Muitos tipos de dados terão propriedades padrão comuns a vários tipos. Este artigo descreve essas propriedades e fornece exemplos de como você pode usá-las em consultas.

Algumas dessas propriedades ainda estão no processo de implementação, portanto, você poderá vê-las em alguns tipos de dados, mas não em outros.

## <a name="timegenerated"></a>TimeGenerated
A propriedade **TimeGenerated** contém a data e a hora em que o registro foi criado. Ela fornece uma propriedade comum a ser usada para filtrar ou para resumir pela hora. Quando você seleciona um intervalo de tempo para uma exibição ou um painel no portal do Azure, ele usa TimeGenerated para filtrar os resultados.

### <a name="examples"></a>Exemplos

A consulta a seguir retorna o número de eventos de erro criados em cada dia da semana anterior.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

## <a name="type"></a>Tipo
A propriedade **Type** contém o nome da tabela da qual o registro foi recuperado, que também pode ser considerada como o tipo de registro. Essa propriedade é útil em consultas que combinam registros de várias tabelas, como aqueles que usam o operador `search`, para distinguir entre registros de tipos diferentes. **$table** pode ser usado no lugar de **Type** em alguns locais.

### <a name="examples"></a>Exemplos
A consulta a seguir retorna a contagem de registros por tipo coletados na última hora.

```Kusto
search * 
| where TimeGenerated > ago(1h) 
| summarize count() by Type 
```

## <a name="resourceid"></a>\_ResourceId
A propriedade **\__ResourceId** contém um identificador exclusivo do recurso ao qual o registro está associado. Isso lhe dá uma propriedade padrão a ser usada para definir o escopo de sua consulta apenas aos registros de um recurso específico, ou para unir dados relacionados em várias tabelas.

Para recursos do Azure, o valor de **_ResourceId** é a [URL de ID de recurso do Azure](../azure-resource-manager/resource-group-template-functions-resource.md). Atualmente, a propriedade está limitada aos recursos do Azure, mas será estendida a recursos fora do Azure, como em computadores locais.

> [!NOTE]
> Alguns tipos de dados já têm campos que contêm a ID de recurso do Azure ou, pelo menos, partes dele, como a ID da assinatura. Embora esses campos sejam mantidos para manter a compatibilidade com versões anteriores, é recomendável usar _ResourceId para executar a correlação cruzada, uma vez que ela é mais consistente.

### <a name="examples"></a>Exemplos
A consulta a seguir une dados de desempenho e de eventos de cada computador. Ele mostra todos os eventos com a ID _101_ e utilização do processador acima de 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

A consulta a seguir une registros de _AzureActivity_ com registros de _SecurityEvent_. Ele mostra todas as operações de atividade com usuários que foram registradas nesses computadores.

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
