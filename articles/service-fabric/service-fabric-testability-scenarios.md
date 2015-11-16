<properties
   pageTitle="Testes de caos e failover | Microsoft Azure"
   description="Usando os cenários de testes de caos e failover do Service Fabric para induzir falhas e verificar a confiabilidade de seus serviços."
   services="service-fabric"
   documentationCenter=".net"
   authors="anmolah"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/26/2015"
   ms.author="anmola"/>

# Cenários de teste
Sistemas grandes distribuídos como infraestruturas de nuvem não são confiáveis por natureza. A Malha do Serviço fornece aos desenvolvedores a capacidade de gravar os serviços para serem executados sobre infraestruturas não confiáveis. Para gravar serviços de alta qualidade, os desenvolvedores precisam ser capazes de induzir essa infraestrutura confiável a testar a estabilidade dos seus serviços. A Malha do Serviço fornece aos desenvolvedores a capacidade de induzir ações de falha para testar serviços na presença de falhas. No entanto, falhas simuladas direcionadas não o levarão tão longe. Para elevar o teste a um outro patamar, a Malha do Serviço fornece cenários de teste predefinidos. Os cenários simulam falhas intercaladas contínuas, amigáveis e não amigáveis, em todo o cluster por longos períodos de tempo. Quando configurado com a taxa e o tipo de falha, ele é executado como uma ferramenta do lado do cliente, por meio de APIs do C# ou PowerShell para gerar falhas no cluster e no serviço. Como parte do recurso de capacidade de teste, fornecemos os cenários a seguir.

1.	Teste de Caos
2.	Teste de Failover

## Teste de Caos
O cenário de caos gera falhas no cluster de toda a malha de serviço. O cenário compacta falhas geralmente vistas em meses ou em anos em apenas algumas horas. A combinação de falhas intercaladas com a alta taxa de falhas localiza casos específicos que de outra forma seriam ignorados. Isso leva a uma melhoria significativa na qualidade do código do serviço.

### Falha simulada no teste de caos
 - Reinício de um nó
 - Reinício de um pacote de código implantado
 - Remoção de uma Réplica
 - Reinício de uma réplica
 - Movimentação de uma réplica primária (opcional)
 - Movimentação de uma réplica secundária (opcional)

O teste de caos executa várias iterações de falhas e validações de cluster durante o período de tempo especificado. O tempo gasto para o cluster se estabilizar e a validação de êxito também são configuráveis. A falha do cenário quando nos deparamos com uma única falha na validação do cluster. Por exemplo, considere um conjunto de teste a ser executado por 1 hora e com o máximo de 3 falhas simultâneas. O teste induzirá 3 falhas e validará a integridade do cluster. O teste vai iterar a etapa anterior até que o cluster se torne não íntegro ou que decorra 1 hora. Se em uma iteração o cluster se tornar não íntegro, ou seja, não se estabilizar em um tempo configurado, o teste falhará com uma exceção. Essa exceção indica que algo deu errado e precisa de mais investigação. Em sua forma atual, o teste do mecanismo de geração de falha de teste de caos induz apenas a falhas seguras. Isso significa que, na ausência de falhas externas, uma perda de quorum ou dados nunca ocorrerá.

### Opções de configuração importantes
 - **TimeToRun**: tempo total em que o teste será executado antes de ser concluído com êxito. O teste pode ser concluído antes, no lugar de uma falha de validação.
 - **MaxClusterStabilizationTimeout**: a quantidade máxima de tempo de espera para que o cluster se torne íntegro antes de falhar no teste. As verificações executadas são as seguintes: se a integridade do cluster está OK, se a integridade do serviço está OK, tamanho do conjunto de réplicas de destino obtido para a partição de serviço e nenhuma réplica do InBuild.
 - **MaxConcurrentFaults**: número máximo de falhas simultâneas induzidas em cada iteração. Quanto maior o número, mais agressivo o teste, resultando em failovers mais complexos e combinações de transição. O teste garante que, na ausência de falhas externas, não haverá uma perda de quorum ou de dados, independentemente de quão alta essa configuração está.
 - **EnableMoveReplicaFaults**: habilita ou desabilita as falhas, causando a movimentação das réplicas primárias ou secundárias. Essas falhas estão desabilitadas por padrão.
 - **WaitTimeBetweenIterations**: tempo de espera entre as iterações, isto é, após uma rodada de falhas e a validação correspondente.

### Como executar o teste de caos
Exemplo de C#

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection & security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The Chaos Test Scenario should run at least 60 minutes or up until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

Powershell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## Teste de failover

O cenário do teste de failover é uma versão do cenário de teste de Caos que visa uma partição de serviço específica. Ele testa o efeito de failover em uma partição de serviço específica, sem afetar os outros serviços. Quando configurado com as informações de partição de destino e outros parâmetros, ele é executado como uma ferramenta do lado do cliente usando APIs do C# ou Powershell para gerar falhas para uma partição de serviço. O cenário itera por meio de uma sequência de falhas simuladas e validação de serviço enquanto a lógica de negócios é executada ao lado para fornecer uma carga de trabalho. Uma falha na validação do serviço indica um problema que precisa de mais investigação.

### Falhas simuladas no teste de failover
- Reiniciar um pacote de código implantado onde a partição está hospedada
- Remover uma réplica Primária/Secundária ou uma instância sem estado
- Reiniciar uma Réplica Primária Secundária (se o serviço persistir)
- Mover uma réplica primária
- Mover uma réplica secundária
- Reinicie a partição.

O teste de failover induz uma falha escolhida e depois executa a validação no serviço para assegurar sua estabilidade. O teste de failover apenas induz a uma falha de cada vez, em vez de possíveis várias falhas no teste de Caos. Se, depois de cada falha, a partição de serviço não estabilizar dentro do tempo limite configurado, o teste falhará. O teste induz apenas falhas seguras. Isso significa que, na ausência de falhas externas, não ocorre uma perda de quorum ou dados.

### Opções de configuração importantes
 - **PartitionSelector**: objeto seletor que especifica a partição que precisa ser direcionada.
 - **TimeToRun**: tempo total que o teste será executado antes da conclusão
 - **MaxServiceStabilizationTimeout**: a quantidade máxima de tempo de espera para que o cluster se torne íntegro antes da falha no teste. As verificações executadas são as seguintes: se a integridade do serviço está OK, o tamanho do conjunto de réplicas de destino obtido para todas as partições e nenhuma réplica do InBuild.
 - **WaitTimeBetweenFaults**: tempo de espera entre cada ciclo de falha e validação

### Como executar o teste de failover
Exemplo de C#

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection & security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The Chaos Test Scenario should run at least 60 minutes or up until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario chaosScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


Powershell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```

 

<!---HONumber=Nov15_HO2-->