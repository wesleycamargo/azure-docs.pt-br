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
ms.topic: article
ms.date: 10/31/2018
ms.author: bwren
ms.openlocfilehash: 645750ec40f0aba2ef58c096a72125fad2947719
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186252"
---
# <a name="log-analytics-query-language-differences"></a>Diferenças da linguagem de consulta do Log Analytics

Enquanto o [Log Analytics](log-query-overview.md) é criado no [Azure Data Explorer](/azure/data-explorer) e usa a [mesma linguagem de consulta](/azure/kusto/query), a versão da linguagem têm algumas diferenças. Este artigo identifica os elementos que são diferentes entre a versão do idioma usado para o Data Explorer e a versão usada para consultas do Log Analytics.

## <a name="data-explorer-elements-not-supported-in-log-analytics"></a>Elementos do Data Explorer sem suporte no Log Analytics
As seções a seguir descrevem os elementos da linguagem de consulta do Data Explorer sem suporte do Log Analytics.

### <a name="statements-not-supported-in-log-analytics"></a>Instruções sem suporte no Log Analytics

* [Alias](/azure/kusto/query/aliasstatement)
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

- Obtenha referências para diferentes [recursos para escrever consultas do Log Analytics](query-language.md).
- Acesse a [documentação de referência completa da linguagem de consulta do Data Explorer](/azure/kusto/query/).
