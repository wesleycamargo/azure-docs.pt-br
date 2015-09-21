<properties 
	pageTitle="Controlar a integridade do serviço" 
	description="Saiba quando o Azure sofreu interrupções de serviço ou degradação do desempenho." 
	authors="stepsic-microsoft-com" 
	manager="kamrani" 
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

# Controlar a integridade do serviço

O Azure publica sempre que há uma degradação no desempenho ou interrupção do serviço. Você pode navegar por esses eventos no Portal do Azure, e também pode usar a [API REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) ou o [SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para acessar o conjunto completo de eventos programaticamente.

## Consulte a integridade do serviço por região

1. Entre no [Portal do Azure](https://portal.azure.com/).

2. Em **Página Inicial** você deve ver um bloco chamado **Integridade do serviço** ![Home](./media/insights-service-health/Insights_Home.png)

3. Ao clicar nesse bloco uma lista com todas as regiões no Azure será exibida. Você pode clicar em qualquer região para ver o histórico de integridade do serviço para essa região. ![Home](./media/insights-service-health/Insights_Regions.png)

4. Você também pode ver os detalhes de um incidente individual clicando nele na tabela.

## Procurar por logs completos de integridade do serviço

2. Clique em **Procurar** e selecione **Logs de auditoria**. ![Hub de procura](./media/insights-service-health/Insights_Browse.png)

3. Isso abrirá uma folha mostrando todos os eventos que afetaram qualquer uma das suas assinaturas nos últimos 7 dias. Entradas de integridade do serviço serão exibidas nessa lista, mas talvez seja difícil encontrá-las, pois a lista pode ser grande.

4. Clique no comando **Filtrar**.

5. Selecione **Categoria de evento** e escolha **Integridade do serviço**: ![Todos os eventos](./media/insights-service-health/Insights_Filter.png)

6. Clique em **Atualizado**.

7. Agora você verá todos os eventos de integridade do serviço que têm impacto em sua assinatura: ![Grupos de recursos](./media/insights-service-health/Insights_HealthEvent.png)

8. A partir daí, você pode ir para a folha de detalhes para ver os detalhes do evento.
   
## Próximas etapas

* [Receber notificações de alerta](insights-receive-alert-notifications.md) sempre que um evento ocorre.
* [Monitore as métricas de serviço](insights-how-to-customize-monitoring.md) para se certificar de que o serviço está disponível e responsivo.
* [Monitore a disponibilidade e a capacidade de resposta de qualquer página da Web](../app-insights-monitor-web-app-availability.md) com o Application Insights para que você possa descobrir se a página está inativa.
 

<!---HONumber=Sept15_HO2-->