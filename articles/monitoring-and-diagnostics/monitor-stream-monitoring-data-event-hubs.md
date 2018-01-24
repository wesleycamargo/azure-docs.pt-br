---
title: Transmitir dados de monitoramento do Azure para os Hubs de Eventos | Microsoft Docs
description: "Saiba como transmitir todos os dados de monitoramento do Azure para um hub de eventos para colocar os dados em um SIEM ou ferramenta de análise de um parceiro."
author: johnkemnetz
manager: robb
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/11/2018
ms.author: johnkem
ms.openlocfilehash: b2813035b4665a36b475e791965d395b84ddb3f1
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>Transmitir os dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa

O Azure Monitor fornece um único pipeline para obter acesso a todos os dados de monitoramento do seu ambiente do Azure, permitindo que você configure facilmente ferramentas de monitoramento e SIEM de parceiro para consumir esses dados. Este artigo orienta você pela configuração de diferentes camadas de dados do seu ambiente do Azure para serem enviados a um único namespace de Hubs de Eventos ou hub de eventos, em que eles podem ser coletados por uma ferramenta externa.

## <a name="what-data-can-i-send-into-an-event-hub"></a>Que dados posso enviar para um hub de eventos? 

Em seu ambiente do Azure há várias 'camadas' de dados de monitoramento e o método de acesso a dados de cada camada varia um pouco. Normalmente, essas camadas podem ser descritas como:

- **Dados de monitoramento de aplicativo:** dados sobre o desempenho e a funcionalidade do código que você escreveu e está executando no Azure. Rastreamentos de desempenho, logs de aplicativos e telemetria do usuário são exemplos de dados de monitoramento de aplicativos. Dados de monitoramento de aplicativo normalmente são coletados de uma das seguintes maneiras:
  - Instrumentando seu código com um SDK como o [SDK do Application Insights](../application-insights/app-insights-overview.md).
  - Executando um agente de monitoramento que escuta em busca de novos logs do aplicativo no computador executando o seu aplicativo, assim como o [Agente de Diagnóstico do Azure do Windows](./azure-diagnostics.md) ou o [Agente de Diagnóstico do Azure do Linux](../virtual-machines/linux/diagnostic-extension.md).
- **Dados de monitoramento de SO convidado:** dados sobre o sistema operacional no qual o aplicativo é executado. Exemplos de dados de monitoramento de SO convidado seriam syslog do Linux ou eventos de sistema do Windows. Para coletar esse tipo de dados, você precisa instalar um agente como o [Agente de Diagnóstico do Azure do Windows](./azure-diagnostics.md) ou o [Agente de Diagnóstico do Azure do Linux](../virtual-machines/linux/diagnostic-extension.md).
- **Dados de monitoramento de recursos do Azure:** dados sobre a operação de um recurso do Azure. Para alguns tipos de recursos do Azure, como máquinas virtuais, há um SO convidado e aplicativos a serem monitorados dentro desse serviço do Azure. Para outros recursos do Azure, como Grupos de Segurança de Rede, o recurso de monitoramento de dados é a camada de dados mais alta disponível (já que não há nenhum SO convidado nem aplicativo em execução nesses recursos). Esses dados podem ser coletados usando as [configurações de diagnóstico do recurso](./monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
- **Dados de monitoramento de plataforma do Azure:** dados sobre a operação e gerenciamento de um locatário ou assinatura do Azure, bem como dados sobre a integridade e a operação do Azure propriamente dito. O [log de atividades](./monitoring-overview-activity-logs.md), incluindo dados de serviço de integridade e auditorias do Active Directory, são exemplos de dados de monitoramento de plataforma. Esses dados também podem ser coletados usando as configurações de diagnóstico.

Dados de qualquer camada podem ser enviados para um hub de eventos, do qual é possível efetuar pull desses dados para uma ferramenta de parceiro. As seções a seguir descrevem como você pode configurar os dados de cada camada para serem transmitidos para um hub de eventos. As etapas pressupõem que você já tem ativos nessa camada a serem monitorados.

Antes de começar, você precisa [criar um namespace dos Hubs de Eventos e um hub de eventos](../event-hubs/event-hubs-create.md). Esse namespace e hub de eventos é o destino para todos os seus dados de monitoramento.

## <a name="how-do-i-set-up-azure-platform-monitoring-data-to-be-streamed-to-an-event-hub"></a>Como configurar os dados de monitoramento de plataforma do Azure para serem transmitidos para um hub de eventos?

Os dados de monitoramento de plataforma do Azure vêm de duas origens principais:
1. O [log de atividades do Azure](./monitoring-overview-activity-logs.md), que contém as operações create, update e delete do Resource Manager, as alterações na [integridade do serviço do Azure](../service-health/service-health-overview.md), que podem impactar os recursos em sua assinatura, as transições de estado do [resource health](../service-health/resource-health-overview.md) e vários outros tipos de eventos em nível de assinatura. [Este artigo fornece detalhes sobre todas as categorias de eventos que aparecem no log de atividades do Azure](./monitoring-activity-log-schema.md).
2. [Relatórios do Azure Active Directory](../active-directory/active-directory-reporting-azure-portal.md), que contêm o histórico de atividade de entrada e a trilha de auditoria das alterações feitas em um locatário específico. Ainda não é possível transmitir dados do Azure Active Directory para um hub de eventos.

### <a name="stream-azure-activity-log-data-into-an-event-hub"></a>Transmitir dados de log de atividades do Azure para um hub de eventos

Para enviar os dados do log de atividades do Azure para um namespace de Hubs de Eventos, você configura um perfil de log em sua assinatura. [Siga este guia](./monitoring-stream-activity-logs-event-hubs.md) para configurar um perfil de log em sua assinatura. Fazer isso uma vez para cada assinatura que você deseja monitorar.

> [!TIP]
> Um perfil de log atualmente só permite que você selecione um namespace de Hubs de Eventos, no qual um hub de eventos é criado com o nome 'insights-operational-logs.' Ainda não é possível especificar seu próprio nome do hub de eventos em um perfil de log.

## <a name="how-do-i-set-up-azure-resource-monitoring-data-to-be-streamed-to-an-event-hub"></a>Como configurar os dados de monitoramento de recurso do Azure para serem transmitidos para um hub de eventos?

Recursos do Azure emitem dois tipos de dados de monitoramento:
1. [Logs de diagnóstico de recurso](./monitoring-overview-of-diagnostic-logs.md)
2. [Métricas](monitoring-overview-metrics.md)

Ambos os tipos de dados são enviados para um hub de eventos usando uma configuração de diagnóstico de recurso. [Siga este guia](./monitoring-stream-diagnostic-logs-to-event-hubs.md) para definir uma configuração de diagnóstico de recurso em um determinado recurso. Defina uma configuração de diagnóstico de recurso em cada recurso do qual você deseja coletar logs.

> [!TIP]
> Você pode usar a Azure Policy para garantir que todos os recursos em um determinado escopo sempre sejam definidos com uma configuração de diagnóstico [usando o efeito DeployIfNotExists na regra de política](../azure-policy/policy-definition.md#policy-rule). Hoje DeployIfNotExists só é compatível com políticas internas.

## <a name="how-do-i-set-up-guest-os-monitoring-data-to-be-streamed-to-an-event-hub"></a>Como configurar os dados de monitoramento de SO convidado para serem transmitidos para um hub de eventos?

Você precisa instalar um agente para enviar dados de monitoramento do SO convidado para um hub de eventos. Para Windows ou Linux, você especifica os dados que você deseja enviar para o hub de eventos, bem como o hub de eventos para o qual os dados devem ser enviados, em um arquivo de configuração e passa esse arquivo para o agente em execução na VM.

### <a name="stream-linux-data-to-an-event-hub"></a>Transmitir dados do Linux para um hub de eventos

O [agente de Diagnóstico do Azure do Linux](../virtual-machines/linux/diagnostic-extension.md) pode ser usado para enviar dados de monitoramento de um computador Linux para um hub de eventos. Faça isso adicionando o hub de eventos como um coletor no JSON de configurações protegidas do arquivo de configuração LAD. [Consulte este artigo para saber mais sobre como adicionar o coletor de hub de eventos para o seu agente de Diagnóstico do Azure do Linux](../virtual-machines/linux/diagnostic-extension.md#protected-settings).

> [!NOTE]
> Você não pode configurar streaming de dados de monitoramento de SO convidado para um hub de eventos no portal. Em vez disso, você deve editar manualmente o arquivo de configuração.

### <a name="stream-windows-data-to-an-event-hub"></a>Transmitir dados do Windows para um hub de eventos

O [agente de Diagnóstico do Azure do Windows](./azure-diagnostics.md) pode ser usado para enviar dados de monitoramento de um computador Windows para um hub de eventos. Faça isso adicionando o hub de eventos como um coletor na seção privateConfig do arquivo de configuração WAD. [Consulte este artigo para saber mais sobre como adicionar o coletor de hub de eventos para o seu agente de Diagnóstico do Azure do Windows](./azure-diagnostics-streaming-event-hubs.md).

> [!NOTE]
> Você não pode configurar streaming de dados de monitoramento de SO convidado para um hub de eventos no portal. Em vez disso, você deve editar manualmente o arquivo de configuração.

## <a name="how-do-i-set-up-application-monitoring-data-to-be-streamed-to-event-hub"></a>Como configurar os dados de monitoramento de aplicativo para serem transmitidos para um hub de eventos?

Dados de monitoramento de aplicativo requerem que seu código seja instrumentado com um SDK, portanto, não existe uma solução de finalidade geral para roteamento de dados de monitoramento de aplicativo para um hub de eventos no Azure. No entanto, o [Azure Application Insights](../application-insights/app-insights-overview.md) é um serviço que pode ser usado para coletar dados de nível de aplicativo do Azure. Se você estiver usando o Application Insights, você poderá transmitir dados de monitoramento para um hub de eventos fazendo o seguinte:

1. [Configure a exportação contínua](../application-insights/app-insights-export-telemetry.md) dos dados do Application Insights para uma conta de armazenamento.

2. Configure um aplicativo lógico disparado por timer que [efetua pull de dados do armazenamento de blobs](../connectors/connectors-create-api-azureblobstorage.md#use-an-action) e [envia esses dados por push como uma mensagem para o hub de eventos](../connectors/connectors-create-api-azure-event-hubs.md#send-events-to-your-event-hub-from-your-logic-app).

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>O que pode fazer com os dados de monitoramento que estão sendo enviados para o hub de eventos?

Rotear dados de monitoramento para um hub de eventos com o Azure Monitor permite fácil integração com as ferramentas de monitoramento e o SIEM de parceiro. A maioria das ferramentas exigem que a cadeia de conexão de hub de eventos e determinadas permissões para sua assinatura do Azure leiam dados do hub de eventos. Aqui está uma lista parcial das ferramentas com integração ao Azure Monitor:

* **IBM QRadar** – o Microsoft Azure DSM e protocolo de Hub de Eventos do Microsoft Azure estão disponíveis para download do [site de suporte da IBM](http://www.ibm.com/support). Você pode [saber mais sobre a integração com o Azure aqui](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
* **Splunk** - [O complemento Azure Monitor para Splunk](https://splunkbase.splunk.com/app/3534/) está disponível na Splunkbase e em um projeto de software livre. [A documentação está aqui](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
* **SumoLogic** – instruções para configurar o SumoLogic para consumir dados de um hub de eventos estão [disponíveis aqui](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)

## <a name="next-steps"></a>Próximas etapas
* [Arquivar o Log de Atividades em uma conta de armazenamento](monitoring-archive-activity-log.md)
* [Leia a visão geral do Log de Atividades do Azure](monitoring-overview-activity-logs.md)
* [Configurar um alerta com base em um evento do Log de Atividades](insights-auditlog-to-webhook-email.md)

