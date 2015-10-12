<properties 
	pageTitle="Como ... no Application Insights" 
	description="Perguntas Frequentes no Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="awills"/>

# Como ... no Application Insights?

## Receber um email quando...

### Enviar emails se meu site ficar inativo

Definir um [teste da Web de disponibilidade](app-insights-monitor-web-app-availability.md).

### Enviar emails de meu site estiver sobrecarregado

Definir um [alerta](app-insights-alerts.md) para **Tempo de resposta do servidor**. Um limite entre 1 e 2 segundos deve funcionar.

![](./media/app-insights-how-do-i/030-server.png)

Seu aplicativo também pode mostrar sinais de sobrecarga retornando códigos de falha. Definir um alerta para **Solicitações com falha**.

Se definir um alerta para **Exceções do servidor**, talvez você precise fazer [algumas configurações adicionais](app-insights-asp-net-exceptions.md) para ver os dados.


### Enviar emails sobre um evento em meu aplicativo

Vamos supor que você gostaria de receber um email quando ocorrer um evento específico. O Application Insights não oferece esse recurso diretamente, mas pode [enviar um alerta quando uma métrica ultrapassar um limite](app-insights-alerts.md).

Alertas podem ser definidos com base em [métricas personalizadas](app-insights-api-custom-events-metrics.md#track-metric), mas não em eventos personalizados. Escreva algum código para aumentar uma métrica quando o evento ocorre:

    telemetry.TrackMetric("Alarm", 10);

ou:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Como os alertas têm dois estados, você precisa enviar um valor baixo quando considerar o alerta concluído:

    telemetry.TrackMetric("Alarm", 0.5);

Crie um gráfico no [Metrics Explorer](app-insights-metric-explorer.md) para ver o alarme:

![](./media/app-insights-how-do-i/010-alarm.png)

Agora defina um alerta para ser disparado quando a métrica ultrapassar um valor médio por um curto período:


![](./media/app-insights-how-do-i/020-threshold.png)

Defina o período de média como o mínimo.

Quando a métrica ficar acima e abaixo do limite, você receberá emails.

Considere o seguinte:

* Um alerta tem dois estados ("alerta" e "íntegro"). O estado é avaliado somente quando uma métrica é recebida.
* Um email é enviado apenas quando o estado é alterado. Por isso você precisa enviar métricas de valor alto e baixo. 
* Para avaliar o alerta, a média é calculada com base dos valores recebidos no período anterior. Isso ocorre sempre que uma métrica é recebida, para que emails podem ser enviados com mais frequência do que o período definido.
* Uma vez que os emails são enviados tanto para "alerta" quanto para "íntegro", convém passar a pensar em seu único como uma condição de dois estados. Por exemplo, em vez de um evento de "trabalho concluído", tenha uma condição de "trabalho em andamento", na qual você recebe emails no início e no final de um trabalho.
 
## Filtro de versão do aplicativo

Quando publicar uma nova versão do seu aplicativo, você desejará ser capaz de separar a telemetria das versões diferentes.

Você pode definir a propriedade de Versão do aplicativo para que possa filtrar resultados de [pesquisa](app-insights-diagnostic-search.md) e do [Metrics Explorer](app-insights-metrics-explorer.md).


![](./media/app-insights-how-do-i/050-filter.png)

Há vários métodos diferentes de definir a propriedade de Versão do aplicativo.

* Definir diretamente:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`

* Defina a quebra automática de linha em um [inicializador de telemetria](app-insights-api-custom-events-metrics.md#telemetry-initializers) para garantir que todas as instâncias de TelemetryClient sejam configuradas de forma consistente.

* [ASP.NET] Definir a versão em `BuildInfo.config`. O módulo da Web selecionará a versão do nó BuildLabel. Inclua esse arquivo no seu projeto e não se esqueça de definir a propriedade Copy Always no Gerenciador de Soluções.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Gerar automaticamente BuildInfo.config no MSBuild. Para fazer isso, adicione algumas linhas ao arquivo .csproj:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
    ```

    Isso gera um arquivo chamado *Nomedoseuprojeto*.BuildInfo.config. O processo de Publicação renomeia o arquivo como BuildInfo.config.

    O rótulo da compilação contém um espaço reservado (AutoGen\_...) quando você cria com o Visual Studio. Mas quando compilado com o MSBuild, ele é preenchido com o número de versão correta.

    Para permitir que o MSBuild gere números de versão, defina a versão como `1.0.*` em AssemblyReference.cs

<!---HONumber=Oct15_HO1-->