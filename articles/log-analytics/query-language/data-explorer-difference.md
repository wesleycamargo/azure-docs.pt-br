---
title: Referência da linguagem do Log Analytics do Azure Monitor | Microsoft Docs
description: Informações de referência para a linguagem de consulta do Data Explorer usada pelo Log Analytics. Inclui elementos adicionais específicos ao Log Analytics e elementos sem suporte em consultas do Log Analytics.
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
ms.date: 10/29/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: e6d097749dae49cf6f1d710bcf01cf99dcd98a4c
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243891"
---
# <a name="log-analytics-query-language-differences"></a>Diferenças da linguagem de consulta do Log Analytics

Enquanto o [Log Analytics](../log-analytics-queries.md) é criado no [Azure Data Explorer](/azure//data-explorer) e usa a [mesma linguagem de consulta](/azure/kusto/query), a versão da linguagem têm algumas diferenças. Este artigo identifica os elementos que são diferentes entre a versão do idioma usado para o Data Explorer e a versão usada para consultas do Log Analytics.

## <a name="data-explorer-elements-not-supported-in-log-analytics"></a>Elementos do Data Explorer sem suporte no Log Analytics
As seções a seguir descrevem os elementos da linguagem de consulta do Data Explorer sem suporte do Log Analytics.

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
Os seguintes operadores dão suporte a recursos específicos do Log Analytics e não estão disponíveis fora do Log Analytics.

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>Próximas etapas

<<<<<<< HEAD:articles/log-analytics/query-language/data-explorer-difference.md
- Obtenha referências para diferentes [recursos para escrever consultas do Log Analytics](kusto.md).
- Acesse a [documentação de referência completa da linguagem de consulta do Data Explorer](/azure/kusto/query/).
=======
- Leia sobre as consultas no [Log Analytics](../log-analytics-queries.md).
- Consulte uma lição sobre como escrever em uma [consulta do Log Analytics](/log-analytics/query-language/get-started-queries.md).
- Acesse a [documentação de referência do Kusto](/azure/kusto/query/) completa.
>>>>>>> 4bccab5ecb17c887658a4d2ed1bab6b22bf29ffd:articles/log-analytics/query-language/kusto.md
