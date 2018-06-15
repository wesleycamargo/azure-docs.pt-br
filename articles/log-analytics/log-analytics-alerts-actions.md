---
title: Respostas aos alertas no Log Analytics do Azure | Microsoft Docs
description: Alertas no Log Analytics identificam informações importantes em seu espaço de trabalho do Azure e podem notificar proativamente problemas ou invocar ações para tentar corrigi-los.  Este artigo descreve como criar uma regra de alerta e detalha as diferentes ações que elas podem executar.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 717adf1b19b9de8542ec507df3a01b187d0df8a5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
ms.locfileid: "31521057"
---
# <a name="add-actions-to-alert-rules-in-log-analytics"></a>Adicionar ações às regras de alerta no Log Analytics

> [!NOTE]
> Alertas no Log Analytics estão [sendo estendidos para o Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md).  Alertas no Azure os [Grupos de Ação](../monitoring-and-diagnostics/monitoring-action-groups.md) para definir suas ações em vez das informações neste artigo.


Quando um [alerta é criado no Log Analytics](log-analytics-alerts.md), você tem a opção de [Configurar a regra de alerta](log-analytics-alerts.md) para executar uma ou mais ações.  Este artigo descreve as várias ações e detalhes disponíveis na configuração de cada tipo.

| Ação | DESCRIÇÃO |
|:--|:--|
| [Email](#email-actions) | Envie um email com os detalhes do alerta para um ou mais destinatários. |
| [Webhook](#webhook-actions) | Invoque um processo externo por meio de uma única solicitação HTTP POST. |
| [Runbook](#runbook-actions) | Inicie um runbook na Automação do Azure. |


## <a name="email-actions"></a>Ações de email
Ações de email enviam um email com os detalhes do alerta para um ou mais destinatários.  Você pode especificar o assunto do email, mas seu conteúdo é um formato padrão criado pelo Log Analytics.  Ele inclui informações de resumo, como o nome do alerta, além dos detalhes de até dez registros retornados pela pesquisa de log.  Ele também inclui um link para uma pesquisa de log no Log Analytics que retornará todo o conjunto de registros da consulta.   O remetente do email é *Microsoft Operations Management Suite Team (Equipe do Microsoft Operations Management Suite) &lt;noreply@oms.microsoft.com&gt;*. 

As ações de email exigem as propriedades indicadas na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| Assunto |Assunto do email.  Não é possível modificar o corpo do email. |
| Destinatários |Endereços de todos os destinatários de email.  Se você especificar mais de um endereço, separe os endereços com ponto e vírgula (;). |


## <a name="webhook-actions"></a>Ações de Webhook

Ações de Webhook permitem invocar um processo externo por meio de uma única solicitação HTTP POST.  O serviço que está sendo chamado deve dar suporte a webhooks e determinar como ele usa as cargas recebidas.  Você também pode chamar uma API REST que não dá suporte a webhooks especificamente desde que a solicitação esteja em um formato que a API compreende.  Exemplos de como usar um webhook em resposta a um alerta de envio de mensagem no [Slack](http://slack.com) ou de criação de um incidente em [PagerDuty](http://pagerduty.com/).  Um passo a passo completo de como criar uma regra de alerta com um webhook para chamar o Slack está disponível em [Webhooks em alertas do Log Analytics](log-analytics-alerts-webhooks.md).

As ações de webhook exigem as propriedades indicadas na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| URL de Webhook |A URL do webhook. |
| Carga JSON personalizada |Carga personalizada para enviar com o webhook.  Confira os detalhes abaixo. |


Webhooks incluem uma URL e uma carga formatada em JSON, que são os dados enviados para um serviço externo.  Por padrão, a carga inclui os valores na tabela a seguir.  Você pode optar por substituir essa carga por uma personalizado de sua preferência.  Nesse caso, você pode usar as variáveis da tabela para cada um dos parâmetros para incluir seus valores na carga personalizada.


| Parâmetro | Variável | DESCRIÇÃO |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Nome da regra de alerta. |
| AlertThresholdOperator |#thresholdoperator |O operador de limite da regra de alerta.  *Greater than (Maior que)* ou *Less than (Menor que)*. |
| AlertThresholdValue |#thresholdvalue |O valor de limite para a regra de alerta. |
| LinkToSearchResults |#linktosearchresults |Vincular a pesquisa de log do Log Analytics que retorna os registros da consulta que criou o alerta. |
| ResultCount |#searchresultcount |Número de registros nos resultados da pesquisa. |
| SearchIntervalEndtimeUtc |#searchintervalendtimeutc |Hora de término da consulta no formato UTC. |
| SearchIntervalInSeconds |#searchinterval |A janela de tempo para a regra de alerta. |
| SearchIntervalStartTimeUtc |#searchintervalstarttimeutc |Hora de início da consulta no formato UTC. |
| SearchQuery |#searchquery |A consulta da pesquisa de log usada pela regra de alerta. |
| SearchResults |Veja abaixo |Registros retornados pela consulta em formato JSON.  Limitado aos primeiros 5.000 registros. |
| WorkspaceID |#workspaceid |ID do seu espaço de trabalho de Log Analytics. |

Por exemplo, você pode especificar a seguinte carga personalizada que inclui um único parâmetro chamado *text*.  O serviço chamado por esse webhook seria esperaria receber esse parâmetro.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Essa carga de exemplo seria resolvida como algo semelhante ao mostrado a seguir quando enviado para o webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Para incluir os resultados da pesquisa em uma carga personalizada, adicione a seguinte linha como uma propriedade de nível superior na carga json.  

    "IncludeSearchResults":true

Por exemplo, para criar uma carga personalizada que inclui apenas o nome do alerta e os resultados da pesquisa, você poderia usar o seguinte. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Veja o passo a passo de um exemplo completo de como criar uma regra de alerta com um webhook para iniciar um serviço externo em [Criar uma ação de webhook de alerta no Log Analytics para enviar uma mensagem ao Slack](log-analytics-alerts-webhooks.md).


## <a name="runbook-actions"></a>Ações de runbook
Ações de runbook iniciam um runbook na Automação do Azure.  Para usar esse tipo de ação, você deve ter a [solução de Automação](log-analytics-add-solutions.md) instalada e configurada em seu espaço de trabalho do Log Analytics.  Você pode selecionar os runbooks na conta de automação configurados na solução de Automação.

As ações de runbook exigem as propriedades indicadas na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--- |:---|
| Runbook | O runbook que você deseja iniciar quando um alerta for criado. |
| Executar em | Especifique **Azure** para executar o runbook na nuvem.  Especifique **Hybrid worker** para executar o runbook em um agente com [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md ) instalado.  |

Ações de runbook iniciam o runbook usando um [webhook](../automation/automation-webhooks.md).  Ao criar a regra de alerta, ela cria automaticamente um novo webhook para o runbook com o nome **OMS Alert Remediation (Remediação de Alerta do OMS)** seguido por um GUID.  

Você não pode preencher diretamente quaisquer parâmetros de runbook, mas o [parâmetro $WebhookData](../automation/automation-webhooks.md) inclui os detalhes do alerta, incluindo os resultados da pesquisa de log que o criou.  O runbook precisa definir o **$WebhookData** como um parâmetro para que possa acessar as propriedades do alerta.  Os dados de alerta estão disponíveis no formato json em uma única propriedade chamada **SearchResult** (para ações de runbook e ações de webhook com payload padrão) ou **SearchResults** (ações de webhook com payload personalizado, incluindo **IncludeSearchResults":true**) na propriedade **RequestBody** de **$WebhookData**.  Isso está relacionado com as propriedades na tabela abaixo.

>[!NOTE]
> Se o espaço de trabalho foi atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), então, a carga de runbook foi alterada.  Os detalhes do formato estão na [API REST do Azure Log Analytics](https://aka.ms/loganalyticsapiresponse).  Você pode ver um exemplo em [Exemplos](#sample-payload) a seguir.  

| Nó | DESCRIÇÃO |
|:--- |:--- |
| ID |Caminho e GUID da pesquisa. |
| __metadata |Informações sobre o alerta, incluindo o número de registros e o status dos resultados da pesquisa. |
| value |Entrada separada para cada registro nos resultados da pesquisa.  Os detalhes da entrada são compatíveis com as propriedades e os valores do registro. |

Por exemplo, os seguintes runbooks devem extrair os registros retornados pela pesquisa de logs e atribuir propriedades diferentes com base no tipo de cada registro.  Observe que o runbook é iniciado com a conversão de **RequestBody** do json para que possa ser editado como um objeto no PowerShell.

>[!NOTE]
> Este runbook usa **SearchResult**, que é a propriedade que contém os resultados de ações de runbook e ações de webhook com payload padrão.  Se o runbook for chamado de uma resposta de webhook usando um payload personalizado, será necessário alterar esta propriedade para **SearchResults**.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody

    # Get all metadata properties    
    $AlertRuleName = $RequestBody.AlertRuleName
    $AlertThresholdOperator = $RequestBody.AlertThresholdOperator
    $AlertThresholdValue = $RequestBody.AlertThresholdValue
    $AlertDescription = $RequestBody.Description
    $LinktoSearchResults =$RequestBody.LinkToSearchResults
    $ResultCount =$RequestBody.ResultCount
    $Severity = $RequestBody.Severity
    $SearchQuery = $RequestBody.SearchQuery
    $WorkspaceID = $RequestBody.WorkspaceId
    $SearchWindowStartTime = $RequestBody.SearchIntervalStartTimeUtc
    $SearchWindowEndTime = $RequestBody.SearchIntervalEndtimeUtc
    $SearchWindowInterval = $RequestBody.SearchIntervalInSeconds

    # Get detailed search results
    if($RequestBody.SearchResult -ne $null)
    {
        $SearchResultRows    = $RequestBody.SearchResult.tables[0].rows 
        $SearchResultColumns = $RequestBody.SearchResult.tables[0].columns;

        foreach ($SearchResultRow in $SearchResultRows)
        {   
            $Column = 0
            $Record = New-Object –TypeName PSObject 
        
            foreach ($SearchResultColumn in $SearchResultColumns)
            {
                $Name = $SearchResultColumn.name
                $ColumnValue = $SearchResultRow[$Column]
                $Record | Add-Member –MemberType NoteProperty –Name $name –Value $ColumnValue -Force
                        
                $Column++
            }

            # Include code to work with the record. 
            # For example $Record.Computer to get the computer property from the record.
            
        }
    }



## <a name="sample-payload"></a>Carga de exemplo
Esta seção mostra a carga de exemplo para ações de webhook e o runbook.

### <a name="webhook-actions"></a>Ações de Webhook
Este exemplo usa **SearchResult**, que é a propriedade que contém os resultados para as ações de webhook com payload padrão.  Se o webhook usar um payload personalizado que inclui os resultados da pesquisa, essa propriedade será **SearchResults**.

A seguir, há uma carga de exemplo para uma ação de webhook.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "WebhookAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "workspaceID",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
                "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-workspaceID",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }


### <a name="runbooks"></a>Runbooks

A seguir, há uma carga de exemplo para uma ação de runbook.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "AutomationAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "861bd466-5400-44be-9552-5ba40823c3aa",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
            "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-861bd466-5400-44be-9552-5ba40823c3aa",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Critical"
    }



## <a name="next-steps"></a>Próximas etapas
- Conclua um passo a passo para [configurar um webhook](log-analytics-alerts-webhooks.md) com uma regra de alerta.  
- Aprenda a criar [runbooks na Automação do Azure](https://azure.microsoft.com/documentation/services/automation) para corrigir os problemas identificados por alertas.
