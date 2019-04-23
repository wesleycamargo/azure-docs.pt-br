---
title: Usando a API REST de alerta do Log Analytics
description: A API de REST alerta do Log Analytics permite que você criar e gerenciar alertas no Log Analytics que faz parte do Log Analytics.  Este artigo fornece detalhes da API e vários exemplos para executar operações diferentes.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2018
ms.author: bwren
ms.openlocfilehash: bee64909c7f3b295691ef1cb1840424aa7e3fe49
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549705"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Criar e gerenciar regras de alerta no Log Analytics com a API REST
A API REST de alerta do Log Analytics permite criar e gerenciar alertas no Log Analytics.  Este artigo fornece detalhes da API e vários exemplos para executar operações diferentes.

A API REST de Pesquisa do Log Analytics é RESTful e pode ser acessada por meio da API REST do Azure Resource Manager. Neste documento, você encontrará exemplos em que a API é acessada por meio de uma linha de comando do PowerShell usando o [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de software livre que simplifica a invocação da API do Azure Resource Manager. O uso do ARMClient e do PowerShell é uma das muitas opções para acessar a API de Pesquisa do Log Analytics. Com essas ferramentas, você pode utilizar a API do RESTful Azure Resource Manager para fazer chamadas para espaços de trabalho do Log Analytics e executar comandos de pesquisa dentro deles. A API produzirá resultados da pesquisa para você no formato JSON, permitindo que você use os resultados da pesquisa de diferentes maneiras por meio de programação.

## <a name="prerequisites"></a>Pré-requisitos
Atualmente, os alertas somente podem ser criados com uma pesquisa salva no Log Analytics.  Você pode consultar a [API REST da Pesquisa de Log](../../azure-monitor/log-query/log-query-overview.md) para obter mais informações.

## <a name="schedules"></a>Agendas
Uma pesquisa salva pode ter um ou mais agendamentos. O agendamento define a frequência com que a pesquisa é executada e o intervalo de tempo em que os critérios são identificados.
Os agendamentos têm as propriedades indicadas na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| Interval |A frequência com que a pesquisa é executada. Medida em minutos. |
| QueryTimeSpan |O intervalo durante o qual os critérios são avaliados. Deve ser igual ou maior que Interval. Medida em minutos. |
| Version |A versão da API que está sendo usada.  Atualmente, isso sempre deve ser definido como 1. |

Por exemplo, considere uma consulta de evento com um Interval (Intervalo) de 15 minutos e Timespan (Período) de 30 minutos. Nesse caso, a consulta deverá ser executada a cada 15 minutos e um alerta deverá ser disparado se os critérios continuarem a ser resolvidos como verdadeiro por um período de 30 minutos.

### <a name="retrieving-schedules"></a>Recuperando agendamentos
Use o método Get para recuperar todos os agendamentos de uma pesquisa salva.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Use o método Get com uma ID de agendamento para recuperar um agendamento específico para uma pesquisa salva.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

A seguir está um exemplo de resposta para um agendamento.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/sampleRG/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15,
            "Enabled": true,
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Criando uma agenda
Use o método Put com uma ID de agendamento única para criar um novo agendamento.  Observe que dois agendamento não podem ter a mesma ID, mesmo se estiverem associados a diferentes pesquisas salvas.  Quando você cria um agendamento no console do Log Analytics, um GUID é criado para o ID do agendamento.

> [!NOTE]
> O nome para todas as pesquisas, agendas e ações salvas criadas com a API do Log Analytics deve estar em letras minúsculas.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Editando um agendamento
Use o método Put com uma ID de agendamento existente para a mesma pesquisa salva para modificar esse agendamento; no exemplo a seguir, o agendamento está desabilitado. O corpo da solicitação deve incluir a *etag* do agendamento.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Excluindo agendamentos
Use o método Delete com uma ID de agendamento para excluir um agendamento.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Ações
Um agendamento pode ter várias ações. Uma ação pode definir um ou mais processos a serem executados, como enviar um email ou iniciar um runbook, ou então ela pode definir um limite que determina quando os resultados de uma pesquisa correspondem a certos critérios.  Algumas ações definirão ambos para que os processos sejam executados quando o limite for atingido.

Todas as ações têm as propriedades indicadas na tabela a seguir.  Diferentes tipos de alertas têm diferentes propriedades adicionais que são descritas abaixo.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| `Type` |Tipo da ação.  Atualmente, os valores possíveis são Alerta e Webhook. |
| `Name` |Nome de exibição para o alerta. |
| `Version` |A versão da API que está sendo usada.  Atualmente, isso sempre deve ser definido como 1. |

### <a name="retrieving-actions"></a>Recuperando ações

> [!NOTE]
> A partir de 14 de maio de 2018, os todos os alertas em uma instância de nuvem pública do Azure do espaço de trabalho do Log Analytics serão automaticamente estendidos para o Azure. Um usuário pode começar voluntariamente a estender os alertas para o Azure antes de 14 de maio de 2018. Para obter mais informações, consulte [Estender alertas para o Azure a partir do Log Analytics](../../azure-monitor/platform/alerts-extend.md). As ações dos usuários que estendem os alertas para o Azure agora são controladas em grupos de ações do Azure. Quando um workspace e seus alertas são estendidos para o Azure, você pode recuperar ou adicionar ações usando a [API do grupo de ações](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Use o método Get para recuperar todas as ações de um agendamento.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Use o método Get com a ID de ação para recuperar uma ação específica de um agendamento.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Criar ou editar ações
Use o método Put com uma ID de ação que seja exclusiva para o agendamento para criar uma nova ação.  Quando você cria uma ação no console do Log Analytics, um GUID é para o ID da ação.

> [!NOTE]
> O nome para todas as pesquisas, agendas e ações salvas criadas com a API do Log Analytics deve estar em letras minúsculas.

Use o método Put com uma ID de ação existente para a mesma pesquisa salva para modificar esse agendamento.  O corpo da solicitação deve incluir a Etag do agendamento.

O formato da solicitação para criar uma nova ação varia conforme o tipo de ação, veja os exemplos fornecidos nas seções a seguir.

### <a name="deleting-actions"></a>Excluindo ações

> [!NOTE]
> A partir de 14 de maio de 2018, os todos os alertas em uma instância de nuvem pública do Azure do espaço de trabalho do Log Analytics serão automaticamente estendidos para o Azure. Um usuário pode começar voluntariamente a estender os alertas para o Azure antes de 14 de maio de 2018. Para obter mais informações, consulte [Estender alertas para o Azure a partir do Log Analytics](../../azure-monitor/platform/alerts-extend.md). As ações dos usuários que estendem os alertas para o Azure agora são controladas em grupos de ações do Azure. Quando um workspace e seus alertas são estendidos para o Azure, você pode recuperar ou adicionar ações usando a [API do grupo de ações](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Use o método Delete com a ID de ação para excluir uma ação.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Ações de Alerta
Um Agendamento deve ter somente uma ação de Alerta.  Ações de alerta têm uma ou mais das seções na tabela a seguir.  Elas são descritas em mais detalhes abaixo.

| Seção | DESCRIÇÃO | Uso |
|:--- |:--- |:--- |
| Limite |Critérios para quando a ação for executada.| Necessário para cada alerta, antes ou depois de ser estendido para o Azure. |
| Severity |Rótulo usado para classificar o alerta quando disparado.| Necessário para cada alerta, antes ou depois de ser estendido para o Azure. |
| Suprimir |Opção para interromper notificações do alerta. | Opcional para cada alerta, antes ou depois de ser estendido para o Azure. |
| Grupos de Ação |IDs do ActionGroup do Azure em que as ações necessárias são especificadas, como - Emails SMSs, Chamadas de voz, Webhooks, Runbooks de automação, Conectores ITSM, etc.| Necessário quando os alertas são estendidos para o Azure|
| Personalizar Ações|Modificar a saída padrão para determinadas ações do ActionGroup| Opcional para cada alerta, pode ser usado depois de alertas serem estendidos para o Azure. |
| EmailNotification |Envie email para vários destinatários. | Não é necessário, se os alertas são estendidos para o Azure|
| Correção |Inicie um runbook na Automação do Azure para tentar corrigir o problema identificado. |Não é necessário, se os alertas são estendidos para o Azure|
| Ações webhook | Enviar dados de Alertas, para o serviço desejado como JSON |Não é necessário, se os alertas são estendidos para o Azure|

> [!NOTE]
> A partir de 14 de maio de 2018, os todos os alertas em uma instância de nuvem pública do Azure do espaço de trabalho do Log Analytics serão automaticamente estendidos para o Azure. Um usuário pode começar voluntariamente a estender os alertas para o Azure antes de 14 de maio de 2018. Para obter mais informações, consulte [Estender alertas para o Azure a partir do Log Analytics](../../azure-monitor/platform/alerts-extend.md).

#### <a name="thresholds"></a>Limites
Uma ação de Alerta deve ter somente um limite.  Quando os resultados de pesquisas salvas coincidem com o limite de uma ação associada à pesquisa, outros processos nesta ação são executados.  Uma ação também pode conter apenas um limite para ser usado com ações de outros tipos que não contêm os limites.

Os limites têm as propriedades indicadas na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| `Operator` |Operador de comparação de limite. <br> gt = Maior Que <br>  lt = Menor Que |
| `Value` |Valor para o limite. |

Por exemplo, considere uma consulta de evento com um Interval (Intervalo) de 15 minutos, Timespan (Período) de 30 minutos e Threshold (Limite) maior que 10. Nesse caso, a consulta deverá ser executada a cada 15 minutos e um alerta deverá ser disparado se ela retornar 10 eventos criados dentro de um período de 30 minutos.

Veja a seguir um exemplo de resposta para uma ação com apenas um limite.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Use o método Put com uma ID de ação única para criar uma nova ação de limite para um agendamento.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Use o método Put com uma ID de ação existente para criar uma nova ação de limite para um agendamento.  O corpo da solicitação deve incluir a Etag da ação.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Severity
O Log Analytics permite classificar alertas em categorias, para facilitar a triagem e o gerenciamento. A gravidade do Alerta definida é: informativo, aviso e crítico. São mapeadas para a escala de gravidade normalizada dos Alertas do Azure como:

|Nível de gravidade do Log Analytics  |Nível de gravidade dos Alertas do Azure  |
|---------|---------|
|`critical` |Sev 0|
|`warning` |Sev 1|
|`informational` | Sev 2|

Veja a seguir um exemplo de resposta para uma ação com apenas um limite e gravidade. 

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Severity": "critical",
        "Version": 1    }

Use o método Put com uma ID de ação única para criar uma nova ação para um agendamento com gravidade.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Use o método Put com uma ID de ação existente para modificar uma ação de gravidade para um agendamento.  O corpo da solicitação deve incluir a Etag da ação.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Suprimir
Os alertas de consulta baseados no Log Analytics serão disparados sempre que o limite for atingido ou excedido. Com base na lógica implícita na consulta, isso pode resultar no disparo de um alerta durante uma série de intervalos e, portanto, no envio de notificações constantemente. Para evitar esse cenário, um usuário pode definir a opção Suprimir instruindo o Log Analytics a aguardar um período estipulado antes que a notificação seja disparada pela segunda vez para o regra de alerta. Portanto, se a supressão for definida para 30 minutos, o alerta será disparado pela primeira vez e enviará as notificações configuradas. Mas, depois, aguarde 30 minutos antes que a notificação da regra de alerta seja usada novamente. No período temporário, o regra de alerta continuará em execução – apenas a notificação é suprimida pelo Log Analytics pelo tempo especificado, independentemente de quantas vezes a regra de alerta foi disparada nesse período.

A propriedade Supressão do regra de alerta do Log Analytics é especificada usando o valor *Limitação* e o período de supressão usando o valor *DurationInMinutes*.

Veja a seguir uma resposta de exemplo para uma ação com apenas uma propriedade de limite, gravidade e supressão

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Throttling": {
          "DurationInMinutes": 30
        },
        "Severity": "critical",
        "Version": 1    }

Use o método Put com uma ID de ação única para criar uma nova ação para um agendamento com gravidade.  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Use o método Put com uma ID de ação existente para modificar uma ação de gravidade para um agendamento.  O corpo da solicitação deve incluir a Etag da ação.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Grupos de Ação
Todos os alertas no Azure, use o Grupo de Ações como mecanismo padrão para lidar com ações. Com o Grupo de Ações, você pode especificar suas ações uma vez e, em seguida, associar o grupo de ações para vários alertas - em todo o Azure. Sem a necessidade de declarar repetidamente as mesmas ações. Os Grupos de Ações dão suportam a várias ações - incluindo Email, SMS, Chamada de voz, Conexão ITSM, Runbook de automação, URI de Webhook e muito mais. 

Para os usuários que tiverem estendido seus alertas ao Azure, agora deve haver uma agenda com detalhes do Grupo de Ações passado junto com o limite para poder criar um alerta. Detalhes de email, URLs de Webhook, detalhes de automação de Runbook e outras ações precisam ser definidos no Grupo de Ações antes de criar um alerta; é possível criar o [Grupo de Ações pelo Azure Monitor](../../azure-monitor/platform/action-groups.md) no Portal ou usar a [API de Grupo de Ações](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Para adicionar a associação de grupo de ações a um alerta, especifique a ID exclusiva do Azure Resource Manager do grupo de ações na definição do alerta. Uma ilustração de exemplo é apresentada abaixo:

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
        },
        "Severity": "critical",
        "Version": 1
      },

Use o método Put com uma ID de ação exclusiva para associar o Grupo de Ações já existente a um agendamento.  Abaixo é apresentada uma ilustração de exemplo do uso.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Use o método Put com uma ID de ação existente para modificar um Grupo de Ações associado a um agendamento.  O corpo da solicitação deve incluir a Etag da ação.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Personalizar Ações
Por padrão, as ações seguem o modelo e o formato padrão para notificações. Mas o usuário pode personalizar algumas ações, mesmo se elas são controladas por Grupos de Ações. Atualmente, a personalização é possível para o Assunto do Email e o Conteúdo de Webhook.

##### <a name="customize-e-mail-subject-for-action-group"></a>Personalizar o Assunto do Email para o Grupo de Ações
Por padrão, o assunto do email para alertas é: Notificação de alerta `<AlertName>` para `<WorkspaceName>`. Mas isso pode ser personalizado, para que você possa especificar palavras ou marcações - para permitir que você facilmente empregue regras de filtro na Caixa de entrada. Os detalhes de cabeçalho de email personalizado precisam ser enviados juntamente com os detalhes de ActionGroup, como no exemplo a seguir.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Use o método Put com uma ID de ação exclusiva para associar o Grupo de Ações já existente com personalização a um agendamento.  Abaixo é apresentada uma ilustração de exemplo do uso.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Use o método Put com uma ID de ação existente para modificar um Grupo de Ações associado a um agendamento.  O corpo da solicitação deve incluir a Etag da ação.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Personalizar o Conteúdo de Webhook para o Grupo de Ações
Por padrão, o webhook enviado pelo Grupo de Ações para análise de logs tem uma estrutura fixa. Mas você pode personalizar o conteúdo de JSON, usando variáveis específicas compatíveis, para atender aos requisitos do ponto de extremidade do webhook. Para obter mais informações, consulte [Ação de webhook para alertas de log](../../azure-monitor/platform/alerts-log-webhook.md). 

Os detalhes de webhook personalizados precisam ser enviados com os detalhes do ActionGroup e serão aplicados a todos os URI do Webhook especificado dentro do grupo de ações, como no exemplo a seguir.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Use o método Put com uma ID de ação exclusiva para associar o Grupo de Ações já existente com personalização a um agendamento.  Abaixo é apresentada uma ilustração de exemplo do uso.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Use o método Put com uma ID de ação existente para modificar um Grupo de Ações associado a um agendamento.  O corpo da solicitação deve incluir a Etag da ação.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="email-notification"></a>Notificação por email
Notificações por Email enviam email para um ou mais destinatários.  Elas incluem as propriedades indicadas na tabela a seguir.

> [!NOTE]
> A partir de 14 de maio de 2018, os todos os alertas em uma instância de nuvem pública do Azure do espaço de trabalho do Log Analytics serão automaticamente estendidos para o Azure. Um usuário pode começar voluntariamente a estender os alertas para o Azure antes de 14 de maio de 2018. Para obter mais informações, consulte [Estender alertas para o Azure a partir do Log Analytics](../../azure-monitor/platform/alerts-extend.md). As ações dos usuários que estendem os alertas para o Azure, como a Notificação de Email, agora são controladas em grupos de ações do Azure. Quando um workspace e seus alertas são estendidos para o Azure, você pode recuperar ou adicionar ações usando a [API do grupo de ações](https://docs.microsoft.com/rest/api/monitor/actiongroups).
   

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| Recipients |Lista de endereços de email. |
| Subject |O assunto do email. |
| Attachment |Atualmente, não há suporte para nexos, por isso este item sempre terá um valor de "None" (Nenhum). |

Veja a seguir uma resposta de exemplo para uma ação de notificação por email com um limite.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Use o método Put com uma ID de ação única para criar uma nova ação de email para um agendamento.  O exemplo a seguir cria uma notificação por email com um limite para que o email seja enviado quando os resultados da pesquisa salvos excederem o limite.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

Use o método Put com uma ID de ação existente para modificar uma ação de email para um agendamento.  O corpo da solicitação deve incluir a Etag da ação.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>Ações de correção
As correções iniciam um runbook na Automação do Azure que tenta corrigir o problema identificado pelo alerta.  Você deve criar um webhook para o runbook usado em uma ação de correção e especificar o URI na propriedade WebhookUri.  Quando você cria essa ação usando o portal do Azure, um novo webhook é criado automaticamente para o runbook.

> [!NOTE]
> A partir de 14 de maio de 2018, os todos os alertas em uma instância de nuvem pública do Azure do espaço de trabalho do Log Analytics serão automaticamente estendidos para o Azure. Um usuário pode começar voluntariamente a estender os alertas para o Azure antes de 14 de maio de 2018. Para obter mais informações, consulte [Estender alertas para o Azure a partir do Log Analytics](../../azure-monitor/platform/alerts-extend.md). As ações dos usuários que estendem os alertas para o Azure, como a Correção usando runbook, agora são controladas em grupos de ações do Azure. Quando um workspace e seus alertas são estendidos para o Azure, você pode recuperar ou adicionar ações usando a [API do grupo de ações](https://docs.microsoft.com/rest/api/monitor/actiongroups).

As correções incluem as propriedades indicadas na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| RunbookName |Nome do runbook. Isso deve corresponder a um runbook publicado na conta de automação configurada na Solução de Automação em seu espaço de trabalho do Log Analytics. |
| WebhookUri |O URI do webhook. |
| Expiry |A data e hora de expiração do webhook.  Caso o webhook não expire, esta pode ser qualquer data futura válida. |

Veja a seguir uma resposta de exemplo para uma ação de correção com um limite.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Use o método Put com uma ID de ação única para criar uma nova ação de correção para um agendamento.  O exemplo a seguir cria uma correção com um limite para que o runbook seja iniciado quando os resultados da pesquisa salvos excederem o limite.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Use o método Put com uma ID de ação existente para modificar uma ação de correção para um agendamento.  O corpo da solicitação deve incluir a Etag da ação.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Exemplo
Veja a seguir um exemplo completo para criar um novo alerta de email.  Ele cria um novo agendamento juntamente com uma ação que contém um limite e um email.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $resourceGroup  = "MyResourceGroup"    
    $workspaceName    = "MyWorkspace"
    $searchId       = "MySearch"
    $scheduleId     = "MySchedule"
    $thresholdId    = "MyThreshold"
    $actionId       = "MyEmailAction"
    
    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/?api-version=2015-03-20 $scheduleJson
    
    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Severity':'Warning', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/actions/$actionId/?api-version=2015-03-20 $emailJson

#### <a name="webhook-actions"></a>Ações de Webhook
Ações de Webhook iniciam um processo chamando uma URL e, opcionalmente, fornecendo uma carga a ser enviada.  Elas são semelhantes às ações de Correção, exceto que se destinam a webhooks que podem invocar outros processos além de runbooks da Automação do Azure.  Eles também oferecem a opção adicional de fornecer uma carga a ser enviada para o processo remoto.

> [!NOTE]
> A partir de 14 de maio de 2018, os todos os alertas em uma instância de nuvem pública do Azure do espaço de trabalho do Log Analytics serão automaticamente estendidos para o Azure. Um usuário pode começar voluntariamente a estender os alertas para o Azure antes de 14 de maio de 2018. Para obter mais informações, consulte [Estender alertas para o Azure a partir do Log Analytics](../../azure-monitor/platform/alerts-extend.md). As ações dos usuários que estendem os alertas para o Azure, como a Correção usando runbook, agora são controladas em grupos de ações do Azure. Quando um workspace e seus alertas são estendidos para o Azure, você pode recuperar ou adicionar ações usando a [API do grupo de ações](https://docs.microsoft.com/rest/api/monitor/actiongroups).


Ações de Webhook não têm um limite, devendo ser adicionadas a um agendamento que tem uma ação de Alerta com um limite.  

Veja a seguir um exemplo de resposta para a ação de webhook e uma ação de alerta associada com um limite.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

##### <a name="create-or-edit-a-webhook-action"></a>Criar ou editar uma ação de webhook
Use o método Put com uma ID de ação única para criar uma nova ação de webhook para um agendamento.  O exemplo a seguir cria uma ação de Webhook e uma ação de alerta com um limite para que o webhook seja acionado quando os resultados da pesquisa salvos excederem o limite.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Use o método Put com uma ID de ação existente para modificar uma ação de webhook para um agendamento.  O corpo da solicitação deve incluir a Etag da ação.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction


## <a name="next-steps"></a>Próximas etapas
* Use a [API REST para executar pesquisas de log](../../azure-monitor/log-query/log-query-overview.md) no Log Analytics.
* Saiba mais sobre os [Alertas de log nos alertas do Azure](../../azure-monitor/platform/alerts-unified-log.md)

