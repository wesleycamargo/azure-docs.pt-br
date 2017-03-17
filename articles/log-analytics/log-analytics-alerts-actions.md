---
title: Respostas aos alertas no OMS Log Analytics | Microsoft Docs
description: "Alertas no Log Analytics identificam informações importantes em seu repositório do OMS e podem notificar proativamente problemas ou invocar ações para tentar corrigi-los.  Este artigo descreve como criar uma regra de alerta e detalha as diferentes ações que elas podem executar."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fdf22ff85a3a76be5de50632c4948df44c2312df
ms.openlocfilehash: b8731e1fe48b7d809b113eb5273e3962542b8f34
ms.lasthandoff: 03/01/2017


---

# <a name="add-actions-to-alert-rules-in-log-analytics"></a>Adicionar ações às regras de alerta no Log Analytics
Quando um [alerta é criado no Log Analytics](log-analytics-alerts.md), você tem a opção de [Configurar a regra de alerta](log-analytics-alerts.md) para executar uma ou mais ações.  Este artigo descreve as várias ações e detalhes disponíveis na configuração de cada tipo.

| Ação | Descrição |
|:--|:--|
| [Email](#email-actions) |    Envie um email com os detalhes do alerta para um ou mais destinatários. |
| [Webhook](#webhook-actions) | Invoque um processo externo por meio de uma única solicitação HTTP POST. |
| [Runbook](#runbook-actions) | Inicie um runbook na Automação do Azure. |


## <a name="email-actions"></a>Ações de email
Ações de email enviam um email com os detalhes do alerta para um ou mais destinatários.  Você pode especificar o assunto do email, mas seu conteúdo é um formato padrão criado pelo Log Analytics.  Ele inclui informações de resumo, como o nome do alerta, além dos detalhes de até dez registros retornados pela pesquisa de log.  Ele também inclui um link para uma pesquisa de log no Log Analytics que retornará todo o conjunto de registros da consulta.   O remetente do email é *Microsoft Operations Management Suite Team (Equipe do Microsoft Operations Management Suite) &lt;noreply@oms.microsoft.com&gt;*. 

As ações de email exigem as propriedades indicadas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Subject |Assunto do email.  Não é possível modificar o corpo do email. |
| Destinatários |Endereços de todos os destinatários de email.  Se você especificar mais de um endereço, separe os endereços com ponto e vírgula (;). |


## <a name="webhook-actions"></a>Ações de Webhook

Ações de Webhook permitem invocar um processo externo por meio de uma única solicitação HTTP POST.  O serviço que está sendo chamado deve dar suporte a webhooks e determinar como ele usará as cargas recebidas.  Você também pode chamar uma API REST que não dá suporte a webhooks especificamente desde que a solicitação esteja em um formato que a API compreende.  Exemplos de como usar um webhook em resposta a um alerta de envio de mensagem no [Slack](http://slack.com) ou de criação de um incidente em [PagerDuty](http://pagerduty.com/).  Um passo a passo completo de como criar uma regra de alerta com um webhook para chamar o Slack está disponível em [Webhooks em alertas do Log Analytics](log-analytics-alerts-webhooks.md).

As ações de webhook exigem as propriedades indicadas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| URL de Webhook |A URL do webhook. |
| Carga JSON personalizada |Carga personalizada para enviar com o webhook.  Confira os detalhes abaixo. |


Webhooks incluem uma URL e uma carga formatada em JSON, que são os dados enviados para um serviço externo.  Por padrão, a carga incluirá os valores na tabela a seguir.  Você pode optar por substituir essa carga por uma personalizado de sua preferência.  Nesse caso, você pode usar as variáveis da tabela para cada um dos parâmetros para incluir seus valores na carga personalizada.

| Parâmetro | Variável | Descrição |
|:--- |:--- |:--- |
| AlertRuleName |#AlertRuleName |Nome da regra de alerta. |
| AlertThresholdOperator |#thresholdoperator |O operador de limite da regra de alerta.  *Greater than (Maior que)* ou *Less than (Menor que)*. |
| AlertThresholdValue |#thresholdvalue |O valor de limite para a regra de alerta. |
| LinkToSearchResults |#LinkToSearchResults |Vincular a pesquisa de log do Log Analytics que retorna os registros da consulta que criou o alerta. |
| ResultCount |#searchresultcount |Número de registros nos resultados da pesquisa. |
| SearchIntervalEndtimeUtc |#SearchIntervalEndtimeUtc |Hora de término da consulta no formato UTC. |
| SearchIntervalInSeconds |#searchinterval |A janela de tempo para a regra de alerta. |
| SearchIntervalStartTimeUtc |#SearchIntervalStartTimeUtc |Hora de início da consulta no formato UTC. |
| SearchQuery |#SearchQuery |A consulta da pesquisa de log usada pela regra de alerta. |
| SearchResults |Veja abaixo |Registros retornados pela consulta em formato JSON.  Limitado aos primeiros 5.000 registros. |
| WorkspaceID |#WorkspaceID |ID do seu espaço de trabalho do OMS. |

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


Veja o passo a passo de um exemplo completo de como criar uma regra de alerta com um webhook para iniciar um serviço externo em [Criar uma ação de webhook de alerta no OMS Log Analytics para enviar uma mensagem ao Slack](log-analytics-alerts-webhooks.md).

## <a name="runbook-actions"></a>Ações de runbook
Ações de runbook iniciam um runbook na Automação do Azure.  Para usar esse tipo de ação, você deve ter a [solução de Automação](log-analytics-add-solutions.md) instalada e configurada em seu espaço de trabalho do OMS.  Você pode selecionar os runbooks na conta de automação configurados na solução de Automação.

As ações de runbook exigem as propriedades indicadas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:---|
| Runbook | O runbook que você deseja iniciar quando um alerta for criado. |
| Executar em | Especifique **Azure** para executar o runbook na nuvem.  Especifique **Hybrid worker** para executar o runbook em um agente com [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md ) instalado.  |

Ações de runbook iniciam o runbook usando um [webhook](../automation/automation-webhooks.md).  Ao criar a regra de alerta, ela criará automaticamente um novo webhook para o runbook com o nome **OMS Alert Remediation (Remediação de Alerta do OMS)** seguido por um GUID.  

Você não pode preencher diretamente quaisquer parâmetros de runbook, mas o [parâmetro $WebhookData](../automation/automation-webhooks.md) incluirá os detalhes do alerta, incluindo os resultados da pesquisa de log que o criou.  Será necessário definir o runbook **$WebhookData** como um parâmetro para que possa acessar as propriedades do alerta.  Os dados de alerta estão disponíveis no formato json em uma única propriedade chamada **SearchResults** na propriedade **RequestBody** do **$WebhookData**.  Isso está relacionado com as propriedades na tabela abaixo.

| Nó | Descrição |
|:--- |:--- |
| ID |Caminho e GUID da pesquisa. |
| __metadata |Informações sobre o alerta, incluindo o número de registros e o status dos resultados da pesquisa. |
| value |Entrada separada para cada registro nos resultados da pesquisa.  Os detalhes da entrada serão compatíveis com as propriedades e os valores do registro. |

Por exemplo, o seguinte runbook deve extrair os registros retornados pela pesquisa de log e atribuir propriedades diferentes com base no tipo de cada registro.  Observe que o runbook é iniciado com a conversão de **RequestBody** do json para que possa ser editado como um objeto no PowerShell.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value

    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer

        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }

        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="next-steps"></a>Próximas etapas
- Conclua um passo a passo para [configurar um webhook](log-analytics-alerts-webhooks.md) com uma regra de alerta.  
- Aprenda a criar [runbooks na Automação do Azure](https://azure.microsoft.com/documentation/services/automation) para corrigir os problemas identificados por alertas.
