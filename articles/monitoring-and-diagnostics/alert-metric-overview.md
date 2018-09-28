---
title: Entender como funcionam os alertas de métrica no Azure Monitor.
description: Obtenha uma visão geral do que você pode fazer com alertas de métrica e como eles funcionam no Azure Monitor.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.component: alerts
ms.openlocfilehash: 1ec47ddf5769dd8ed624277a86db57f449581b90
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948682"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Entender como funcionam os alertas de métrica no Azure Monitor

Os alertas de métrica no Azure Monitor funcionam com métricas multidimensionais. Essas métricas podem ser métricas de plataforma, métricas personalizadas (versão prévia), logs populares do Log Analytics convertidos em métricas ou métricas padrão do Application Insights. Os alertas de métrica verificam, em intervalos regulares, se as condições em uma métrica ou em uma série temporal delas são verdadeiras e notificam você quando as condições são atendidas. Os alertas de métrica são baseados no estado, ou seja, eles só enviam notificações quando o estado é alterado.

## <a name="how-do-metric-alerts-work"></a>Como funcionam os alertas de métrica

Você pode definir uma regra de alerta de métrica especificando um recurso de destino para ser monitorado, o nome e a condição da métrica (um operador e um limite) e um grupo de ação a ser disparado quando a regra de alerta é acionada.
Digamos que você tenha criado uma regra de alerta de métrica simples da seguinte maneira:

- Recurso de destino (o recurso do Azure que você deseja monitorar): myVM
- Métrica: porcentagem de CPU
- Agregação de tempo (estatística que é executada sobre os valores brutos de métrica. As agregações de tempo com suporte são Mín., Máx., Média, Total): média
- Período (a janela de pesquisa na qual os valores de métrica são verificados): nos últimos 5 minutos
- Frequência (a frequência com que o alerta de métrica verifica se as condições foram atendidas): 1 min
- Operador: maior que
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
- Métrica: porcentagem de CPU
- Dimensões
  - Instance = InstanceName1, InstanceName2
- Agregação de tempo: média
- Período: nos últimos 5 minutos
- Frequência: 1 min
- Operador: GreaterThan
- Limite: 70

Como antes, essa regra monitora se a média de uso da CPU nos últimos 5 minutos ultrapassou 70%. No entanto, com a mesma regra você pode monitorar duas instâncias em execução no site. Cada instância será monitorada individualmente e você receberá notificações individualmente.

Digamos que você tem um aplicativo Web que esteja sob grande demanda e será necessário adicionar mais instâncias. A regra acima ainda monitora apenas duas instâncias. No entanto, você pode criar uma regra como mostrado abaixo.

- Recurso de destino: myAppServicePlan
- Métrica: porcentagem de CPU
- Dimensões
  - Instância = *
- Agregação de tempo: média
- Período: nos últimos 5 minutos
- Frequência: 1 min
- Operador: GreaterThan
- Limite: 70

Essa regra monitorará automaticamente todos os valores para a instância, ou seja, você pode monitorar suas instâncias conforme elas surgem sem precisar modificar a regra de alerta de métrica novamente.

### <a name="monitoring-multiple-resource-using-metric-alerts"></a>Monitorando vários recursos usando alertas de métrica

Como você viu na seção anterior, é possível ter uma única regra de alerta de métrica que monitora cada combinação de dimensão individual (ou seja, uma série temporal de métrica). No entanto, você ainda está limitado a fazê-la um recurso por vez. Os alertas de métrica também dão suporte ao monitoramento de vários recursos com uma regra na versão prévia. Se você tiver centenas de VMs em sua assinatura, esse novo recurso ajuda a configurar rapidamente o monitoramento para elas. 

Esse recurso está atualmente na visualização. Atualmente, não há suporte no portal do Azure para a criação de regras de alerta de métrica que monitoram vários recursos. Você pode criar essas regras por meio de modelos do Azure Resource Manager.

## <a name="typical-latency"></a>Latência típica

Para alertas de métrica, normalmente você será notificado em menos de 5 minutos se definir a frequência de regra de alerta para ser de 1 minuto. Em caso de carga pesada para sistemas de notificação, você poderá ter latência mais longa.

## <a name="supported-resource-types-for-metric-alerts"></a>Tipos de recursos com suporte para alertas de métrica

Você pode encontrar a lista completa dos tipos de recursos com suporte neste [artigo](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported)

## <a name="next-steps"></a>Próximas etapas

- [Saiba como criar, exibir e gerenciar alertas de métrica no Azure](alert-metric.md)
- [Saiba como criar alertas de métrica usando modelos do Azure Resource Manager](monitoring-create-metric-alerts-with-templates.md)
- [Saiba mais sobre grupos de ação](monitoring-action-groups.md)
