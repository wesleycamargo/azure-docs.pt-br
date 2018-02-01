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
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 6dfee26d7585c8ec295a1f0ea1bd0bc14a34cc5a
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2018
---
# <a name="log-analytics-new-log-search-faq-and-known-issues"></a>Perguntas frequentes e problemas conhecidos sobre a nova pesquisa de logs do Log Analytics

Este artigo inclui as perguntas frequentes e os problemas conhecidos sobre o upgrade do [Log Analytics para a nova linguagem de consulta](log-analytics-log-search-upgrade.md).  Leia o artigo na íntegra antes de tomar a decisão de fazer upgrade de seu espaço de trabalho.


## <a name="alerts"></a>Alertas

### <a name="question-i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>Pergunta: Tenho várias regras de alerta. É necessário criá-las novamente na nova linguagem depois de atualizar?  
Não, as regras de alerta são convertidas automaticamente para a nova linguagem de pesquisa durante a atualização.  

### <a name="question-i-have-alert-rules-with-webhook-and-runbook-actions-will-these-continue-to-work-when-i-upgrade"></a>Pergunta: Eu tenho regras de alerta com ações de webhook e runbook. Elas continuarão funcionando quando eu atualizar?

Não, há algumas alterações nas ações de webhook e runbook que podem exigir que você faça alterações no modo de processamento de carga. Fizemos essas alterações para padronizar os diversos formatos de saída e reduzir o tamanho da carga. Detalhes sobre esses formatos podem ser obtidos em [Adicionar ações a regras de alerta no Log Analytics](log-analytics-alerts-actions.md).


## <a name="computer-groups"></a>Grupos de computadores

### <a name="question-im-getting-errors-when-trying-to-use-computer-groups--has-their-syntax-changed"></a>Pergunta: Recebo erros ao tentar usar grupos de computadores.  A sintaxe deles mudou?
Sim, a sintaxe para usar grupos de computadores muda quando o espaço de trabalho é atualizado.  Consulte [Grupos de computadores em pesquisas de logs do Log Analytics](log-analytics-computer-groups.md) para obter detalhes.


## <a name="dashboards"></a>Painéis

### <a name="question-can-i-still-use-dashboards-in-an-upgraded-workspace"></a>Pergunta: Ainda posso usar painéis em um espaço de trabalho atualizado?
Com a atualização, estamos começando o processo de retirada de **Meu Painel**.  Você pode continuar usando os blocos adicionados ao painel antes do upgrade do espaço de trabalho, mas não pode editar esses blocos nem adicionar novos.  Você pode continuar criando e editando as exibições com o [Designer de Exibição](log-analytics-view-designer.md), que tem um conjunto de recursos mais avançado e também criar painéis no portal do Azure.


## <a name="log-searches"></a>Pesquisas de log

### <a name="question-i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>Pergunta: Tenho pesquisas salvas fora de meu espaço de trabalho atualizado. Posso convertê-las para a nova linguagem de pesquisa automaticamente?
Você pode usar a ferramenta de conversão de linguagem na página de pesquisa de logs para converter cada uma delas.  Não há nenhum método para converter automaticamente várias pesquisas sem atualizar o espaço de trabalho.

### <a name="question-why-are-my-query-results-not-sorted"></a>Pergunta: Por que os resultados de minha consulta não são classificados?
Os resultados não são classificados por padrão na nova linguagem de consulta.  Use o [operador sort](https://go.microsoft.com/fwlink/?linkid=856079) para classificar os resultados por uma ou mais propriedades.

### <a name="question-where-did-the-metrics-view-go-after-i-upgraded"></a>Pergunta: Para onde a exibição de métricas foi desde do upgrade?
O modo de exibição de métricas fornecia uma representação gráfica dos dados de desempenho de uma pesquisa de logs.  Essa exibição não está mais disponível após a atualização.  Você pode usar o [operador renderizar](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) para formatar a saída de uma consulta em um gráfico.

### <a name="question-where-did-minify-go-after-i-upgraded"></a>Pergunta: Para onde Minify foi desde do upgrade?
O Minify é um recurso que fornece uma exibição resumida dos resultados da pesquisa.  Após a atualização, a opção Minify não aparece no portal de pesquisa de logs.  Você pode obter uma funcionalidade semelhante com o novo idioma de pesquisa utilizando [reduzir](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/reduce-operator) ou [autocluster_v2](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/evaluate-operator/autocluster). 

    Event
    | where TimeGenerated > ago(10h)
    | reduce by RenderedDescription

    Event
    | where TimeGenerated > ago(10h)
    | evaluate autocluster_v2()



## <a name="log-search-api"></a>API da Pesquisa de Log

### <a name="question-does-the-log-search-api-get-updated-after-i-upgrade"></a>Pergunta: a API da Pesquisa de Logs é atualizada após o upgrade?
A [API de pesquisa de Logs](log-analytics-log-search-api.md) herdada deixará de funcionar quando você tiver atualizado seu espaço de trabalho.  Consulte [API REST do Azure Log Analytics](https://dev.loganalytics.io/) para obter detalhes sobre a nova API.


## <a name="portals"></a>Portais

### <a name="question-should-i-use-the-new-advanced-analytics-portal-or-keep-using-the-log-search-portal"></a>Pergunta: Devo usar o novo portal de Análise Avançada ou continuar usando o portal da Pesquisa de Logs?
Veja uma comparação dos dois portais em [Portais para criar e editar consultas de log no Azure Log Analytics](log-analytics-log-search-portals.md).  Cada portal tem diferentes vantagens para que você possa escolher o melhor para seus requisitos.  É comum escrever consultas no portal de Análise Avançada e colá-las em outros lugares, como no Designer de Exibição.  Leia mais sobre os [problemas a serem considerados](log-analytics-log-search-portals.md#advanced-analytics-portal) ao fazer isso.


### <a name="question--after-upgrade-i-get-an-error-trying-to-run-queries-and-am-also-seeing-errors-in-my-views"></a>Pergunta: Após o upgrade, obtenho um erro ao tentar executar consultas e também estou vendo erros em minhas exibições.

Seu navegador requer acesso aos endereços a seguir para executar consultas do Log Analytics após o upgrade.  Se seu navegador estiver acessando o portal do Azure por meio de um firewall, você deverá habilitar o acesso a esses endereços.

| Uri | IP | Portas |
|:---|:---|:---|
| portal.loganalytics.io | Dinâmico | 80.443 |
| api.loganalytics.io    | Dinâmico | 80.443 |
| docs.loganalytics.io   | Dinâmico | 80.443 |



## <a name="power-bi"></a>Power BI

### <a name="question-does-anything-change-with-powerbi-integration"></a>Pergunta: alguma coisa muda com a integração do PowerBI?
Sim.  Uma vez atualizado o espaço de trabalho, o processo para exportar dados do Log Analytics para o Power BI deixará de funcionar.  Todas as agendas existentes criadas antes da atualização serão desabilitadas.  

Após fazer upgrade, o Azure Log Analytics utiliza a mesma plataforma do Application Insights e você usará o mesmo processo para exportar consultas do Log Analytics para o Power BI como no [processo para exportar consultas do Application Insights para o Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).  Agora Exportar para o Power BI chama diretamente o ponto de extremidade de API. Isso permite que você obtenha até 500.000 linhas ou 64.000.000 bytes de dados, exporte longas consultas e personalize o tempo limite da consulta (o tempo limite padrão é de 3 minutos e o tempo limite máximo é de 10 minutos).

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell

### <a name="question-does-the-log-search-powershell-cmdlet-get-updated-after-i-upgrade"></a>Pergunta: o cmdlet do PowerShell da Pesquisa de Logs é atualizado após o upgrade?
O [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/Get-AzureRmOperationalInsightsSearchResults) será substituído após a conclusão do upgrade de todos os espaços de trabalho.  Use o [cmdlet Invoke-LogAnalyticsQuery](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets) para executar pesquisas de log em espaços de trabalho com upgrade.




## <a name="resource-manager-templates"></a>Modelos do Gerenciador de Recursos

### <a name="question-can-i-create-an-upgraded-workspace-with-a-resource-manager-template"></a>Pergunta: Posso criar um espaço de trabalho atualizado com um modelo do Resource Manager?
Sim.  Você deve usar a versão de API 2017-03-15-preview e incluir uma seção **recursos** no modelo, como no exemplo a seguir.

    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2017-03-15-preview",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceRegion')]",
            "properties": {
                "sku": {
                    "name": "Free"
                },
                "features": {
                    "legacy": 0,
                    "searchVersion": 1
                }
            }
        }
    ],



## <a name="solutions"></a>Soluções

### <a name="question-will-my-solutions-continue-to-work"></a>Pergunta: Minhas soluções continuarão funcionando?
Todas as soluções continuarão funcionando em um espaço de trabalho atualizado, embora seu desempenho melhorará se elas forem convertidas na nova linguagem de consulta.  Há problemas conhecidos com algumas soluções existentes que são descritos nesta seção.

### <a name="known-issue-perspectives-in-application-insights-connector"></a>Problema conhecido: perspectivas no conector do Application Insights
Não há mais suporte para as perspectivas na [solução do Conector do Application Insights](log-analytics-app-insights-connector.md) na solução do conector do Application Insights.  É possível usar o Designer de exibição para criar exibições personalizadas usando dados do Application Insights.

### <a name="known-issue-backup-solution"></a>Problema conhecido: solução de backup
A Solução de backup poderá não coletar dados se tiver sido instalada antes da atualização de um espaço de trabalho. Desinstale a solução e instale a versão mais recente.  A nova versão da solução não dá suporte a cofres de backup clássicos. Portanto, também é necessário atualizar para cofres de Serviços de Recuperação para continuar usando a solução.

## <a name="upgrade-process"></a>Processo de atualização

### <a name="question-i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>Pergunta: Tenho vários espaços de trabalho. Posso atualizar todos os espaços de trabalho ao mesmo tempo?  
Nº  A atualização se aplica a um único espaço de trabalho por vez. Atualmente não há nenhuma maneira de atualizar vários espaços de trabalho ao mesmo tempo. Observe que outros usuários do espaço de trabalho atualizado também serão afetados.  

### <a name="question-will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>Pergunta: os dados de logs existentes coletados em meu espaço de trabalho serão modificados se eu fizer o upgrade?  
Nº Os dados de log disponíveis para as pesquisas de espaço de trabalho não são afetados pela atualização. Pesquisas salvas, alertas e modos de exibição serão convertidos para a nova linguagem de pesquisa automaticamente.  

### <a name="question-what-happens-if-i-dont-upgrade-my-workspace"></a>Pergunta: o que acontecerá se eu não fizer o upgrade de meu espaço de trabalho?  
A pesquisa de logs herdada será preterida nos próximos meses. Os espaços de trabalho que não forem atualizados até lá serão atualizados automaticamente.

### <a name="question-can-i-revert-back-after-i-upgrade"></a>Pergunta: Posso reverter após o upgrade?
Antes da disponibilidade geral, você podia reverter o seu espaço de trabalho após o upgrade.  Agora que a nova linguagem atingiu a disponibilidade geral, esse recurso foi removido porque começamos a desativar a plataforma herdada.


## <a name="views"></a>Modos de exibição

### <a name="question-how-do-i-create-a-new-view-with-view-designer"></a>Pergunta: Como fazer para criar uma nova exibição com o Designer de Exibição?
Antes do upgrade, era possível criar uma nova exibição com o Designer de Exibição por meio de um bloco no painel principal no portal do OMS.  Quando o espaço de trabalho é atualizado, esse bloco é removido.  Crie uma nova exibição com o Designer de Exibição no portal do OMS clicando no botão + verde no menu à esquerda.  Você continua a criar uma nova exibição com o Portal do Azure, clicando no bloco Designer de Exibição.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [atualizar seu espaço de trabalho para a nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md).
