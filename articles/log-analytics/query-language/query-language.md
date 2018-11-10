---
title: Linguagem de consulta do Log Analytics no Azure Monitor | Microsoft Docs
description: Referências a recursos para aprender a escrever consultas no Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: f8bef8c6ab5c0639f9a99eb2c0443c33d7b6d84e
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243811"
---
# <a name="log-analytics-query-language"></a>Linguagem de consulta do Log Analytics
O Log Analytics fornece coleta e análise de logs para o Azure Monitor. Ele se baseia no Azure Data Explorer e usa uma versão da mesma linguagem de consulta. A [Documentação da linguagem de consulta do Azure Data Explorer](/azure/kusto/query) tem todos os detalhes da linguagem e deve ser seu principal recurso para escrever consultas do Log Analytics. Esta página fornece links para outros recursos sobre como aprender a escrever consultas e sobre diferenças na implementação da linguagem no Log Analytics.

## <a name="getting-started"></a>Introdução

- [Introdução ao Log Analytics no portal do Azure](get-started-analytics-portal.md) é uma lição sobre como escrever consultas e trabalhar com os resultados no portal do Azure.
-  [Introdução às consultas no Log Analytics](get-started-queries.md) é uma lição sobre como escrever consultas usando dados do Log Analytics.

## <a name="concepts"></a>Conceitos
- [Analisar dados do Log Analytics no Azure Monitor](../log-analytics-queries.md) fornece uma breve visão geral das consultas de log e descreve como os dados do Log Analytics são estruturados.
- [Exibir e analisar dados no Log Analytics](../log-analytics-log-search-portals.md) explica os portais nos quais você pode criar e executar consultas do Log Analytics.

## <a name="reference"></a>Referência

- [Referência da linguagem de consulta](/azure/kusto/query) é a referência completa da linguagem de consulta do Data Explorer.
- [Diferenças da linguagem de consulta do Log Analytics](data-explorer-difference.md) descreve as diferenças entre as versões da linguagem de consulta do Data Explorer.
- [Propriedades padrão em registros do Log Analytics](../log-analytics-standard-properties.md) descreve as propriedades que são padrão para todos os dados do Log Analytics.
- [Executar pesquisas de log de recursos cruzados no Log Analytics](../log-analytics-cross-workspace-search.md) descreve como escrever consultas que usam dados de vários workspaces do Log Analytics e aplicativos do Application Insights.


## <a name="examples"></a>Exemplos

- [Exemplos de consulta do Log Analytics](examples.md) fornece consultas de exemplo usando dados do Log Analytics.



## <a name="lessons"></a>Lições

- [Trabalhar com cadeias de caracteres em consultas do Log Analytics](string-operations.md) descreve como trabalhar com os dados de cadeia de caracteres.
- [Trabalhar com valores de data/hora em consultas do Log Analytics](datetime-operations.md) descreve como trabalhar com os dados de data e hora. 
- [Agregações em consultas do Log Analytics](aggregations.md) e [Agregações avançadas em consultas do Log Analytics](advanced-aggregations.md) descrevem como agregar e resumir dados.
- [Junções em consultas do Log Analytics](joins.md) descreve como unir dados de várias tabelas.
- [Trabalhar com JSON e estruturas de dados em consultas do Log Analytics](json-data-structures.md) descreve como analisar dados JSON.
- [Escrever consultas avançadas no Log Analytics](advanced-query-writing.md) descreve estratégias para criar consultas complexas e reutilizar código.
- [Criar gráficos e diagramas de consultas do Log Analytics](charts.md) descreve como visualizar dados de uma consulta.

## <a name="cheatsheets"></a>Folhas de cola

-  [Folha de referências da linguagem de consulta SQL para Log Analytics](sql-cheatsheet.md) auxilia usuários que já estão familiarizados com o SQL.
-  [Folha de referências da linguagem de consulta Splunk para Log Analytics](sql-cheatsheet.md) auxilia usuários que já estão familiarizados com o Splunk.
 
## <a name="next-steps"></a>Próximas etapas

- Acesse a [documentação de referência completa da linguagem de consulta do Data Explorer](/azure/kusto/query/).