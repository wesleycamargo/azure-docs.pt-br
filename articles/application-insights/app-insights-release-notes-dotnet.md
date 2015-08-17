<properties 
	pageTitle="Notas de versão do Application Insights" 
	description="As últimas atualizações." 
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
	ms.date="06/18/2015" 
	ms.author="sergkanz"/>
 
# Notas de versão do SDK do Application Insights para .NET

O [SDK do Application Insights para .NET](app-insights-start-monitoring-app-health-usage.md) envia telemetria sobre seu aplicativo ao vivo para o [Application Insights](http://azure.microsoft.com/services/application-insights/), no qual você pode analisar seu uso e o desempenho.


#### Como instalar o SDK em seu aplicativo

Consulte [Introdução ao Application Insights para .NET](app-insights-start-monitoring-app-health-usage.md).

#### Como atualizar para o SDK mais recente 

* Após a atualização, será necessário mesclar novamente todas as personalizações feitas em ApplicationInsights.config. Se não tiver certeza se você personalizou, crie um novo projeto, adicione o Application Insights a ele e compare o seu arquivo .config com o novo projeto. Anote as diferenças.
* No Gerenciador de Soluções, clique com o botão direito do mouse no seu projeto e escolha **Gerenciar pacotes NuGet**.
* Defina o filtro para mostrar os pacotes instalados. 
* Selecione **Microsoft.ApplicationInsights.Web** e escolha **Atualizar**. (Isso também vai atualizar todos os pacotes dependentes).
* Compare o ApplicationInsights.config com a cópia antiga. A maioria das alterações que você ocorreu porque removemos alguns módulos e tornamos outros parametrizáveis. Reaplique as personalizações feitas no arquivo antigo.
* Recompile sua solução.

## Versão 1.2

- Inicializadores de telemetria que não têm dependências em bibliotecas do ASP.NET foram movidos de `Microsoft.ApplicationInsights.Web` para o novo nuget de dependência `Microsoft.ApplicationInsights.WindowsServer`
- `Microsoft.ApplicationInsights.Web.dll` foi renomeado para `Microsoft.AI.Web.dll`.
- O NuGet `Microsoft.Web.TelemetryChannel` foi renomeado em `Microsoft.WindowsServer.TelemetryChannel`. O assembly `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` foi renomeado em `Microsoft.AI.ServerTelemetryChannel.dll`. A classe `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` foi renomeada em `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`.
- Todos os namespaces que fazem parte do Web SDK foram alterados para excluir a parte `Extensibility`. Isso inclui todos os inicializadores de telemetria em Applicationinsights. config e o módulo `ApplicationInsightsWebTracking` no web.config.
- As dependências são coletadas usando o agente de instrumentação do tempo de execução (habilitado por meio da extensão do Monitor de Status ou do Site do Azure) não serão marcadas como assíncronas se não houver nenhum HttpContext.Current no thread.
- A Propriedade `SamplingRatio` de `DependencyTrackingTelemetryModule` não faz nada e foi marcado como obsoleto.
- O assembly `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector` foi renomeado em `Microsoft.AI.PerfCounterCollector`
- Várias correções de bugs secundários na Web e SDKs de Dispositivos


## Versão 1.1

- O novo tipo de telemetria `DependencyTelemetry` foi adicionado, que pode ser usado para enviar informações sobre chamadas de dependência do aplicativo (como chamadas SQL, HTTP, etc.).
- O novo método de sobrecarga `TelemetryClient.TrackDependency` foi adicionado, o que permite enviar informações sobre chamadas de dependência.
- NullReferenceException fixo lançado pelo módulo de diagnóstico quando TelemetryConfiguration.CreateDefault é usado.

## Versão 1.0

- Os módulos e inicializadores de telemetria movidos dos subnamespaces separados para o namespace `Microsoft.ApplicationInsights.Extensibility.Web` da raiz.
- O prefixo “Web” foi removido de nomes dos inicializadores e módulos de telemetria porque ele já está incluído no nome do namespace `Microsoft.ApplicationInsights.Extensibility.Web`.
- `DeviceContextInitializer` movido do assembly `Microsoft.ApplicationInsights` para o assembly `Microsoft.ApplicationInsights.Extensibility.Web` e convertido em um `ITelemetryInitializer`.
- Altere os nomes do namespace e do assembly de `Microsoft.ApplicationInsights.Extensibility.RuntimeTelemetry` para `Microsoft.ApplicationInsights.Extensibility.DependencyCollector` para consistência com o nome do pacote NuGet.
- Renomeie `RemoteDependencyModule` para `DependencyTrackingTelemetryModule`.
- Renomeie `CustomPerformanceCounterCollectionRequest` para `PerformanceCounterCollectionRequest`.

## Versão 0.17
- Dependência removida do NuGet EventSource para os aplicativos do framework 4.5.
- Cookies de sessão e do usuário anônimos não serão gerados no lado do servidor. Para implementar o rastreamento de usuário e sessão para aplicativos Web, a instrumentação com o SDK do JS agora é obrigatória — cookies do SDK do JavaScript ainda são respeitados. Os módulos de telemetria ```WebSessionTrackingTelemetryModule``` e ```WebUserTrackingTelemetryModule``` não têm mais suporte e foram removidos do arquivo ApplicationInsights.config. Observe que essa alteração pode causar uma reformulação significativa das contagens de usuário e sessão, pois apenas sessões originadas pelo usuário estão sendo contadas agora.
- O OSVersion não é mais populado pelo SDK por padrão. Quando estiver vazio, o SO e o OSVersion são calculados pelo pipeline do Application Insights, com base no agente de usuário. 
- O canal de persistência otimizado para cenários de carga alta é usado para o SDK web. Corrigido problema "Espiral morte". Espiral da morte é uma condição que ocorre quando o pico da contagem de itens de telemetria excede bastante o limite de limitação no ponto de extremidade, o que levará a uma repetição depois de determinado tempo e será limitado durante a repetição novamente.
- O modo de desenvolvedor é otimizado para produção. Se deixado por engano, ele não ocasionará em uma sobrecarga grande como antes da tentativa de obter informações adicionais de saída.
- O modo de desenvolvedor, por padrão, será habilitado somente quando o aplicativo estiver sob o depurador. Você pode substituí-lo usando a propriedade ```DeveloperMode``` da interface ```ITelemetryChannel```.

## Versão 0.16 

Visualização 2015-04-28

- O SDK agora dá suporte à plataforma de destino DNX para habilitar o monitoramento de aplicativos de [estrutura de Núcleo .NET](http://www.dotnetfoundation.org/NETCore5).
- Instâncias do ```TelemetryClient``` não armazenam mais em cache a Chave de Instrumentação. Agora, se a chave de instrumentação não tiver sido definida no ```TelemetryClient``` explicitamente, a ```InstrumentationKey``` retornará nulo. Ela corrige um problema quando você define a ```TelemetryConfiguration.Active.InstrumentationKey``` depois que alguma telemetria já foi coletada; módulos de telemetria como o coletor de dependências, a coleta de dados de solicitações da Web e o coletor de contadores de desempenho usarão a nova chave de instrumentação.

## Versão 0.15

- A nova propriedade ```Operation.SyntheticSource``` já está disponível no ```TelemetryContext```. Agora, você pode marcar os itens de telemetria como "não é tráfego de um usuário real" e especificar como esse tráfego foi gerado. Por exemplo, definindo essa propriedade, você pode distinguir o tráfego de sua automação de teste do tráfego de teste de carga.
- A lógica de canal foi movida para o NuGet separado chamado Microsoft.ApplicationInsights.PersistenceChannel. O canal padrão agora é chamado de InMemoryChannel
- O novo método ```TelemetryClient.Flush``` permite liberar itens de telemetria do buffer de forma síncrona

## Versão 0.13

Não há notas de versão disponíveis para versões anteriores.

 

<!---HONumber=August15_HO6-->