<properties 
	pageTitle="Application Insights para aplicativos de área de trabalho do Windows" 
	description="Analise utilização e desempenho de seu aplicativo do Windows com o Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/04/2015" 
	ms.author="awills"/>

# Application Insights em aplicativos de área de trabalho do Windows

*O Application Insights está em modo de visualização.*

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

O Application Insights permite que você monitore seu aplicativo implantado quanto à utilização e ao desempenho.

*Embora seja possível fazer o SDK do Application Insights funcionar em um aplicativo de área de trabalho, não é um cenário ao qual damos suporte atualmente. Mas, se desejar tentar fazê-lo experimentalmente, aqui estão algumas dicas para isso.*




## <a name="add"></a> Criar um recurso do Application Insights


1.  No [Portal do Azure][portal], crie um novo recurso do Application Insights. Para o tipo de aplicativo, escolha aplicativo ASP.NET ou aplicativo da Windows Store. 

    ![Clique em Novo, Application Insights](./media/app-insights-windows-get-started/01-new.png)

    \(Sua escolha do tipo de aplicativo define o conteúdo da folha Visão Geral e as propriedades disponíveis no [Metrics Explorer][metrics].\)

2.  Faça uma cópia da chave de instrumentação.

    ![Clique em Propriedades, selecione a chave e pressione ctrl + C](./media/app-insights-windows-get-started/02-props.png)

## <a name="sdk"></a>Instalar o SDK no seu aplicativo


1. No Visual Studio, edite os pacotes do NuGet do seu projeto de aplicativo de área de trabalho. ![Clique com o botão direito no projeto e selecione Gerenciar Pacotes Nuget](./media/app-insights-windows-get-started/03-nuget.png)

2. Instale o núcleo do SDK do Application Insights.

    ![Selecione **Online**, **Incluir pré-lançamento**, e procure "Application Insights"](./media/app-insights-windows-get-started/04-ai-nuget.png)

    \(Como alternativa, você poderia escolher o SDK do Application Insights para aplicativos Web. Isso oferece alguma telemetria interna de contadores de desempenho.\)

3. Edite o ApplicationInsights.config \(que foi adicionado pela instalação do NuGet\). Insira isto logo antes da marca de fechamento:

    &lt;InstrumentationKey&gt;\*a chave que você copiou\*&lt;/InstrumentationKey&gt;

    Como alternativa, você pode alcançar o mesmo efeito com esse código:
    
    `TelemetryConfiguration.Active.InstrumentationKey = "your key";`

4. Se você instalou o SDK de aplicativos Web, talvez você também deseje comentar os módulos de telemetria da Web de ApplicationInsights.config

## <a name="telemetry"></a>Inserir chamadas de telemetria

Crie uma instância `TelemetryClient` e, em seguida, [use-a para enviar telemetria][track].

Use `TelemetryClient.Flush` para enviar mensagens antes de fechar o aplicativo. \(Isso não é recomendado para outros tipos de aplicativo\).

Por exemplo, em um Aplicativo Windows Forms, você poderia escrever:

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps
            }
            base.OnClosing(e);
        }

```

Use qualquer uma das [APIs do Application Insights][track] para enviar telemetria. Em aplicativos de área de trabalho do Windows, nenhuma telemetria é enviada automaticamente. Normalmente você utilizaria:

* TrackPageView\(pageName\) para alternar entre formulários, páginas ou guias
* TrackEvent\(eventName\) para outras ações de usuário
* TrackTrace\(logEvent\) para [registro de diagnóstico][diagnostic]
* TrackException\(exception\) para cláusulas catch
* TrackMetric \(nome, valor\) em uma tarefa em segundo plano para enviar relatórios regulares de métricas não anexadas a eventos específicos.

Para ver as contagens de usuários e sessões, defina um inicializador de contexto:

    class TelemetryInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
            context.User.Id = Environment.UserName;
            context.Session.Id = DateTime.Now.ToFileTime().ToString();
            context.Session.IsNewSession = true;
        }
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.ContextInitializers.Add(
                new TelemetryInitializer());
            ...



## <a name="run"></a>Executar seu projeto

[Execute o aplicativo com F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) e use-o para gerar alguma telemetria.

No Visual Studio, você verá uma contagem dos eventos que foram recebidos.

![](./media/appinsights/appinsights-09eventcount.png)



## <a name="monitor"></a>Ver dados de monitoramento

Retorne para a folha de seu aplicativo no portal do Azure.

Os primeiros eventos aparecerão na Pesquisa de Diagnóstico.

Se você estiver esperando mais dados, clique em Atualizar depois de alguns segundos.

Se você usou o TrackMetric ou o parâmetro de medidas do TrackEvent, abra o [Metrics Explorer][metrics] e abra a folha Filtros, na qual você verá suas métricas.



## <a name="usage"></a>Próximas etapas

[Acompanhar o uso do seu aplicativo][track]

[Capturar logs de diagnóstico e pesquisar neles][diagnostic]

[Solucionar problemas][qna]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md


<!--HONumber=54-->