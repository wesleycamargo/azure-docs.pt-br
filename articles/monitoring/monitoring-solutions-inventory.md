---
title: Detalhes da coleta de dados para soluções de gerenciamento no Azure | Microsoft Docs
description: As soluções de gerenciamento do Azure são uma coleção de lógica, visualização e regras de aquisição de dados que fornecem as métricas relacionadas a uma área de problema específica.  Este artigo fornece uma lista de soluções de gerenciamento disponíveis na Microsoft e detalhes sobre o método e a frequência de coleta de dados.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: bwren
ms.openlocfilehash: 2a8313640d2d0bd678d0e10da39c87de8cfce00b
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Detalhes da coleta de dados para soluções de gerenciamento no Azure
Este artigo inclui uma lista de [soluções de gerenciamento](monitoring-solutions.md) disponíveis na Microsoft com links para a documentação detalhada.  Fornece também informações sobre o método e a frequência de coleta de dados no Log Analytics.  É possível usar as informações deste artigo para identificar as diferentes soluções disponíveis e compreender o fluxo de dados e os requisitos de conexão para diferentes soluções de gerenciamento. 

## <a name="list-of-management-solutions"></a>Lista de soluções de gerenciamento

A tabela a seguir lista as [soluções de gerenciamento](monitoring-solutions.md) no Azure fornecidas pela Microsoft. Uma entrada na coluna significa que a solução coleta dados no Log Analytics usando esse método.  Se uma solução não tiver colunas selecionadas, ela gravará diretamente no Log Analytics de outro serviço do Azure. Siga cada link para obter a documentação detalhada e mais informações.

As explicações das colunas são as seguintes:

- **Microsoft Monitoring Agent** - Agente usado no Windows e Linux para executar o pacote de gerenciamento do SCOM e as soluções de gerenciamento do Azure. Nessa configuração, o agente é conectado diretamente ao Log Analytics sem estar conectado a um grupo de gerenciamento do Operations Manager. 
- **Operations Manager** - Agente igual ao Microsoft Monitoring Agent. Nessa configuração, ele é [conectado a um grupo de gerenciamento do Operations Manager](../log-analytics/log-analytics-om-agents.md) que está conectado ao Log Analytics. 
-  **Armazenamento do Microsoft Azure** - A solução coleta dados de uma conta de armazenamento do Azure. 
- **O Operations Manager é necessário?** - Um grupo de gerenciamento do Operations Manager conectado é necessário para a coleta de dados pela solução de gerenciamento. 
- **Dados do agente do Operations Manager enviados por meio do grupo de gerenciamento** - Se o agente estiver [conectado a um grupo de gerenciamento do SCOM](../log-analytics/log-analytics-om-agents.md), os dados serão enviados para o Log Analytics do servidor de gerenciamento. Nesse caso, o agente não precisa conectar diretamente ao Log Analytics. Se essa caixa não estiver selecionada, os dados serão enviados diretamente do agente para o Log Analytics, mesmo se o agente estiver conectado a um grupo de gerenciamento do SCOM. ele deverá comunicar-se com o Log Analytics por meio de um [Gateway do OMS](../log-analytics/log-analytics-oms-gateway.md).
- **Frequência de coleta** - Especifica a frequência em que os dados são coletados pela solução de gerenciamento. 



| **Solução de gerenciamento** | **Plataforma** | **Microsoft Monitoring Agent** | **Agente do Operations Manager** | **Armazenamento do Azure** | **O Operations Manager é necessário?** | **Dados do agente do Operations Manager enviados por meio do grupo de gerenciamento** | **Frequência de coleta** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Log Analytics de Atividade](../log-analytics/log-analytics-activity.md) | Azure | | | | | | após a notificação |
| [Avaliação do AD](../log-analytics/log-analytics-ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dias |
| [Status de replicação do AD](../log-analytics/log-analytics-ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dias |
| [Integridade do Agente](../operations-management-suite/oms-solution-agenthealth.md) | Windows e Linux | &#8226; | &#8226; | | | &#8226; | 1 minuto |
| [Gerenciamento de Alertas](../log-analytics/log-analytics-solution-alert-management.md) (Nagios) |Linux |&#8226; | | | | |na chegada |
| [Gerenciamento de Alertas](../log-analytics/log-analytics-solution-alert-management.md) (Zabbix) |Linux |&#8226; | | | | |1 minuto |
| [Gerenciamento de Alertas](../log-analytics/log-analytics-solution-alert-management.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minutos |
| [Azure Site Recovery](../site-recovery/site-recovery-overview.md) | Azure | | | | | | n/d |
| [Conector do Application Insights (versão prévia)](../log-analytics/log-analytics-app-insights-connector.md) | Azure | | | |  |  | após a notificação |
| [Hybrid Worker de Automação](../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | n/d |
| [Análise de Gateway de Aplicativo do Azure](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | após a notificação |
| **Solução de gerenciamento** | **Plataforma** | **Microsoft Monitoring Agent** | **Agente do Operations Manager** | **Armazenamento do Azure** | **O Operations Manager é necessário?** | **Dados do agente do Operations Manager enviados por meio do grupo de gerenciamento** | **Frequência de coleta** |
| [Análise de Grupo de Segurança de Rede do Azure](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | após a notificação |
| [Azure SQL Analytics (Visualização)](../log-analytics/log-analytics-azure-sql.md) | Windows | | | | | | 10 minutos |
| [Backup](../backup/backup-introduction-to-azure-backup.md) | Azure |  |  |  |  |  | n/d |
| [Capacidade e Desempenho (versão prévia)](../log-analytics/log-analytics-capacity.md) |Windows |&#8226; |&#8226; | | |&#8226; |na chegada |
| [Controle de alterações](../log-analytics/log-analytics-change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |por hora |
| [Controle de alterações](../log-analytics/log-analytics-change-tracking.md) |Linux |&#8226; | | | | |por hora |
| [Contêineres](../log-analytics/log-analytics-containers.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 3 minutos |
| [Análise do Cofre de Chaves](../log-analytics/log-analytics-azure-key-vault.md) |Windows | | | | | |após a notificação |
| [Avaliação de malware](../log-analytics/log-analytics-malware.md) |Windows |&#8226; |&#8226; | | |&#8226; |por hora |
| [Monitor de Desempenho de Rede](../log-analytics/log-analytics-network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | Handshakes TCP a cada cinco segundos; dados enviados a cada três minutos |
| [Análise do Office 365 (versão prévia)](../operations-management-suite/oms-solution-office-365.md) |Windows | | | | | |após a notificação |
| **Solução de gerenciamento** | **Plataforma** | **Microsoft Monitoring Agent** | **Agente do Operations Manager** | **Armazenamento do Azure** | **O Operations Manager é necessário?** | **Dados do agente do Operations Manager enviados por meio do grupo de gerenciamento** | **Frequência de coleta** |
| [Segurança e Auditoria](../operations-management-suite/oms-security-getting-started.md) (Syslog) | Linux | &#8226; | | |  |  | na chegada |
| [Segurança e Auditoria](../operations-management-suite/oms-security-getting-started.md) (Logs de eventos de segurança) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | na chegada |
| [Segurança e Auditoria](../operations-management-suite/oms-security-getting-started.md) (Logs de Firewall) |Windows |&#8226; |&#8226; |  |  |  |na chegada |
| [Análise do Service Fabric (visualização)](../log-analytics/log-analytics-service-fabric.md) |Windows | | |&#8226; | | |5 minutos |
| [Mapa do Serviço](../operations-management-suite/operations-management-suite-service-map.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 15 s |
| [Avaliação do SQL](../log-analytics/log-analytics-sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dias |
| [SurfaceHub](../log-analytics/log-analytics-surface-hubs.md) |Windows |&#8226; | | | | |na chegada |
| [Avaliação do System Center Operations Manager (versão prévia)](../log-analytics/log-analytics-scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | sete dias |
| [Gerenciamento de atualizações](../operations-management-suite/oms-solution-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |pelo menos, 2 vezes por dia e 15 minutos após a instalação de uma atualização |
| [Preparação para atualização](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dias |
| [Monitoramento do VMware (visualização)](../log-analytics/log-analytics-vmware.md) | Linux | &#8226; |  |  |  |  | 3 minutos |
| [Wire Data 2.0 (versão prévia)](../log-analytics/log-analytics-wire-data.md) |Windows (2012 R2/8.1 ou posterior) |&#8226; |&#8226; | | | | 1 minuto |




## <a name="next-steps"></a>Próximas etapas
* Saiba como [criar consultas](../log-analytics/log-analytics-log-searches.md) para analisar dados coletados pelas soluções de gerenciamento.
