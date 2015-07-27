<properties 
	pageTitle="Exibir eventos e logs de auditoria" 
	description="Saiba como ver todos os eventos que acontecem em sua assinatura do Azure." 
	authors="HaniKN-MSFT" 
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
	ms.date="04/28/2015" 
	ms.author="hanikn"/>

# Exibir eventos e logs de auditoria

Todas as operações executadas nos recursos do Azure são auditadas totalmente pelo Gerenciador de Recursos do Azure, desde a criação e exclusões a conceder ou revogar acessos. Você pode navegar por esses logs no portal do Azure, e também pode usar a [API REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) ou o [SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para acessar o conjunto completo de eventos programaticamente.

## Procure os eventos que afetam sua assinatura do Azure

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Procurar** e selecione **Logs de auditoria**. ![Hub de procura](./media/insights-debugging-with-events/Insights_Browse.png)
3. Isso abrirá uma folha mostrando todos os eventos que afetaram qualquer uma das suas assinaturas nos últimos 7 dias. Na parte superior, fica um gráfico mostrando os dados por nível e, abaixo dele, fica a lista completa de logs: ![Todos os eventos](./media/insights-debugging-with-events/Insights_AllEvents.png)

>[AZURE.NOTE]Você só pode exibir os 500 eventos mais recentes para uma determinada assinatura no portal do Azure.

4. Você pode clicar em qualquer entrada de log para ver os eventos que a compõem. Por exemplo, quando você implanta algo em um grupo de recursos, muitos recursos diferentes podem ser criados ou modificados. Para cada entrada, você pode ver:
    * O **nível** do evento - por exemplo, pode ser algo para monitorar (**Informativo**), ou quando algo deu errado e você precisa ficar sabendo (**Erro**). 
    * O **Status** - o status final será geralmente **Bem-sucedido** ou **Com falha**, mas também pode ser **Aceito** para operações de longa duração.
    * *Quando* o evento ocorreu.
    * *Quem* realizou a operação, se houver. Nem todas as operações são executadas pelos usuários, algumas são executadas pelos serviços de back-end, por isso, elas podem não ter um **Chamador**.
    * A **ID de correlação** do evento - esse é o identificador exclusivo para esse conjunto de operações.

5. A partir daí, você pode ir para a folha de detalhes para ver os detalhes do evento.
   
    ![Grupos de recursos](./media/insights-debugging-with-events/Insights_EventDetails.png)

    Para eventos **Com falha**, esta página geralmente inclui uma seção **Substatus** e uma seção **Propriedades** que incluem detalhes úteis para fins de depuração.

## Filtrar logs específicos

Para ver os eventos que se aplicam a uma entidade específica ou de um tipo específico, você pode filtrar a folha de logs de auditoria clicando no comando **Filter**. Você também usar a folha de filtro para alterar o **Período de tempo** da folha Logs de auditoria.

Depois que você clicar nesse comando, uma nova folha será aberta:

![Filtro](./media/insights-debugging-with-events/Insights_EventFilter.png)

Há quatro tipos de filtros:

1. Por assinatura
2. Por um **Grupo de recursos**
3. Por um **Tipo de recursos**
4. Por um determinado **recurso** - para isso, é necessário fornecer a *ID do recurso* completa do recurso em que está interessado

Além disso, você também pode filtrar eventos por quem executou o evento ou o nível do evento.

Depois de escolher o que você deseja ver, clique no botão **Atualizar** na parte inferior da folha.

## Monitorar eventos que afetam recursos específicos

1. Clique em **Procurar** para localizar o recurso em que você está interessado. Você também pode ver todos os logs de um **Grupo de recursos** inteiro.
2. Na folha do recurso, role para baixo até encontrar o bloco **Eventos**. ![Bloco de eventos](./media/insights-debugging-with-events/Insights_EventsTile.png)
3. Clique no bloco para ver os eventos filtrados para apenas o recurso que você selecionou. Você pode usar o comando **Filter** para alterar o intervalo de tempo ou aplicar filtros mais específicos.

## Próximas etapas

* [Receber notificações de alerta](insights-receive-alert-notifications.md) sempre que um evento ocorre.
* [Monitore as métricas de serviço](insights-how-to-customize-monitoring.md) para se certificar de que o serviço está disponível e responsivo.
* [Acompanhe a integridade do serviço](insights-service-health.md) para saber quando o Azure sofreu interrupções de serviço ou degradação do desempenho.  

<!---HONumber=July15_HO3-->