---
title: "Exportar dados de segurança do Azure para configuração de Pipeline SIEM - [versão prévia] | Microsoft Docs"
description: "Este artigo documenta a produção para obtenção de logs da Central de segurança do Azure a um SIEM"
services: security-center
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: barclayn
ms.openlocfilehash: aef623f047bd7e14cb5bd17fb2a2c18e3c5d42b9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-security-data-export-to-siem--pipeline-configuration-preview"></a>Exportar dados de segurança do Azure para configuração de Pipeline SIEM - [versão prévia]

Este documento detalha o procedimento para exportar dados de segurança da Central de Segurança do Azure para um SIEM.

Eventos processados produzidos pela Central de Segurança do Azure são publicados no [log de atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) do Azure, um log de tipos disponíveis por meio do Azure Monitor. O Azure Monitor oferece um pipeline consolidado para qualquer um dos seus dados de monitoramentos de roteamento para uma ferramenta do SIEM. Isso é feito transmitindo dados para um Hub de eventos onde ele pode então ser extraído para uma ferramenta de parceiro.

Este pipe usa o [monitoramento do Azure único pipeline](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) para obter acesso aos dados de monitoramento de seu ambiente do Azure. Isso permite que você configure facilmente SIEMs e ferramentas de monitoramento para consumir os dados.

As seções a seguir descrevem como você pode configurar os dados para serem transmitidos para um hub de eventos. As etapas pressupõem que você já tem a Central de Segurança do Azure configurado na sua assinatura do Azure.

Visão geral de alto nível

![Visão geral de alto nível](media/security-center-export-data-to-siem/overview.png)

## <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Quais são os dados de segurança do Azure expostos para SIEM?

Nesta versão prévia mostramos os [alertas de segurança.](../security-center/security-center-managing-and-responding-alerts.md) Em versões futuras, enriqueceremos o conjunto de dados com as recomendações de segurança.

## <a name="how-to-setup-the-pipeline"></a>Como configurar o pipeline? 

### <a name="create-an-event-hub"></a>Criar um Hub de Evento 

Antes de começar, você precisa [criar um namespace dos Hubs de Eventos](../event-hubs/event-hubs-create.md). Esse namespace e Hub de Eventos é o destino para todos os seus dados de monitoramento.

### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Transmissão do Log de Atividades do Azure para os Hubs de Eventos

Consulte o seguinte artigo [log de atividades de fluxo para Hubs de Eventos](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)

### <a name="install-a-partner-siem-connector"></a>Instalar um conector do SIEM parceiro 

Rotear dados de monitoramento para um Hub de Eventos com o Azure Monitor permite fácil integração com as ferramentas de monitoramento e o SIEM de parceiro.

Consulte o link a seguir para ver a lista de [SIEMs de suporte](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

## <a name="example-for-querying-data"></a>Exemplo de consulta de dados 

Eis algumas consultas Splunk que você pode usar para extrair dados de alerta:

| **Descrição da consulta:**                                | **Consulta**                                                                                                                              |
|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Todos os Alertas                                              | index=main Microsoft.Security/locations/alerts                                                                                         |
| Resumir contagem de operações por seu nome             | **Alertas** index=main sourcetype="amal:security" \| tabela operationName \| contagem de estatísticas por operationName                                |
| Obter informações de alertas: hora, nome, estado, ID e assinatura | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Próximas etapas

- [SIEMs com suporte](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)
- [Transmitir log de atividades para Hubs de Eventos](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
- [Alertas de segurança.](../security-center/security-center-managing-and-responding-alerts.md)