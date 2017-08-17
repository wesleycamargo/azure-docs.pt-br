---
title: Perguntas frequentes sobre a nova pesquisa de logs do Log Analytics | Microsoft Docs
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
ms.date: 08/08/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 2de9a87390da11a034c6cebaa37d4cc89edf3cad
ms.contentlocale: pt-br
ms.lasthandoff: 08/09/2017

---

# <a name="log-analytics-new-log-search-frequently-asked-questions"></a>Perguntas frequentes sobre a nova pesquisa de logs do Log Analytics

Este artigo fornece as perguntas frequentes sobre a atualização do [Log Analytics para a nova linguagem de consulta](log-analytics-log-search-upgrade.md).



## <a name="upgrade-process"></a>Processo de atualização

### <a name="i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>Tenho vários espaços de trabalho. Posso atualizar todos os espaços de trabalho ao mesmo tempo?  
R: Não.  A atualização se aplica a um único espaço de trabalho por vez. Atualmente não há nenhuma maneira de atualizar vários espaços de trabalho ao mesmo tempo. Observe que outros usuários do espaço de trabalho atualizado também serão afetados.  

### <a name="will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>Dados de logs existentes coletados no meu espaço de trabalho serão modificados se eu atualizar?  
Nº Os dados de log disponíveis para as pesquisas de espaço de trabalho não são afetados pela atualização. Pesquisas salvas, alertas e modos de exibição serão convertidos para a nova linguagem de pesquisa automaticamente.  

### <a name="what-happens-if-i-dont-upgrade-my-workspace"></a>O que acontece se eu não atualizar meu espaço de trabalho?  
A pesquisa de logs herdada será preterida nos próximos meses. Os espaços de trabalho que não forem atualizados até lá serão atualizados automaticamente.

### <a name="i-didnt-choose-to-upgrade-but-my-workspace-has-been-upgraded-anyway-what-happened"></a>Eu não optei por atualizar, mas o meu espaço de trabalho foi atualizado assim mesmo! o que aconteceu?  
Outro administrador deste espaço de trabalho pode ter atualizado o espaço de trabalho. Observe que todos os espaços de trabalho serão atualizados automaticamente quando a nova linguagem alcançar disponibilidade geral.  

### <a name="i-have-upgraded-by-mistake-and-now-need-to-cancel-it-and-restore-everything-back-what-should-i-do"></a>Eu atualizei por engano e agora preciso cancelar a atualização e restaurar tudo. O que devo fazer?  
Sem problema.  Criamos um instantâneo do seu espaço de trabalho antes da atualização, então você pode restaurá-lo. Tenha em mente que as pesquisas, os alertas ou os modos de exibição salvos após a atualização serão perdidos.  Para restaurar seu ambiente de espaço de trabalho, siga o procedimento em [Posso voltar atrás depois de atualizar?](log-analytics-log-search-upgrade.md#can-i-go-back-after-i-upgrade).



## <a name="log-searches"></a>Pesquisas de log

### <a name="i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>Tenho pesquisas salvas fora do meu espaço de trabalho atualizado. Posso convertê-las para a nova linguagem de pesquisa automaticamente?
Você pode usar a ferramenta de conversão de linguagem na página de pesquisa de logs para converter cada uma delas.  Não há nenhum método para converter automaticamente várias pesquisas sem atualizar o espaço de trabalho.


## <a name="alerts"></a>Alertas

### <a name="i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>Tenho várias regras de alerta. É necessário criá-las novamente na nova linguagem depois de atualizar?  
Não, as regras de alerta são convertidas automaticamente para a nova linguagem de pesquisa durante a atualização.  

## <a name="power-bi"></a>Power BI

### <a name="does-anything-change-with-powerbi-integration"></a>Alguma coisa é alterada com a integração do PowerBI?
Sim.  Uma vez atualizado o espaço de trabalho, o processo para exportar dados do Log Analytics para o Power BI deixará de funcionar.  Todas as agendas existentes criadas antes da atualização serão desabilitadas.  Após fazer upgrade, o Azure Log Analytics utiliza a mesma plataforma do Application Insights e você usará o mesmo processo para exportar consultas do Log Analytics para o Power BI como no [processo para exportar consultas do Application Insights para o Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).

## <a name="dashboards"></a>Painéis

### <a name="can-i-still-use-dashboards"></a>Ainda é possível usar painéis?
Você pode continuar a usar os painéis que você criou antes de seu espaço de trabalho ser atualizado, mas você não pode editar esses painéis ou criar novos.  Você pode continuar a criar e editar modos de exibição com [Designer de Exibição](log-analytics-view-designer.md). 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [atualizar seu espaço de trabalho para a nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md).

