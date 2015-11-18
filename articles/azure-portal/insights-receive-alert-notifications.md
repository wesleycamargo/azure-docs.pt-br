<properties 
	pageTitle="Receber notificações de alerta" 
	description="Seja notificado quando as condições das regras de alerta forem atendidas." 
	authors="stepsic-microsoft-com" 
	manager="ronmart" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2015" 
	ms.author="stepsic"/>

# Receber notificações de alerta

Você pode receber um alerta com base em métricas de monitoramento ou em eventos nos serviços do Azure.

Para uma regra de alerta sobre um valor de métrica, quando o valor de uma métrica especificada ultrapassa um limite atribuído, a regra de alerta é ativada e pode enviar uma notificação. Para uma regra de alerta sobre eventos, uma regra pode enviar uma notificação *cada* evento ou somente quando ocorrer um determinado número de eventos.

Quando você cria uma regra de alerta, pode selecionar opções para enviar uma notificação por email para o administrador de serviço e os co-administradores ou outro administrador que você pode especificar. É enviado um email de notificação quando a regra for ativada e quando uma condição de alerta for resolvida.

Você pode usar o [API REST](https://msdn.microsoft.com/library/azure/dn931945.aspx) ou [SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para configurar e obter informações sobre regras de alerta por meio de programação.

## Criar uma regra de alerta

1. No [portal](https://portal.azure.com/), clique em **Navegar** e, em seguida, clique em um recurso que você esteja interessado em monitorar.

2. Clique no bloco **Regras de alerta** na lente **Operações**.

3. Clique no comando **Adicionar alerta**.
    ![Adicionar alerta](./media/insights-receive-alert-notifications/Insights_AddAlert.png)

4. Você pode nomear a regra de alerta e escolher uma descrição que aparecerá no email de notificação.

5. Quando você seleciona **Métricas**, poderá escolher uma condição e um valor de limite para a métrica. Esse é o período de tempo que o Azure usa para monitorar e plotar a atividade de alerta.
    ![Condição e limite](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)

6. Você também pode escolher **Eventos** e receber uma notificação quando ocorrer um determinado evento. 
    ![Eventos](./media/insights-receive-alert-notifications/Insights_Events.png)
    
>[AZURE.NOTE] Neste momento, apenas há suporte para os Eventos para Aplicativos Web. 

7. Finalmente, você pode optar por enviar uma notificação por email aos administradores responsáveis.

Depois de clicar em **Salvar**, dentro de alguns minutos você será informado sempre que a métrica que escolheu exceder o limite. 

## Gerenciar suas regras de alerta

Depois de criar uma regra de alerta, você pode exibir uma visualização de seu limite de alerta comparado à métrica do dia anterior. 

![Eventos](./media/insights-receive-alert-notifications/Insights_EditAlert.png)


É claro que você pode editar essa regra de alerta e **Desabilitar** ou **Habilitar** se deseja interromper temporariamente a recepção de notificações sobre ele. 

## Próximas etapas

* [Configurar webhooks em seus alertas](insights-webhooks-alerts.md) para direcionar notificações a vários canais
* [Monitore as métricas de serviço](insights-how-to-customize-monitoring.md) para se certificar de que o serviço está disponível e responsivo.
* [Ative o monitoramento e diagnóstico](insights-how-to-use-diagnostics.md) para coletar métricas detalhadas de alta frequência em seu serviço.
* [Monitore a disponibilidade e a capacidade de resposta de qualquer página da Web](../app-insights-monitor-web-app-availability.md) com o Application Insights para que você possa descobrir se a página está inativa.
* [Monitore o desempenho do aplicativo](insights-perf-analytics.md) se você quiser compreender exatamente como seu código está sendo executado na nuvem.
* [Exiba eventos e logs de auditoria](insights-debugging-with-events.md) para saber tudo o que aconteceu no seu serviço.
* [Acompanhe a integridade do serviço](insights-service-health.md) para saber quando o Azure sofreu interrupções de serviço ou degradação do desempenho.
 

<!----HONumber=Oct15_HO4-->