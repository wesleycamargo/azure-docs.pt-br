<properties 
	pageTitle="Application Insights para serviços e aplicativos da Área de Trabalho do Windows" 
	description="Analise a utilização e o desempenho do aplicativo da área de trabalho do Windows com o Application Insights." 
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
	ms.date="08/12/2015" 
	ms.author="awills"/>

# Application Insights em serviços e aplicativos da Área de Trabalho do Windows

*O Application Insights está em modo de visualização.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

O Application Insights permite que você monitore seu aplicativo implantado quanto à utilização e ao desempenho.

Suporte para serviços e aplicativos da Área de Trabalho do Windows é fornecido pelo SDK principal do Application Insights. Esse SDK fornece suporte completo à API para todos os dados de telemetria, mas não oferece nenhuma coleta automática de telemetria.


## <a name="add"></a> Criar um recurso do Application Insights


1.  No [Portal do Azure][portal], crie um novo recurso do Application Insights. Para o tipo de aplicativo, escolha aplicativo da Windows Store. 

    ![Clique em Novo, Application Insights](./media/app-insights-windows-desktop/01-new.png)

    (Sua escolha do tipo de aplicativo define o conteúdo da folha Visão Geral e as propriedades disponíveis no [Metrics Explorer][metrics].)

2.  Faça uma cópia da chave de instrumentação.

    ![Clique em Propriedades, selecione a chave e pressione ctrl + C](./media/app-insights-windows-desktop/02-props.png)

## <a name="sdk"></a>Instalar o SDK no seu aplicativo


1. No Visual Studio, edite os pacotes do NuGet do seu projeto de aplicativo de área de trabalho. ![Clique com o botão direito no projeto e selecione Gerenciar Pacotes Nuget](./media/app-insights-windows-desktop/03-nuget.png)

2. Instale o pacote API principal do Application Insights.

    ![Pesquise “Application Insights”](./media/app-insights-windows-desktop/04-core-nuget.png)

3. Defina seu InstrumentationKey no código, por meio em ().

    `TelemetryConfiguration.Active.InstrumentationKey = "your key";`

*Por que não existe um ApplicationInsights.config?*

* O arquivo. config não é instalado pelo pacote de API principal, que só é usado para configurar os coletores de telemetria. Então você escreve seu próprio código para definir a chave de instrumentação e enviar telemetria.

*Poderia eu usar um pacote NuGet diferente?*

* Sim, você poderia usar o pacote de servidor web, que instalaria coletores para contadores de desempenho. Você precisaria [ desabilitar o coletor de solicitação HTTP](app-insights-configuration-with-applicationinsights-config.md). Ele instalaria um arquivo. config, onde você colocaria sua chave de instrumentação.

## <a name="telemetry"></a>Inserir chamadas de telemetria

Crie uma instância `TelemetryClient` e, em seguida, [use-a para enviar telemetria][api].

Use `TelemetryClient.Flush()` para enviar mensagens antes de fechar o aplicativo. O SDK do Core utiliza um buffer na memória. O método flush garantirá que esse buffer seja esvaziado, ajudando a garantir que não haja perda de dados no encerramento do processo. (Isso não é recomendado para outros tipos de aplicativo. Os SDKs da plataforma implementam esse comportamento automaticamente.)

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

Para ver as contagens de usuários e sessões, você pode definir os valores em cada instância `TelemetryClient`. Como alternativa, você pode usar um inicializador de contexto para executar essa adição para todos os clientes:

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
 

<!---HONumber=August15_HO7-->