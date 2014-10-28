<properties title="How to customize monitoring" pageTitle="How to customize monitoring" description="Learn how to customize monitoring charts in Azure." authors="stepsic"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stepsic"></tags>

# Personalizando o monitoramento

Na Visualização do Portal do Azure, agora você pode exibir os dados de monitoramento de mais maneiras do que antes, inclusive personalizar o intervalo de tempo e escolher mais métricas.

1.  Na [Visualização do Portal do Azure][Visualização do Portal do Azure], clique em **Navegar** e, em seguida, clique em um recurso que você esteja interessado em monitorar.
2.  O lente **Monitoramento** contém as métricas mais importantes para cada recurso do Azure. Por exemplo, Sites têm Solicitações, Erros, [testes da Web][testes da Web], e [Análises][Análises]. Um clique na parte **Solicitações e erros hoje** mostrará a lâmina **Métrica**.<br />
    ![Lente monitoramento][Lente monitoramento]
3.  A lâmina **Métrica** mostra detalhes das métricas que você seleciona. Na parte superior da lâmina há um gráfico, abaixo dele há uma tabela que mostra agregação dessas métricas, como a média, o mínimo e o máximo. Abaixo, há uma lista dos alertas que você definiu, filtrados pelas métricas que aparecem na tabela. Dessa forma, se você tiver muitos alertas, você verá apenas os que são relevantes aqui. Você ainda pode ver todos os alertas de seu site clicando na parte **Regras de alerta** na lâmina **Site**.<br />
    ![Lâmina métrica][Lâmina métrica]
4.  Para personalizar as métricas exibidas, clique com o botão direito do mouse no gráfico e selecione **Editar consulta**:<br />
    ![Editar consulta][Editar consulta]
5.  Na lâmina Editar Consulta você pode fazer duas coisas: alterar o intervalo de tempo e escolher métricas diferentes.<br />
    ![Editar Consulta][Editar Consulta]
6.  A alteração do intervalo de tempo é tão fácil quanto selecionar um intervalo diferente (como **Última Hora**) e clicar em **Salvar** na parte inferior da lâmina. Novidade na Visualização do Portal, você pode escolher **Personalizar**:<br />
    ![Intervalo de tempo personalizado][Intervalo de tempo personalizado]
7.  A opção Personalizar permite que você escolha qualquer período de tempo nas últimas duas semanas, por exemplo, você pode ver as duas semanas inteiras ou apenas uma hora de ontem. Digite na caixa de texto para inserir outra hora.
8.  Abaixo do intervalo de tempo, você pode escolher qualquer número de métricas para mostrar no gráfico. Você pode ver algumas novas métricas: **Conjunto de trabalho de memória** e **Conjunto de trabalho de memória média**.

9.  Quando você clica em Salvar, suas alterações persistirão até que você saia da lâmina Site. Quando voltar mais tarde, você verá a métrica e o intervalo de tempo originais novamente.

## Monitoramento de novos recursos

A novidade na Visualização do Portal do Azure é a capacidade de monitorar as métricas de desempenho para vários Recursos novos, incluindo:

-   Planos de hospedagem na Web
-   Cache Redis
-   Conta DocumentDB

Os planos de hospedagem da Web são um pouco mais complicados que outros recursos, porque representam o desempenho das instâncias nas quais seus **sites** são executadas. Para acessar as métricas do plano de hospedagem da Web, clique no ícone de plano de hospedagem na Web no lente Resumo para seu site.

![Plano de hospedagem na Web][Plano de hospedagem na Web]

Ali, você pode ver um gráfico na lente **Monitoramento** que se comporta exatamente como o gráfico na lâmina Site, com a exceção de que você pode ver as novas métricas:

-   Porcentagem de CPU
-   Porcentagem de Memória
-   Profundidade da Fila HTTP
-   Profundidade da Fila do Disco

## Criando gráficos lado a lado

Com a personalização avançada pelo usuário na Visualização do Portal do Azure, você pode criar gráficos lado a lado para personalização.

1.  Primeiro, clique com o botão direito do mouse no gráfico no qual você deseja iniciar e selecione **Personalizar**<br />
    ![Personalizar gráfico][Personalizar gráfico]
2.  Em seguida, clique em **Clonar** no menu **...** para copiar a parte<br />
    ![Clonar parte][Clonar parte]
3.  Finalmente, clique em **Finalizar** na barra de ferramentas no topo da tela. Agora você pode tratar essa parte como uma parte de métrica normal. Se você clicar com o botão direito do mouse e alterar a métrica exibida, você verá duas métricas diferentes lado a lado ao mesmo tempo:<br />
    ![Duas métricas lado a lado][Duas métricas lado a lado]

Observe que o intervalo de tempo do gráfico e as métricas escolhidas serão redefinidas quando você sair do portal.

  [Visualização do Portal do Azure]: https://portal.azure.com/
  [testes da Web]: http://go.microsoft.com/fwlink/?LinkID=394528&clcid=0x409
  [Análises]: http://go.microsoft.com/fwlink/?LinkID=394529&clcid=0x409
  [Lente monitoramento]: ./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png
  [Lâmina métrica]: ./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png
  [Editar consulta]: ./media/insights-how-to-customize-monitoring/Insights_MetricMenu.png
  [Editar Consulta]: ./media/insights-how-to-customize-monitoring/Insights_EditQuery.png
  [Intervalo de tempo personalizado]: ./media/insights-how-to-customize-monitoring/Insights_CustomTime.png
  [Plano de hospedagem na Web]: ./media/insights-how-to-customize-monitoring/Insights_WHPSelect.png
  [Personalizar gráfico]: ./media/insights-how-to-customize-monitoring/Insights_Customize.png
  [Clonar parte]: ./media/insights-how-to-customize-monitoring/Insights_ClonePart.png
  [Duas métricas lado a lado]: ./media/insights-how-to-customize-monitoring/Insights_SideBySide.png
