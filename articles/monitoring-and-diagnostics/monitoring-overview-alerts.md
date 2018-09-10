---
title: Visão geral dos alertas clássicos no Microsoft Azure e no Azure Monitor
description: Alerta para permitir que você monitore as métricas dos recursos do Azure, eventos ou logs, e seja notificado quando uma condição especificada for atendida.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: a0abcbdaa7e998413efb717be6e0addc5607ec5c
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114004"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>O que são alertas clássicos no Microsoft Azure?

> [!NOTE]
> Este artigo descreve como criar alertas de métrica clássicos mais antigos. O Azure Monitor agora dá suporte para [alertas de métrica quase em tempo real mais recentes e uma nova experiência de alertas](monitoring-overview-unified-alerts.md). 
>

Usando alertas, é possível configurar condições sobre dados e ser notificado quando as condições corresponderem aos dados de monitoramento mais recentes.


## <a name="alerts-on-azure-monitor-data"></a>Alertas sobre dados do Azure Monitor
Há dois tipos de alertas clássicos disponíveis: alertas de métrica e alertas de log de atividades.

* **Alertas de Métricas Clássicos**: esse alerta é disparado quando o valor de uma métrica especificada ultrapassa um limite atribuído. O alerta gera uma notificação quando é "ativado" (quando o limite é ultrapassado e a condição de alerta é atendida). Ele também gera um alerta quando é "resolvido" (quando o limite é ultrapassado novamente e a condição não é mais atendida). 

* **Alertas do log de atividades clássicos**: esse alerta de log de streaming é disparado quando um evento de log de atividades é gerado que corresponde aos critérios de filtro que você atribuiu. Esses alertas têm apenas um estado, "ativado", porque o mecanismo de alerta simplesmente aplica os critérios de filtro a qualquer novo evento. Esses alertas podem notificá-lo quando ocorrer um novo incidente de Integridade do Serviço ou quando um usuário ou aplicativo executar uma operação na assinatura, como "Excluir máquina virtual."

Para receber dados de log de diagnóstico disponíveis que estão disponíveis por meio do Azure Monitor, roteie os dados para o Log Analytics (antigo Operations Management Suite) e use um alerta de consulta do Log Analytics. O Log Analytics agora usa o [novo método de alerta](monitoring-overview-unified-alerts.md). 

O diagrama a seguir resume as fontes de dados no Azure Monitor e sugere como você pode alertar esses dados.

![Alertas explicados](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Taxonomia de alertas do Azure Monitor (clássico)
O Azure usa os termos a seguir para descrever alertas clássicos e suas funções:
* **Alerta**: a definição de critérios (uma ou mais regras ou condições) que são ativadas quando atendidas.
* **Ativo**: o estado que ocorre quando os critérios definidos por um alerta clássico são atendidos.
* **Resolvido**: o estado que ocorre quando os critérios definidos por um alerta clássico não são mais atendidos após terem sido atendidos anteriormente.
* **Notificação**: a ação que é tomada com base em quando um alerta clássico torna-se ativo.
* **Ação**: a chamada específica enviada ao destinatário de uma notificação (por exemplo, um email ou uma postagem para uma URL do webhook). As notificações normalmente podem disparar várias ações.

## <a name="how-do-i-receive-notifications-from-an-azure-monitor-classic-alert"></a>Como fazer para receber notificações de um alerta clássico do Azure Monitor?
Historicamente, os alertas do Azure de serviços diferentes usavam seus próprios métodos de notificação internos. 

Agora, o Azure Monitor oferece agrupamento de notificação reutilizável chamado *grupos de ações*. Grupos de ações especificam um conjunto de receptores para uma notificação. Quando um alerta é ativado que referencia o grupo de ações, todos os receptores recebem essa notificação. Essa funcionalidade permite reutilizar um agrupamento de receptores (por exemplo, a lista de engenheiros em serviço) em vários objetos de alerta. Os grupos de ações dão suporte à notificação através de vários métodos. Esses métodos podem incluir postagem em um URL do webhook, envio de emails, mensagens SMS e várias outras ações. Para obter mais informações, consulte [Criar e gerenciar grupos de ações no portal do Azure](monitoring-action-groups.md). 

Os alertas de log de atividades clássicos mais antigos usam grupos de ações.

No entanto, os alertas de métrica mais antigos não usam grupos de ações. Em vez disso, é possível configurar as ações a seguir: 
* Enviar notificações por email ao administrador do serviço, coadministradores ou para endereços de email adicionais que você especificar.
* Chamar um webhook, o que permite inicializar ações de automação adicionais.

Webhooks permitem automação e correção, por exemplo, usando os serviços a seguir:
- Runbook de Automação do Azure
- Funções do Azure
- Aplicativo lógico do Azure
- Um serviço de terceiros

## <a name="next-steps"></a>Próximas etapas
Obtenha informações sobre regras de alerta e como configurá-las, usando a documentação a seguir:

* Saiba mais sobre [métricas](monitoring-overview-metrics.md)
* Configurar [alertas de métrica clássicos usando o portal do Azure](insights-alerts-portal.md)
* Configurar [alertas de métrica clássicos usando PowerShell](insights-alerts-powershell.md)
* Configurar [alertas de métrica clássicos usando a CLI do Azure](insights-alerts-command-line-interface.md)
* Configurar [alertas de métrica clássicos usando a API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Saiba mais sobre [logs de atividades](monitoring-overview-activity-logs.md)
* Configurar [alertas de log de atividades usando o portal do Azure](monitoring-activity-log-alerts.md)
* Configurar [alertas de log de atividades usando o Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Examine o [esquema do webhook de alertas do Log de Atividade](monitoring-activity-log-alerts-webhook.md)
* Saiba mais sobre [grupos de ação](monitoring-action-groups.md)
* Configurar [alertas mais recentes](monitor-alerts-unified-usage.md)
