---
title: Criar e gerenciar grupos de ações no portal do Azure
description: Este artigo mostra como criar e gerenciar grupos de ações no portal do Azure.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/26/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 695a2ff827fc5514c3a32364026bc9d47c8a2121
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500297"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Criar e gerenciar grupos de ações no portal do Azure
## <a name="overview"></a>Visão geral ##
Um grupo de ações é uma coleção de preferências de notificação definidas pelo proprietário de uma assinatura do Azure. Alertas do Azure Monitor e da Integridade do Serviço usam grupos de ações para notificar usuários de que um alerta foi disparado. Vários alertas podem usar o mesmo grupo de ação ou grupos de ações diferentes dependendo dos requisitos do usuário. Você pode configurar até 2 mil grupos de ação em uma assinatura.

Você configurar uma ação para notificar que uma pessoa por email ou SMS, eles recebem uma confirmação indicando que eles foram adicionados ao grupo de ação.

Este artigo mostra como criar e gerenciar grupos de ação no Portal do Azure.

Cada ação é composta das seguintes propriedades:

* **Nome**: um identificador exclusivo dentro do grupo de ações.  
* **Tipo de ação**: A ação executada. Exemplos incluem o envio de um email de chamada, SMS, voz; ou disparando vários tipos de ações automatizadas. Veja os tipos mais adiante neste artigo. 
* **Detalhes**: Os detalhes correspondentes que variam de acordo com *tipo de ação*. 

Para saber mais sobre como usar modelos do Azure Resource Manager para configurar grupos de ação, veja [Modelos do Resource Manager de grupos de ações](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Como criar um grupo de ações usando o portal do Azure ##
1. No [portal](https://portal.azure.com), selecione **Monitor**. O **Monitor** painel consolida todas as suas configurações e dados em uma exibição monitoramento.

    ![O serviço “Monitor”](./media/action-groups/home-monitor.png)
1. Selecione **Alertas**, em seguida, selecione **Gerenciar grupos de ação**.

    ![Botão Gerenciar Grupos de Ações](./media/action-groups/manage-action-groups.png)
1. Selecione **Adicionar grupo de ações** e preencha os campos.

    ![O comando "Adicionar grupo de ações"](./media/action-groups/add-action-group.png)
1. Insira um nome na caixa **Nome do grupo de ação** e, em seguida, digite um nome na caixa **Nome curto**. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

      ![Caixa de diálogo Adicionar grupo de ações](./media/action-groups/action-group-define.png)

1. A caixa **Assinatura** é automaticamente preenchida com a sua assinatura atual. Esta assinatura é aquela na qual o grupo de ação é salvo.

1. Selecione o **Grupo de recursos** no qual o grupo de ações é salvo.

1. Defina uma lista de ações. Forneça o seguinte para cada ação:

     a. **Nome**: insira um identificador exclusivo para esta ação.

    b. **Tipo de ação**: selecione Email/SMS/Push/Voz, Aplicativo Lógico, Webhook, ITSM ou Runbook de Automação.

    c. **Detalhes**: de acordo com o tipo de ação, insira um número de telefone, endereço de email, URI de webhook, aplicativo do Azure, conexão de ITSM ou runbook de Automação. Para Ação do ITSM, além disso, especifique **Item de Trabalho** e outros campos necessários para a ferramenta de ITSM.

1. Selecione **OK** para criar o grupo de ações.

## <a name="manage-your-action-groups"></a>Gerenciar seus grupos de ação ##
Depois de criar um grupo de ação, ele ficará visível na **grupos de ação** seção o **Monitor** painel. Selecione o grupo de ações que você deseja gerenciar:

* Adicionar, editar ou remover ações.
* Excluir o grupo de ação.

## <a name="action-specific-information"></a>Informações específicas da ação
> [!NOTE]
> Ver [limites de serviço de subscrição para monitoramento](https://docs.microsoft.com/azure/azure-subscription-service-limits#monitor-limits) para limites numéricos em cada um dos itens abaixo.  

**Aplicativo do Azure por Push** -você pode ter um número limitado de ações de aplicativo do Azure em um grupo de ações. Neste momento, a ação de aplicativo do Azure dá suporte apenas aos alertas do ServiceHealth. Qualquer outro tipo de alerta será ignorado. Consulte [configurar alertas sempre que uma notificação de integridade do serviço for postada](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

**E-mail**: os e-mails serão enviados dos seguintes endereços de e-mail. Certifique-se de que a filtragem de email esteja configurada adequadamente
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Você pode ter um número limitado de ações de email em um grupo de ação. Consulte o artigo [informações de limitação da taxa](./../../azure-monitor/platform/alerts-rate-limiting.md)

**ITSM** -você pode ter um número limitado de número limitado de ações de ITSM em um grupo de ação. Ação de ITSM exige uma Conexão de ITSM. Saiba como criar uma [Conexão de ITSM](../../azure-monitor/platform/itsmc-overview.md).

**Aplicativo lógico** -você pode ter um número limitado de ações do aplicativo lógico em um grupo de ação.

**Função de aplicativo** -a função essas chaves para aplicativos de função configurado como ações são lidos por meio da API de funções, que atualmente requer que os aplicativos de função do v2 para configurar o aplicativo de configuração "AzureWebJobsSecretStorageType" para "arquivos". Para obter mais informações, consulte [muda para o gerenciamento de chaves no Functions V2]( https://aka.ms/funcsecrets).

**Runbook** -você pode ter um número limitado de ações de Runbook em um grupo de ação. Consulte a [limites de serviço de assinatura do Azure](../../azure-subscription-service-limits.md) para limites de cargas de Runbook.

**SMS** -você pode ter um número limitado de ações de SMS em um grupo de ação. Consulte também o [informações de limitação de taxa](./../../azure-monitor/platform/alerts-rate-limiting.md) e [comportamento de alertas por SMS](../../azure-monitor/platform/alerts-sms-behavior.md) para obter informações adicionais importantes. 

**Voz** -você pode ter um número limitado de ações de voz em um grupo de ação. Consulte a [informações de limitação de taxa](./../../azure-monitor/platform/alerts-rate-limiting.md) artigo.

**Webhook** -você pode ter um número limitado de ações de Webhook em um grupo de ação. Webhooks são repetidos usando as regras a seguir. A chamada webhook será repetida um máximo de 2 vezes quando os seguintes códigos de status HTTP forem retornados: 408, 429, 503, 504 ou quando o ponto de extremidade HTTP não responder. A primeira nova tentativa ocorre após 10 segundos. A segunda tentativa ocorre após 100 segundos. Após duas falhas, nenhum grupo de ação chamará o ponto de extremidade por 30 minutos. 

Intervalos de endereços IP de fonte
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117
 - 51.4.138.199
 - 51.5.148.86

Para receber atualizações sobre as alterações para esses endereços IP, recomendamos que você configure um [serviço Alerta de integridade, que monitora para notificações informativas sobre o serviço de grupos de ação.


## <a name="next-steps"></a>Próximas etapas ##

* Saiba mais sobre o [comportamento de alertas por SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Tenha uma [compreensão do esquema de webhook de alerta do log de atividades](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Saiba mais sobre o [Conector de ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Saiba mais sobre a [limitação de taxa](../../azure-monitor/platform/alerts-rate-limiting.md) para alertas.
* Obtenha uma [visão geral dos alertas do log de atividades](../../azure-monitor/platform/alerts-overview.md) e saiba como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de integridade do serviço é postada](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

