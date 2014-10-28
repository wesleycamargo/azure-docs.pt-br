<properties title="How to debug with events" pageTitle="How to debug with events" description="Learn how to see events in Azure." authors="hanikn"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="hanikn"></tags>

# Monitorar os eventos que afetam seus recursos ou grupo de recursos do Azure

1.  Entre na [Visualização do Portal do Azure][Visualização do Portal do Azure].
2.  Clique no botão **Procurar** na barra de navegação à esquerda (também conhecida como a **barra de salto**).<br />
    ![Hub de procura][Hub de procura]
3.  Em seguida, escolha qualquer recurso (dependendo dos eventos nos quais você estiver interessado). Para fins de ilustração, as capturas de tela deste documento incluem o Evento de grupos de recursos.
4.  Na lâmina **Grupos de recursos**, clique no nome do grupo de recursos. Isso fará com que você navegue até a lâmina do grupo de recursos.<br />
    ![Grupos de recursos][Grupos de recursos]
5.  A lâmina do grupo de recursos inclui uma parte denominada **Eventos da semana anterior**. Cada uma das barras dessa parte, representam o número de eventos que ocorreram em cada um dos dias da semana anterior. Cada uma das barras pode ter duas cores diferentes: rosa e azul. Rosa representa eventos **Com falha** naquele dia, e azul representa todos os outros eventos.<br />
    ![Grupos de recursos][1]
6.  Agora, clique na parte **Eventos da semana anterior**. Você verá uma nova lâmina chamada **Eventos** que inclui todos os eventos da semana anterior que afetaram seu grupo de recursos.<br />
    ![Grupos de recursos][2]
7.  Clique em um dos eventos.<br />
    ![Grupos de Recursos][Grupos de Recursos]
    Uma nova lâmina será aberta incluindo muitos detalhes do evento. Para eventos **Com falha**, esta página geralmente inclui uma seção **Substatus** e uma seção **Propriedades** que incluem detalhes úteis para fins de depuração.

  [Visualização do Portal do Azure]: https://portal.azure.com/
  [Hub de procura]: ./media/insights-debugging-with-events/Insights_Browse.png
  [Grupos de recursos]: ./media/insights-debugging-with-events/Insights_SelectRG.png
  [1]: ./media/insights-debugging-with-events/Insights_RGBlade.png
  [2]: ./media/insights-debugging-with-events/Insights_AllEvents.png
  [Grupos de Recursos]: ./media/insights-debugging-with-events/Insights_EventDetails.png
