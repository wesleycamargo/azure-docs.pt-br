<properties title="Como personalizar o monitoramento" pageTitle="Como personalizar o monitoramento" description="Saiba como personalizar os gráficos de monitoramento no Azure." authors="stepsic"  />

# Personalizando o monitoramento

Na Visualização do Portal do Azure, agora você pode exibir os dados de monitoramento de mais maneiras do que antes, inclusive personalizar o intervalo de tempo e escolher mais métricas.

1. Na [Visualização do Portal do Azure](https://portal.azure.com/), clique em **Procurar**, em seguida, em **Sites** e clique no nome de um Site para abrir a lâmina.

2. Na lente **Monitoramento**, você pode ver Solicitações, Erros, [Testes da Web](http://go.microsoft.com/fwlink/?LinkID=394528&clcid=0x409) e [Análises](http://go.microsoft.com/fwlink/?LinkID=394529&clcid=0x409). Um clique na parte **Solicitações e erros hoje** mostrará a lâmina **Métrica**.

   ![Lente Monitoramento](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. A lâmina **Métrica** mostra detalhes das métricas que você seleciona. Na parte superior da lâmina há um gráfico, abaixo dele há uma tabela que mostra agregação dessas métricas, como a média, o mínimo e o máximo. Abaixo, há uma lista dos alertas que você definiu, filtrados pelas métricas que aparecem na tabela. Dessa forma, se você tiver muitos alertas, você verá apenas os que são relevantes aqui. Você ainda pode ver todos os alertas de seu site clicando na parte **Regras de alerta** na lâmina **Site**.

   ![Lâmina Métrica](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. Para personalizar as métricas exibidas, clique com o botão direito do mouse no gráfico e selecione **Editar Consulta**. 

   ![Editar Consulta](./media/insights-how-to-customize-monitoring/Insights_MetricMenu.png)

5. Na lâmina Editar Consulta você pode fazer duas coisas: alterar o intervalo de tempo e escolher métricas diferentes.

   ![Editar Consulta](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. A alteração do intervalo de tempo é tão fácil quanto selecionar um intervalo diferente (como **Última Hora**) e clicar em **Salvar** na parte inferior da lâmina. Novidade na Visualização do Portal, você pode escolher **Personalizar**:

  ![Intervalo de tempo personalizado](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
   
7. A opção Personalizar permite que você escolha qualquer período de tempo nas últimas duas semanas, por exemplo, você pode ver as duas semanas inteiras ou apenas uma hora de ontem. Digite na caixa de texto para inserir outra hora.

8. Abaixo do intervalo de tempo, você pode escolher qualquer número de métricas para mostrar no gráfico. Você pode ver algumas novas métricas: **Conjunto de trabalho de memória** e **Conjunto de trabalho de memória média**.

9. Quando você clica em Salvar, suas alterações persistirão até que você saia da lâmina Site. Quando voltar mais tarde, você verá a métrica e o intervalo de tempo originais novamente.

## Monitoramento do plano de hospedagem na Web

Outra novidade na Visualização do Portal do Azure é a capacidade de monitorar as métricas de desempenho sobre as instâncias nos quais seus sites são executados. Para acessar essas métricas, clique no ícone de Plano de Hospedagem na Web na lente Resumo.

   ![Plano de hospedagem na Web](./media/insights-how-to-customize-monitoring/Insights_WHPSelect.png)
   
Ali, você pode ver um gráfico na lente **Monitoramento** que se comporta exatamente como o gráfico na lâmina Site, com a exceção de que você pode ver as novas métricas:
- Porcentagem de CPU
- Porcentagem de Memória
- Profundidade da Fila HTTP
- Profundidade da Fila do Disco

## Criando gráficos lado a lado

Com a personalização avançada pelo usuário na Visualização do Portal do Azure, você pode criar gráficos lado a lado para personalização.

1. Primeiro, clique com o botão direito do mouse no gráfico no qual você deseja iniciar e selecione **Personalizar**

   ![Personalizar gráfico](./media/insights-how-to-customize-monitoring/Insights_Customize.png)

2. Em seguida, clique em **Clonar** no menu **...** para copiar a parte

   ![Clonar parte](./media/insights-how-to-customize-monitoring/Insights_ClonePart.png)

3. Finalmente, clique em **Concluído** na barra de ferramentas na parte superior da tela. Agora você pode tratar essa parte como uma parte de métrica normal. Se você clicar com o botão direito do mouse e alterar a métrica exibida, você verá duas métricas diferentes lado a lado ao mesmo tempo:

   ![Duas métricas lado a lado](./media/insights-how-to-customize-monitoring/Insights_SideBySide.png)
   
Observe que o intervalo de tempo do gráfico e as métricas escolhidas serão redefinidas quando você sair do portal.




