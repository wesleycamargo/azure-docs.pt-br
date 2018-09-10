---
title: Usar o portal do Azure para criar alertas clássicos para serviços do Azure | Microsoft Docs
description: Dispare emails ou notificações, ou chame URLs de sites (webhooks) ou automação quando as condições especificadas forem atendidas.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/23/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: d0e5512eb3f963898ded6d7155f8b75cfb6ef911
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287422"
---
# <a name="use-the-azure-portal-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>Usar o portal do Azure para criar alertas de métricas clássicas nos serviços do Azure Monitor para Azure 

> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>

> [!NOTE]
> Este artigo descreve como criar alertas de métrica clássicos mais antigos. O Azure Monitor agora dá suporta a [alertas de métrica mais recentes](monitoring-near-real-time-metric-alerts.md). 


Este artigo mostra como configurar os alertas de métrica clássicos do Azure, usando o portal do Azure. 

Você pode receber um alerta com base nas métricas dos serviços do Azure ou receber alertas de eventos que ocorrem no Azure.

* **Valores de métrica**: o alerta dispara quando o valor de uma métrica especificada ultrapassa um limite que você atribui em qualquer direção. Ou seja, ele dispara quando a condição é atendida pela primeira vez e, em seguida, quando essa condição não é mais atendida.    

* **Eventos do log de atividades**: um alerta pode disparar em *cada* evento ou quando determinados eventos ocorrem. Saiba mais sobre os [alertas do log de atividades](monitoring-activity-log-alerts.md).

Você pode configurar um alerta de métrica clássico para fazer o seguinte quando ele disparar:

* Enviar notificações por email para o administrador e coadministradores de serviços.
* Envie um email para endereços de email adicionais que você especificar.
* Chamar um webhook.
* Iniciar a execução de um runbook do Azure (apenas no portal do Azure).

É possível configurar e obter informações sobre regras de alerta de métricas clássicas nos seguintes locais: 

* [Portal do Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [CLI do Azure](insights-alerts-command-line-interface.md)
* [API REST do Monitor do Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta em uma métrica com o Portal do Azure
1. No [portal](https://portal.azure.com/), localize o recurso que você quer monitorar e, em seguida, selecione-o.

2. Na seção**MONITORAMENTO**, selecione **Alertas (Clássico)**. O texto e o ícone podem variar ligeiramente para diferentes recursos. Se você não localizar **Alertas (Clássico)** aqui, poderá encontrá-lo em **Alertas** ou **Regras de Alerta**.

    ![Monitoramento](./media/insights-alerts-portal/AlertRulesButton.png)

3. Selecione o comando **Adicionar alerta de métrica (clássico)** e preencha os campos.

    ![Adicionar alerta](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **Nomeie** a regra de alerta. Em seguida, escolha uma **Descrição**, que também aparece nos emails de notificação.

5. Selecione a **Métrica** que você quer monitorar. Em seguida, escolha um valor de **Condição** e **Limite** para a métrica. Escolha também o **Período** durante o qual a regra de métrica deverá ser atendida antes de o alerta disparar. Por exemplo, se você usar o período "Pelos últimos 5 minutos" e o alerta procura por CPU acima de 80%, o alerta disparará quando a CPU estiver consistentemente acima de 80% por 5 minutos. Depois que o primeiro gatilho ocorrer, ele será disparado novamente quando a CPU ficar abaixo de 80% por 5 minutos. A medição da métrica da CPU acontece a cada minuto.

6. Selecione **Proprietários de email...** se você quiser que administradores e coadministradores recebam notificações por email quando o alerta for acionado.

7. Se você quiser enviar notificações para endereços de email adicionais quando o alerta for acionado, adicione-os no campo **Emails adicionais do administrador**. Separe vários emails com ponto e vírgula, no formato a seguir: *email@contoso.com; email2@contoso.com*

8. Coloque um URI válido no campo **Webhook** se você quiser que ele seja chamado quando o alerta for acionado.

9. Se você usar a Automação do Azure, selecione um runbook a ser executado quando o alerta for acionado.

10. Selecione **OK** para criar o alerta.   

Em alguns minutos, o alerta estará ativo e disparará conforme descrito anteriormente.

## <a name="manage-your-alerts"></a>Gerenciar seus alertas
Após criar um alerta, você poderá selecioná-lo e executar uma das seguintes tarefas:

* Exibir um gráfico mostrando o limite da métrica e os valores reais do dia anterior.
* Editar ou exclui-lo.
* **Desabilitar** ou **Habilitar** se você quiser interromper temporariamente ou continuar recebendo notificações do alerta.

## <a name="next-steps"></a>Próximas etapas
* [Obter uma visão geral do monitoramento do Azure](monitoring-overview.md), incluindo os tipos de informações que você pode coletar e monitorar.
* Saiba mais sobre os [alertas de métrica novos](monitoring-near-real-time-metric-alerts.md).
* Saiba mais sobre como [configurar webhooks em alertas](insights-webhooks-alerts.md).
* Saiba mais sobre [configurar alertas em eventos de log de atividades](monitoring-activity-log-alerts.md).
* Saiba mais sobre [runbooks da Automação do Azure](../automation/automation-starting-a-runbook.md).
* Tenha uma [visão geral dos logs de diagnóstico](monitoring-overview-of-diagnostic-logs.md) e colete métricas detalhadas de alta frequência em seu serviço.
* Tenha uma [visão geral da coleção de métricas](insights-how-to-customize-monitoring.md) para garantir que o serviço está disponível e responsivo.
