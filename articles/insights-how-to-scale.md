<properties title="Como dimensionar um site" pageTitle="Como dimensionar um site" description="Saiba como dimensionar seu plano de hospedagem no Azure." authors="stepsic"  />

# Como dimensionar um site

Na Visualização do Portal do Azure, você pode definir manualmente a contagem de instâncias de seu Plano de Hospedagem na Web ou definir parâmetros para que o dimensionamento seja feito automaticamente. Antes de configurar o dimensionamento de seu Plano de Hospedagem na Web, você deve considerar que o dimensionamento será afetado pelo tamanho da instância. Tamanhos maiores têm mais núcleos e memória e, portanto, terão melhor desempenho para o mesmo número de instâncias.

A escala afeta todo um Plano de Hospedagem na Web. Quando cria um Site, você tem a opção de criar um novo Plano de Hospedagem na Web ou um Plano de Hospedagem na Web existente. Quando você tem um Plano de Hospedagem na Web, todos os sites compartilham as mesmas instâncias e, portanto, todas elas são dimensionadas coletivamente.

## Dimensionando um Plano de Hospedagem na Web

1. Na [Visualização do Portal do Azure](https://portal.azure.com/), clique em **Procurar**, em seguida, em **Sites** e clique no nome do Site para abrir a lâmina.

2. A parte **Dimensionar** na lente **Operações** da lâmina Site informará o status do Plano de Hospedagem da Web: **Desativado** quando você estiver dimensionando manualmente, **Desempenho** quando você estiver dimensionando por uma ou mais métricas de desempenho, e **Agendamento** quando você tiver habilitado vários perfis de dimensionamento automático.

    ![Parte Dimensionar](./media/insights-how-to-scale/Insights_ScalePartOff.png)

3. Um clique na parte abrirá a lâmina **Escala**. Na parte superior da lâmina de escala, você pode ver um histórico das ações de dimensionamento automático de Plano de Hospedagem na Web.

    ![Lâmina Escala](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)

4. Você pode ajustar manualmente o número de máquinas virtuais que executam seu Plano de Hospedagem na Web com controle deslizante **Instância**. 

5. Se desejar que o número de instâncias seja ajustado automaticamente com base na carga, selecione **Desempenho** em **Modo de Autoescala**. Neste ponto, você não pode selecionar **Agendamento** na Visualização do Portal do Azure.

    ![Lâmina Escala com a porcentagem de CPU](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png) 

6. Depois que você selecionar Desempenho, há duas alterações:
    - **Intervalo de Instâncias** agora permite que você escolha uma contagem máxima e mínima de instâncias. A autoescala sempre mantém você nesse intervalo, independentemente da carga.
    - Você pode definir as métricas de desempenho na seção **Métricas de Destino**

7. A seção **Porcentagem de CPU** permite que você defina um destino para a média de CPU entre todas as instâncias no seu Plano de Hospedagem na Web. Uma expansão acontecerá quando a média de CPU exceder o máximo definido.

Com a autoescala habilitada, você verá **Desempenho** na parte da lâmina Site e o histórico de sua escala no gráfico:

![Lâmina Escala com a porcentagem de CPU](./media/insights-how-to-scale/Insights_ScalePartBladeOn.png) 

Observe que, na Visualização do Portal do Azure, você não pode alterar o número de instâncias de um Plano de Hospedagem na Web Compartilhado.

## Dimensionamento avançado

Novidade na Visualização do Portal do Azure, você pode dimensionar com base em métricas diferentes de Porcentagem de CPU e até mesmo ter um conjunto complexo de regras de expansão e redução.

### Dimensionamento com base em outras métricas de desempenho
Além da CPU, você pode dimensionar com base em:

- Média de Memória - se o percentual médio de memória usado nas instâncias ficar acima ou abaixo dos limites especificados, as instâncias serão adicionadas ou removidas.
- Profundidade da Fila HTTP ou Profundidade da Fila do Disco - se o número de mensagens na fila das solicitações HTTP ou do Disco ficar acima ou abaixo do limite especificado, as instâncias serão adicionadas ou removidas.

Há duas maneiras diferentes de dimensionar por outra métrica. Se você desejar dimensionar apenas por uma única métrica, selecione a divisa ao lado do controle deslizante **Porcentagem da CPU**. Isso abrirá a lâmina Detalhes da Métrica:

   ![Ponto de entrada para métricas de escala](./media/insights-how-to-scale/Insights_ScaleMetricChevron.png)

Para dimensionar por mais de uma métrica ao mesmo tempo, você pode clicar em **Adicionar Métrica** na barra de comandos:

   ![Adicionar métricas](./media/insights-how-to-scale/Insights_AddMetric.png)
   
A lâmina Detalhes da Métrica contém todos os controles que você precisa para configurar seu perfil ideal de escala. Na parte superior, escolha a nova métrica pela qual você deseja dimensionar.

### Dimensionamento com várias etapas

Abaixo do gráfico da métrica há duas seções: **Regras de aumento** e **Regras de redução**. Seu serviço será aumentado se **qualquer uma** das regras de aumento for atendida. De modo oposto, seu serviço será reduzido se **todas** as regras de redução forem atendidas.

Para cada regra que você escolher:
- Condição - maior ou menor que
- Limite - o número que essa métrica tem que passar para disparar a ação
- Nos últimos - o número de minutos no qual a média dessa métrica é definida
- Aumentar ou reduzir por - o tamanho da ação da escala
- Resfriamento - quanto tempo esta regra deve esperar após a ação anterior de escala para dimensionar novamente

   ![Várias regras de escala](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Com várias regras de escala, você pode ser mais agressivo sobre como expandir (ou reduzir) à medida que o desempenho muda. Por exemplo, você pode definir duas regras de escala:

1. Aumentar uma instância se a porcentagem de CPU estiver acima de 60%

2. Aumentar três instâncias se a porcentagem de CPU estiver acima de 85%

Com essa regra adicional, se sua carga exceder 85% antes de uma ação de escala, você terá duas instâncias adicionais em vez de uma. 

