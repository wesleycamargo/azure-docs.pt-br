---
title: Induzir o Chaos em clusters do Service Fabric | Microsoft Docs
description: "Como usar as APIs de serviço de Injeção de falhas e análise de cluster para gerenciar o Chaos no cluster."
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2017
ms.author: motanv
translationtype: Human Translation
ms.sourcegitcommit: 1a9bec270650223cd40b3b60e5bc9fc7e212a207
ms.openlocfilehash: ea8f76d146bd630cc8cb0a417d8c090b656150a4


---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Induzir o Controlled Chaos em clusters do Service Fabric
Os sistemas distribuídos em larga escala, como as infraestruturas de nuvem, não são confiáveis por natureza. O Azure Service Fabric permite aos desenvolvedores escrever serviços confiáveis sobre uma infraestrutura não confiável. Para escrever serviços robustos, os desenvolvedores precisam ser capazes de induzir falhas nessa infraestrutura não confiável para testar a estabilidade dos seus serviços.

O serviço de Injeção de Falhas e Análise de Cluster (também conhecido como Serviço de Análise de Falhas) fornece aos desenvolvedores a capacidade de induzir ações de falha para testar os serviços. No entanto, as falhas simuladas direcionadas só levarão você até aí. Para aprofundar ainda mais o teste, use o Chaos.

O Chaos simula falhas intercaladas contínuas, amigáveis e não amigáveis, em todo o cluster durante longos períodos de tempo. Depois que você configura o Chaos com a taxa e o tipo de falhas, você pode ser iniciá-lo ou interrompê-lo por meio de APIs do C# ou do PowerShell para gerar falhas no cluster e no serviço.

Enquanto o Chaos estiver em execução, ele produzirá eventos diferentes que capturam o estado da execução no momento. Por exemplo, um ExecutingFaultsEvent contém todas as falhas que estão sendo executadas na iteração. Um ValidationFailedEvent contém os detalhes de uma falha foi encontrada durante a validação de cluster. Você pode invocar a API GetChaosReportAsync para obter o relatório de execuções do Chaos.

## <a name="faults-induced-in-chaos"></a>Falhas induzidas no Chaos
O Chaos gera falhas em todo o cluster do Service Fabric e compacta as falhas vistas em meses ou anos em poucas horas. A combinação de falhas intercaladas com a alta taxa de falhas localiza casos específicos que de outra forma seriam ignorados. Esse exercício do Chaos leva a uma melhoria significativa na qualidade do código do serviço.

O Chaos induz falhas a partir das seguintes categorias:

* Reiniciar um nó
* Reiniciar um pacote de códigos implantado
* Remover uma réplica
* Reiniciar uma réplica
* Mover uma réplica primária (configurável)
* Mover uma réplica secundária (configurável)

O Chaos é executado em várias iterações. Cada iteração é composta por falhas e validações de cluster para o período especificado. Você pode configurar o tempo gasto para o cluster se estabilizar e a validação de êxito. Se uma falha for encontrada na validação do cluster, o Chaos gerará e persistirá um ValidationFailedEvent com o carimbo de data/hora UTC e os detalhes da falha.

Por exemplo, considere uma instância do Chaos, definida para ser executada por uma hora com, no máximo, três falhas simultâneas. O Chaos induz três falhas e valida a integridade do cluster. Ele itera através da etapa anterior até que seja explicitamente interrompido por meio da API StopChaosAsync ou após uma hora. Se o cluster se tornar não íntegro em qualquer iteração, ou seja, não se estabilizar em um tempo configurado, o Chaos vai gerar um ValidationFailedEvent. Esse evento indica que algo deu errado e pode precisar de mais investigação.

Em sua forma atual, o Chaos induz apenas falhas seguras. Isso significa que, na ausência de falhas externas, uma perda de quorum ou dados nunca ocorrerá.

## <a name="important-configuration-options"></a>Opções de configuração importantes
* **TimeToRun**: tempo total durante o qual o Chaos é executado antes de ser finalizado com êxito. Você pode interromper o Chaos antes que ele seja executado pelo período de TimeToRun usando a API StopChaos.
* **MaxClusterStabilizationTimeout**: a quantidade máxima de tempo de espera para que o cluster se torne íntegro antes de verificar novamente. Essa espera serve para reduzir a carga no cluster enquanto ele está se recuperando. As verificações executadas são:
  * Se a integridade do cluster está OK
  * Se a integridade do serviço está OK
  * Se o tamanho do conjunto de réplicas de destino é obtido para a partição de serviço
  * Não há réplicas InBuild
* **MaxConcurrentFaults**: o número máximo de falhas simultâneas induzidas em cada iteração. Quanto mais alto o número, mais agressivo é o Chaos. Isso resulta em failovers mais complexos e combinações de transição. O Chaos garante que, na ausência de falhas externas, não haverá uma perda de quorum ou de dados, independentemente de quão alto o valor dessa configuração está.
* **EnableMoveReplicaFaults**: habilita ou desabilita as falhas que causam a movimentação das réplicas primárias ou secundárias. Essas falhas estão desabilitadas por padrão.
* **WaitTimeBetweenIterations**: o tempo de espera entre as iterações, isto é, após uma rodada de falhas e a validação correspondente.
* **WaitTimeBetweenFaults**: o tempo de espera entre as duas falhas consecutivas em uma iteração.

## <a name="how-to-run-chaos"></a>Como executar o Chaos
**C#:**

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```
**PowerShell:**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```



<!--HONumber=Jan17_HO1-->


