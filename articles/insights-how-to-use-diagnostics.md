<properties title="How to use diagnostics" pageTitle="How to use diagnostics" description="Learn how to set up diagnostics for your resources in Azure." authors="stepsic"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stepsic" />

# Configurando os diagnósticos

Na Visualização do Portal do Azure, você agora pode configurar os dados completos, frequentes, de monitoramento e de diagnóstico sobre suas máquinas virtuais do Windows e suas contas de armazenamento.

## Coletando dados completos das máquinas virtuais

1.  Na [Visualização do Portal do Azure](https://portal.azure.com/), clique em **Procurar**, em seguida, em **Máquinas virtuais**. Selecione a máquina virtual que você está interessado em monitorar.
2.  As lentes de **Monitoramento** contém algumas métricas padrão, como **porcentagem de CPU**, **leitura e gravação de disco** e **Rede ativa e inativa**. Um clique em qualquer uma das partes o mostrará a folha **Métrica**.  
    ![Lentes de monitoramento](./media/insights-how-to-use-diagnostics/Insights_VMMonitoringLens.png)
3.  A folha **Métrica** mostra detalhes das métricas que você seleciona. Na parte superior da lâmina há um gráfico, abaixo dele há uma tabela que mostra agregação dessas métricas, como a média, o mínimo e o máximo. Abaixo há uma lista de alertas que você definiu, filtrados pelas métricas que aparecem na folha.
    
	![Folha Métrica](./media/insights-how-to-use-diagnostics/Insights_VMMetricBlade.png)
4.  Para habilitar os diagnósticos completos, clique no botão **Configurações** e você verá a folha **Diagnósticos**. Escolha **ON**:
    ![Folha de diagnósticos](./media/insights-how-to-use-diagnostics/Insights_VMDiagnosticsBlade.png)

    -   **Métricas básicas**: Métricas de Integridade sobre sua máquina virtual como processador e memória
    -   **Por métricas de disco**: Métricas sobre todos os discos anexados à sua máquina virtual
    -   **Métricas .NET**: Métricas sobre os aplicativos .NET e ASP.NET que executam na sua máquina virtual
    -   **Métricas de rede**: Métricas sobre suas conexões de rede e serviços da Web
    -   **Logs do aplicativo do evento Windows**: Eventos do Windows que são enviados para o canal do aplicativo
    -   **Logs do sistema de evento do Windows** : Eventos do Windows que são enviados para o canal do sistema. Isso também inclui todos os eventos do [Microsoft Antimalware](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409).
    -   **Logs de segurança de evento do Windows** : Eventos do Windows que são enviados para o canal de segurança
    -   **Logs de infraestrutura de diagnósticos** : Registro em log sobre a infraestrutura de coleta de diagnósticos
    -   **Logs IIS**: Logs sobre seu servidor IIS
        Todas as métricas e logs serão registrados em intervalos de um minuto, então você pode sempre ter a maioria das informações sobre sua máquina atualizadas.

Quando você habilita as informações de diagnóstico para uma conta de armazenamento, essa conta será carregada no armazenamento normal, transação e custos egressos. No entanto, estes recursos não produzem um grande volume de dados, com a possível exceção dos logs IIS. A fim de minimizar os custos egressos, não exigimos que você selecione uma conta de armazenamento na mesma região do que a máquina virtual.

Uma vez que você clica em **OK**, os dados começarão a aparecer na sua conta de armazenamento dentro de alguns minutos. Você não pode habilitar os diagnósticos para máquinas virtuais que executam o Linux, e o Agente Convidado deve estar instalado para habilitar os diagnósticos.

## Coletando dados completos das contas de armazenamento

Você sempre será capaz de coletar aguns dados das contas de armazenamento, mas agora na Visualização do Portal do Azure você pode coletar dados de granularidade de um minuto para realmente entender o que está acontecendo dentro de sua conta de armazenamento. As etapas para permitir métricas de um minuto são semelhantes para as máquinas virtuais:

1.  Vá para a folha **Métrica** ao clicar em qualquer um dos gráficos na folha **Conta de Armazenamento**.
2.  Clique no botão **Diagnósticos** na barra de comandos.
3.  Selecione quais dados você quer coletar a partir da conta de armazenamento:
    ![Diagnósticos de Armazenamento](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)
4.  Clique em **OK**. Levará alguns minutos para que os dados apareçam pela primeira vez.

## Visualizando dados de diagnósticos

Uma vez que você habilitou os diagnósticos, você pode ver a lista completa de métricas disponíveis clicando com o botão direito do mouse em qualquer gráfico e indo para **Editar consulta**:

![Editar consulta](./media/insights-how-to-use-diagnostics/Insights_VMEditQuery.png)

Você pode plotar estas métricas, e ampliar para a **Última hora**, para a **Semana passada** ou até mesmo escolher um intervalo de tempo **Personalizado**:

![Timerange personalizado](./media/insights-how-to-use-diagnostics/Insights_VMCustomTime.png)

Você perceberá que essas métricas estão mais para granulares do que os dados que estavam disponíveis anteriormente, e que existem um retardo mínimo.

Neste momento, não existe uma forma para plotar métricas que tem instâncias múltiplas, como medidas por processo ou por disco. Para obter mais informações sobre como personalizar os gráficos de monitoramento, consulte [Como personalizar o monitoramento](http://go.microsoft.com/fwlink/?LinkID=394523&clcid=0x409).

## Alertas nos dados de diagnóstico

Além de visualizar as métricas, você pode alertar sobre qualquer dessas métricas na Visualização do Portal. Primeiro, role para baixo para a parte de **Regras de alerta** na máquina virtual ou folha de armazenamento e, em seguida, clique em **Adicionar alerta**:

![Adicionar alerta](./media/insights-how-to-use-diagnostics/Insights_VMAlerts.png)

Em seguida, você pode selecionar de qualquer uma das métricas que você tem habilitada para diagnóstico:

![Alerta JIT](./media/insights-how-to-use-diagnostics/Insights_VMJITAlert.png)

O gráfico lhe mostrará uma visualização de seu limite de alerta comparado à métrica do dia anterior. Depois de clicar em **Salvar**, dentro de alguns minutos você será informado sempre que a métrica que escolheu exceder o limite.

Observe que as métricas em que aparece apenas no Portal de Visualização não são alertáveis no Portal Completo. Como resultado, certas regras de alerta no Portal de Visualização não estão no Portal Completo.


