<properties 
	pageTitle="Monitorando uso e desempenho para aplicativos de área de trabalho do Windows" 
	description="Analise a utilização e o desempenho do aplicativo da área de trabalho do Windows com o HockeyApp e o Application Insights." 
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
	ms.date="04/27/2016" 
	ms.author="awills"/>

# Monitorando uso e desempenho em aplicativos de área de trabalho do Windows

*O Application Insights está em modo de visualização.*

O [Application Insights do Visual Studio](app-insights-get-started.md) e o [HockeyApp](https://hockeyapp.net) permite que você monitore seu aplicativo implantado quanto a uso e desempenho.

> [AZURE.IMPORTANT] Recomendamos o [HockeyApp](https://hockeyapp.net) para distribuir e monitorar aplicativos de área de trabalho e dispositivo. Com o HockeyApp, você pode gerenciar a distribuição, testes ao vivo e comentários do usuário, bem como monitorar relatórios de uso e falhas.

> Embora a telemetria possa ser enviada para o Application Insights de um aplicativo de área de trabalho, isso é principalmente útil para fins experimentais e de depuração.


## Para enviar telemetria ao Application Insights de um aplicativo do Windows

1. No [Portal do Azure](https://portal.azure.com), crie um novo recurso do Application Insights. Para o tipo de aplicativo, escolha o aplicativo ASP.NET.
2. Faça uma cópia da chave de instrumentação. Localize a chave no menu suspenso Essentials do novo recurso que você acabou de criar. Feche o Mapa do Aplicativo ou role para a esquerda até a folha de visão geral do recurso.
3. No Visual Studio, edite os pacotes do NuGet do seu projeto de aplicativo e adicione Microsoft.ApplicationInsights.WindowsServer. (Ou escolha Microsoft.ApplicationInsights se você quiser apenas a API básica, sem os módulos de coleta da telemetria padrão.)
4. Defina a chave de instrumentação no seu código:

    `TelemetryConfiguration.Active.InstrumentationKey = "` *sua chave* `";`

    ou em ApplicationInsights.config (se tiver instalado um dos pacotes de telemetria padrão):
 
    `<InstrumentationKey>`*sua chave*`</InstrumentationKey>`

    Se você usar ApplicationInsights.config, verifique se suas propriedades no Gerenciador de Soluções estão definidas como **Ação de Compilação = Conteúdo, Copiar para Diretório de Saída = Copiar**.
5. [Usar a API](app-insights-api-custom-events-metrics.md) para enviar telemetria.
6. Execute o aplicativo e veja a telemetria no recurso criado no Portal do Azure.

## <a name="telemetry"></a>Código de exemplo

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
            tc.Context.User.Id = Environment.UserName;
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

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```


 

<!---HONumber=AcomDC_0518_2016-->