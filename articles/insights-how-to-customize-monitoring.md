<properties title="How to customize monitoring" pageTitle="Como personalizar o monitoramento" description="Saiba como personalizar gráficos de monitoramento no Azure." authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-11-04" ms.author="awills" />

# Personalizando o monitoramento

O aplicativo Azure tem uma variedade de métricas que você pode monitorar, e você pode colocá-las em gráficos durante um período de tempo que você escolher.

1. Na [Visualização do Portal do Azure](https://portal.azure.com/), clique em **Navegar** e, em seguida, clique em um recurso que você esteja interessado em monitorar.
2. A lente **Monitoramento** contém as métricas mais importantes para cada recurso do Azure. Por exemplo, Sites têm Solicitações, Erros, [testes da Web](http://go.microsoft.com/fwlink/?LinkID=394528&clcid=0x409), e [Análises](http://go.microsoft.com/fwlink/?LinkID=394529&clcid=0x409). Um clique na parte **Solicitações e erros hoje** mostrará a folha **Métrica**.  
    ![Monitoring lens](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. A lâmina **Métrica** mostra detalhes das métricas que você seleciona. Na parte superior da lâmina há um gráfico, abaixo dele há uma tabela que mostra agregação dessas métricas, como a média, o mínimo e o máximo. Abaixo, há uma lista dos alertas que você definiu, filtrados pelas métricas que aparecem na tabela. Dessa forma, se você tiver muitos alertas, você verá apenas os que são relevantes aqui. Você ainda pode ver todos os alertas de seu site clicando na parte **Regras de alerta** na folha **Site**.  
    ![Metric blade](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. Para personalizar as métricas exibidas, clique com o botão direito do mouse no gráfico e selecione **Editar Consulta**:  
    ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_MetricMenu.png)
5. Na folha Editar Consulta, você pode fazer duas coisas: alterar o intervalo de tempo e escolher métricas diferentes.  
    ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. A alteração do intervalo de tempo é tão fácil quanto selecionar um intervalo diferente (como **Última Hora**) e clicar em **Salvar** na parte inferior da lâmina. Novidade na Visualização do Portal, você pode escolher **Personalizar**:  
    ![Custom time range](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. A opção Personalizar permite que você escolha qualquer período de tempo nas últimas duas semanas, por exemplo, você pode ver as duas semanas inteiras ou apenas uma hora de ontem. Digite na caixa de texto para inserir outra hora.
8. Abaixo do intervalo de tempo, você pode escolher qualquer número de métricas para mostrar no gráfico. Você pode ver algumas novas métricas: **Conjunto de trabalho da memória** e **Conjunto de trabalho da média de memória**.

9. Quando você clica em Salvar, suas alterações persistirão até que você saia da lâmina Site. Quando voltar mais tarde, você verá a métrica e o intervalo de tempo originais novamente.

## Monitoramento de novos recursos

A novidade na Visualização do Portal do Azure é a capacidade de monitorar as métricas de desempenho para vários Recursos novos, incluindo:
- Planos de hospedagem na Web
- Cache Redis
- Conta DocumentDB

Os planos de hospedagem da Web são um pouco mais complicados que outros recursos, porque representam o desempenho das instâncias nas quais seus **sites** são executadas. Para acessar as métricas do plano de hospedagem da Web, clique no ícone de plano de hospedagem na Web no lente Resumo para seu site.

![Web hosting plan](./media/insights-how-to-customize-monitoring/Insights_WHPSelect.png)

Ali, você pode ver um gráfico na lente **Monitoramento** que se comporta exatamente como o gráfico na lâmina Site, com a exceção de que você pode ver as novas métricas:

- Porcentagem de CPU
- Porcentagem de Memória
- Profundidade da Fila HTTP
- Profundidade da Fila do Disco

## Criando gráficos lado a lado

Com a personalização avançada pelo usuário na Visualização do Portal do Azure, você pode criar gráficos lado a lado para personalização.

1. Primeiro, clique com o botão direito do mouse no gráfico no qual você deseja iniciar e selecione **Personalizar**  
    ![Customize chart](./media/insights-how-to-customize-monitoring/Insights_Customize.png)
2. Em seguida, clique em **Clonar** no menu **...** para copiar a parte  
    ![Clone part](./media/insights-how-to-customize-monitoring/Insights_ClonePart.png)
3. Finalmente, clique em **Concluído** na barra de ferramentas na parte superior da tela. Agora você pode tratar essa parte como uma parte de métrica normal. Se você clicar com o botão direito do mouse e alterar a métrica exibida, você verá duas métricas diferentes lado a lado ao mesmo tempo:  
    ![Two metrics Side by Side](./media/insights-how-to-customize-monitoring/Insights_SideBySide.png)

Observe que o intervalo de tempo do gráfico e as métricas escolhidas serão redefinidas quando você sair do portal.

