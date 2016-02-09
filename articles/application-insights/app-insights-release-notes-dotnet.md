<properties 
	pageTitle="Notas de versão do Application Insights para .NET" 
	description="As atualizações mais recentes para o SDK do .NET." 
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
	ms.date="01/12/2016" 
	ms.author="abaranch"/>
 
# Notas de versão do SDK do Application Insights para .NET

O [SDK do Application Insights para .NET](app-insights-asp-net.md) envia telemetria sobre seu aplicativo ao vivo para o [Application Insights](https://azure.microsoft.com/services/application-insights/), no qual você pode analisar seu uso e o desempenho.


#### Como instalar o SDK em seu aplicativo

Consulte [Introdução ao Application Insights para .NET](app-insights-asp-net.md).

#### Como atualizar para o SDK mais recente 

* Após a atualização, será necessário mesclar novamente todas as personalizações feitas em ApplicationInsights.config. Se não tiver certeza se você personalizou, crie um novo projeto, adicione o Application Insights a ele e compare o seu arquivo .config com o novo projeto. Anote as diferenças.
* No Gerenciador de Soluções, clique com o botão direito do mouse no seu projeto e escolha **Gerenciar pacotes NuGet**.
* Defina o filtro para mostrar os pacotes instalados. 
* Selecione **Microsoft.ApplicationInsights.Web** e escolha **Atualizar**. (Isso também vai atualizar todos os pacotes dependentes).
* Compare o ApplicationInsights.config com a cópia antiga. A maioria das alterações que você ocorreu porque removemos alguns módulos e tornamos outros parametrizáveis. Reaplique as personalizações feitas no arquivo antigo.
* Recompile sua solução.

## Versão 2.0.0-beta4

- Os métodos de extensão de UseAdaptiveSampling e UseSampling foram movidos para Microsoft.ApplicationInsights.Extensibility
- O suporte para aplicativos Universal Windows Phone e Store foi removido
- ```DependencyTelemetry``` atualizado ter as novas propriedades ```ResultCode``` e ```Id```. ```ResultCode``` será usado para fornecer o código de resposta HTTP para dependências HTTP e o código de erro para as dependências do SQL. ```Id``` serão usados para correlação entre componentes. 
- Se ```ServerTelemetryChannel``` é inicializada por meio de programação agora é necessário chamar o ```ServerTelemetryChannel.Initialize()``` método. Caso contrário, o armazenamento persistente não será inicializado (o que significa que se a telemetria não pode ser enviada em caso de problemas de conectividade temporários, ela será descartada).
- ```ServerTelemetryChannel``` tem a nova propriedade ```StorageFolder``` que podem ser definidas por meio de código ou por meio da configuração. Se essa propriedade está definida, o ApplicationInsights usa localização fornecida para armazenar telemetria que não foi enviada em caso de problemas de conectividade temporários. Se a propriedade não está definida, ou se a pasta está inacessível, o ApplicationInsights tentará usar as pastas LocalAppData ou Temp como foi feito antes.
- O método de extensão ```TelemetryConfiguration.GetTelemetryProcessorChainBuilder``` é removido. Em seu lugar, esse método usa o método ```TelemetryConfiguration.TelemetryProcessorChainBuilder```.
- A classe ```TelemetryConfiguration``` tem uma nova propriedade ```TelemetryProcessors``` que oferece acesso somente leitura para a coleção ```TelemetryProcessors```.
- ```Use```, ```UseSampling``` e ```UseAdaptiveSampling``` preservam ```TelemetryProcessors``` carregados de configuração.
- Dois processadores de telemetria são fornecidos prontos para uso no arquivo de configuração - processador de telemetria de filtro de agente do usuário e o processador de telemetria do manipulador de solicitações. O comportamento deles pode ser personalizado. Você pode acrescentar uma cadeia de caracteres de agente do usuário que você deseja que sejam filtrados no arquivo AI.config. Por padrão, realizamos a filtragem de sequência de agente do usuário ```AllwaysOn```. O comportamento atual compara cadeias de caracteres no arquivo de configuração à cadeia de caracteres de agente do usuário usando uma comparação que não diferencia, em absoluto, maiúsculas e minúsculas. Você também pode personalizar a lista de manipuladores para os quais você deseja que solicitações sejam filtradas. 
- A versão do nuget Microsoft.ApplicationInsights.Agent.Intercept dependente foi atualizada para 1.2.1. Ela tem correções de bugs de coleção de dependência do SQL.

## Versão 2.0.0-beta3

- [Amostragem adaptável](app-insights-sampling.md) ativada por padrão no canal de telemetria do servidor. 
- Assinatura fixa de ```UseSampling``` para permitir o encadeamento com outras chamadas para ```Use``` dos processadores de telemetria.  
- Propriedade ```Request.ID``` retornada. ```OperationContext``` agora tem uma propriedade ```ParentId``` para correlação de ponta a ponta.
- ```TimestampTelemetryInitializer``` foi removida. O carimbo de hora será adicionado automaticamente por ```TelemetryClient```.
- ```OperationCorrelationTelemetryInitializer``` é adicionado por padrão para ativar a correlação de operações.
- ```OperationCorrelationTelemetryInitializer``` é usado em vez de ```OperationIdTelemetryInitializer```.
- O Agente do Usuário não será coletado por padrão. O inicializador de telemetria do Agente do Usuário foi removido.
- O campo ```DependencyTelemetry.Async``` não será coletado pelo módulo de telemetria do coletor de dependência. 
- As solicitações de diagnóstico e conteúdo estático não serão coletadas pelo módulo de telemetria de solicitação. Use ```HandlersToFilter``` da coleção ```RequestTrackingTelemetryModule``` para filtrar solicitações geradas por manipuladores específicos de http. 
- A telemetria de solicitação gerada automaticamente acessível através do método de extensão HttpContext: System.Web.HttpContextExtension.GetRequestTelemetry  


## Versão 2.0.0-beta2
- Suporte adicionado a ITelemetryProcessor e a capacidade de configurar por meio de código ou configuração. [Habilita a filtragem personalizada no SDK](app-insights-api-filtering-sampling/#filtering)
- Inicializadores de contexto removidos. Em vez disso, use [Inicializadores de Telemetria]( https://azure.microsoft.com/documentation/articles/app-insights-api-filtering-sampling/#filtering).
- Application Insights atualizado para o .NET Framework 4.6. 
- Os nomes de evento personalizado agora podem ter até 512 caracteres.
- Propriedade ```OperationContext.Name``` renomeada para ```RootName```.
- Propriedade ```RequestTelemetry.Id``` removida.
- Propriedades ```Id``` e ```Context.Operation.Id``` de RequestTelemetry não seriam inicializadas durante a criação de uma nova RequestTelemetry.
- ```RequestTelemetry.Name``` não é mais inicializado. Em vez disso, ```RequestTelemetry.Context.Operation.Name``` será usado.
- No monitoramento de solicitação, o código de resposta 401 faz parte do handshake de autenticação normal e resultará em uma solicitação bem-sucedida.
- Corrija o bloqueio de thread da interface do usuário ao inicializar InMemoryChannel (canal padrão) do thread da interface do usuário. Isso corrige problemas de congelamento da interface do usuário com aplicativos do WPF.
 
## Versão 2.0.0-beta1
- O TrackDependency produzirá um JSON válido quando nem todos os campos obrigatórios tiverem sido especificados.
- A propriedade redundante ```RequestTelemetry.ID``` agora é apenas um proxy para ```RequestTelemetry.Operation.Id```.
- Nova interface ```ISupportSampling``` e sua implementação explícita pela maioria dos tipos de item de dados.
- A propriedade ```Count``` em DependencyTelemetry foi marcada como Obsoleta. Em vez disso, use ```SamplingPercentage```.
- Novo ```CloudContext``` introduzido e propriedades ```RoleName``` e ```RoleInstance``` movidas dele para ```DeviceContext```.
- Nova propriedade ```AuthenticatedUserId``` em ```UserContext``` para especificar a identidade do usuário autenticado.
- `Microsoft.ApplicationInsights.Web.AccountIdTelemetryInitializer` adicionado, `Microsoft.ApplicationInsights.Web.AuthenticatedUserIdTelemetryInitializer` que inicializa o contexto do usuário autenticado, conforme definido pelo SDK do Javascript.
- `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor` adicionado, bem como o suporte de amostragem de taxa fixa como sua implementação.
- `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.TelemetryChannelBuilder` adicionado, que permite a criação de um `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` com um conjunto de `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor`.

## Versão 1.2

- Inicializadores de telemetria que não têm dependências em bibliotecas do ASP.NET foram movidos de `Microsoft.ApplicationInsights.Web` para o novo NuGet de dependência `Microsoft.ApplicationInsights.WindowsServer`
- `Microsoft.ApplicationInsights.Web.dll` foi renomeado em `Microsoft.AI.Web.dll`
- `Microsoft.ApplicationInsights.Web.TelemetryChannel` o nuget foi renomeado em `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`. `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` o assembly foi renomeado em `Microsoft.AI.ServerTelemetryChannel.dll`. `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` a classe foi renomeada em `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`.
- Todos os namespaces que fazem parte do SDK da Web foram alterados para excluir a parte `Extensibility`. Isso inclui todos os inicializadores de telemetria em ApplicationInsights.config e o módulo `ApplicationInsightsWebTracking` no web.config.
- As dependências são coletadas usando o agente de instrumentação do tempo de execução (habilitado por meio da extensão do Monitor de Status ou do Site do Azure) não serão marcadas como assíncronas se não houver nenhum HttpContext.Current no thread.
- A propriedade `SamplingRatio` de `DependencyTrackingTelemetryModule` não faz nada e foi marcada como obsoleta.
- O assembly `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector` foi renomeado em `Microsoft.AI.PerfCounterCollector`
- Várias correções de bugs secundários na Web e SDKs de Dispositivos


## Versão 1.1

- O novo tipo de telemetria `DependencyTelemetry` foi adicionado, que pode ser usado para enviar informações sobre chamadas de dependência do aplicativo (como chamadas SQL, HTTP, etc.).
- O novo método de sobrecarga `TelemetryClient.TrackDependency` foi adicionado, que permite enviar informações sobre chamadas de dependência.
- NullReferenceException fixo lançado pelo módulo de diagnóstico quando TelemetryConfiguration.CreateDefault é usado.

## Versão 1.0

- Módulos e inicializadores de telemetria movidos dos subnamespaces separados para o namespace `Microsoft.ApplicationInsights.Extensibility.Web` raiz.
- O prefixo "Web" foi removido dos nomes dos inicializadores e módulos de telemetria porque ele já está incluído no nome do namespace `Microsoft.ApplicationInsights.Extensibility.Web`.
- `DeviceContextInitializer` movido do assembly `Microsoft.ApplicationInsights` para o assembly `Microsoft.ApplicationInsights.Extensibility.Web` e convertido em um `ITelemetryInitializer`.
- Altere os nomes do namespace e do assembly de `Microsoft.ApplicationInsights.Extensibility.RuntimeTelemetry` para `Microsoft.ApplicationInsights.Extensibility.DependencyCollector`, a fim de manter a consistência com o nome do pacote NuGet.
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

 

<!---HONumber=AcomDC_0128_2016--->