---
title: Arquivo de inclusão
description: Arquivo de inclusão
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: cf07d19966c08a63b9aa50475622aa0a1e5e1600
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236527"
---
## <a name="business-disaster-recovery"></a>Recuperação de desastre de negócios

Esta seção descreve os recursos do Azure Time Series Insights que mantêm os aplicativos e serviços em execução, mesmo se ocorrer um desastre (**recuperação de desastres de negócios**).

### <a name="high-availability"></a>Alta disponibilidade

Como um serviço do Azure, o Time Series Insights fornece certas **alta disponibilidade** recursos com redundâncias no nível da região do Azure. Por exemplo, o Microsoft Azure dá suporte à recuperação de desastre por meio do Azure **disponibilidade de região cruzada** recurso.

Recursos adicionais de alta disponibilidade fornecidos por meio do Azure (e também está disponível em qualquer instância do Time Series Insights) incluem:

1. **Failover**: O Azure fornece [replicação geográfica e carregando balanceamento](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
1. **Restauração de dados** e **recuperação do armazenamento**: O Azure fornece [várias opções para preservar e recuperar dados](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
1. **Recuperação de site** recursos por meio [do Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Certifique-se de habilitar esses recursos do Azure fornecer global, entre regiões, alta disponibilidade para seus dispositivos e usuários.

> [!NOTE]
> Se o Azure está configurado para habilitar **disponibilidade de região cruzada**, nenhuma configuração de disponibilidade de região cruzada adicional é necessária no Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT e hubs de eventos

Alguns serviços de IoT do Azure também incluem recursos de recuperação de desastres de negócios integrados:

1. [Recuperação de desastres de alta disponibilidade do IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr) incluindo redundância entre regiões.
1. [Políticas de Hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).
1. [Redundância de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

A integração com esses outros serviços do Time Series Insights fornece desastres adicionais oportunidades de recuperação. Por exemplo, a telemetria enviada ao Hub de eventos pode ser persistente para um backup de banco de dados do armazenamento de BLOBs do Azure.

### <a name="time-series-insights"></a>Time Series Insights

Há várias maneiras de manter seus dados de séries temporais, aplicativos e serviços em execução, mesmo se elas são interrompidas. Você também pode determinar que uma cópia completa, duplicada, o backup do seu ambiente Azure Time Series é necessária:

1. Como um específico TSI **instância failover** para redirecionar dados e para o tráfego.
1. Para fins de preservação de dados e de auditoria.

Em geral, a melhor maneira para duplicar um ambiente do TSI é criar um ambiente de TSI de segundo em um backup de região do Azure. Eventos também são enviados para esse ambiente secundário da fonte de evento principal. Certifique-se de usar um grupo de consumidores dedicado, segundo e seguir as diretrizes de recuperação desastre business que da origem (fornecidas acima).

Especificamente, para criar um ambiente duplicado:

1. Criar um ambiente em uma segunda região ([criar um novo ambiente do Time Series Insights no portal do Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)).
1. Crie um segundo grupo de consumidores dedicado para a origem do evento.
1. Conecte-se essa origem do evento para o novo ambiente, certificando-se designar o grupo de consumidores dedicado segundo.
1. Examine o Time Series Insights [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) e [hub de eventos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) documentação.

Por fim:

* Se a região primária for afetada durante um incidente de desastres, redirecione as operações para o backup do ambiente do Time Series Insights.
* Use sua segunda região para fazer backup e recuperar todos os dados de telemetria e consulta TSI.

> [!IMPORTANT]
> * Pode haver atraso no evento no caso de failover.
> * Failover também pode causar um aumento momentâneo no processamento de mensagem, como operações são redirecionadas.
> * Para saber mais, confira [Reduzir a latência no Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).