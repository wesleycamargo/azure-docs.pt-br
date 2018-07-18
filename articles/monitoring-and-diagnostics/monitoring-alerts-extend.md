---
title: Estender (copiar) alertas do Portal do OMS para o Azure - Visão geral | Microsoft Docs
description: Visão geral do processo de copiar alertas do Portal do OMS para os Alertas do Azure, detalhes sobre as preocupações comuns dos clientes.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: vinagara
ms.openlocfilehash: 25dcbad8607a651a7dd4b79f4f418cc473a2bf0e
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166482"
---
# <a name="extend-copy-alerts-from-oms-portal-into-azure"></a>Estender (copiar) alertas do Portal do OMS para o Azure
O Portal do OMS (Microsoft Operations Management Suite) mostra apenas alertas do Log Analytics.  A nova experiência de alertas agora integrou a experiência de alertas em vários serviços e partes no Microsoft Azure. A nova experiência disponível como **Alertas** no Azure Monitor no Portal do Azure contém alertas de log de atividades, alertas de métrica e alertas de log para o Log Analytics e o Application Insights. 


Mas para alguns usuários, o uso do Log Analytics e de funcionalidades aliadas, como alertas, passou pelo [Portal do OMS (Microsoft Operation Management Suite)](../operations-management-suite/operations-management-suite-overview.md). E, portanto, para permitir que eles gerenciem facilmente seus recursos do Azure junto com o uso do Log Analytics, a Microsoft tem sistematicamente garantindo que os recursos do Portal do OMS também estejam disponíveis no Portal do Azure. Com relação a isso, os alertas do Azure já permitem que os usuários gerenciem alertas com base em consulta para o Log Analytics. Para obter mais informações, consulte [Registrar alertas nos Alertas do Azure](monitor-alerts-unified-log.md). Em Alertas, no Azure Monitor, os alertas criados no Portal do OMS já estão listados no espaço de trabalho apropriado do Log Analytics. Mas, qualquer edição ou alteração nesses alertas criada no Portal do OMS exigirá que o usuário saia do Azure e utilize o Portal do OMS e, depois, retorne ao Azure se precisar gerenciar qualquer outro serviço. Para reduzir essa dificuldade, a Microsoft agora está permitindo que os usuários estendam os alertas do Portal do OMS para o Azure.

## <a name="benefits-of-extending-your-alerts"></a>Benefícios de estender seus alertas
Além do benefício acumulado de não precisar navegar fora do Portal do Azure, há outras vantagens evidentes em estender alertas do Portal do OMS para o Azure

- Ao contrário do Portal do OMS, onde apenas 250 alertas podiam ser criados e exibidos, nos Alertas do Azure não há essa limitação
- Nos Alertas do Azure, todos os tipos de alertas podem ser gerenciados, enumerados e exibidos, não somente alertas do Log Analytics, como é o caso do Portal do OMS
- Controlar o acesso aos usuários para apenas Monitoramento e Alertas, usando a [função do Azure Monitor](monitoring-roles-permissions-security.md)
- Os Alertas do Azure utilizam [Grupos de Ações](monitoring-action-groups.md),que permitem que você tenha mais de uma ação para cada alerta, incluindo SMS, Chamada de Voz, Runbook de Automação, Webhook, Conector de Gerenciamento de Serviços de TI, e muito mais. 

## <a name="process-of-extending-your-alerts"></a>Processo de extensão dos alertas
O processo de extensão de alertas do Portal do OMS para o Azure **não** envolve a alteração de sua definição, consulta ou configuração de alertas de nenhuma forma. A única alteração necessária é que, no Azure, todas as ações, como notificação por email, chamada de webhook, execução de runbook de automação ou conexão com a ferramenta ITSM, são feitas por meio de Grupo de Ação. Portanto, se o grupo de ação apropriado for associado ao seu alerta, ele se tornará estendidos para o Azure.

Como o processo de extensão não é destrutivo e não possui interrupções, a Microsoft estenderá automaticamente os alertas criados no Portal do OMS para os alertas do Azure - a partir de **14 de maio de 2018**. A partir deste dia, a Microsoft começará a programar a extensão dos alertas para o Azure e, gradualmente, disponibilizará todos os alertas no Portal do OMS, gerenciáveis a partir do Portal do Azure também. 

> [!NOTE]
> A partir de 14 de maio de 2018 - a Microsoft iniciará o processo de estender automaticamente os alertas para o Azure. Nem todos os espaços de trabalho e alertas serão estendidos nesse dia; em vez disso, o Microsoft começará a estender alertas automaticamente em partes nas próximas semanas. Portanto, alertas no portal do OMS não serão autoestendidos no Azure imediatamente em 14 de maio de 2018, e o usuário poderá ainda [manualmente estender seus alertas](monitoring-alerts-extend-tool.md) durante esse tempo.

Quando os alertas em um espaço de trabalho do Log Analytics forem agendados para estender para o Azure, eles continuarão a funcionar e **não** comprometerão o monitoramento. Quando agendados, os alertas podem ficar indisponíveis para modificação/edição temporariamente; mas novos alertas do Azure podem continuar a ser criados neste breve período. Nesse breve período, se qualquer edição ou criação de alerta for feita no Portal do OMS, os usuários terão a opção de continuar no Log Analytics do Azure ou nos Alertas do Azure.

 ![Durante o período agendado, a ação do usuário em alertas é redirecionada para o Azure](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Não há encargos para a extensão de alertas do Portal do OMS para o Azure e o uso de alertas do Azure para alertas do Log Analytics baseados em consulta não será cobrado, quando usado dentro das condições e dos limites definidos na [Política de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/)  

Os usuários podem aproveitar os benefícios da extensão de alertas antes dessa data aceitando voluntariamente tornar seus alertas gerenciáveis no Azure.

### <a name="how-to-voluntarily-extending-your-alerts"></a>Como estender voluntariamente seus alertas
Para permitir aos usuários do OMS uma transição fácil para os Alertas do Azure, a Microsoft criou ferramentas para habilitar a expansão dos alertas. Os clientes do Portal do Microsoft OMS podem estender seus alertas para o Azure a partir de um assistente no Portal do OMS (ou) por meio de uma abordagem programática usando uma nova API. Para obter mais informações, consulte [Estendendo alertas para o Azure usando o portal do OMS e a API](monitoring-alerts-extend-tool.md).


## <a name="usage-after-extending-your-alerts"></a>Uso após a extensão dos alertas
Conforme mencionado, alertas criados no Pacote de Gerenciamento do Microsoft Operation serão estendidos para os Alertas do Azure; após isso, você pode gerenciá-los no Azure. Os alertas continuarão a ser listados no portal do OMS - seção Configuração de alertas. Conforme ilustrado abaixo:

 ![Portal do OMS listando alertas depois de serem estendidos para o Azure](./media/monitor-alerts-extend/PostExtendList.png)

Para qualquer operação em alertas como edição ou criação feito no portal do OMS, os usuários serão direcionados de forma transparente para os Alertas do Azure. 

> [!NOTE]
> Como os usuários serão transparentemente direcionados para o Azure, em qualquer ação de adição ou edição em um alerta no OMS, certifique-se de que os usuários sejam mapeados corretamente com as devidas [permissões para usar o Azure Monitor e Alertas](monitoring-roles-permissions-security.md)

A criação de alertas continuará na [API do Log Analytics](../log-analytics/log-analytics-api-alerts.md) e no [Modelo de Recursos do Log Analytics](../monitoring/monitoring-solutions-resources-searches-alerts.md) existentes como antes, com algumas pequenas alterações depois que os alertas forem estendidos para o Azure - grupos de ação precisariam estar associados na programação.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as ferramentas para [iniciar a extensão de alertas do OMS para o Azure](monitoring-alerts-extend-tool.md)
* Saiba mais sobre a nova [experiência de alertas do Azure](monitoring-overview-unified-alerts.md).
* Saiba mais sobre os [Alertas de log nos Alertas do Azure](monitor-alerts-unified-log.md).
