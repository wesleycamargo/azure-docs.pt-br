<properties title="Como depurar com eventos" pageTitle="Como depurar com eventos" description="Saiba como ver eventos no Azure." authors="hanikn"  />

# Monitorar os eventos que afetam seus sites ou grupos de recursos do Azure 

1.	Entre na [Visualização do Portal do Azure](https://portal.azure.com/). 

2.	Clique no botão **Procurar** na barra de navegação à esquerda (também conhecida como a **barra de salto**).

   ![Hub de procura](./media/insights-debugging-with-events/Insights_Browse.png)
	
3. Escolha **Grupos de recursos** ou **Sites** (dependendo dos eventos nos quais você está interessado). Para fins de ilustração, as capturas de tela deste documento incluem o Evento de grupos de recursos.

4.	Na lâmina **Grupos de recursos**, clique no nome do grupo de recursos. Isso fará com que você navegue até a lâmina do grupo de recursos.

	![Grupos de recursos](./media/insights-debugging-with-events/Insights_SelectRG.png)

5.	A lâmina do grupo de recursos inclui uma parte denominada **Eventos da semana anterior**. Cada uma das barras dessa parte, representam o número de eventos que ocorreram em cada um dos dias da semana anterior. Cada uma das barras pode ter duas cores diferentes: rosa e azul. Rosa representa eventos **Com falha** naquele dia, e azul representa todos os outros eventos.
 
 	![Grupos de recursos](./media/insights-debugging-with-events/Insights_RGBlade.png)

6.	Agora, clique na parte **Eventos da semana anterior**. Você verá uma nova lâmina chamada **Eventos** que inclui todos os eventos da semana anterior que afetaram seu grupo de recursos.

 ![Grupos de recursos](./media/insights-debugging-with-events/Insights_AllEvents.png)

6.	Clique em um dos eventos. 

 ![Grupos de recursos](./media/insights-debugging-with-events/Insights_EventDetails.png)

Uma nova lâmina será aberta incluindo muitos detalhes do evento. Para eventos **Com falha**, esta página geralmente inclui uma seção **Substatus** e uma seção **Propriedades** que incluem detalhes úteis para fins de depuração.



