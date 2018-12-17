---
title: Funções do Azure Log Analytics | Microsoft Docs
description: Este artigo descreve como usar funções para chamar uma consulta de outra consulta no Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 8f2855ed56d298ec4c6abee02dd59ce9471f0d2e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52884229"
---
# <a name="using-functions-in-azure-monitor-log-analytics"></a>Usando funções no Azure Monitor Log Analytics

> [!NOTE]
> Você deve concluir [Introdução ao portal do Analytics](get-started-portal.md) e [Introdução às consultas](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]


Para usar uma consulta do Log Analytics com outra consulta, você pode salvar como uma função. Isso permite que você simplifique consultas complexas, dividindo-os em partes e permite a reutilização de código comum com várias consultas.

## <a name="create-a-function"></a>Criar uma função

Crie uma função no portal do Azure clicando em **Salvar** e, em seguida, fornecendo as informações na tabela a seguir.

| Configuração | DESCRIÇÃO |
|:---|:---|
| NOME           | Nome de exibição para a consulta no **Gerenciador de consultas**. |
| Salvar como        | Função |
| Alias da função | Nome curto para usar a função em outras consultas. Não pode conter espaços e deve ser exclusivo. |
| Categoria       | Uma categoria para organizar consultas salvas e funções na **Explorador de consultas**. |

> [!NOTE]
> Uma função no Log Analytics não pode conter outra função.

> [!NOTE]
> Salvar uma função é possível em consultas do Log Analytics, mas atualmente não para consultas do Application Insights.



## <a name="use-a-function"></a>Use uma função
Use uma função, incluindo seu alias em outra consulta. Pode ser usado como qualquer outra tabela.

## <a name="example"></a>Exemplo
A consulta padrão a seguir retorna todas as atualizações de segurança faltantes relatadas no último dia. Salve essa consulta como função com o alias _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Criar outra consulta e referência na função _security_updates_last_day_ ao procurar por atualizações de segurança necessárias relacionadas ao SQL.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Próximas etapas
Consulte outras lições para usar a linguagem de consulta do Log Analytics:

- [Operações de cadeia de caracteres](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Junções](joins.md)
- [Gráficos](charts.md)
