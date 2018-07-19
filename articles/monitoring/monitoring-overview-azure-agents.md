---
title: Visão geral agentes de monitoramento do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral detalhada dos agentes do Azure disponíveis que dão suporte ao monitoramento de VMs do Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: magoedte
ms.openlocfilehash: a399c3968e5ee1e2d1f6d623a68dbb1e15cef212
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37088381"
---
# <a name="overview-of-the-azure-agents-to-monitor-azure-virtual-machines"></a>Visão geral dos agentes do Azure para monitorar Máquinas Virtuais do Azure
O Microsoft Azure fornece várias maneiras de coletar diferentes tipos de dados de Máquinas Virtuais hospedadas no Azure ou outros provedores de nuvem que executam Microsoft Windows e Linux.  Este artigo o ajudará a descrever as diferenças e os recursos disponíveis com cada agente para que você possa determinar qual deles oferecerá suporte a seu gerenciamento de serviços ou requisitos gerais de monitoramento.  

## <a name="comparing-azure-diagnostic-and-log-analytics-agent"></a>Comparando o Diagnóstico do Azure e o agente do Log Analytics
Hoje no Azure há dois tipos de agentes disponíveis para monitorar uma VM do Azure – a extensão do Diagnóstico do Azure e o agente do Log Analytics para Linux e Windows.  Fundamentalmente, esses agentes são projetados para coletar métricas e logs e encaminhá-los para um repositório. No entanto, é aí que suas semelhanças terminam.  

A [extensão do Diagnóstico do Azure](../monitoring-and-diagnostics/azure-diagnostics.md), que foi fornecida para os Serviços de Nuvem do Azure desde que foi disponibilizada, em 2010, é um agente que fornece uma coleção simples de dados de diagnóstico de um recurso de IaaS do Azure como uma VM, e a mantém no armazenamento do Azure.  Uma vez no armazenamento, você opta por exibir com uma das várias ferramentas disponíveis, como [Gerenciador de Servidores o Visual Studio](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md) e [Gerenciador de Armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Você pode optar por coletar:

* Um conjunto predefinido de contadores de desempenho do sistema operacional e logs de eventos ou pode especificar qual deseja coletar. 
* Todas as solicitações de e/ou solicitações com falha para um servidor Web IIS
* Logs de saída de rastreamento de aplicativos .NET
* Eventos de ETW (Rastreamento de Eventos para Windows) 
* Coletar eventos de log do syslog  
* Despejos de falhas 

Dados podem ser encaminhados como alternativa para [Application Insights](../application-insights/app-insights-cloudservices.md), [Log Analytics](../log-analytics/log-analytics-overview.md) ou serviços não Azure usando o [Hub de Eventos](../event-hubs/event-hubs-what-is-event-hubs.md). 

Para monitoramento avançado em que você precisa mais do que coletar métricas e um subconjunto de logs, o agente do Log Analytics para Windows e Linux é necessário.  Com esse agente, é possível utilizar os serviços do Azure como Automação e Log Analytics, incluindo o conjunto completo de recursos oferecidos, para fornecer gerenciamento abrangente de suas VMs do Azure por meio de seu ciclo de vida. Isso inclui:

* [Gerenciamento de Atualizações da Automação do Azure](../automation/automation-update-management.md) de atualizações do sistema operacional
* [Configuração de Estado Desejado da Automação do Azure](../automation/automation-dsc-overview.md) para manter o estado de configuração consistente
* Controlar alterações de configuração com o [Controle de Alterações e Inventário da Automação do Azure](../automation/automation-change-tracking.md)
* Coleção de logs personalizados do sistema operacional e aplicativos hospedados, como [FluentD](../log-analytics/log-analytics-data-sources-json.md), [logs personalizados](../log-analytics/log-analytics-data-sources-custom-logs.md), [MySQL e Apache](../log-analytics/log-analytics-data-sources-linux-applications.md) com o Log Analytics
* Serviços do Azure, como [Application Insights](https://docs.microsoft.com/azure/application-insights/) e a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/), armazenam nativamente seus dados diretamente no Log Analytics.  

## <a name="next-steps"></a>Próximas etapas

- Consulte [Coletar dados de computadores em seu ambiente com o Log Analytics](../log-analytics/log-analytics-concept-hybrid.md) para revisar requisitos e métodos disponíveis para implantar o agente em computadores em seu data center ou em outro ambiente de nuvem.
- Consulte [Coletar dados sobre as máquinas virtuais do Azure](../log-analytics/log-analytics-quick-collect-azurevm.md) para configurar a coleta de dados de máquinas virtuais do Azure. 