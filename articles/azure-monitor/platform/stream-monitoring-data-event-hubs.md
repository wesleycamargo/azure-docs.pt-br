---
title: Transmitir dados de monitoramento do Azure para os Hubs de Eventos
description: Saiba como transmitir os dados de monitoramento do Azure para um hub de eventos para obter os dados em um SIEM de parceiro ou uma ferramenta de análise.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: ab439eb77113c53ab046256dd8d448a18b63f887
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58850076"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>Transmitir os dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa

Este artigo orienta você pela configuração de diferentes camadas de dados do seu ambiente do Azure para serem enviados a um único namespace de Hubs de Eventos ou hub de eventos, em que eles podem ser coletados por uma ferramenta externa.

> [!VIDEO https://www.youtube.com/embed/SPHxCgbcvSw]

## <a name="what-data-can-i-send-into-an-event-hub"></a>Que dados posso enviar para um hub de eventos?

Em seu ambiente do Azure há várias 'camadas' de dados de monitoramento e o método de acesso a dados de cada camada varia um pouco. Normalmente, essas camadas podem ser descritas como:

- **Dados de monitoramento de aplicativo:** dados sobre o desempenho e a funcionalidade do código que você escreveu e está executando no Azure. Rastreamentos de desempenho, logs de aplicativos e telemetria do usuário são exemplos de dados de monitoramento de aplicativos. Dados de monitoramento de aplicativo normalmente são coletados de uma das seguintes maneiras:
  - Instrumentando seu código com um SDK como o [SDK do Application Insights](../../azure-monitor/app/app-insights-overview.md).
  - Executando um agente de monitoramento que escuta em busca de novos logs do aplicativo no computador executando o seu aplicativo, assim como o [Agente de Diagnóstico do Azure do Windows](./../../azure-monitor/platform/diagnostics-extension-overview.md) ou o [Agente de Diagnóstico do Azure do Linux](../../virtual-machines/extensions/diagnostics-linux.md).
- **Dados de monitoramento do SO convidado:** dados sobre o sistema operacional no qual seu aplicativo está em execução. Exemplos de dados de monitoramento de SO convidado seriam syslog do Linux ou eventos de sistema do Windows. Para coletar esse tipo de dados, você precisa instalar um agente como o [Agente de Diagnóstico do Azure do Windows](./../../azure-monitor/platform/diagnostics-extension-overview.md) ou o [Agente de Diagnóstico do Azure do Linux](../../virtual-machines/extensions/diagnostics-linux.md).
- **Dados de monitoramento de recursos do Azure:** dados sobre a operação de um recurso do Azure. Para alguns tipos de recursos do Azure, como máquinas virtuais, há um SO convidado e aplicativos a serem monitorados dentro desse serviço do Azure. Para outros recursos do Azure, como Grupos de Segurança de Rede, o recurso de monitoramento de dados é a camada de dados mais alta disponível (já que não há nenhum SO convidado nem aplicativo em execução nesses recursos). Esses dados podem ser coletados usando as [configurações de diagnóstico do recurso](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings).
- **Dados de monitoramento de assinatura do Azure**: dados sobre a operação e o gerenciamento de uma assinatura do Azure, além de dados sobre a integridade e a operação do próprio Azure. O [log de atividades](./../../azure-monitor/platform/activity-logs-overview.md) contém a maioria dos dados de monitoramento de assinatura, como incidentes de integridade de serviço e auditorias do Azure Resource Manager. Você pode coletar esses dados usando um perfil de Log.
- **Dados de monitoramento de locatário do Azure**: dados sobre a operação de serviços de nível de locatário do Azure, como o Azure Active Directory. As auditorias e logins do Azure Active Directory são exemplos de dados de monitoramento de locatários. Esses dados podem ser coletados usando uma configuração de diagnóstico de locatário.

Dados de qualquer camada podem ser enviados para um hub de eventos, do qual é possível efetuar pull desses dados para uma ferramenta de parceiro. Algumas fontes podem ser configurados para enviar dados diretamente para um hub de eventos, enquanto outro processo, como um aplicativo lógico pode ser necessário para recuperar os dados necessários. As seções a seguir descrevem como você pode configurar os dados de cada camada para serem transmitidos para um hub de eventos. As etapas pressupõem que você já tem ativos nessa camada a serem monitorados.

## <a name="set-up-an-event-hubs-namespace"></a>Configurar um namespace dos Hubs de Eventos

Antes de começar, você precisa [criar um namespace dos Hubs de Eventos e um hub de eventos](../../event-hubs/event-hubs-create.md). Esse namespace e hub de eventos é o destino para todos os seus dados de monitoramento. Um namespace dos Hubs de Eventos é um agrupamento lógico de hubs de eventos que compartilham a mesma política de acesso, assim como uma conta de armazenamento tem blobs individuais dentro dessa conta de armazenamento. Observe alguns detalhes sobre o namespace dos hubs de eventos e os hubs de eventos que você criou:
* É recomendável utilizar um namespace dos Hubs de Evento Standard.
* Normalmente, apenas uma unidade de taxa de transferência é necessária. Se for necessário aumentar a escala vertical conforme o uso de log aumentar, sempre será possível aumentar manualmente o número de unidades de taxa transferência para o namespace posteriormente, ou habilitar a inflação automática.
* O número de unidades de taxa de transferência permite aumentar a escala de taxa de transferência para os hubs de eventos. O número de partições permite paralelizar o consumo em muitos consumidores. Uma única partição pode fazer até 20 MBps, ou aproximadamente 20.000 mensagens por segundo. Dependendo da ferramenta que consome os dados, poderá ou não dar suporte ao consumo de várias partições. Se você não tiver certeza sobre o número de partições a definir, é recomendável iniciar com quatro partições.
* É recomendável definir a retenção de mensagens no seu hub de eventos para 7 dias. Se a ferramenta de consumo ficar inativa por mais de um dia, isso garante que a ferramenta poderá retirar de onde parou (para eventos de até 7 dias).
* É recomendável utilizar o grupo de consumidores padrão para o hub de eventos. Não é necessário criar outros grupos de consumidores ou utilizar um grupo de consumidores separado, exceto se você planejar ter duas ferramentas diferentes que consumam os mesmos dados do mesmo hub de eventos.
* Para o Log de Atividades do Azure, você escolhe um namespace dos Hubs de Eventos e o Azure Monitor cria um hub de eventos dentro desse namespace chamado 'insights-logs-operationallogs.' Para outros tipos de log, é possível escolher um hub de eventos existente (permitindo que você reutilize o mesmo hub de eventos insights-logs-operationallogs) ou que o Azure Monitor crie um hub de eventos por categoria de log.
* Normalmente, as portas 5671 e 5672 devem ser abertas no computador que consome dados do hub de eventos.

Consulte também as [Perguntas frequentes sobre Hubs de Eventos do Azure](../../event-hubs/event-hubs-faq.md).

## <a name="azure-tenant-monitoring-data"></a>Dados de monitoramento de locatário do Azure

No momento, os dados de monitoramento do inquilino do Azure estão disponíveis apenas para o Active Directory do Azure. Você pode usar os dados do [relatório do Active Directory do Azure](../../active-directory/reports-monitoring/overview-reports.md), que contém o histórico da atividade de entrada e a trilha de auditoria das alterações feitas em um determinado inquilino.

### <a name="azure-active-directory-data"></a>Dados do Azure Active Directory

Para enviar dados do log do Azure Active Directory para um namespace de Hubs de Eventos, defina uma configuração de diagnóstico de inquilino em seu inquilino do AAD. [Siga este guia](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) para definir uma configuração de diagnóstico de inquilino.

## <a name="azure-subscription-monitoring-data"></a>Dados de monitoramento de assinatura do Azure

Os dados de monitoramento de assinatura do Azure estão disponíveis no [log de atividades do Azure](./../../azure-monitor/platform/activity-logs-overview.md). Contém as operações de criação, atualização e exclusão do Gerenciador de Recursos, as alterações na [integridade do serviço do Azure](../../service-health/service-health-overview.md) que podem afetar os recursos da sua assinatura, as [transições de estado da integridade do recurso](../../service-health/resource-health-overview.md) e vários outros tipos de eventos no nível da assinatura. [Este artigo fornece detalhes sobre todas as categorias de eventos que aparecem no log de atividades do Azure](./../../azure-monitor/platform/activity-log-schema.md).

### <a name="activity-log-data"></a>Dados do log de atividades

Para enviar os dados do log de atividades do Azure para um namespace de Hubs de Eventos, você configura um perfil de log em sua assinatura. [Siga este guia](./activity-logs-stream-event-hubs.md) para configurar um perfil de log em sua assinatura. Fazer isso uma vez para cada assinatura que você deseja monitorar.

> [!TIP]
> Um perfil de log atualmente só permite que você selecione um namespace de Hubs de Eventos, no qual um hub de eventos é criado com o nome 'insights-operational-logs.' Ainda não é possível especificar seu próprio nome do hub de eventos em um perfil de log.

## <a name="azure-resource-metrics-and-diagnostics-logs"></a>Log de diagnóstico e métricas de recurso do Azure

Recursos do Azure emitem dois tipos de dados de monitoramento:
1. [Logs de diagnóstico de recurso](diagnostic-logs-overview.md)
2. [Métricas](data-platform.md)

Ambos os tipos de dados são enviados para um hub de eventos usando uma configuração de diagnóstico de recurso. [Siga este guia](diagnostic-logs-stream-event-hubs.md) para definir uma configuração de diagnóstico de recurso em um determinado recurso. Defina uma configuração de diagnóstico de recurso em cada recurso do qual você deseja coletar logs.

> [!TIP]
> Você pode usar a Azure Policy para garantir que todos os recursos em um determinado escopo sempre sejam definidos com uma configuração de diagnóstico [usando o efeito DeployIfNotExists na regra de política](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="guest-os-data"></a>Dados de SO convidado

Você precisa instalar um agente para enviar dados de monitoramento do SO convidado para um hub de eventos. Para Windows ou Linux, você especifica os dados que você deseja enviar para o hub de eventos, bem como o hub de eventos para o qual os dados devem ser enviados, em um arquivo de configuração e passa esse arquivo para o agente em execução na VM.

### <a name="linux-data"></a>Dados do Linux

O [agente de Diagnóstico do Azure do Linux](../../virtual-machines/extensions/diagnostics-linux.md) pode ser usado para enviar dados de monitoramento de um computador Linux para um hub de eventos. Faça isso adicionando o hub de eventos como um coletor no JSON de configurações protegidas do arquivo de configuração LAD. [Consulte este artigo para saber mais sobre como adicionar o coletor de hub de eventos para o seu agente de Diagnóstico do Azure do Linux](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings).

> [!NOTE]
> Você não pode configurar streaming de dados de monitoramento de SO convidado para um hub de eventos no portal. Em vez disso, você deve editar manualmente o arquivo de configuração.

### <a name="windows-data"></a>Dados do Windows

O [agente de Diagnóstico do Azure do Windows](./../../azure-monitor/platform/diagnostics-extension-overview.md) pode ser usado para enviar dados de monitoramento de um computador Windows para um hub de eventos. Faça isso adicionando o hub de eventos como um coletor na seção privateConfig do arquivo de configuração WAD. [Consulte este artigo para saber mais sobre como adicionar o coletor de hub de eventos para o seu agente de Diagnóstico do Azure do Windows](./../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md).

> [!NOTE]
> Você não pode configurar streaming de dados de monitoramento de SO convidado para um hub de eventos no portal. Em vez disso, você deve editar manualmente o arquivo de configuração.

## <a name="application-monitoring-data"></a>Dados de monitoramento de aplicativo

Dados de monitoramento de aplicativo requerem que seu código seja instrumentado com um SDK, portanto, não existe uma solução de finalidade geral para roteamento de dados de monitoramento de aplicativo para um hub de eventos no Azure. No entanto, o [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) é um serviço que pode ser usado para coletar dados de nível de aplicativo do Azure. Se você estiver usando o Application Insights, você poderá transmitir dados de monitoramento para um hub de eventos fazendo o seguinte:

1. [Configure a exportação contínua](../../azure-monitor/app/export-telemetry.md) dos dados do Application Insights para uma conta de armazenamento.

2. Configure um aplicativo lógico disparado por timer que [efetua pull de dados do armazenamento de blobs](../../connectors/connectors-create-api-azureblobstorage.md#add-action) e [envia esses dados por push como uma mensagem para o hub de eventos](../../connectors/connectors-create-api-azure-event-hubs.md#add-action).

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>O que pode fazer com os dados de monitoramento que estão sendo enviados para o hub de eventos?

Rotear dados de monitoramento para um hub de eventos com o Azure Monitor permite fácil integração com as ferramentas de monitoramento e o SIEM de parceiro. A maioria das ferramentas exigem que a cadeia de conexão de hub de eventos e determinadas permissões para sua assinatura do Azure leiam dados do hub de eventos. Aqui está uma lista parcial das ferramentas com integração ao Azure Monitor:

* **IBM QRadar** – o Microsoft Azure DSM e protocolo de Hub de Eventos do Microsoft Azure estão disponíveis para download do [site de suporte da IBM](https://www.ibm.com/support). Você pode [saber mais sobre a integração com o Azure aqui](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
* **Splunk** -dependendo da configuração de Splunk, há duas abordagens:
    1. [O Azure Monitor Add-On for Splunk](https://splunkbase.splunk.com/app/3534/) está disponível no Splunkbase e em um projeto de software livre. [A documentação está aqui](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
    2. Se você não puder instalar um complemento na instância do Splunk (por exemplo, Se usar um proxy ou estiver executando na Splunk Cloud), você poderá encaminhar esses eventos para o Splunk HTTP Event Collector usando [essa função disparada por novas mensagens no hub de eventos](https://github.com/Microsoft/AzureFunctionforSplunkVS).
* **SumoLogic** – instruções para configurar o SumoLogic para consumir dados de um hub de eventos estão [disponíveis aqui](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)
* **ArcSight** - O conector inteligente ArcSight Azure Event Hub está disponível como parte de [a coleção de conectores inteligentes ArcSight aqui](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852).
* **Servidor syslog** – se você deseja transmitir os dados do Azure Monitor diretamente para um servidor syslog, você pode fazer check-out [este repositório GitHub](https://github.com/miguelangelopereira/azuremonitor2syslog/).

## <a name="next-steps"></a>Próximas etapas
* [Arquivar o Log de Atividades em uma conta de armazenamento](../../azure-monitor/platform/archive-activity-log.md)
* [Leia a visão geral do Log de Atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md)
* [Configurar um alerta com base em um evento do Log de Atividades](../../azure-monitor/platform/alerts-log-webhook.md)


