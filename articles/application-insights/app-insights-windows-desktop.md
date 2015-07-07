<properties 
	pageTitle="Application Insights para serviços e aplicativos da Área de Trabalho do Windows" 
	description="Analise utilização e desempenho de seu aplicativo do Windows com o Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/13/2015" 
	ms.author="awills"/>

# Application Insights em serviços e aplicativos da Área de Trabalho do Windows

*O Application Insights está em modo de visualização.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

O Application Insights permite que você monitore seu aplicativo implantado quanto à utilização e ao desempenho.

Suporte para serviços e aplicativos da Área de Trabalho do Windows é fornecido pelo SDK principal do Application Insights. Esse SDK fornece o suporte completo à API para todos os dados de telemetria, mas não oferece nenhuma coleta automática de telemetria.


## <a name="add"></a> Criar um recurso do Application Insights


1.  No [Portal do Azure][portal], crie um novo recurso do Application Insights. Para o tipo de aplicativo, escolha aplicativo da Windows Store. 

    ![Clique em Novo, Application Insights](./media/app-insights-windows-desktop/01-new.png)


2.  Faça uma cópia da chave de instrumentação.

    ![Clique em Propriedades, selecione a chave e pressione ctrl + C](./media/app-insights-windows-desktop/02-props.png)

## <a name="sdk"></a>Instalar o SDK no seu aplicativo


1. No Visual Studio, edite os pacotes do NuGet do seu projeto de aplicativo de área de trabalho. ![Clique com o botão direito no projeto e selecione Gerenciar Pacotes Nuget](./media/app-insights-windows-desktop/03-nuget.png)

2. Instale o pacote de API do Application Insights.

    ![Selecione **Online**, **Incluir pré-lançamento**, e procure "Application Insights"](./media/app-insights-windows-desktop/04-ai-nuget.png)

3. Edite o ApplicationInsights.config (que foi adicionado pela instalação do NuGet). Insira isto logo antes da marca de fechamento:

    &lt;InstrumentationKey&gt;*a chave que você copiou*&lt;/InstrumentationKey&gt;

    Como alternativa, você pode alcançar o mesmo efeito com esse código:
    
    `TelemetryConfiguration.Active.InstrumentationKey = "your key";`


## <a name="telemetry"></a>Inserir chamadas de telemetria

Crie uma instância `TelemetryClient` e, em seguida, [use-a para enviar telemetria][api].

Use `TelemetryClient.Flush` para enviar mensagens antes de fechar o aplicativo. (Isso não é recomendado para outros tipos de aplicativo).

Por exemplo, em um Aplicativo Windows Forms, você poderia escrever:

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.GetUserName();
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
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

Use qualquer uma das [APIs do Application Insights][api] para enviar telemetria. Em aplicativos de área de trabalho do Windows, nenhuma telemetria é enviada automaticamente. Normalmente você utilizaria:

* TrackPageView(pageName) para alternar entre formulários, páginas ou guias
* TrackEvent(eventName) para outras ações de usuário
* TrackMetric (nome, valor) em uma tarefa em segundo plano para enviar relatórios regulares de métricas não anexadas a eventos específicos.
* TrackTrace(logEvent) para [registro de diagnóstico][diagnostic]
* TrackException(exception) para cláusulas catch

#### Inicializadores de contexto

Como alternativa a definir os dados de sessão em cada instância de TelemetryClient, você pode usar um inicializador de contexto:

```C#
    class UserSessionInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
            context.User.Id = Environment.UserName;
            context.Session.Id = Guid.NewGuid().ToString();
        }
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.ContextInitializers.Add(
                new UserSessionInitializer());
            ...
```



## <a name="run"></a>Executar seu projeto

[Execute o aplicativo com F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) e use-o para gerar alguma telemetria.

No Visual Studio, você verá uma contagem dos eventos que foram recebidos.

![](./media/app-insights-windows-desktop/appinsights-09eventcount.png)



## <a name="monitor"></a>Ver dados de monitoramento

Retorne para a folha de seu aplicativo no portal do Azure.

Os primeiros eventos aparecerão na Pesquisa de Diagnóstico.

Se você estiver esperando mais dados, clique em Atualizar depois de alguns segundos.

Se você usou o TrackMetric ou o parâmetro de medidas do TrackEvent, abra o [Metrics Explorer][metrics] e abra a folha Filtros, na qual você verá suas métricas.



## <a name="usage"></a>Próximas etapas

[Acompanhar o uso do seu aplicativo][knowUsers]

[Capturar logs de diagnóstico e pesquisar neles][diagnostic]

[Solucionar problemas][qna]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[knowUsers]: app-insights-overview-usage.md
[api]: app-insights-api-custom-events-metrics.md
[CoreNuGet]: https://www.nuget.org/packages/Microsoft.ApplicationInsights
 

<!---HONumber=62-->