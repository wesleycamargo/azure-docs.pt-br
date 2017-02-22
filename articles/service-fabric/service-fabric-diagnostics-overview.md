---
title: "Visão geral de diagnóstico e monitoramento do Azure Service Fabric| Microsoft Docs"
description: Saiba como monitorar e diagnosticar aplicativos do Microsoft Azure Service Fabric hospedados no Azure, em desenvolvimento ou locais.
services: service-fabric
documentationcenter: .net
author: ms-toddabel
manager: mfussell
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/3/2017
ms.author: toddabel
translationtype: Human Translation
ms.sourcegitcommit: fdb15c3af4980e284e1a9effe434b1cab959d24c
ms.openlocfilehash: 826ae11e4826b37379caa53d85d8ec834a5ed0fb


---
# <a name="monitor-and-diagnose-azure-service-fabric-applications"></a>Monitorar e diagnosticar aplicativos do Azure Service Fabric

O monitoramento, a detecção, o diagnóstico e a solução de problemas permitem dar continuidade aos serviços com interrupção mínima da experiência do usuário, fornecendo informações de negócios, usando recursos de monitoramento, detectando problemas de desempenho ou falhas de hardware e software e diagnosticando possíveis problemas de comportamento de serviços. Embora o monitoramento e o diagnóstico sejam essenciais em um ambiente de produção implantado, a eficácia dependerá da adoção de um modelo semelhante durante o desenvolvimento de serviços para garantir que eles funcionem ao serem movidos para uma configuração real. O Service Fabric facilita para o desenvolvedor de serviço implementar diagnóstico que possa funcionar perfeitamente tanto em configurações de desenvolvimento local de computador único, quanto em configurações reais de cluster de produção. 

O monitoramento é um termo abrangente que engloba instrumentar o código, coletar os logs de instrumentação, analisar os logs, visualizar informações com base nos dados de log, emitir alertas com base em valores e informações de registro, monitorar a infraestrutura e permitir que os engenheiros detectem e diagnostiquem problemas que afetam seus clientes. Este artigo visa fornecer uma visão geral do monitoramento de clusters do Service Fabric hospedados no Azure ou localmente, implantados em Windows ou Linux usando .NET. Vamos começar dividindo o problema em três partes
- Instrumentação de código ou infraestrutura
- Coleta de eventos gerados
- Armazenamento, agregação, visualização e análise

Embora haja alguns produtos que abranjam os três aspectos, muitos clientes escolhem tecnologias diferentes para cada área. É importante que todas se juntem para oferecer uma solução de monitoramento de ponta a ponta para o aplicativo. 

## <a name="monitoring-infrastructure"></a>Monitoramento de Infraestrutura

Embora o Service Fabric ajude a manter um aplicativo em execução durante falhas de infraestrutura, os operadores do aplicativo precisam compreender se o erro está ocorrendo dentro do aplicativo ou na infraestrutura subjacente. O monitoramento da infraestrutura também é necessário para que o planejamento de capacidade saiba quando adicionar ou remover infraestruturas. É importante que a infraestrutura e o aplicativo que compõem uma implantação do Service Fabric passem por monitoramentos e soluções de problemas. Na medida em que o aplicativo está disponível para os clientes, a infraestrutura pode passar por problemas.

### <a name="azure-monitoring"></a>Monitoramento do Azure

Para clusters implantados no Azure, o [Monitoramento do Azure](../monitoring-and-diagnostics/toc.md) fornece a capacidade de monitorar muitos dos recursos do Azure em que um cluster do Service Fabric é criado. Um conjunto de métricas é automaticamente coletado e exibido no portal do Azure para o [conjunto de dimensionamento de máquinas virtuais (VMSS)](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) e [VMs individuais](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines). Essas informações podem ser visualizadas no portal do Azure, selecionando o grupo de recursos em que o cluster do Service Fabric está contido e, depois, selecionando o VMSS que deseja ver. Em seguida, selecione Métricas dentro da seção de navegação Monitoramento para exibir um gráfico dos valores

![Exibição de informações de métrica coletadas no portal do Azure](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Os gráficos podem ser personalizados, seguindo as instruções do artigo [Visão geral de métricas no Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). Os alertas também podem ser criados com base nessas métricas, conforme descrito no artigo [Usar o portal do Azure para criar alertas para os serviços do Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Os alertas podem ser enviados para um serviço de notificação usando webhooks, conforme descrito no artigo [Configurar um webhook em um alerta de métrica do Azure (.../monitoring-and-diagnostics/insights-webhooks-Alerts.md). O Monitoramento do Azure dá suporte a uma única assinatura. Se o suporte para várias assinaturas ou recursos adicionais forem necessários, o [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) do [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) fornecerá uma solução abrangente de gerenciamento de TI para infraestruturas baseadas em nuvem e locais. Os dados do Monitoramento do Azure podem ser roteados diretamente para o Log Analytics para que você possa ver as métricas e logs de todo o ambiente em um único lugar.

O [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) é o método recomendado para monitorar sua infraestrutura local, mas qualquer outra solução existente que sua empresa use para monitoramento da infraestrutura pode ser usada.

### <a name="service-fabric-support-logs"></a>Logs de suporte do Service Fabric

Se houver necessidade de entrar em contato com o suporte da Microsoft para obter ajuda com o Cluster do Azure Service Fabric, os logs de suporte serão quase sempre necessários. Se o cluster está hospedado no Azure, esses logs são automaticamente configurados e coletados como parte da criação de um cluster. Os logs são armazenados em uma conta de armazenamento dedicada que pode ser vista no grupo de recursos do cluster. Não há nenhum nome fixo para a conta de armazenamento, mas dentro da conta você verá as tabelas e contêineres de blob começando com 'fabric'. No caso de um cluster autônomo, você deve configurar a coleta desses logs seguindo as orientações dos artigos [Criar e gerenciar um cluster autônomo do Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md) e [Configurações de cluster autônomo do Windows](service-fabric-cluster-manifest.md). Para Service Fabric autônomo, os logs devem ser enviados para um compartilhamento de arquivo local. Você **deve** ter esses logs em mãos para o suporte, mas eles não serão utilizados por qualquer pessoa que não faça parte da equipe de suporte ao cliente da Microsoft. De acordo com os meus conhecimentos de Obi-Wan Kenobi, estes não são os logs que você está procurando...

## <a name="instrument-your-code"></a>Instrumentalize seu código

Instrumentar o código é a base para a maioria dos outros aspectos do monitoramento de seus serviços. As pessoas costumam ficar surpresas quanto à necessidade de instrumentação, mas considerando que a instrumentação é a única maneira de ter certeza de que algo está errado e de diagnosticar o que deve ser corrigido, não deveria ser surpresa alguma. Embora seja tecnicamente possível fazer isso, é incomum conectar um depurador a um serviço de produção. Por isso, é importante ter dados de instrumentação detalhados. Gerar este volume de informações enviando todos os eventos do nó local pode ser caro. Muitos serviços usam uma estratégia de duas partes para lidar com o volume de dados de instrumentação:
* Todos os eventos são mantidos no arquivo de log local sem interrupção por um curto intervalo de dias e são coletados apenas quando necessário para depuração. Normalmente, os eventos necessários para um diagnóstico detalhado são deixados no nó para reduzir os custos e a utilização de recursos
* Todos os eventos que indicam a integridade do serviço, como eventos de pulsação ou de desempenho, são enviados para um repositório central no qual podem ser usados para gerar alertas de um serviço não íntegro.

Há soluções que automaticamente instrumentam seu código. Embora esses produtos possam funcionar bem, a instrumentação manual é quase sempre necessária. No fim das contas, você deve ter informações suficientes para depurar legalmente o aplicativo. As próximas seções descrevem as diferentes abordagens para instrumentar seu código e você deve escolher apenas uma.

### <a name="eventsource"></a>EventSource

Quando você cria uma solução do Azure Service Fabric de um modelo do Visual Studio, uma classe derivada de EventSource (*ServiceEventSource* ou *ActorEventSource*) é gerada. Isso fornece um modelo ao qual você pode adicionar eventos adicionais apropriados para seu aplicativo ou serviço. O nome EventSource **deve** ser exclusivo e deve ser renomeado na cadeia de caracteres inicial de 'MyCompany -&lt;solução&gt;-&lt;projeto&gt;'. Ter várias definições de EventSource usando o mesmo nome causa um problema no tempo de execução. Cada evento definido deve ter um identificador exclusivo. Se um identificador não for exclusivo, ocorrerá uma falha de tempo de execução. Geralmente, os intervalos de valores são predefinidos aos identificadores para evitar conflitos entre equipes de desenvolvimento separadas. Para obter mais informações sobre o EventSource, confira o [blog da Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) ou a [documentação de MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

#### <a name="using-structured-eventsource-events"></a>Usando eventos estruturados de EventSource

Cada um dos seguintes eventos é definido para um caso específico, por exemplo, um tipo de serviço que foi registrado. Ter mensagens definidas dessa maneira permite que os dados sejam empacotados juntamente com o texto do erro. Isso permite melhor pesquisa e filtragem com base em nomes ou valores das propriedades especificadas. Estruturar a saída de instrumentação torna o consumo mais fácil, mas exige mais ideias e mais tempo para definir um novo evento para cada caso de uso. Algumas definições de eventos podem ser compartilhadas pelo aplicativo inteiro, por exemplo, um método para iniciar ou interromper um evento seria reutilizado em muitos serviços dentro de um aplicativo. Um serviço de domínio específico, como um sistema de pedidos, pode ter um evento CriarPedido, que terá seu próprio evento exclusivo. Geralmente, essa abordagem gera muitos eventos e potencialmente requer a coordenação de identificadores entre as equipes de projeto. Para um exemplo completo de estrutura de EventSources no Service Fabric, veja PartyCluster.ApplicationDeployService na amostra Party Cluster.

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // Instance constructor is private to enforce singleton semantics
        private ServiceEventSource() : base() { }

        ...

        // ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event and the code implementation of the event.
        private const int ServiceTypeRegisteredEventId = 3;
        [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
        public void ServiceTypeRegistered(int hostProcessId, string serviceType)
        {
            WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
        }

        // ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event and the code implementation of the event.
        private const int ServiceHostInitializationFailedEventId = 4;
        [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
        public void ServiceHostInitializationFailed(string exception)
        {
            WriteEvent(ServiceHostInitializationFailedEventId, exception);
        }
```
#### <a name="using-eventsource-generically"></a>Usando o EventSource de forma genérica

Como definir eventos específicos pode ser difícil, muitas pessoas definem alguns eventos com um conjunto comum de parâmetros que geralmente emitem suas informações como uma cadeia de caracteres. Grande parte do aspecto estruturado é perdida, tornando mais difícil pesquisar e filtrar os resultados. Alguns eventos, geralmente correspondentes aos níveis de registro de log, são definidos com essa abordagem. O trecho a seguir define uma mensagem de erro e de depuração.
```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // Instance constructor is private to enforce singleton semantics
        private ServiceEventSource() : base() { }

        ...

        private const int DebugEventId = 10;
        [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
        public void Debug(string msg)
        {
            WriteEvent(DebugEventId, msg);
        }

        private const int ErrorEventId = 11;
        [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
        public void Error(string error, string msg)
        {
            WriteEvent(ErrorEventId, error, msg);
        }
```
Usar uma abordagem híbrida de instrumentação estruturada e genérica também pode funcionar bem. Neste caso, a instrumentação estruturada é usada para relatar erros e métricas, enquanto os eventos genéricos podem ser usados para o log detalhado que é consumido pelos engenheiros ao solucionar problemas.

### <a name="aspnet-core-logging"></a>Registro em log de ASP.NET Core

Escolher como instrumentar seu código pode ser difícil. Se você escolheu a maneira errada e precisar refazer a instrumentação, você vai revisitar e possivelmente desestabilizar sua base de código. Para reduzir o risco, os desenvolvedores podem escolher uma biblioteca de instrumentação, como a [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), fornecida pelo ASP.NET Core. Isso fornece uma interface [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) que permite que o provedor de sua escolha seja usado enquanto minimiza o impacto causado ao código existente. Outro aspecto interessante disso é que o código pode ser usado não apenas no .NET Core no Windows e Linux, mas também em toda a estrutura, oferecendo a capacidade de padronizar o código de instrumentação em .NET e .NET Core.

#### <a name="how-to-use-microsoftextensionslogging-within-service-fabric"></a>Como usar Microsoft.Extensions.Logging dentro do Service Fabric

1. Adicione o pacote NuGet **Microsoft.Extensions.Logging** ao projeto a ser instrumentado. Você também pode adicionar quaisquer pacotes de provedor; faremos isso com um pacote de terceiros abaixo. Consulte [Entrar no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) para obter mais informações
2. Adicionar uma diretiva de 'Microsoft.Extensions.Logging' ao seu arquivo de serviço
3. Definir uma variável privada em sua classe de serviço
```csharp
        private ILogger _logger = null;
```
4. No construtor da sua classe de serviço, adicionar
```csharp
        _logger = new LoggerFactory().CreateLogger<Stateless>();
```
5. Iniciar a instrumentação do código em seus métodos. Veja alguns exemplos
```csharp
        _logger.LogDebug("Debug level event from Microsoft.Logging");
        _logger.LogInformation("Informational level event from Microsoft.Logging");

        // In this variant, we're adding structured properties RequestName and Duration that has values MyRequest and the duration of the request.
        // More on why you'll want to do this later.
        _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
```

#### <a name="using-other-logging-providers"></a>Usando outros provedores de registro log

Há alguns provedores de terceiros que funcionam com essa abordagem, por exemplo, [SeriLog](https://serilog.net/), [NLog](http://nlog-project.org/) e [loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Cada um deles pode ser conectado ao registro de log de ASP.Net Core e também pode ser usado separadamente. O SeriLog tem um recurso que permite o enriquecimento de todas as mensagens enviadas de um agente, que pode ser útil para emitir o nome do serviço, tipo e informações de partição. Para usar essa funcionalidade com a infraestrutura do ASP.NET Core, faça o seguinte

1. Adicione os pacotes NuGet **Serilog**, **Serilog.Extensions.Logging** e **Serilog.Sinks.Observable** ao projeto. Também adicione o **SeriLog.Sinks.Literate** para este exemplo; uma abordagem melhor será mostrada posteriormente neste artigo
2. Crie um LoggerConfiguration e a instância do agente no SeriLog
```csharp
    Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
```
3. Adicione um argumento SeriLog.ILogger ao construtor do serviço e passe o agente recém-criado
```csharp
    ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
```
4. No construtor de serviço, adicione o seguinte, que cria os enriquecedores de propriedade para as propriedades ServiceTypeName, ServiceName, PartitionId e InstanceId do serviço. Isso também adiciona à fábrica de registro de log do ASP.NET Core para que o Microsoft.Extensions.Logging.ILogger possa ser usado em seu código.
```csharp
        public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
            : base(context)
        {
            PropertyEnricher[] properties = new PropertyEnricher[]
            {
                new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
                new PropertyEnricher("ServiceName", context.ServiceName),
                new PropertyEnricher("PartitionId", context.PartitionId),
                new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
            };

            serilog.ForContext(properties);

            _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
        }
```
5. Instrumente o código da mesma forma usada com o ASP.NET Core sem SeriLog.

> [!NOTE] 
> Não é recomendável que o Log.Logger estático seja usado com essa abordagem, porque o Service Fabric pode hospedar várias instâncias do mesmo tipo de serviço em um único processo. Isso significa que o último gravador de enriquecedores de propriedade poderia ter seus valores mostrados para todas as instâncias em execução. É por isso que a variável _logger é uma variável de membro particular da classe de serviço. Isso também significa que o _logger deve ser disponibilizado para códigos comuns que possam ser usado entre serviços. 

### <a name="which-one-should-i-use"></a>Qual deles devo usar?

Se o desempenho for importante para o seu aplicativo, o EventSource será a melhor abordagem a ser usada, porque ele **geralmente** usa menos recursos e tem desempenho melhor do que o registro de log do ASP.NET Core ou quaisquer soluções de terceiros.  Isso não é um problema para muitos serviços, mas se o seu serviço for orientado ao desempenho, usar o EventSource poderá ser uma opção melhor. Para obter os mesmos benefícios do registro de log estruturado, o EventSource requer um grande investimento da equipe de engenharia. A melhor maneira de determinar o que escolher para o seu projeto é fazer um protótipo rápido do que você poderia fazer para cada abordagem e, em seguida, escolher o método que melhor atenda às suas necessidades.

## <a name="event-and-log-collection"></a>Coleta de logs e eventos

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

Além dos recursos que o Monitoramento do Azure já oferece, o Azure também fornece um meio de coletar eventos de cada um dos serviços em um local central. Há dois artigos que mostram como configurar a coleta de eventos para [Windows](service-fabric-diagnostics-how-to-setup-wad.md) e [Linux](service-fabric-diagnostics-how-to-setup-lad.md). Esses artigos mostram como realizar a coleta de dados de eventos e enviá-la ao Armazenamento do Azure. Isso pode ser feito de forma simples no portal ou no modelo do Resource Manager habilitando o diagnóstico. Ativar essa opção coleta algumas fontes de evento que o Service Fabric gera automaticamente:

- Contadores de desempenho e eventos do EventSource ao usar o modelo de programação de Reliable Actor. Os eventos são enumerados no artigo sobre [Monitoramento de desempenho e diagnóstico para Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
- Eventos do EventSource ao usar o modelo de programação de Reliable Services. Os eventos são enumerados no artigo sobre [Funcionalidade de diagnóstico para Reliable Services com estado](service-fabric-reliable-services-diagnostics.md)
- Os eventos do sistema são emitidos como eventos de ETW. Há muitos eventos que são emitidos pelo Service Fabric como parte desta categoria, incluindo posicionamento do serviço, eventos de início/parada. A melhor maneira de ver os eventos emitidos é usar o [Visualizador de diagnóstico do Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) quando em execução no computador local. Como esses eventos são eventos de ETW nativos, existem algumas limitações para como eles podem ser coletados
- A partir da versão 5.4 do Service Fabric, eventos de métrica de carga e integridade estão expostos. Isso permite que a coleta desses eventos seja usada para o histórico de relatórios e alertas. Esses eventos também são eventos de ETW nativos com algumas limitações para como eles podem ser coletados

Quando configurados, esses eventos são exibidos em uma das contas de armazenamento do Azure criadas quando o cluster foi criado, supondo que o diagnóstico foi habilitado. Os nomes das tabelas são *WADServiceFabricReliableActorEventTable*, *WADServiceFabricReliableServiceEventTable*, e *WADServiceFabricSystemEventTable*. Os eventos de integridade não são adicionados por padrão e exigem que o modelo do Resource Manager seja modificado. Consulte [Coletar logs usando o diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-wad.md) para obter mais detalhes.

Esses artigos também mostram como obter eventos personalizados no armazenamento do Azure. Qualquer dos artigos do diagnóstico do Azure sobre como configurar contadores de desempenho ou outras informações de monitoramento de uma VM para o diagnóstico do Azure também funciona com um cluster do Service Fabric. Por exemplo, se o armazenamento de tabelas do Azure como destino não é o que você deseja, há um artigo sobre como [Transmitir dados de diagnóstico do Azure no caminho de acesso por meio de Hubs de evento](../event-hubs/event-hubs-streaming-azure-diags-data.md). Depois que os eventos estiverem no Hub de eventos, eles podem ser lidos e enviados para o local de sua escolha. Também há um artigo sobre a integração de [Informações de diagnóstico do Azure com o Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)

Uma das desvantagens do uso do diagnóstico do Azure é que a configuração é feita usando um modelo do Resource Manager e, portanto, ocorre apenas no nível do VMSS. Um VMSS corresponde a um tipo de nó no Service Fabric. Isso significa que você deve configurar cada tipo de nó para todos os aplicativos e serviços que podem ser executados em um nó desse tipo. Isso pode ser um grande número de EventSources, dependendo do número de aplicativos e serviços configurados. Além disso, uma implantação do Resource Manager deve ser feita sempre que a configuração de qualquer aplicativo for alterada. O ideal é que a configuração de monitoramento viaje juntamente com a configuração de serviço.

O diagnóstico do Azure funciona somente para clusters do Service Fabric implantados no Azure, mas funciona para clusters do Windows e Linux.

### <a name="eventflow"></a>EventFlow

[O EventFlow foi liberado pela equipe do Visual Studio](service-fabric-diagnostic-collect-logs-without-an-agent.md) e fornece um mecanismo para eventos de roteamento de um nó a um ou mais destinos de monitoramento. Como ele está incluído como um pacote NuGet em seu projeto de serviço, o código e a configuração do EventFlow viajam com o serviço, eliminando o problema de configuração por nó mencionado sobre o diagnóstico do Azure. O EventFlow é executado em seu processo de serviço e conecta-se diretamente com as saídas configuradas. Devido a essa conexão direta, o EventFlow funciona para implantações de um serviço no Azure, em contêineres ou locais. Tenha cuidado ao executar em cenários de alta densidade, como um contêiner, porque cada pipeline do EventFlow faz uma conexão externa e, se você estiver hospedando vários processos, terá muitas conexões de saída. Não é um problema para aplicativos do Service Fabric, porque todas as réplicas de ServiceType são executadas dentro do mesmo processo, limitando o número de conexões de saída. O EventFlow também oferece a filtragem de eventos, portanto, somente os eventos que correspondem ao filtro especificado são enviadas. Para obter informações detalhadas sobre como usar o EventFlow com o Service Fabric, consulte [Coletar logs diretamente de um processo de serviço do Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md)

> [!NOTE]
> Em uma versão futura do Service Fabric, um aplicativo host do EventSource será disponibilizado, permitindo a escuta de entradas com base em ETW, a coleta de métricas a nível de nó e suporte a arquivos de log sem interrupção.

É muito fácil usar o EventFlow
1. Adicione o pacote NuGet ao seu projeto de serviço
2. Dentro da função **principal** do serviço, crie o pipeline do EventFlow e configure as saídas. Nesse caso, estamos mostrando como usar o SeriLog como uma saída
```csharp
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                using (var pipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MonitoringE2E-Stateless-Pipeline"))
                {
                    IObserver<LogEvent> serilogInput = pipeline.Inputs.OfType<SerilogInput>().First();
                    Log.Logger = new LoggerConfiguration().WriteTo.Observers(events => events.Subscribe(serilogInput)).CreateLogger();

                    // The ServiceManifest.XML file defines one or more service type names.
                    // Registering a service maps a service type name to a .NET type.
                    // When Service Fabric creates an instance of this service type,
                    // an instance of the class is created in this host process.

                    ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless).Name);
                    
                    // Prevents this host process from terminating so services keep running.
                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
```
3. Criar um arquivo no PackageRoot do serviço | Pasta de configuração chamada *eventFlowConfig.json*. Dentro do arquivo, a configuração é semelhante ao seguinte
```json
    {
    "inputs": [
        {
        "type": "EventSource",
        "sources": [
            { "providerName": "Microsoft-ServiceFabric-Services" },
            { "providerName": "Microsoft-ServiceFabric-Actors" },
            { "providerName": "MyCompany-MonitoringE2E-Stateless" }
        ]
        },
        {
        "type": "Serilog"
        }
    ],
    "filters": [
        {
        "type": "drop",
        "include": "Level == Verbose"
        },
        {
        "type": "metadata",
        "metadata": "request",
        "requestNameProperty": "RequestName",
        "include":  "RequestName==MyRequest",
        "durationProperty": "Duration",
        "durationUnit": "milliseconds"
        }
    ],
    "outputs": [
        {
        "type": "StdOutput"
        },
        {
        "type": "ApplicationInsights",
        "instrumentationKey": "== instrumentation key here =="
        }
    ],
    "schemaVersion": "2016-08-11",
    "extensions": []
    }
```
Existem duas entradas definidas na configuração, as duas fontes com base em EventSource criadas pelo Service Fabric e o EventSource para o serviço. Observe que os eventos de integridade e nível do sistema que usam o ETW não estão disponíveis para o EventFlow. Isso ocorre porque um privilégio de alto nível é necessário para escutar uma fonte de ETW e os serviços nunca devem ser executados com privilégios altos. A outra entrada é SeriLog, a configuração ocorrida no método **principal**.  Há também alguns filtros aplicados, o primeiro diz ao EventFlow para remover todos os eventos que tenham um nível detalhado de evento. Veremos a outra definição de filtro em breve. Há também duas saídas configuradas. Uma delas é a saída padrão, que gravará na janela de saída do Visual Studio. A outra saída é ApplicationInsights, certifique-se de adicionar a chave de instrumentação.

4. A última etapa é instrumentar o código. Neste exemplo, vamos instrumentar o RunAsync de algumas maneiras diferentes para fins de exemplo. No código a seguir, ainda estamos usando o SeriLog e parte da sintaxe usada é específica do SeriLog. Lembre-se dos recursos específicos da solução de registro de log que você escolher. Há três eventos gerados, um evento a nível de depuração e dois informativos, com o segundo controlando a duração da solicitação. Com a configuração do EventFlow acima, o evento a nível de depuração não deve fluir para a saída.

```csharp
    Stopwatch sw = Stopwatch.StartNew();

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        sw.Restart();

        // Delay a random interval to provide a more interesting request duration.
        await Task.Delay(TimeSpan.FromMilliseconds(DateTime.Now.Millisecond), cancellationToken);

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);
        _logger.LogDebug("Debug level event from Microsoft.Logging");
        _logger.LogInformation("Informational level event from Microsoft.Logging");
        _logger.LogInformation("{RequestName} {Duration}", "MyRequest", sw.ElapsedMilliseconds);
    }
```

Para exibir os eventos no Application Insights, abra o portal do Azure e navegue até o recurso ApplicationInsights. Em seguida, clique em "Pesquisar" na parte superior esquerda e os eventos serão exibidos.

![Exibição de eventos da Pesquisa do Application Insights](./media/service-fabric-diagnostics-overview/ai-search-events.png)

Os rastreamentos estão na parte inferior da figura. Você pode ver que temos apenas dois eventos, e o evento a nível de depuração foi descartado pelo EventFlow. E o que é a entrada de solicitação acima do rastreamento? É a terceira linha de instrumentação de _logger que mostra que o evento foi convertido em uma métrica de solicitação no Application Insights. Vamos voltar para a definição do filtro, com o tipo 'metadados'. Isso quer dizer que o evento tem uma propriedade de 'RequestName' com o valor 'MyRequest' e que outra propriedade, 'Duração', contém a duração da solicitação em milissegundos. Isso é o que você vê no evento de solicitação no Application Insights. A mesma abordagem funciona com qualquer uma das entradas do EventFlow com suporte, incluindo o EventSource.

Se o cluster for autônomo e não puder ser conectado a uma solução baseada em nuvem por motivos de política, o EventFlow dará suporte à pesquisa elástica como uma saída, mas outras saídas poderão ser gravadas e as solicitações pull serão recomendadas. Alguns dos provedores de terceiros para o registro de log de ASP.NET Core também têm soluções com suporte para instalações locais.

## <a name="azure-service-fabric-health-and-load-reporting"></a>Relatórios de carga e integridade do Azure Service Fabric

O Service Fabric tem seu próprio modelo de integridade que é descrito em detalhes em alguns artigos
- [Introdução ao monitoramento de integridade do Service Fabric](service-fabric-health-introduction.md)
- [Relatar e verificar a integridade de serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Adicionar relatórios de integridade personalizados do Service Fabric](service-fabric-report-health.md)
- [Como exibir relatórios de integridade do Service Fabric](service-fabric-view-entities-aggregated-health.md)

O monitoramento de integridade é fundamental para vários aspectos da operação de um serviço. É importante que quando o Service Fabric estiver executando uma atualização de aplicativo nomeado, e depois que cada domínio de atualização do serviço for atualizado e disponibilizado aos clientes, verificações de integridade sejam realizadas antes de passar para o próximo domínio de atualização. Se não for possível obter uma boa integridade, a implantação será revertida, deixando o aplicativo em um bom estado conhecido novamente. Embora alguns clientes sejam afetados antes que os serviços possam ser revertidos, a maioria dos clientes não enfrentam qualquer tipo de problema. Além disso, a resolução ocorreu relativamente rápido e sem precisar aguardar a ação de um operador humano. Quanto mais verificações de integridade são incorporadas ao código, mais flexível será o serviço aos problemas de implantação.

Outro aspecto da integridade do serviço são os relatórios de métrica do serviço. As métricas são importantes no Service Fabric, porque elas são usadas para equilibrar o uso de recursos e podem ser usadas como um indicador de integridade do sistema. Digamos que seu aplicativo contém muitos serviços e cada instância informa uma métrica de solicitação por segundo (RPS). Se um dos serviços estiver usando mais recursos do que outro serviço, o Service Fabric moverá as instâncias de serviço pelo cluster para tentar manter a utilização de recursos uniforme. O artigo [Gerenciar o consumo e a carga de recursos com métricas no Service Fabric](service-fabric-cluster-resource-manager-metrics.md) fornece uma explicação detalhada de como isso funciona.

As métricas também fornecem informações sobre o desempenho do seu serviço e, ao longo do tempo, pode ser usado para verificar se o serviço está operando dentro dos parâmetros esperados. Por exemplo, se, com base nas tendências, às 9:00 da segunda-feira de manhã a média de RPS for de 1.000, você poderá configurar um relatório de integridade que alertará se o RPS estiver abaixo de 500 ou acima de 1.500. Tudo pode estar ocorrendo perfeitamente bem, mas pode valer a pena dar uma olhada para garantir que seus clientes estão tendo uma ótima experiência. Seu serviço pode definir um conjunto de métricas que podem ser informadas para fins de integridade sem afetar o balanceamento de recursos do cluster, definindo o peso da métrica como zero. É recomendável que você inicie todas as métricas com peso zero e não aumente o peso até ter certeza de que você compreende como isso afeta o balanceamento de recursos do cluster.

> [!TIP]
> Tome cuidado para não ter muitas métricas ponderadas, pois fica difícil de entender por que as instâncias de serviço estão sendo movidas, já que algumas métricas podem ir muito longe!

Os candidatos para relatórios de integridade e métricas são tudo aquilo que pode indicar a integridade e o desempenho do seu aplicativo. Um contador de desempenho de CPU pode dizer o quão utilizado o nó é, mas não diz se um serviço em particular está íntegro ou não, porque vários serviços podem estar em execução em um único nó. Por outro lado, uma métrica, como RPS, itens processados ou solicitação de latência, pode indicar a integridade de um serviço específico.

Para informar a integridade, adicione um código como

```csharp
 if (!result.HasValue)
 {
     HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
     this.Partition.ReportInstanceHealth(healthInformation);
 }
```

Para relatar um métrica, adicione um código semelhante ao seguinte ao seu serviço

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
```

## <a name="watchdogs"></a>Watchdogs

Um watchdog é um serviço separado que pode inspecionar a integridade e a carga entre serviços e informar a integridade de qualquer coisa na hierarquia de modelo de integridade. Isso pode ajudar a evitar erros que não seriam detectados com base no modo de exibição de um único serviço. Os watchdogs também são um bom local de host para códigos que podem realizar ações de correção para condições conhecidas sem qualquer interação humana.

## <a name="visualization-analysis-and-alerting"></a>Visualização, análise e alertas

A última parte do monitoramento é visualizar o fluxo de eventos, informar sobre o desempenho do serviço e alertar ao detectar um problema. Há várias soluções que são usadas para esse aspecto do monitoramento. O Application Insights e o OMS podem ser usados para alertas baseados no fluxo de eventos. O Power BI ou uma solução de terceiros, como [Kibana](https://www.elastic.co/products/kibana) ou [Splunk](https://www.splunk.com/), podem ser usados para visualizar os dados.

## <a name="next-steps"></a>Próximas etapas

* [Como coletar logs com o Diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Coletar logs diretamente de um processo do serviço do Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [Gerenciar o consumo e a carga de recursos com métricas no Service Fabric](service-fabric-cluster-resource-manager-metrics.md)




<!--HONumber=Feb17_HO1-->


