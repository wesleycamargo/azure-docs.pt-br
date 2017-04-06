---
title: "Visão geral de diagnóstico e monitoramento do Azure Service Fabric| Microsoft Docs"
description: Saiba como monitorar e diagnosticar aplicativos do Microsoft Azure Service Fabric hospedados no Azure, em desenvolvimento ou locais.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: mfussell
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/9/2017
ms.author: dekapur
translationtype: Human Translation
ms.sourcegitcommit: ebbb29ee031fb477ce284f7a0d27c1522317f4f0
ms.openlocfilehash: 46a35fa4ec341561ab234f7ec19fb20658fcb2c4
ms.lasthandoff: 02/27/2017


---
# <a name="monitor-and-diagnose-azure-service-fabric-applications"></a>Monitorar e diagnosticar aplicativos do Azure Service Fabric

O monitoramento e o diagnóstico são essenciais em um ambiente de produção ao vivo. O Azure Service Fabric pode ajudar a você implementar o monitoramento e diagnóstico à medida que desenvolve seu serviço para garantir que o serviço funcione perfeitamente em um único computador, um ambiente de desenvolvimento local e em uma configuração de cluster de produção real.

Monitoramento e diagnóstico pode ajudá-lo à medida que desenvolve seus serviços:
* Minimize as interrupções para seus clientes.
* Forneça ideias de negócios.
* Monitore o uso de recursos.
* Detecte falhas de hardware e de software ou problemas de desempenho.
* Diagnosticar potenciais problemas de serviço.

O monitoramento é um termo abrangente que inclui as seguintes tarefas:
* Instrumentação do código
* Coleta de logs de instrumentação
* Análise de logs
* Visualização de informações com base nos dados de log
* Configurar alertas com base em valores de log e informações
* Monitoramento da infraestrutura
* Detectar e diagnosticar problemas que afetam os clientes

Este artigo fornece uma visão geral do monitoramento de clusters do Service Fabric hospedado no Azure, no local, implantado no Windows ou Linux, ou usando o Microsoft .NET Framework. Vamos examinar três aspectos importantes do monitoramento e diagnóstico:
- Instrumentação do código ou infraestrutura
- Coleta de eventos gerados
- Armazenamento, agregação, visualização e análise

Embora vários produtos estão disponíveis que cobrem todas essas três áreas, muitos clientes escolhem tecnologias diferentes para cada aspecto do monitoramento. É importante que cada parte funcione em conjunto para oferecer uma solução de monitoramento de ponta a ponta para o aplicativo.

## <a name="monitoring-infrastructure"></a>Monitoramento de Infraestrutura

O Service Fabric pode ajudar a manter um aplicativo em execução durante falhas de infra-estrutura, mas você precisa entender se o erro está ocorrendo no aplicativo ou da infra-estrutura subjacente. Você também precisa monitorar a infraestrutura para o planejamento de capacidade, para que você saiba quando adicionar ou remover infraestruturas. É importante monitorar e solucionar problemas da infraestrutura e do aplicativo que compõem uma implantação do Service Fabric. Mesmo se um aplicativo está disponível para os clientes, a infra-estrutura ainda pode apresentar problemas.

### <a name="azure-monitor"></a>Azure Monitor

Você pode usar [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) para monitorar muitos dos recursos do Azure em que um cluster de Service Fabric é criado. Um conjunto de métricas para o [conjunto de dimensionamento de máquinas virtuais](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) e para [máquinas virtuais](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) individuais é automaticamente coletado e exibido no portal do Azure. Para exibir as informações coletadas, no portal do Azure, selecione o grupo de recursos que contém o cluster de Service Fabric. Em seguida, selecione o conjunto de dimensionamento de máquinas virtuais que você deseja exibir. No **monitoramento** seção, selecione **métricas** para exibir um gráfico dos valores.

![Exibição de informações de métrica coletadas no portal do Azure](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.PNG)

Para personalizar os gráficos, siga as instruções em [métricas no Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). Você também pode criar alertas com base nessas métricas, conforme descrito em [criar alertas no Azure Monitor para serviços do Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Você pode enviar alertas para um serviço de notificação usando webhooks, conforme descrito em [Configurar um webhook em um alerta de métrica Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md). O Azure Monitor oferece suporte a apenas uma assinatura. Se você precisar monitorar várias assinaturas, ou se você precisar de recursos adicionais, o [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), parte do Microsoft Operations Management Suite fornece uma solução de gerenciamento de TI holística para local e infraestruturas de nuvem. Você pode rotear dados do Azure Monitor diretamente para o Log Analytics para que você possa ver as métricas e logs de todo o ambiente em um único lugar.

É recomendável usar o Operations Management Suite para monitorar sua infraestrutura local, mas você pode usar qualquer solução existente que sua organização usa para monitoramento da infraestrutura.

### <a name="service-fabric-support-logs"></a>Logs de suporte do Service Fabric

Se houver necessidade de entrar em contato com o suporte da Microsoft para obter ajuda com o cluster do Azure Service Fabric, os logs de suporte serão quase sempre necessários. Se o cluster está hospedado no Azure, os logs de suporte são automaticamente configurados e coletados como parte da criação de um cluster. Os logs são armazenados em uma conta de armazenamento dedicada no grupo de recursos do cluster. A conta de armazenamento não tem um nome fixo, mas na conta, você vê contêineres de blob e tabelas com nomes que começam com *malha*. Para obter informações sobre como configurar coleções de log para um cluster autônomo, consulte [criar e gerenciar um cluster de Service Fabric Azure autônomo](service-fabric-cluster-creation-for-windows-server.md) e [definições de configuração para um cluster do Windows autônoma](service-fabric-cluster-manifest.md). Para instâncias autônomas do Service Fabric, os logs devem ser enviados para um compartilhamento de arquivos local. Você **deve** ter esses logs em mãos para o suporte, mas eles não serão utilizados por qualquer pessoa que não faça parte da equipe de suporte ao cliente da Microsoft.

## <a name="instrument-your-code"></a>Instrumentalize seu código

Instrumentar o código é a base para a maioria dos outros aspectos do monitoramento de seus serviços. A instrumentação é a única maneira que você possa saber que algo está errado e para diagnosticar o que precisa ser corrigido. Embora tecnicamente é possível conectar um depurador a um serviço de produção, ele não é uma prática comum. Portanto, é importante ter dados de instrumentação detalhados. Quando você estiver gerando este volume de informações, o envio de todos os eventos do nó local poderá ser caro. Muitos serviços usam uma estratégia de duas partes para lidar com o volume de dados de instrumentação:
1.  Todos os eventos são mantidos no arquivo de log local sem interrupção por um curto intervalo e são coletados apenas quando necessários para a depuração. Normalmente, os eventos necessários para um diagnóstico detalhado são deixados no nó para reduzir os custos e a utilização de recursos.
2.  Todos os eventos que indicam a integridade do serviço são enviados para um repositório central, onde podem ser usados para gerar alertas de um serviço não íntegro. Os eventos de integridade do serviço incluem eventos de erro, eventos de pulsação e eventos de desempenho.

Alguns produtos instrumentam automaticamente seu código. Embora essas soluções podem funcionar bem, quase sempre a instrumentação manual é necessária. No fim das contas, você deve ter informações suficientes para depurar legalmente o aplicativo. As próximas seções descrevem as diferentes abordagens para instrumentar seu código quando for necessário escolher uma abordagem em vez de outra.

### <a name="eventsource"></a>EventSource

Quando você cria uma solução do Service Fabric de um modelo do Visual Studio, uma classe derivada de **EventSource** (**ServiceEventSource** ou **ActorEventSource**) é gerada. Um modelo é criado, no qual você pode adicionar eventos para seu aplicativo ou serviço. O nome **EventSource** **deve** ser exclusivo e deve ser renomeado na cadeia de caracteres do modelo padrão MyCompany-&lt;solution&gt;-&lt;project&gt;. Ter várias definições de **EventSource** que usam o mesmo nome causa um problema no tempo de execução. Cada evento definido deve ter um identificador exclusivo. Se um identificador não for exclusivo, ocorrerá uma falha de tempo de execução. Algumas organizações atribuem antecipadamente intervalos de valores para evitar conflitos entre equipes de desenvolvimento separadas. Para saber mais, veja [blog do Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) ou a [documentação do MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

#### <a name="using-structured-eventsource-events"></a>Usando eventos estruturados de EventSource

Cada um dos eventos nos exemplos de código nesta seção são definidos para um caso específico, por exemplo, quando um tipo de serviço é registrado. Quando você definir mensagens pelo caso de uso, dados podem ser empacotados com o texto do erro e mais, você pode facilmente procurar e filtrar com base nos nomes ou valores das propriedades especificadas. Estruturar a saída de instrumentação torna o consumo mais fácil, mas exige mais ideias e mais tempo para definir um novo evento para cada caso de uso. Algumas definições de eventos podem ser compartilhadas em todo o aplicativo. Por exemplo, um evento de início ou de parada de método seria reutilizado em vários serviços em um aplicativo. Um serviço de domínio específico, como um sistema de pedidos, pode ter um evento **CreateOrder**, que terá seu próprio evento exclusivo. Essa abordagem pode gerar muitos eventos e potencialmente requer a coordenação de identificadores entre as equipes de projeto. Para um exemplo completo de eventos **EventSource** de estrutura no Service Fabric, veja **PartyCluster.ApplicationDeployService** na amostra Party Cluster.

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The instance constructor is private to enforce singleton semantics.
        private ServiceEventSource() : base() { }

        ...

        // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceTypeRegisteredEventId = 3;
        [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
        public void ServiceTypeRegistered(int hostProcessId, string serviceType)
        {
            WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
        }

        // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceHostInitializationFailedEventId = 4;
        [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
        public void ServiceHostInitializationFailed(string exception)
        {
            WriteEvent(ServiceHostInitializationFailedEventId, exception);
        }
```

#### <a name="using-eventsource-generically"></a>Usando o EventSource de forma genérica

Como definir eventos específicos pode ser difícil, muitas pessoas definem alguns eventos com um conjunto comum de parâmetros que geralmente emitem suas informações como uma cadeia de caracteres. Grande parte do aspecto estruturado é perdida, tornando mais difícil pesquisar e filtrar os resultados. Alguns eventos, geralmente correspondentes aos níveis de registro de log, são definidos com essa abordagem. O trecho a seguir define uma mensagem de erro e de depuração:

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The Instance constructor is private, to enforce singleton semantics.
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

Usar uma mistura de instrumentação genérica e não estruturados também pode funcionar bem. A instrumentação estruturada é usada para relatar erros e métricas. Eventos genéricos podem ser usados para o log detalhado que é consumido pelos engenheiros para solução de problemas.

### <a name="aspnet-core-logging"></a>Registro em log de ASP.NET Core

É importante planejar cuidadosamente como você instrumentará o seu código. O plano de instrumentação certa pode ajudar a evitar potencialmente desestabilizar sua base de código e, em seguida, precisar reinstrument o código. Para reduzir o risco, você pode escolher uma biblioteca de instrumentação como [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), que faz parte do Microsoft ASP.NET Core. Núcleo do ASP.NET tem um [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) interface que você pode usar com o provedor de sua escolha, minimizando o efeito no código existente. Você pode usar o código ASP.NET Core no Windows e Linux e no .NET Framework completo, então o código de instrumentação é padronizado.

#### <a name="using-microsoftextensionslogging-in-service-fabric"></a>Uso de Microsoft.Extensions.Logging no Service Fabric

1. Adicione o pacote NuGet Microsoft.Extensions.Logging ao projeto que você deseja instrumentar. Além disso, adicione os pacotes de provedor (para um pacote de produtos de terceiros, veja o exemplo a seguir). Para saber mais, veja [Entrar no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Adicione uma diretiva **using** para Microsoft.Extensions.Logging ao seu arquivo de serviço.
3. Definir uma variável privada em sua classe de serviço.

  ```csharp
  private ILogger _logger = null;

  ```
4. No construtor da sua classe de serviço, adicione este código.

  ```csharp
  _logger = new LoggerFactory().CreateLogger<Stateless>();

  ```
5. Iniciar a instrumentação do código em seus métodos. Veja alguns exemplos:

  ```csharp
  _logger.LogDebug("Debug-level event from Microsoft.Logging");
  _logger.LogInformation("Informational-level event from Microsoft.Logging");

  // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
  // Later in the article, we discuss why this step is useful.
  _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);

  ```

#### <a name="using-other-logging-providers"></a>Usando outros provedores de registro log

Alguns provedores de terceiros usam a abordagem descrita na seção anterior, incluindo [Serilog](https://serilog.net/), [NLog](http://nlog-project.org/) e [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Você pode conectar cada uma delas no log do ASP.NET Core, ou pode usá-las separadamente. O Serilog tem um recurso que aprimora todas as mensagens enviadas de um agente de log. Esse recurso pode ser útil para saída do nome do serviço, do tipo e das informações de partição. Para usar esse recurso na infra-estrutura do ASP.NET Core, siga estas etapas:

1. Adicione os pacotes NuGet Serilog, Serilog.Extensions.Logging e Serilog.Sinks.Observable ao projeto. No próximo exemplo, adicione também Serilog.Sinks.Literate. Uma abordagem melhor será mostrada posteriormente neste artigo.
2. No Serilog, crie uma LoggerConfiguration e a instância do agente.

  ```csharp
  Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
  ```

3. Adicione um argumento SeriLog.ILogger ao construtor do serviço e passe o agente recém-criado.

  ```csharp
  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
  ```

4. No construtor de serviço, adicione o código a seguir. O código cria os enriquecedores de propriedade para as propriedades **ServiceTypeName**, **ServiceName**, **PartitionId** e **InstanceId** do serviço. Ele também adiciona um aprimorador de propriedade para a fábrica de logs do ASP.NET Core, para que você possa usar Microsoft.Extensions.Logging.ILogger no seu código.

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

5. Instrumente o código da mesma forma com se você estivesse usando o ASP.NET Core sem SeriLog.

  > [!NOTE]
  > É recomendável que você não use o Log.Logger estático com o exemplo anterior. O Service Fabric pode hospedar várias instâncias do mesmo tipo de serviço em um único processo. Se você usar o Log.Logger estático, o último gravador de aprimoradores de propriedade mostrará valores para todas as instâncias em execução. É por isso que a variável _logger é uma variável de membro particular da classe de serviço. Além disso, você deve disponibilizar o _logger para código comum, que pode ser usado entre serviços.

### <a name="choosing-a-logging-provider"></a>Escolher um provedor de log

Se seu aplicativo depende de alto desempenho, **EventSource** é a melhor abordagem a ser usada. **EventSource** *geralmente* usa menos recursos e é melhor do que o ASP.NET Core log ou qualquer das soluções de terceiros disponíveis.  Isso não é um problema para muitos serviços, mas se o seu serviço for orientado ao desempenho, usar o **EventSource** poderá ser uma opção melhor. Para obter os mesmos benefícios do registro de log estruturado, o **EventSource** requer um grande investimento da equipe de engenharia. Para determinar a abordagem a ser usada para o seu projeto, fazer um protótipo rápido do que envolve cada opção e, em seguida, escolha a que melhor atenda às suas necessidades.

## <a name="event-and-log-collection"></a>Coleta de logs e eventos

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

Além das informações fornecidas pelo Azure Monitor, o Azure coleta eventos de cada um dos serviços em um local central. Para saber mais, saiba como configurar a coleta de eventos para [Windows](service-fabric-diagnostics-how-to-setup-wad.md) e [Linux](service-fabric-diagnostics-how-to-setup-lad.md). Esses artigos mostram como você pode coletar dados de eventos e enviá-los para o armazenamento do Azure. Você pode fazer isso no portal do Azure em seu modelo do Azure Resource Manager, permitindo o diagnóstico. O Diagnóstico do Azure coleta algumas fontes de evento que o Service Fabric gera automaticamente:

- Os contadores de desempenho e eventos do **EventSource** quando você usa o modelo de programação de Reliable Actor. Os eventos são enumerados em [Monitoramento de desempenho e diagnóstico para Reliable Actors](service-fabric-reliable-actors-diagnostics.md).
- Os eventos do **EventSource** ao usar o modelo de programação de Reliable Services. Os eventos são enumerados nem [Funcionalidade de diagnóstico para Reliable Services com estado](service-fabric-reliable-services-diagnostics.md).
- Os eventos do sistema são emitidos como eventos de ETW (Rastreamento de Eventos para Windows). Muitos eventos são emitidos pelo Service Fabric como parte desta categoria, incluindo posicionamento do serviço e iniciar/parar eventos. A melhor maneira de ver os eventos que são emitidos é usar o [Visual Studio diagnóstico Visualizador de eventos](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) no computador local. Como esses eventos são eventos de ETW nativos, existem algumas limitações para como eles podem ser coletados.
- A partir da versão 5.4 do Service Fabric, eventos de métrica de carga e integridade estão expostos. Dessa forma, você pode usar coleta de eventos para o histórico de relatórios e alertas. Esses eventos também são eventos de ETW nativos com algumas limitações para como eles podem ser coletados.

Quando configurado, os eventos são exibidos em uma conta de armazenamento do Azure que foi criada quando você criar o cluster, supondo que você habilitou o diagnóstico. Os nomes das tabelas são WADServiceFabricReliableActorEventTable, WADServiceFabricReliableServiceEventTable, e WADServiceFabricSystemEventTable. Eventos de integridade não são adicionados por padrão. Você deve modificar o modelo do Gerenciador de recursos para adicioná-los. Para saber mais, veja [Coletar logs usando o Diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-wad.md).

Esses artigos listados nesta seção também podem mostram como obter eventos personalizados no armazenamento do Azure. Outros artigos de diagnóstico do Azure sobre configurar contadores de desempenho ou artigos com outras informações de monitoramento das máquinas virtuais para diagnóstico do Azure também se aplicam a um cluster de Service Fabric. Por exemplo, se você não quiser usar o armazenamento de tabelas do Azure como um destino, consulte [dados de Streaming de diagnóstico do Azure no caminho de acesso por meio de Hubs de evento](../event-hubs/event-hubs-streaming-azure-diags-data.md). Quando os eventos em Hubs de eventos do Azure, você pode lê-las e enviá-los para o local escolhido. Você pode obter mais informações sobre como integrar o [informações de diagnóstico do Azure com o Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/).

Uma desvantagem de usar o diagnóstico do Azure é que você configurá-lo usando um modelo do Gerenciador de recursos. Então, os diagnósticos só ocorrerão no nível do conjunto de dimensionamento de máquinas virtuais. Um conjunto de dimensionamento de máquinas virtuais corresponde a um tipo de nó na Service Fabric. Você configura cada tipo de nó para todos os aplicativos e serviços que podem ser executados em um nó desse tipo. Isso pode ter muitos **EventSource** eventos, dependendo do número de aplicativos e serviços que você configurar. Você também deve implantar o Resource Manager sempre que alterar uma configuração de aplicativo. O ideal é que a configuração de monitoramento viaje com a configuração de serviço.

Diagnóstico do Azure funciona somente para clusters de Service Fabric implantados no Azure. Ele funciona para clusters do Windows e do Linux.

### <a name="eventflow"></a>EventFlow

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) pode encaminhar eventos de um nó a um ou mais destinos de monitoramentos. Como ele está incluído como um pacote NuGet em seu projeto de serviço, o código e a configuração do EventFlow viajam com o serviço, eliminando o problema de configuração por nó mencionado anteriormente sobre o Diagnóstico do Azure. O EventFlow é executado em seu processo de serviço e conecta-se diretamente às saídas configuradas. Devido a essa conexão direta, o EventFlow funciona para o Azure, o contêiner e as implantações de serviço locais. Tenha cuidado se você executar EventFlow em cenários de alta densidade, como em um contêiner, pois cada pipeline EventFlow faz uma conexão externa. Se você hospedar muitos processos, poderá obter muitas conexões de saída! Isso não é tanto uma preocupação para aplicativos de Service Fabric, porque todas as réplicas de um `ServiceType` executados no mesmo processo, seja isso limita o número de conexões de saída. O EventFlow também oferece a filtragem de eventos e, portanto, somente os eventos que correspondem ao filtro especificado são enviadas. Para obter informações detalhadas sobre como usar o EventFlow com o Service Fabric, consulte [Coletar logs diretamente de um processo de serviço do Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md).

Para usar o EventFlow:

1. Adicione o pacote NuGet ao seu projeto de serviço.
2. Na função **Main** do serviço, crie o pipeline do EventFlow e configure as saídas. No exemplo a seguir, usamos o Serilog como uma saída.

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
                  Log.Logger = new LoggerConfiguration().WriteTo.EventFlow(pipeline).CreateLogger();

                  // The ServiceManifest.xml file defines one or more service type names.
                  // Registering a service maps a service type name to a .NET type.
                  // When Service Fabric creates an instance of this service type,
                  // an instance of the class is created in this host process.

                  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
                  ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless).Name);

                  // Prevents this host process from terminating, so services keep running.
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

3. Criar um arquivo chamado eventFlowConfig.json na pasta \\PackageRoot\\Config do serviço. Dentro do arquivo, a configuração se parece com esta:

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
    Existem duas entradas definidas na configuração, as duas fontes com base em **EventSource** criadas pelo Service Fabric e o **EventSource** para o serviço. Os eventos de integridade e nível do sistema que usam o ETW não estão disponíveis para o EventFlow. Isso ocorre porque um privilégio de alto nível é necessário para escutar uma fonte de ETW e os serviços nunca devem ser executados com privilégios altos. A outra entrada é Serilog. A configuração do Serilog ocorreu no método **Main**.  Alguns filtros são aplicados. O primeiro filtro diz ao EventFlow para descartar todos os eventos com um nível detalhado de evento. Duas saídas são configuradas: saída padrão, que grava na janela de saída no Visual Studio e ApplicationInsights. Adicione a chave de instrumentação.

4. Instrumente o código. No próximo exemplo, podemos instrumentar `RunAsync` algumas maneiras diferentes, para mostrar exemplos. No código a seguir, ainda estamos usando Serilog. Parte da sintaxe que usamos é específico para o Serilog. Observe os recursos específicos da solução de registro de log que você escolher. Três eventos são gerados: um evento de nível de depuração e dois eventos informativos. O segundo evento informativo controla a duração da solicitação. Na configuração do EventFlow descrita anteriormente, o evento no nível de depuração não deve fluir para a saída.

  ```csharp
      Stopwatch sw = Stopwatch.StartNew();

      while (true)
      {
          cancellationToken.ThrowIfCancellationRequested();

          sw.Restart();

          // Delay a random interval, to provide a more interesting request duration.
          await Task.Delay(TimeSpan.FromMilliseconds(DateTime.Now.Millisecond), cancellationToken);

          ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);
          _logger.LogDebug("Debug level event from Microsoft.Logging");
          _logger.LogInformation("Informational level event from Microsoft.Logging");
          _logger.LogInformation("{RequestName} {Duration}", "MyRequest", sw.ElapsedMilliseconds);
      }
  ```

Para exibir os eventos no Application Insights do Azure, abra o portal do Azure e vá até o recurso Application Insights. Para ver os eventos, selecione a caixa **Pesquisa**.

![Exibição de eventos da Pesquisa do Application Insights](./media/service-fabric-diagnostics-overview/ai-search-events.PNG)

Você pode ver os rastreamentos na parte inferior do instantâneo anterior. Ele mostra somente dois eventos e se o evento a nível de depuração foi descartado pelo EventFlow. A entrada de solicitação que precede o rastreamento é o terceiro `_logger` linha de instrumentação. A linha mostra que o evento foi convertido em uma métrica de solicitação no Application Insights.

Na definição do filtro, o tipo é **metadados**. Isso declara que um evento que tem uma propriedade de `RequestName` com o valor `MyRequest`e outra propriedade, `Duration`, contém a duração da solicitação, em milissegundos. Isso é o que você vê no evento de solicitação no Application Insights. A mesma abordagem funciona com qualquer uma das entradas do EventFlow com suporte, incluindo o **EventSource**.

Se você tiver um cluster autônomo que não pode ser conectado a uma solução baseada em nuvem por motivos de política, você pode usar Elasticsearch como uma saída. No entanto, outras saídas podem ser gravadas e as solicitações pull são incentivadas. Alguns dos provedores de terceiros para o registro de log de ASP.NET Core também têm soluções com suporte para instalações locais.

## <a name="azure-service-fabric-health-and-load-reporting"></a>Relatórios de carga e integridade do Azure Service Fabric

O Service Fabric tem seu próprio modelo de integridade que é descrito em detalhes nestes artigos:
- [Introdução ao monitoramento de integridade do Service Fabric](service-fabric-health-introduction.md)
- [Relatar e verificar a integridade de serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Adicionar relatórios de integridade personalizados do Service Fabric](service-fabric-report-health.md)
- [Como exibir relatórios de integridade do Service Fabric](service-fabric-view-entities-aggregated-health.md)

O monitoramento de integridade é fundamental para vários aspectos da operação de um serviço. Monitoramento de integridade é especialmente importante quando o Service Fabric executa uma atualização do aplicativo nomeado. Depois de cada domínio de atualização do serviço é atualizado e está disponível para seus clientes, o domínio de atualização deve passar verificações de integridade antes da implantação passe para o próximo domínio de atualização. Se não for possível obter um status de integridade, a implantação será revertida, deixando o aplicativo em um bom estado conhecido novamente. Embora alguns clientes talvez seja afetados antes que os serviços são revertidos, a maioria dos clientes não enfrentar um problema. Além disso, a resolução ocorreu relativamente rápido e sem precisar aguardar a ação de um operador humano. Quanto mais verificações de integridade são incorporadas ao código, mais flexível será o serviço aos problemas de implantação.

Outro aspecto da integridade do serviço são os relatórios de métrica do serviço. As métricas são importantes no Service Fabric porque eles são usados para equilibrar o uso de recursos. As métricas também podem ser um indicador de integridade do sistema. Por exemplo, você pode ter um aplicativo que tenha muitos serviços, e cada instância informa uma métrica RPS (solicitações por segundo). Se um dos serviços estiver usando mais recursos do que outro serviço, o Service Fabric moverá as instâncias de serviço pelo cluster para tentar manter a utilização de recursos uniforme. Para obter uma explicação mais detalhada de como funciona a utilização de recursos, consulte [gerenciar o consumo de recursos e carga na Service Fabric com métricas](service-fabric-cluster-resource-manager-metrics.md).

Métricas também podem ajudar a fornecer informações sobre o desempenho do seu serviço. Ao longo do tempo, você pode usar métricas para verificar se o serviço está operando dentro dos parâmetros esperados. Por exemplo, se as tendências mostram em 9: 00 na segunda-feira de manhã média RPS é 1.000, você pode definir um relatório de integridade que o alerta se o RPS estiver abaixo de 500 ou acima 1.500. Tudo pode estar ocorrendo perfeitamente bem, mas pode valer a pena dar uma olhada para garantir que seus clientes estão tendo uma ótima experiência. Seu serviço pode definir um conjunto de métricas que podem ser informadas para fins de verificação de integridade sem afetar o balanceamento de recursos do cluster. Para fazer isso, defina o peso da métrica como zero. É recomendável que você inicie todas as métricas com peso zero e não aumente o peso até ter certeza de que você compreende como ponderar a métrica afeta o balanceamento de recursos do cluster.

> [!TIP]
> Não use um número excessivo de métricas ponderadas. Pode ser difícil entender por que instâncias de serviço estão sendo movidas para balanceamento. Algumas métricas podem ir muito longe!

Todas as informações que possam indicar a integridade e o desempenho do seu aplicativo são candidatas aos relatórios de métricas e de integridade. Um contador de desempenho de CPU pode dizer como seu nó é utilizado, mas não diz se um serviço em particular está íntegro ou não, porque vários serviços podem estar em execução em um único nó. Mas, métricas como RPS, itens processados, e todos os de latência de solicitação pode indicar a integridade de um serviço específico.

Para relatar a integridade, use um código semelhante a este:

  ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
  ```

Para relatar uma métrica, use um código semelhante a este:

  ```csharp
    this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
  ```

## <a name="watchdogs"></a>Watchdogs

Um watchdog é um serviço separado que pode inspecionar a integridade e a carga entre serviços e informar a integridade de qualquer coisa na hierarquia de modelo de integridade. Isso pode ajudar a evitar erros que não seriam detectados com base no modo de exibição de um único serviço. Os watchdogs também são um bom local para hospedar os códigos que podem realizar ações de correção para condições conhecidas sem qualquer interação com o usuário.

## <a name="visualization-analysis-and-alerts"></a>Visualização, análise e alertas

A parte final do monitoramento é visualizar o fluxo de eventos, informar sobre o desempenho do serviço e alertar ao detectar um problema. Você pode usar diferentes soluções para esse aspecto do monitoramento. Você pode usar o Operations Management Suite e o Azure Application Insights para alerta com base no fluxo de eventos. Você pode usar o Microsoft Power BI ou uma solução de terceiros, como [Kibana](https://www.elastic.co/products/kibana) ou [Splunk](https://www.splunk.com/) para visualizar os dados.

## <a name="next-steps"></a>Próximas etapas

* [Coletar logs com o Diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Coletar logs diretamente de um processo do serviço do Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [Gerenciar o consumo e a carga de recursos com métricas no Service Fabric](service-fabric-cluster-resource-manager-metrics.md)

