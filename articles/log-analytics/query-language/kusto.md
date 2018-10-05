---
title: Referência da linguagem do Log Analytics do Azure Monitor | Microsoft Docs
description: Informações de referência para a linguagem Kusto usada pelo Log Analytics. Inclui elementos adicionais específicos ao Log Analytics e elementos sem suporte em consultas do Log Analytics.
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
ms.topic: article
ms.date: 09/25/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 5173790436a29fa9947346d711da1a2ddb32bf62
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451061"
---
# <a name="log-analytics-query-language-reference"></a>Referência da linguagem de consulta do Log Analytics
[Consultas do Log Analytics](../log-analytics-queries.md) usam a mesma linguagem de consulta e o mecanismo usado pelo [Azure Data Explorer](/azure/data-explorer/). Você pode acessar a referência de linguagem e outros detalhes sobre a linguagem do seguinte local: [referência de linguagem do Kusto](/azure/kusto/query)



## <a name="kusto-elements-not-support-in-log-analytics"></a>Os elementos do Kusto não têm suporte no Log Analytics
Enquanto as consultas do Log Analytics usam uma implementação do Kusto, há alguns elementos do Kusto sem suporte, conforme descrito nas seções a seguir.

### <a name="statements-not-supported-in-log-analytics"></a>Instruções sem suporte no Log Analytics

* [Alias](/kusto/query/aliasstatement)
* [Parâmetros de consulta](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-log-analytics"></a>Funções sem suporte no Log Analytics

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-log-analytics"></a>Operadores sem suporte no Log Analytics

* [Junção entre clusters](/azure/kusto/query/joincrosscluster)
* [Operador externaldata](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-log-analytics"></a>Plug-ins sem suporte no Log Analytics

* [Plug-in do sql_request](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-log-analytics"></a>Operadores adicionais no Log Analytics
Para dar suporte a recursos específicos do Log Analytics, os seguintes operadores adicionais do Kusto fornecidos não estão disponíveis fora do Log Analytics. 

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>Próximas etapas

- Leia sobre as consultas no [Log Analytics](../log-analytics-queries.md).
- Consulte uma lição sobre como escrever em uma [consulta do Log Analytics](/log-analytics/query-language/get-started-queries.md).
- Acesse a [documentação de referência do Kusto](/azure/kusto/query/) completa.