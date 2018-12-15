---
title: Gerenciar alertas de outros serviços de monitoramento no Azure Monitor
description: Gerenciamento de alertas do Nagios, Zabbix e SCOM no Azure Monitor
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: bc597d42fe89c0e03c4af1db3a935031b9043a98
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344831"
---
# <a name="manage-alerts-from-other-monitoring-services"></a>Gerenciar alertas de outros serviços de monitoramento

Agora você pode exibir os alertas do Nagios, Zabbix e do System Center Operations Manager na [experiência unificada de alertas](https://aka.ms/azure-alerts-overview). Esses alertas provenientes de integrações com servidores Nagios/Zabbix ou o System Center Operations Manager para o Log Analytics. 

## <a name="prerequisites"></a>Pré-requisitos
Quaisquer registros no repositório do Log Analytics com um tipo de alerta serão importados para a experiência unificada de alertas e, portanto, você deverá executar a configuração necessária para coletar esses registros.
1. Para alertas do **Nagios** e **Zabbix**, [configure esses servidores](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) para enviar alertas para o Log Analytics.
1. Para alertas do **System Center Operations Manager**, [conecte seu grupo de gerenciamento do Operations Manager para seu workspace do Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Todos os alertas criados no System Center Operations Manager são importados para o Log Analytics.

## <a name="view-your-alert-instances"></a>Exibir suas instâncias de alerta
Depois de configurar a importação no Log Analytics, você pode começar a visualizar as instâncias de alerta desses serviços de monitoramento na [experiência de alertas unificados](https://aka.ms/azure-alerts-overview). Quando eles estiverem presentes na experiência unificada de alertas, você poderá [gerenciar suas instâncias de alerta](https://aka.ms/managing-alert-instances), [gerenciar grupos inteligentes criados nesses alertas](https://aka.ms/managing-smart-groups) e [alterar o estado dos seus alertas e grupos inteligentes](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  Os alertas do Nagios na experiência unificada de alertas não tem estado, por exemplo, a [condição de monitoramento](https://aka.ms/azure-alerts-overview) de um alerta não mudará de “Disparado” para “Resolvido”. Em vez disso, "Disparado" e "Resolvido" são exibidos como instâncias de alerta separadas. 
