---
title: Gerenciar grupos inteligentes
description: Como gerenciar Grupos Inteligentes criados de acordo com suas instâncias de alerta
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: d04684e7d4eed1b997ec803dc9ccc1dcc079bde0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959369"
---
# <a name="manage-smart-groups"></a>Gerenciar grupos inteligentes
Os [Grupos inteligentes](https://aka.ms/smart-groups) usam algoritmos de aprendizado de máquina para agrupar alertas juntos com base na co-ocorrência ou a similaridade, para que o usuário agora possa gerenciar grupos inteligentes em vez de precisar gerenciar cada alerta individualmente. Este artigo explica como acessar e usar grupos inteligentes no Azure Monitor.
1.  Para ver os Grupos Inteligentes criados para suas instâncias de alerta, você pode
     1. Clique em **Grupos Inteligentes** da página **Resumo de Alertas**    
    ![Monitoramento](./media/monitoring-alerts-managing-smart-groups/sg-alerts-summary.jpg)
     2. Clique em Alertas por Grupos Inteligentes da página Todos os Alertas   
     ![Monitoramento](./media/monitoring-alerts-managing-smart-groups/sg-all-alerts.jpg)
2.  Isso leva você até a exibição de lista de todos os Grupos Inteligentes criados ao longo de suas instâncias de alerta. Em vez de precisar examinar vários alertas, agora você pode lidar com os grupos inteligentes.   
![Monitoramento](./media/monitoring-alerts-managing-smart-groups/sg-list.jpg)
3.  Clicar em qualquer Grupo Inteligente abre a página de detalhes, onde você pode ver o motivo de agrupamento, juntamente com os alertas de membro. Essa agregação permite que você lide com um grupo inteligente singular, em vez de precisar examinar vários alertas.   
![Monitoramento](./media/monitoring-alerts-managing-smart-groups/sg-details.jpg)

