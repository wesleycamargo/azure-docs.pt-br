---
title: Criar e gerenciar grupos de ações no portal do Azure
description: Este artigo mostra como criar e gerenciar grupos de ações no portal do Azure.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 38cccf17980f5a6a2cf162cdecdc6aad40d4f38e
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432550"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Criar e gerenciar grupos de ações no portal do Azure
## <a name="overview"></a>Visão geral ##
Um grupo de ações é uma coleção de preferências de notificação definidas pelo proprietário de uma assinatura do Azure. Alertas do Azure Monitor e da Integridade do Serviço usam grupos de ações para notificar usuários de que um alerta foi disparado. Vários alertas podem usar o mesmo grupo de ação ou grupos de ações diferentes dependendo dos requisitos do usuário.

Quando uma ação for configurada para notificar uma pessoa por email ou SMS, a pessoa receberá uma confirmação indicando que ela foi adicionada ao grupo de ações.

Este artigo mostra como criar e gerenciar grupos de ação no Portal do Azure.

Cada ação é composta das seguintes propriedades:

* **Nome**: um identificador exclusivo dentro do grupo de ações.  
* **Tipo de ação**: a ação a ser executada. Exemplos incluem o envio de um email de chamada, SMS, voz; ou disparando vários tipos de ações automatizadas. Veja os tipos mais adiante neste artigo. 
* **Detalhes**: os detalhes correspondentes que variam de acordo com *tipo de ação*. 

Para saber mais sobre como usar modelos do Azure Resource Manager para configurar grupos de ação, veja [Modelos do Resource Manager de grupos de ações](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Como criar um grupo de ações usando o portal do Azure ##
1. No [portal](https://portal.azure.com), selecione **Monitor**. A folha **Monitor** consolida todas as suas configurações e dados em uma exibição de monitoramento.

    ![O serviço “Monitor”](./media/action-groups/home-monitor.png)
1. Selecione **Alertas**, em seguida, selecione **Gerenciar grupos de ação**.

    ![Botão Gerenciar Grupos de Ações](./media/action-groups/manage-action-groups.png)
1. Selecione **Adicionar grupo de ações** e preencha os campos.

    ![O comando "Adicionar grupo de ações"](./media/action-groups/add-action-group.png)
1. Insira um nome na caixa **Nome do grupo de ação** e, em seguida, digite um nome na caixa **Nome curto**. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

      ![Caixa de diálogo Adicionar grupo de ações](./media/action-groups/action-group-define.png)

1. A caixa **Assinatura** é automaticamente preenchida com a sua assinatura atual. Esta assinatura é aquela na qual o grupo de ação é salvo.

1. Selecione o **Grupo de recursos** no qual o grupo de ações é salvo.

1. Defina uma lista de ações fornecendo estas ações:

     a. **Nome**: insira um identificador exclusivo para esta ação.

    b. **Tipo de ação**: selecione Email/SMS/Push/Voz, Aplicativo Lógico, Webhook, ITSM ou Runbook de Automação.

    c. **Detalhes**: de acordo com o tipo de ação, insira um número de telefone, endereço de email, URI de webhook, aplicativo do Azure, conexão de ITSM ou runbook de Automação. Para Ação do ITSM, além disso, especifique **Item de Trabalho** e outros campos necessários para a ferramenta de ITSM.

1. Selecione **OK** para criar o grupo de ações.

## <a name="manage-your-action-groups"></a>Gerenciar seus grupos de ação ##
Depois de criar um grupo, ele ficará visível na seção **Grupos de ações** da folha **Monitor**. Selecione o grupo de ações que você deseja gerenciar:

* Adicionar, editar ou remover ações.
* Excluir o grupo de ação.

## <a name="action-specific-information"></a>Informações específicas da ação
**Push do aplicativo do Azure**: você pode ter até 10 ações do aplicativo do Azure em um grupo de ação. No momento, a ação do aplicativo do Azure dá suporte apenas aos alertas do ServiceHealth. Qualquer outra hora do alerta será ignorada. Consulte [configurar alertas sempre que uma notificação de integridade do serviço for postada](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

**E-mail**: os e-mails serão enviados dos seguintes endereços de e-mail. Certifique-se de que a filtragem de email esteja configurada adequadamente
   - azure-noreply@microsoft.com
   - azureemail-noreply@microsoft.com
   - alerts-noreply@mail.windowsazure.com

Você pode ter até 1000 ações de e-mail em um grupo de ação. Consulte o artigo [informações de limitação da taxa](./../../azure-monitor/platform/alerts-rate-limiting.md)

**ITSM** - você pode ter até 10 ações de ITSM em uma ação de ITSM do grupo de ação que requer uma conexão de ITSM. Saiba como criar uma [Conexão de ITSM](../../azure-monitor/platform/itsmc-overview.md).

**Aplicativo Lógico**: você pode ter até 10 ações do Aplicativo Lógico em um Grupo de Ação

**Aplicativo de funções** -as teclas de função para o Aplicativo de funções configurado como ações são lidos por meio da API de funções, que atualmente requer os aplicativos de função do v2 para configurar a configuração "AzureWebJobsSecretStorageType" para "arquivos" do aplicativo, consulte [Altera para o gerenciamento de chaves no Functions V2]( https://aka.ms/funcsecrets) para obter mais informações.

**Runbook** - Você pode ter até 10 ações do Runbook em um Grupo de Ação Consulte os [limites do serviço de assinatura do Azure](../../azure-subscription-service-limits.md) para obter limites para cargas úteis do Runbook

**SMS** - Você pode ter até 10 ações de SMS em um grupo de ação Consulte o [artigo sobre informações limitadoras de taxa](./../../azure-monitor/platform/alerts-rate-limiting.md) Veja o [comportamento de alerta por SMS](../../azure-monitor/platform/alerts-sms-behavior.md)

**Voz** -você pode ter até 10 ações de voz em um grupo de ação</dd>
Consulte o artigo [informações de limitação da taxa](./../../azure-monitor/platform/alerts-rate-limiting.md)</dd>

**Webhook** - Você pode ter até 10 ações do Webhook em um Grupo de Ação. Lógica de repetição - o período de tempo limite para uma resposta é 10 segundos. A chamada de webhook será repetida no máximo 2 vezes quando os seguintes códigos de status HTTP forem retornados: 408, 429, 503, 504 ou quando o ponto de extremidade HTTP não responder. A primeira nova tentativa ocorre após 10 segundos. A segunda e última nova tentativa ocorre após 100 segundos.

Intervalos de endereços IP de fonte
    - 13.106.57.181
    - 13.106.54.3
    - 13.106.54.19
    - 13.106.38.142
    - 13.106.38.148
    - 13.106.57.196

Para receber atualizações sobre as alterações para esses endereços IP, é recomendável que você configure um [alerta de Integridade do Serviço do Azure](./../../azure-monitor/platform/service-notifications.md) que monitora notificações informativas sobre o serviço de grupos de ação.


## <a name="next-steps"></a>Próximas etapas ##
* Saiba mais sobre o [comportamento de alertas por SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Tenha uma [compreensão do esquema de webhook de alerta do log de atividades](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Saiba mais sobre o [Conector de ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Saiba mais sobre a [limitação de taxa](../../azure-monitor/platform/alerts-rate-limiting.md) para alertas.
* Obtenha uma [visão geral dos alertas do log de atividades](../../azure-monitor/platform/alerts-overview.md) e saiba como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de integridade do serviço é postada](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

