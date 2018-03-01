---
title: Integre os registros de recursos do Azure nos seus sistemas SIEM | Microsoft Docs
description: "Saiba mais sobre a integração de log do Azure, seus principais recursos e como ela funciona."
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
ms.openlocfilehash: e427e2f7dafb6db9bc5c15d841fbbf83a02fc0e1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Introdução à integração de log do Microsoft Azure

A integração de log do Azure permite que você integre logs brutos de recursos do Azure com seus sistemas SIEM (Segurança da Informação e Gerenciamento de Evento) locais no caso em que um conector para o [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) não está disponível do seu fornecedor SIEM.

É o método preferencial para integrar os logs do Azure usando o conector do Azure Monitor do seu fornecedor SIEM e seguindo estas [instruções](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). No entanto, se o fornecedor do SIEM não fornecer um conector para o Azure Monitor, você poderá usar a Integração de Logs do Azure como solução temporária (se o SIEM for compatível com a Integração de Logs do Azure) até que o conector esteja disponível.

>[!IMPORTANT]
>Se seu interesse principal é coletar logs de máquina virtual, a maioria dos fornecedores SIEM inclui isso em sua solução. O uso do conector do fornecedor SIEM sempre será a opção preferencial.

A Integração do Log do Azure coleta eventos do Windows de logs do Visualizador de Eventos do Windows, [Logs de Atividades do Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), [Alertas da Central de Segurança do Azure](../security-center/security-center-intro.md) e [Logs de Diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) de recursos do Azure. Esta integração ajuda sua solução de SIEM a fornecer um painel unificado para todos os seus ativos, locais ou na nuvem, para que você possa agregar, correlacionar, analisar e emitir alertas de eventos de segurança.

>[!NOTE]
No momento, as únicas nuvens com suporte são o Azure comercial e Azure Governamental. Não há suporte para outras nuvens.

![Integração de log do Azure][1]

## <a name="what-logs-can-i-integrate"></a>Quais logs posso integrar?

O Azure produz um log abrangente para cada um de seus serviço. Esses logs representam três tipos de logs:

* **Logs de controle/gerenciamento**, que fornecem visibilidade das operações CREATE, UPDATE e DELETE do [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Os Logs de Atividades do Azure são um exemplo desse tipo de log.
* **Logs do plano de dados**, que fornecem visibilidade dos eventos gerados como parte do uso de um recurso do Azure. Um exemplo desse tipo de log são os canais **Sistema**, **Segurança** e **Aplicativo** do Visualizador de Eventos do Windows em uma máquina virtual Windows. Outro exemplo é o Log de Diagnóstico configurado por meio do Azure Monitor
* **Eventos processados** fornecem informações de eventos e alertas processados em seu nome. Um exemplo desse tipo de evento são os Alertas da Central de Segurança do Azure, em que a Central de Segurança do Azure processou e analisou sua assinatura para fornecer alertas relevantes para sua postura de segurança atual.

A integração de Log do Azure dá suporte a ArcSight, QRadar e Splunk. Em todas as circunstâncias, entre em contato com o fornecedor do SIEM para avaliar se eles têm um conector nativo. Você não deve usar a Integração de Logs do Azure quando conectores nativos estiverem disponíveis.

Se não houver nenhuma das outras opções disponíveis, a Integração do Logs do Azure pode ser considerada. A tabela a seguir inclui nossas recomendações.

|**SIEM** | **Cliente já usa o integrador de log** | **Cliente avaliando as opções de integração do SIEM**|
|---------|--------------------------|-------------------------------------------|
|**SPLUNK** | Inicie a migração para o [complemento do monitor do Azure para Splunk](https://splunkbase.splunk.com/app/3534/) | Use [conector SPLUNK](https://splunkbase.splunk.com/app/3534/) |
|**IBM QRADAR** | Migre para ou comece a usar o conector QRadar documentado no final da http://aka.ms/azmoneventhub | Use o conector QRadar documentado no final da http://aka.ms/azmoneventhub  |
|**ARCSIGHT** | Continue a usar o integrador de Log até que um conector esteja disponível, em seguida, migre para a solução baseada no conector.  | Considere o Log Analytics do Azure como uma alternativa. Não integre à Integração de Logs do Azure, a menos que você esteja disposto a passar pelo processo de migração quando o conector estiver disponível. |

>[!NOTE]
>Embora a Integração do Log do Azure seja uma solução gratuita, há custos de armazenamento do Azure resultantes do armazenamento de informações do arquivo de log.

Se você precisar de ajuda pode abrir uma [solicitação de suporte](../azure-supportability/how-to-create-azure-support-request.md). Para fazer isso, selecione **integração de log** como o serviço para o qual você está solicitando suporte.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você foi apresentado à integração de log do Azure. Para saber mais sobre a integração de log do Azure e os tipos de logs com suporte, confira o seguinte:

* [Introdução à integração do log do Azure](security-azure-log-integration-get-started.md) — este tutorial explica as etapas de instalação da integração do log do Azure, bem como a integração de logs do armazenamento WAD do Azure, Logs de Atividades do Azure, alertas da Central de Segurança do Azure e logs de auditoria do Azure Active Directory.
* [Perguntas frequentes sobre o log de integração do Azure](security-azure-log-integration-faq.md) – encontre as respostas para as perguntas frequentes sobre a integração de log do Azure.
* [Transmitir os dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
