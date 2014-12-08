<properties title="How to debug with events" pageTitle="Como depurar com eventos" description="Learn how to see events in Azure." authors="hanikn" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="hanikn" />

# Monitorar os eventos que afetam seus recursos ou grupo de recursos do Azure

1. Entre na [Visualização do Portal do Azure](https://portal.azure.com/).
2. Clique no botão **Procurar** na barra de navegação à esquerda (também conhecida como a **barra de salto**).  
    ![Browse Hub](./media/insights-debugging-with-events/Insights_Browse.png)
3. Em seguida, escolha qualquer recurso (dependendo dos eventos nos quais você estiver interessado). Para fins de ilustração, as capturas de tela deste documento incluem o Evento de grupos de recursos.
4. Na folha **Grupos de recursos** clique no nome do grupo de recursos. Isso fará com que você navegue até a folha do grupo de recursos.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_SelectRG.png)
5. A folha do grupo de recursos inclui uma parte denominada **Eventos da semana anterior**. Cada uma das barras dessa parte, representam o número de eventos que ocorreram em cada um dos dias da semana anterior. Cada uma das barras pode ter duas cores diferentes: azul e rosa. Rosa representa eventos **Com falha** naquele dia, e azul representa todos os outros eventos.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_RGBlade.png)
6. Agora, clique na parte **Eventos da semana anterior**. Você verá uma nova folha chamada **Eventos** que inclui todos os eventos da semana anterior que afetaram seu grupo de recursos.
    ![Resource groups](./media/insights-debugging-with-events/Insights_AllEvents.png)
7. Clique em um dos eventos.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_EventDetails.png)  
    Uma nova folha será aberta incluindo muitos detalhes do evento. Para eventos **Com falha**, esta página geralmente inclui uma seção **Substatus** e uma seção **Propriedades** que incluem detalhes úteis para fins de depuração.

