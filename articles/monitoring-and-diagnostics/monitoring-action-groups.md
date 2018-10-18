---
title: Criar e gerenciar grupos de ações no portal do Azure
description: Este artigo mostra como criar e gerenciar grupos de ações no portal do Azure.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 6163a099894a823614355f71a3e1af4a6a9026ec
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717668"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Criar e gerenciar grupos de ações no portal do Azure
## <a name="overview"></a>Visão geral ##
Um grupo de ação é uma coleção de preferências de notificação definidas pelo usuário. Os alertas do Azure Monitor e da Integridade do Serviço do Azure são configurados para usar um grupo de ação específico quando o alerta for disparado. Vários alertas podem usar o mesmo grupo de ação ou grupos de ações diferentes dependendo dos requisitos do usuário.

Este artigo mostra como criar e gerenciar grupos de ação no Portal do Azure.

Cada ação é composta das seguintes propriedades:

* **Nome:** um identificador exclusivo dentro do grupo de ações.  
* **Tipo de ação**: envie uma Chamada de voz ou SMS, envie um email, chame um webhook, envie dados para uma ferramenta de ITSM, chame um Aplicativo Lógico, envie uma notificação por push para o aplicativo do Azure ou execute um runbook de Automação.
* **Detalhes**: o número de telefone correspondente, o endereço de email, o URI de webhook ou os Detalhes de Conexão de ITSM.

Para saber mais sobre como usar modelos do Azure Resource Manager para configurar grupos de ação, veja [Modelos do Resource Manager de grupos de ações](monitoring-create-action-group-with-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Como criar um grupo de ações usando o portal do Azure ##
1. No [portal](https://portal.azure.com), selecione **Monitor**. A folha **Monitor** consolida todas as suas configurações e dados em uma exibição de monitoramento.

    ![O serviço “Monitor”](./media/monitoring-action-groups/home-monitor.png)
1. Selecione **Alertas**, em seguida, selecione **Gerenciar grupos de ação**.

    ![Botão Gerenciar Grupos de Ações](./media/monitoring-action-groups/manage-action-groups.png)
1. Selecione **Adicionar grupo de ações** e preencha os campos.

    ![O comando "Adicionar grupo de ações"](./media/monitoring-action-groups/add-action-group.png)
1. Insira um nome na caixa **Nome do grupo de ação** e, em seguida, digite um nome na caixa **Nome curto**. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

      ![Caixa de diálogo Adicionar grupo de ações](./media/monitoring-action-groups/action-group-define.png)

1. A caixa **Assinatura** é automaticamente preenchida com a sua assinatura atual. Esta assinatura é aquela na qual o grupo de ação é salvo.

1. Selecione o **Grupo de recursos** no qual o grupo de ações é salvo.

1. Defina uma lista de ações fornecendo estas ações:

    a. **Nome**: insira um identificador exclusivo para esta ação.

    b. **Action Type**: selecione Email/SMS/Push/Voz, Aplicativo Lógico, Webhook, ITSM ou Runbook de Automação.

    c. **Detalhes**: de acordo com o tipo de ação, insira um número de telefone, endereço de email, URI de webhook, aplicativo do Azure, conexão de ITSM ou runbook de Automação. Para Ação do ITSM, além disso, especifique **Item de Trabalho** e outros campos necessários para a ferramenta de ITSM.

1. Selecione **OK** para criar o grupo de ações.

## <a name="action-specific-information"></a>Informações específicas da ação
<dl>
<dt>Envio por Push do Aplicativo do Azure</dt>
<dd>É possível ter até 10 ações do aplicativo do Azure em um Grupo de Ações.</dd>
<dd>No momento, a ação do aplicativo do Azure dá suporte apenas aos alertas do ServiceHealth. Qualquer outra hora do alerta será ignorada. Consulte [configurar alertas sempre que uma notificação de integridade do serviço for postada](monitoring-activity-log-alerts-on-service-notifications.md).</dd>

<dt>Email</dt>
<dd>Os emails serão enviados dos endereços de email a seguir. Certifique-se de que a filtragem de email esteja configurada adequadamente
<ul>
    <li>azure-noreply@microsoft.com</li>
    <li>azureemail-noreply@microsoft.com</li>
    <li>alerts-noreply@mail.windowsazure.com</li>
</ul>
</dd>
<dd>É possível ter até 1000 ações de email em um Grupo de Ações</dd>
<dd>Consulte o artigo [informações de limitação da taxa](./monitoring-alerts-rate-limiting.md)</dd>

<dt>ITSM</dt>
<dd>É possível ter até 10 ações de ITSM em um Grupo de Ações</dd>
<dd>Ação de ITSM exige uma Conexão de ITSM. Saiba como criar uma [Conexão de ITSM](../log-analytics/log-analytics-itsmc-overview.md).</dd>

<dt>Aplicativo Lógico</dt>
<dd>É possível ter até 10 ações do Aplicativo Lógico em um Grupo de Ações</dd>

<dt>Runbook</dt>
<dd>É possível ter até 10 ações de Runbook em um Grupo de Ações</dd>
<dd>Veja os [Limites do serviço de assinatura do Azure](../azure-subscription-service-limits.md) para limites nos conteúdos do Runbook</dd>

<dt>SMS</dt>
<dd>É possível ter até 10 ações de SMS em um Grupo de Ações</dd>
<dd>Consulte o artigo [informações de limitação da taxa](./monitoring-alerts-rate-limiting.md)</dd>
<dd>Consulte o artigo [Comportamento de alerta por SMS](monitoring-sms-alert-behavior.md)</dd>

<dt>Voz</dt>
<dd>É possível ter até 10 ações de Voz em um Grupo de Ações</dd>
<dd>Consulte o artigo [informações de limitação da taxa](./monitoring-alerts-rate-limiting.md)</dd>

<dt>Webhook</dt>
<dd>É possível ter até 10 ações de Webhook em um Grupo de Ações
<dd>Lógica de repetição - o período de tempo limite para uma resposta é 10 segundos. A chamada webhook será repetida no máximo 2 vezes quando os seguintes códigos de status HTTP forem retornados: 408, 429, 503, 504 ou o ponto de extremidade HTTP não responder. A primeira nova tentativa ocorre após 10 segundos. A segunda e última nova tentativa ocorre após 100 segundos.</dd>
<dd>Intervalos de endereços IP de fonte
<ul>
    <li>13.106.57.181</li>
    <li>13.106.54.3</li>
    <li>13.106.54.19</li>
    <li>13.106.38.142</li>
    <li>13.106.38.148</li>
    <li>13.106.57.196</li>
</ul>
Para receber atualizações sobre as alterações para esses endereços IP, é recomendável que você configure um [alerta de Integridade do Serviço do Azure](./monitoring-service-notifications.md) que monitora notificações informativas sobre o serviço de grupos de ação.
</dd>
</dl>

## <a name="manage-your-action-groups"></a>Gerenciar seus grupos de ação ##
Depois de criar um grupo, ele ficará visível na seção **Grupos de ações** da folha **Monitor**. Selecione o grupo de ações que você deseja gerenciar:

* Adicionar, editar ou remover ações.
* Excluir o grupo de ação.

## <a name="next-steps"></a>Próximas etapas ##
* Saiba mais sobre o [comportamento de alertas por SMS](monitoring-sms-alert-behavior.md).  
* Tenha uma [compreensão do esquema de webhook de alerta do log de atividades](monitoring-activity-log-alerts-webhook.md).  
* Saiba mais sobre o [Conector de ITSM](../log-analytics/log-analytics-itsmc-overview.md)
* Saiba mais sobre a [limitação de taxa](monitoring-alerts-rate-limiting.md) para alertas.
* Obtenha uma [visão geral dos alertas do log de atividades](monitoring-overview-alerts.md) e saiba como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de integridade do serviço é postada](monitoring-activity-log-alerts-on-service-notifications.md).
