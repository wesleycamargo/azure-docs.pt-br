---
title: O que mudou no Azure Log Analytics? | Microsoft Docs
description: "Este artigo fornece as perguntas frequentes sobre a atualização do Log Analytics para a nova linguagem de consulta."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: bwren
ms.openlocfilehash: 783223a37c2a13c9affbf382209ca2aa4f1ba4c7
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2017
---
# <a name="whats-changed-in-azure-log-analytics"></a>O que mudou no Azure Log Analytics?
Além da linguagem de consulta em si, há vários aprimoramentos e mudanças que você deve estar ciente de quando seu espaço de trabalho de Log Analytics é [atualizado para uma nova linguagem de consulta](log-analytics-log-search-new.md).  Este artigo descreve brevemente as mudanças entre um espaço de trabalho herdado e atualizada com links para conteúdo detalhado para cada um. 

Consulte [problemas conhecidos e perguntas frequentes sobre a pesquisa de log no Log Analytics](log-analytics-log-search-faq.md) para obter uma descrição de todos os problemas conhecidos com a atualização e respostas para perguntas comuns.  

## <a name="query-language"></a>Idioma de consulta
A principal alteração na atualização de Log Analytics é a nova linguagem de consulta que tem melhorias significativas sobre a linguagem anterior.  

Você pode obter uma comparação direta de operações comuns entre a linguagem herdada e a nova linguagem em [Fazer a transição para a nova linguagem de consulta de Log Analytics do Azure](log-analytics-log-search-transition.md).  A documentação completa sobre a nova linguagem está disponível em [linguagem de consulta do Azure Log Analytics](https://docs.loganalytics.io).


## <a name="computer-groups"></a>Grupos de computadores
O método para criar um grupo de computadores não foi alterado, embora agora você deve fornecer um alias exclusivo para cada um.  Grupos de computadores com base em pesquisas de log devem usar a sintaxe da nova linguagem.

Há uma nova sintaxe para usar grupos de computadores em uma pesquisa de log.  Em vez de usar a função $ComputerGroups, grupos de computadores estão agora implementados como uma função com um alias exclusivo.  Você pode usar o alias na pesquisa de log como qualquer outra função.  

Consulte [Grupos de computadores em pesquisas de logs do Log Analytics](log-analytics-computer-groups.md) para obter detalhes.


## <a name="log-search-portals"></a>Portais de pesquisa de logs
Além do portal de pesquisa de Log para criar e executar pesquisas de log, você pode agora usar o portal de Análises Avançadas, o qual fornece recursos de edição significativamente melhorados.

Veja uma comparação dos dois portais em [Portais para criar e editar consultas de log no Azure Log Analytics](log-analytics-log-search-portals.md).  Você pode percorrer um tutorial sobre o novo portal no [Guia de Introdução com o Portal de Análises](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal).

## <a name="alerts"></a>Alertas
Alertas funcionam da mesma forma que em espaços de trabalho atualizados, mas a consulta que eles executam deve ser gravada na nova linguagem.  Todas as regras de alerta existentes que tinham antes da atualização serão convertidas automaticamente para a nova linguagem.  Você pode obter mais detalhes em [Noções básicas sobre alertas em Log Analytics](log-analytics-alerts.md).

O formato de carga para runbooks e webhooks enviados de alertas foi alterado.  Detalhes sobre esses formatos podem ser obtidos em [Adicionar ações a regras de alerta no Log Analytics](log-analytics-alerts-actions.md).

## <a name="dashboards"></a>Painéis
[Painéis](log-analytics-dashboards.md) estão no processo de serem preteridos.  Você pode continuar usando os blocos adicionados ao painel antes do upgrade do espaço de trabalho, mas não pode editar esses blocos nem adicionar novos.  Use o Designer de exibição para criar exibições personalizadas que tenham um recurso mais rico que os painéis.

Detalhes sobre o Designer de exibição estão disponíveis em [Use o Designer de exibição para criar exibições personalizadas no Log Analytics](log-analytics-view-designer.md).

## <a name="power-bi"></a>Power BI
O processo de exportação de dados de Log Analytics para o Power BI foi alterado para espaços de trabalho atualizados, e agendamentos existentes que você criou antes de atualizar ficarão desabilitados.  

Detalhes estão disponíveis em [dados de Log Analytics de exportação para o Power BI](log-analytics-powerbi.md).

## <a name="powershell"></a>PowerShell
O Get-AzureRmOperationalInsightsSearchResults para executar pesquisas de log do PowerShell não funcionará com um espaço de trabalho atualizado.  Use Invoke-LogAnalyticsQuery para essa funcionalidade com um espaço de trabalho atualizado.

Detalhes estão disponíveis em [API de REST de Log Analytics do Azure - Cmdlets do PowerShell](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets).

## <a name="log-search-api"></a>API da Pesquisa de Log
A pesquisa de log REST API foi alterada e as soluções que usam a versão herdada precisam ser atualizadas para usar a nova versão da API.   

Detalhes sobre a nova versão da API estão disponíveis em [API REST do Azure Log Analytics](https://dev.loganalytics.io/).

## <a name="next-steps"></a>Próximas etapas

- Consulte [problemas conhecidos e perguntas frequentes sobre a pesquisa de log no Log Analytics](log-analytics-log-search-faq.md) para obter uma descrição de todos os problemas conhecidos com a atualização e respostas para perguntas comuns.