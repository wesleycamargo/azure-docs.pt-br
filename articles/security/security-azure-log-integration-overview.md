---
title: Integre os registros de recursos do Azure aos seus sistemas SIEM | Microsoft Docs
description: Saiba mais sobre a Integração de Logs do Azure, seus principais recursos e como ela funciona.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 6d91692a64a4d3def80990a439fe0a0898bf2f09
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-azure-log-integration"></a>Introdução à Integração de Logs do Azure

Você pode usar a Integração do Logs do Azure para integrar registros brutos de recursos do Azure a seus sistemas de SIEM (Segurança da Informação e Gerenciamento de Evento) locais. Use a Integração de Logs do Azure apenas se um conector para [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) não for disponibilizada pelo seu fornecedor SIEM.

É o método preferencial para integrar os logs do Azure usando o conector do Azure Monitor do seu fornecedor SIEM. Para usar o conector, siga as instruções em [Monitorar o monitoramento de transmissão para hubs de eventos de dados](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). 

No entanto, se o fornecedor do SIEM não fornecer um conector para o Azure Monitor, você poderá usar a Integração de Logs do Azure como solução temporária até que o conector esteja disponível. A Integração de Logs do Azure é uma opção apenas se a Integração de Logs do Azure oferece suporte ao seu SIEM.

> [!IMPORTANT]
> Se o seu interesse principal é coletar logs de máquina virtual, a maioria dos fornecedores de SIEM inclui essa opção em sua solução. O uso do conector do fornecedor SIEM sempre é a opção preferencial.

A Integração de Logs do Azure coleta eventos do Windows de logs do Visualizador de Eventos do Windows, [Logs de atividades do Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), [Alertas da Central de Segurança do Azure](../security-center/security-center-intro.md) e [Logs de Diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) de recursos do Azure. A integração ajuda a sua solução de SIEM a oferecer um painel unificado para todos os seus ativos, locais ou na nuvem. Você pode usar um painel para receber, agregar, correlacionar e analisar os alertas para eventos de segurança.

> [!NOTE]
> Atualmente, a Integração de Logs do Azure é compatível apenas com as nuvens do Azure comercial e Azure Governamental. Não há suporte para outras nuvens.

![Diagrama do processo de Integração de Logs do Azure][1]

## <a name="what-logs-can-i-integrate"></a>Quais logs posso integrar?

O Azure produz um log abrangente para cada um de seus serviço. Os logs representam três tipos de log:

* **Logs de controle/gerenciamento**: fornecem visibilidade das operações CREATE, UPDATE e DELETE do [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). O log de atividades do Azure é um exemplo desse tipo de log.
* **Logs de plano de dados**: dão visibilidade a eventos que são criados quando você usa um recurso do Azure. Um exemplo desse tipo de log são os canais **Sistema**, **Segurança** e **Aplicativo** do Visualizador de Eventos do Windows em uma máquina virtual Windows. Outro exemplo é o Log de Diagnóstico do Azure que você configura por meio do Azure Monitor.
* **Eventos processados**: fornecem informações de eventos e alertas que são processados para você. Um exemplo desse tipo de evento são os alertas da Central de Segurança do Azure. A Central de Segurança do Azure processa e analisa sua assinatura para fornecer alertas que são relevantes para sua postura de segurança atual.

A integração de Log do Azure dá suporte a ArcSight, QRadar e Splunk. Entre em contato com o fornecedor do SIEM para avaliar se ele tem um conector nativo. Não use a Integração de Logs do Azure se um conector nativo estiver disponível.

Se nenhuma outra opção estiver disponível, considere usar a Integração de Logs do Azure. A tabela a seguir inclui nossas recomendações:

|SIEM | O cliente já usa o Integrador de Logs do Azure | O cliente está avaliando as opções de integração do SIEM|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Inicie a migração para o [complemento do Azure Monitor para Splunk](https://splunkbase.splunk.com/app/3534/). | Use o [conector Splunk](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Migre para ou comece a usar o conector QRadar documentado na última seção [Transmitir dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). | Use o conector QRadar documentado na última seção [Transmitir dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). |
|**ArcSight** | Continue a usar o Integrador de Logs do Azure até que um conector esteja disponível. Em seguida, migre para a solução baseada no conector.  | Considere usar o Log Analytics do Azure como alternativa. Não integre à Integração de Logs do Azure, a menos que você esteja disposto a passar pelo processo de migração quando o conector estiver disponível. |

> [!NOTE]
> Embora a Integração do Log do Azure seja uma solução gratuita, há custos de armazenamento do Azure associados ao armazenamento de informações do arquivo de log.

Se você precisar de ajuda, pode criar uma [solicitação de suporte](../azure-supportability/how-to-create-azure-support-request.md). Para o serviço, selecione **Integração de Logs**.

## <a name="next-steps"></a>Próximas etapas

Este artigo apresenta a Integração de Logs do Azure. Para saber mais sobre a Integração de Logs do Azure e os tipos de logs com suporte, confira os seguintes artigos:

* [Introdução à integração de log do Azure](security-azure-log-integration-get-started.md). Este tutorial orienta você durante a instalação da Integração de Logs do Azure. Ele também descreve como integrar os logs do armazenamento do Diagnóstico do Microsoft Azure (WAD), os logs de atividades do Azure, os alertas da Central de Segurança do Azure e os logs de auditoria do Azure Active Directory.
* [Perguntas frequentes sobre a Integração de Logs do Azure](security-azure-log-integration-faq.md). Este artigo de perguntas comuns responde às perguntas sobre a Integração de Logs do Azure.
* Saiba mais sobre como [Transmitir os dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
