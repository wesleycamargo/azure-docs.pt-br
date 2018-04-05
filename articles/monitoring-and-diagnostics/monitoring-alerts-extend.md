---
title: Estender alertas do OMS para o Azure - Visão Geral | Microsoft Docs
description: Visão geral do processo de estender alertas do OMS para os Alertas do Azure, detalhes sobre preocupações comuns do cliente.
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
ms.date: 03/16/2018
ms.author: vinagara
ms.openlocfilehash: 045a7f97d9c4d380e83325c04c209a6afcc761a7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="extend-alerts-from-oms-into-azure"></a>Estender alertas do OMS para o Azure
A nova experiência de Alertas agora integra a experiência de alertas em vários serviços e peças no Microsoft Azure. A nova experiência disponível como **Alertas** no Azure Monitor no Portal foi colocada em um local comum - alertas de log de atividades, alertas de métricas e alertas de log no Log Analytics, bem como no Application Insights. 

Mas para alguns usuários, o uso do Log Analytics e funcionalidade aliada como alertas, passou pelo [Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). E, portanto, para permitir que eles gerenciem facilmente seus recursos do Azure junto com o uso do Log Analytics, a Microsoft tem sistematicamente garantindo que os recursos do OMS também estejam disponíveis no Portal do Azure. Com relação a isso, os alertas do Azure já permitem que os usuários gerenciem alertas com base em consulta para o Log Analytics. Para obter mais informações, consulte [Registrar alertas nos Alertas do Azure](monitor-alerts-unified-log.md). Em Alertas, no Azure Monitor, os alertas criados no OMS já estão listados no espaço de trabalho apropriado do Log Analytics. Mas qualquer edição ou alteração nesses alertas criados no OMS exigem que o usuário saia do Azure e use o OMS; em seguida, retorne ao Azure se precisarem gerenciar qualquer outro serviço. Para reduzir essa dificuldade, a Microsoft agora está permitindo que os usuários estendam os alertas do OMS para o Azure.

## <a name="benefits-of-extending-your-alerts"></a>Benefícios de estender seus alertas
Além do benefício acumulado de não precisar navegar fora do Portal do Azure, há outras vantagens evidentes em estender alertas do OMS para o Azure

- Ao contrário do OMS, onde somente 250 alertas podiam ser criados e exibidos, nos Alertas do Azure essa limitação não está presente
- Nos Alertas do Azure, todos os tipos de alertas podem ser gerenciados, enumerados e exibidos, não apenas os alertas do Log Analytics, como o é caso com o OMS
- Os Alertas do Azure utilizam [Grupos de Ação](monitoring-action-groups.md), que permitem que você tenha para cada alerta mais de uma ação, incluindo SMS, chamada de voz, runbook de automação, webhook, Conector ITSM e muito mais. Enquanto no OMS os alertas estão limitados tanto no número quanto no tipos de ações possíveis

## <a name="process-of-extending-your-alerts"></a>Processo de extensão dos alertas
O processo de extensão de alertas do OMS para o Azure **não** envolve a alteração de sua definição, consulta ou configuração de alertas de nenhuma forma. A única alteração necessária é que, no Azure, todas as ações, como notificação por email, chamada de webhook, execução de runbook de automação ou conexão com a ferramenta ITSM, são feitas por meio de Grupo de Ação. Portanto, se o grupo de ação apropriado for associado ao seu alerta, ele se tornará estendidos para o Azure.

Como o processo de extensão não é destrutivo e não possui interrupções, a Microsoft estenderá os alertas criados no OMS (Operations Management Suite) para alertas do Azure automaticamente - a partir de **23 de abril de 2018**. Após essa data, a Microsoft começar a programar a extensão dos alertas para o Azure e gradualmente tornará todos os alertas presentes no OMS gerenciáveis no Portal do Azure. 

Quando os alertas no espaço de trabalho do OMS forem agendados para extensão para o Azure, eles continuarão a funcionar e **não** comprometerão de forma alguma o monitoramento. Quando agendados, os alertas podem ficar indisponíveis para modificação/edição temporariamente; mas novos alertas do Azure podem continuar a ser criados neste breve período. Nesse breve período, se qualquer edição ou criação de alerta for feita no OMS, os usuários terão a opção de continuar no Log Analytics do Azure ou nos Alertas do Azure.

 ![Durante o período agendado, a ação do usuário em alertas é redirecionada para o Azure](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Estender alertas do OMS (Operations Management Suite) para o Azure não será cobrado e o uso de Alertas do Azure para consulta com base em alertas do Log Analytics não será faturado quando usado dentro dos limites e condições estabelecidos na [política de preços do Azure Monitor](https://azure.microsoft.com/en-us/pricing/details/monitor/)  

Os usuários podem aproveitar os benefícios da extensão de alertas antes dessa data aceitando voluntariamente tornar seus alertas gerenciáveis no Azure.

### <a name="how-to-voluntarily-extending-your-alerts"></a>Como estender voluntariamente seus alertas
Para permitir aos usuários do OMS uma transição fácil para os Alertas do Azure, a Microsoft criou ferramentas para habilitar a expansão dos alertas. Os clientes do OMS (Microsoft Operations Management Suite) podem estender os alertas para o Azure a partir de um assistente no portal do OMS (ou) por uma abordagem programática, usando uma nova API. Para obter mais informações, consulte [Estendendo alertas para o Azure usando o portal do OMS e a API](monitoring-alerts-extend-tool.md).


## <a name="usage-after-extending-your-alerts"></a>Uso após a extensão dos alertas
Conforme mencionado, alertas criados no Pacote de Gerenciamento do Microsoft Operation serão estendidos para os Alertas do Azure; após isso, você pode gerenciá-los no Azure. Os alertas continuarão a ser listados no portal do OMS - seção Configuração de alertas. Conforme ilustrado abaixo:

 ![Portal do OMS listando alertas depois de serem estendidos para o Azure](./media/monitor-alerts-extend/PostExtendList.png)

Para qualquer operação em alertas como edição ou criação feito no portal do OMS, os usuários serão direcionados de forma transparente para os Alertas do Azure. A criação de alertas continuará na [API do Log Analytics](../log-analytics/log-analytics-api-alerts.md) existente como antes, com algumas pequenas alterações depois que os alertas forem estendidos para o Azure - grupos de ação precisariam estar associados na programação.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as ferramentas para [iniciar a extensão de alertas do OMS para o Azure](monitoring-alerts-extend-tool.md)
* Saiba mais sobre a nova [experiência de alertas do Azure](monitoring-overview-unified-alerts.md).
* Saiba mais sobre os [Alertas de log nos Alertas do Azure](monitor-alerts-unified-log.md).
