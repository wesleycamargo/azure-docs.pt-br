---
title: Integre os logs de recursos do Azure aos seus sistemas SIEM | Microsoft Docs
description: Saiba mais sobre a Integração de Logs do Azure, seus principais recursos e como ela funciona.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: c644d81b647fc1151b10a94fb636e3eeaba1ce5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60478287"
---
# <a name="introduction-to-azure-log-integration"></a>Introdução à Integração de Logs do Azure

>[!IMPORTANT]
> O recurso Integração de log do Azure será preterido em 01/06/2019. Downloads de AzLog foram desabilitados em 27 de junho de 2018. Para obter diretrizes sobre o que fazer para prosseguir com a análise da postagem [Usar o Azure Monitor para a integração com ferramentas SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

A Integração do Logs do Azure foi disponibilizada para simplificar a tarefa de integrar registros do Azure aos seus sistemas de SIEM (Segurança da Informação e Gerenciamento de Evento) locais.

 O método recomendado para a integração de logs do Azure é usar conectores do seu fornecedor de SIEM. O Azure Monitor oferece a capacidade de transmitir os logs em hubs de eventos, e os fornecedores de SIEM podem gravar conectores para integrá-los ainda mais no SIEM.  Para ver uma descrição de como isso funciona, siga as instruções em [Monitorar o monitoramento de transmissão para hubs de eventos de dados](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). O artigo também lista os SIEMs nos quais conectores diretos do Azure já estão disponíveis.  

> [!IMPORTANT]
> Se o seu interesse principal é coletar logs de máquina virtual, a maioria dos fornecedores de SIEM inclui essa opção em sua solução. O uso do conector do fornecedor SIEM sempre é a opção preferencial.

A documentação sobre o recurso de Integração de Logs do Azure será mantida até o recurso ser preterido.

Leia para saber mais sobre o recurso de Integração de Logs do Azure:

A Integração de Logs do Azure coleta eventos do Windows de logs do Visualizador de Eventos do Windows, [Logs de atividades do Azure](../azure-monitor/platform/activity-logs-overview.md), [Alertas da Central de Segurança do Azure](../security-center/security-center-intro.md) e [Logs de Diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md) de recursos do Azure. A integração ajuda a sua solução de SIEM a oferecer um painel unificado para todos os seus ativos, locais ou na nuvem. Você pode usar um painel para receber, agregar, correlacionar e analisar os alertas para eventos de segurança.

> [!NOTE]
> Atualmente, a Integração de Logs do Azure é compatível apenas com as nuvens do Azure comercial e Azure Governamental. Não há suporte para outras nuvens.

![O processo de Integração de Logs do Azure][1]

## <a name="what-logs-can-i-integrate"></a>Quais logs posso integrar?

O Azure produz um log abrangente para cada um de seus serviço. Os logs representam três tipos de log:

* **Logs de controle/gerenciamento**: fornecem visibilidade às operações CREATE, UPDATE e DELETE do [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). O log de atividades do Azure é um exemplo desse tipo de log.
* **Logs do plano de dados**: dão visibilidade a eventos que são criados quando você usa um recurso do Azure. Um exemplo desse tipo de log são os canais **Sistema**, **Segurança** e **Aplicativo** do Visualizador de Eventos do Windows em uma máquina virtual Windows. Outro exemplo é o Log de Diagnóstico do Azure que você configura por meio do Azure Monitor.
* **Eventos processados**: fornecem informações de eventos e alertas que são processados para você. Um exemplo desse tipo de evento são os alertas da Central de Segurança do Azure. A Central de Segurança do Azure processa e analisa sua assinatura para fornecer alertas que são relevantes para sua postura de segurança atual.

A integração de Log do Azure dá suporte a ArcSight, QRadar e Splunk. Entre em contato com o fornecedor do SIEM para avaliar se ele tem um conector nativo. Não use a Integração de Logs do Azure se um conector nativo estiver disponível.

Se nenhuma outra opção estiver disponível, considere usar a Integração de Logs do Azure. A tabela a seguir inclui nossas recomendações:

|SIEM | O cliente já usa o Integrador de Logs do Azure | O cliente está avaliando as opções de integração do SIEM|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Inicie a migração para o [complemento do Azure Monitor para Splunk](https://splunkbase.splunk.com/app/3534/). | Use o [conector Splunk](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Migre ou comece a usar o conector QRadar documentado na última seção [Transmitir dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). | Use o conector QRadar documentado na última seção [Transmitir dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). |
|**ArcSight** | Continue a usar o integrador de log do Azure até que um conector esteja disponível, em seguida, migre para a solução baseada no conector.  | Considere o uso de logs do Azure Monitor como uma alternativa. Não integre à Integração de Logs do Azure, a menos que você esteja disposto a passar pelo processo de migração quando o conector estiver disponível. |

> [!NOTE]
> Embora a Integração do Log do Azure seja uma solução gratuita, há custos de armazenamento do Azure associados ao armazenamento de informações do arquivo de log.

Se você precisar de ajuda, pode criar uma [solicitação de suporte](../azure-supportability/how-to-create-azure-support-request.md). Para o serviço, selecione **Integração de Logs**.

## <a name="next-steps"></a>Próximas etapas

Este artigo apresenta a Integração de Logs do Azure. Para saber mais sobre a Integração de Logs do Azure e os tipos de logs com suporte, confira os seguintes artigos:

* [Introdução à integração de log do Azure](security-azure-log-integration-get-started.md). Este tutorial orienta você durante a instalação da Integração de Logs do Azure. Ele também descreve como integrar os logs do armazenamento do Diagnóstico do Microsoft Azure (WAD), os logs de atividades do Azure, os alertas da Central de Segurança do Azure e os logs de auditoria do Azure Active Directory.
* [Perguntas frequentes sobre a Integração de Logs do Azure](security-azure-log-integration-faq.md). Este artigo de perguntas comuns responde às perguntas sobre a Integração de Logs do Azure.
* Saiba mais sobre como [Transmitir os dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
