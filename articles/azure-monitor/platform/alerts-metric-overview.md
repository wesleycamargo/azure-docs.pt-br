---
title: Entender como funcionam os alertas de métrica no Azure Monitor.
description: Obtenha uma visão geral do que você pode fazer com alertas de métrica e como eles funcionam no Azure Monitor.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.component: alerts
ms.openlocfilehash: 24d0965fa2a88bc844a81c7430d46c071a9b874b
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53580035"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Entender como funcionam os alertas de métrica no Azure Monitor

Os alertas de métrica no Azure Monitor funcionam com métricas multidimensionais. Essas métricas podem ser métricas de plataforma, [métricas personalizadas](../../azure-monitor/platform/metrics-custom-overview.md), [logs populares do Log Analytics convertidos em métricas](../../azure-monitor/platform/alerts-metric-logs.md) ou métricas padrão do Application Insights. Os alertas de métrica verificam, em intervalos regulares, se as condições em uma ou mais séries temporais de métrica são verdadeiras e notificam você quando as condições são atendidas. Os alertas de métrica são baseados em estado, ou seja, eles só enviam notificações quando o estado é alterado.

## <a name="how-do-metric-alerts-work"></a>Como funcionam os alertas de métrica

Você pode definir uma regra de alerta de métrica especificando um recurso de destino para ser monitorado, o nome e a condição da métrica (um operador e um limite) e um grupo de ação a ser disparado quando a regra de alerta é acionada.
Digamos que você tenha criado uma regra de alerta de métrica simples da seguinte maneira:

- Recurso de destino (o recurso do Azure que você deseja monitorar): myVM
- Métrica: Porcentagem de CPU
- Agregação de tempo (estatística que é executada sobre os valores brutos de métrica. As agregações de tempo com suporte são Mín., Máx., Média, Total): Média
- Período (a janela de pesquisa na qual os valores de métrica são verificados):      Nos últimos 5 minutos
- Frequência (a frequência com que o alerta de métrica verifica se as condições foram atendidas): 1 minuto
- Operador:     Maior que
- Limite:      70

Desde o momento em que a regra de alerta é criada, o monitor é executado a cada minuto, examina valores de métrica para os últimos 5 minutos e verifica se a média desses valores excede 70. Se a condição for atendida, ou seja, a média de porcentagem de CPU nos últimos 5 minutos exceder 70, a regra de alerta disparará uma notificação de ativação. Se você configurou um email ou uma ação de web hook no grupo de ações associado à regra de alerta, receberá uma notificação de ativação em ambos.

Essa instância específica do acionamento de regra de alerta também pode ser exibida no portal do Azure na folha Todos os Alertas.

Se, por acaso, o uso de “myVM” continuar ficando acima do limite em verificações subsequentes, a regra de alerta não será acionada novamente até que a condição seja resolvida.

Após algum tempo, o uso de “myVM” volta ao normal, ou seja, abaixo do limite especificado. A regra de alerta monitora a condição mais duas vezes, para enviar uma notificação de resolução. A regra de alerta envia uma mensagem de resolução/desativação quando a condição de alerta não é atendida por três períodos consecutivos para reduzir o excesso em caso de condições intermitentes.

Como a notificação de resolução é enviada por email ou web hooks, o status da instância do alerta (chamado de estado do Monitor) no portal do Azure também é definido como resolvido.

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitoramento em escala usando alertas de métrica no Azure Monitor

### <a name="using-dimensions"></a>Usando dimensões

Os alertas de métrica no Azure Monitor também dão suporte ao monitoramento de várias combinações de valores de dimensão com uma regra. Vamos entender por que você pode usar várias combinações de dimensão com a ajuda de um exemplo.

Digamos que você tem um Plano do Serviço de Aplicativo para seu site. Você deseja monitorar o uso da CPU em várias instâncias de execução do site da Web/aplicativo. É possível fazer isso usando uma regra de alerta de métrica como mostrado abaixo

- Recurso de destino: myAppServicePlan
- Métrica: Porcentagem de CPU
- Dimensões
  - Instance = InstanceName1, InstanceName2
- Agregação de tempo: Média
- Período: Nos últimos 5 minutos
- Frequência: 1 minuto
- Operador: GreaterThan
- Limite: 70

Como antes, essa regra monitora se a média de uso da CPU nos últimos 5 minutos ultrapassou 70%. No entanto, com a mesma regra você pode monitorar duas instâncias em execução no site. Cada instância será monitorada individualmente e você receberá notificações individualmente.

Digamos que você tem um aplicativo Web que esteja sob grande demanda e será necessário adicionar mais instâncias. A regra acima ainda monitora apenas duas instâncias. No entanto, você pode criar uma regra como mostrado abaixo.

- Recurso de destino: myAppServicePlan
- Métrica: Porcentagem de CPU
- Dimensões
  - Instância = *
- Agregação de tempo: Média
- Período: Nos últimos 5 minutos
- Frequência: 1 minuto
- Operador: GreaterThan
- Limite: 70

Essa regra monitorará automaticamente todos os valores para a instância, ou seja, você pode monitorar suas instâncias conforme elas surgem sem precisar modificar a regra de alerta de métrica novamente.

### <a name="monitoring-multiple-resources-using-metric-alerts"></a>Monitoramento de vários recursos usando alertas de métrica

Como você viu na seção anterior, é possível ter uma única regra de alerta de métrica que monitora cada combinação de dimensão individual (ou seja, uma série temporal de métrica). No entanto, anteriormente havia a limitação de fazer isso em um recurso de cada vez. O Azure Monitor também dá suporte ao monitoramento de vários recursos com uma regra de alerta de métrica. Esse recurso está atualmente na versão prévia com suporte apenas em máquinas virtuais. Além disso, um único alerta de métrica pode monitorar os recursos em uma única região do Azure.

Você pode especificar o escopo de monitoramento de um único alerta de métrica de uma destas três maneiras:

- como uma lista de máquinas virtuais em uma região do Azure dentro de uma assinatura
- todas as máquinas virtuais (em uma única região do Azure) em um ou mais grupos de recursos em uma assinatura
- todas as máquinas virtuais (em uma região do Azure) em uma assinatura

Atualmente, não há suporte no portal do Azure para a criação de regras de alerta de métrica que monitoram vários recursos. Você pode criar essas regras por meio de [modelos do Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md#resource-manager-template-for-metric-alert-that-monitors-multiple-resources). Você receberá notificações individuais para cada máquina virtual. 

## <a name="typical-latency"></a>Latência típica

Para alertas de métrica, normalmente você será notificado em menos de 5 minutos se definir a frequência de regra de alerta para ser de 1 minuto. Em caso de carga pesada para sistemas de notificação, você poderá ter latência mais longa.

## <a name="supported-resource-types-for-metric-alerts"></a>Tipos de recursos com suporte para alertas de métrica

Você pode encontrar a lista completa dos tipos de recursos com suporte neste [artigo](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

Se você estiver usando alertas de métricas clássicas e procurando saber se os alertas de métricas dão suporte a todos os tipos de recursos sendo utilizados, a tabela a seguir mostra os tipos de recursos com suporte dos alertas de métrica clássicos e se atualmente há suporte para alertas de métricas ou não.

|Tipo de recurso com suporte dos alertas de métrica clássicos | Com suporte dos alertas de métrica |
|-------------------------------------------------|----------------------------|
| Microsoft.ApiManagement/service | SIM |
| Microsoft.Batch/batchAccounts| SIM|
|Microsoft.Cache/redis| SIM
|Microsoft.ClassicCompute/virtualMachines | Não  |
|Microsoft.ClassicCompute/domainNames/slots/roles | Não |
|Microsoft.CognitiveServices/accounts | Não  |
|Microsoft.Compute/virtualMachines | SIM|
|Microsoft.Compute/virtualMachineScaleSets| SIM|
|Microsoft.ClassicStorage/storageAccounts| Não  |
|Microsoft.DataFactory/datafactories | SIM|
|Microsoft.DBforMySQL/servers| SIM|
|Microsoft.DBforPostgreSQL/servers| SIM|
|Microsoft.Devices/IotHubs | Não |
|Microsoft.DocumentDB/databaseAccounts| Não |
|Microsoft.EventHub/namespaces | SIM|
|Microsoft.Logic/workflows | SIM|
|Microsoft.Network/loadBalancers |SIM|
|Microsoft.Network/publicIPAddresses| SIM|
|Microsoft.Network/applicationGateways| SIM|
|Microsoft.Network/expressRouteCircuits| SIM|
|Microsoft.Network/trafficManagerProfiles | SIM|
|Microsoft.Search/searchServices | Não |
|Microsoft.ServiceBus/namespaces| Não |
|Microsoft.Storage/storageAccounts | SIM|
|Microsoft.StreamAnalytics/streamingjobs| SIM|
|Microsoft.TimeSeriesInsights/environments | SIM|
|Microsoft. Web/serverfarms | SIM |
|Microsoft. Web/sites (excluindo funções) | SIM|
|Microsoft. Web/hostingEnvironments/multiRolePools | Não |
|Microsoft. Web/hostingEnvironments/workerPools| Não 
|Microsoft.SQL/Servers | Não |

## <a name="next-steps"></a>Próximas etapas

- [Saiba como criar, exibir e gerenciar alertas de métrica no Azure](alerts-metric.md)
- [Saiba como criar alertas de métrica usando modelos do Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Saiba mais sobre grupos de ação](action-groups.md)
