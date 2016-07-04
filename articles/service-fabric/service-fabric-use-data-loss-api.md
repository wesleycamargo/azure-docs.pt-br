<properties
   pageTitle="Como invocar a perda de dados nos serviços do Service Fabric | Microsoft Azure"
   description="Descreve como usar a api de perda de dados"
   services="service-fabric"
   documentationCenter=".net"
   authors="LMWF"
   manager="rsinha"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/14/2016"
   ms.author="lemai"/>
   
# Como invocar a perda de dados nos serviços

>[AZURE.WARNING] Este documento descreve como fazer com que os dados em seus serviços se percam, devendo ser usado com cuidado.

## Introdução
Você pode invocar a perda de dados em uma partição do serviço do Service Fabric chamando StartPartitionDataLossAsync(). Essa api usa o Serviço de Análise e Injeção de Falha para executar o trabalho que causa as condições de perda de dados.

## Usando o Serviço de Análise e Injeção de Falha

Atualmente, o Serviço de Análise e Injeção de Falha é compatível com as APIs do quadro abaixo. À direita, temos o cmdlet do PowerShell correspondente. Consulte a documentação do msdn em cada API para obter mais informações sobre cada uma.

| API do C# | Cmdlet do PowerShell |
|-------------------------------------|-----------------------------------------------:|
|[StartPartitionDataLossAsync][dl] |[Start-ServiceFabricPartitionDataLoss][psdl] |
|[StartPartitionQuorumLossAsync][ql] |[Start-ServiceFabricPartitionQuorumLoss][psql] |
|[StartPartitionRestartAsync][rp] |[Start-ServiceFabricPartitionRestart][psrp] |

## Visão geral conceitual de execução de um comando

O Serviço Análise e Injeção de Falha usa um modelo assíncrono em que você inicia o comando com uma API, conhecida como a API "Start" neste documento, em seguida, verifica o progresso desse comando usando uma API "GetProgress" até que ela tenha atingido um estado terminal ou até que você a cancele. Para iniciar um comando, chame a API "Start" da API correspondente. Essa API retornará quando o Serviço de Análise e Injeção de Falha tiver aceitado a solicitação. No entanto, ela não indica o quanto um comando foi executado, nem mesmo se ele já foi iniciado. Para verificar o andamento de um comando, chame a API "GetProgress" que corresponde à API "Start" chamada anteriormente. A API "GetProgress" retornará um objeto que indica o status atual do comando dentro de sua propriedade State. Um comando é executado indefinidamente até:

1.	Que seja concluído com êxito. Se você chamar "GetProgress" dentro dele nesse caso, o estado do objeto de progresso será concluído.
2.	Que ele encontre um erro fatal. Se você chamar "GetProgress" dentro dele nesse caso, a State do objeto de progresso será Faulted
3.	Você o cancela por meio da API [CancelTestCommandAsync][cancel] ou do cmdlet [Stop-ServiceFabricTestCommand][cancelps] do PowerShell. Se você chamar "GetProgress" dentro dele nesse caso, a State do objeto de progresso será Cancelled ou ForceCancelled, dependendo de um argumento para essa API. Confira a documentação de [CancelTestCommandAsync][cancel] para obter mais detalhes.


## Detalhes da execução de um comando

Para iniciar um comando, chame a API Start com os argumentos esperados. Todas as APIs Start têm um argumento GUID chamado operationId. É preciso controlar o argumento operationId, uma vez que ele é usado para rastrear o progresso desse comando. Ele deve ser passado para a API "GetProgress" para rastrear o progresso do comando. A operationId deve ser exclusiva.

Depois de chamar a API Start com êxito, a API GetProgress deverá ser chamada em um loop até que a propriedade State do objeto de progresso seja Completed. Todas as [FabricTransientExceptions][fte] e OperationCanceledExceptions devem ser recuperadas. Quando o comando tiver atingido um estado terminal (Completed, Faulted ou Cancelled), a propriedade Result do objeto de progresso retornado terá informações adicionais. Se o estado for Completed, Result.SelectedPartition.PartitionId conterá a id da partição que foi selecionada. Result.Exception será nulo. Se o estado for Faulted, Result.Exception apresentará o motivo pelo qual o comando do Serviço de Análise e Injeção de Falha falhou. Result.SelectedPartition.PartitionId terá a id da partição que foi selecionada. Em algumas situações, o comando pode não ser executado o suficiente para escolher uma partição. Nesse caso, PartitionId será 0. Se o estado for Cancelled, Result.Exception será nulo. Assim como no caso de Faulted, Result.SelectedPartition.PartitionId terá a id da partição que foi escolhida, mas se o comando não tiver prosseguido o suficiente para isso, será 0. Veja também o exemplo abaixo.

O exemplo de código abaixo mostra como iniciar e depois verificar o progresso em um comando para reiniciar uma partição específica.

```csharp
    static async Task PerformDataLossSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/MyService");

        // The id of the target partition inside the target service
        Guid targetPartitionId = new Guid("00000000-0000-0000-0000-000002233445");

        PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(targetServiceName, targetPartitionId);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.        

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                // In a terminal state .Result.SelectedPartition.PartitionId will have the chosen partition
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);
                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}'", operationId, progress.Result.Exception);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(5.0d)).ConfigureAwait(false);
        }
    }
```

O exemplo abaixo mostra como usar PartitionSelector para escolher uma partição aleatória de um serviço especificado:

```csharp
    static async Task PerformDataLossUseSelectorSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/SampleService ");

        // Use a PartitionSelector that will have the Fault Injection and Analysis Service choose a random partition of “targetServiceName”
        PartitionSelector partitionSelector = PartitionSelector.RandomOf(targetServiceName);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }
            catch (Exception e)
            {
                Console.WriteLine("Unexpected exception '{0}'", e);
                throw;
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                Console.WriteLine("Printing progress.Result:");
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);

                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}', SelectedPartition {2}", operationId, progress.Result.Exception, progress.Result.SelectedPartition);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }
    }
```

## Histórico e truncamento

Depois que um comando tiver atingido um estado terminal, seus metadados permanecerão no Serviço de Análise e Injeção de Falha por um tempo determinado, antes de ser removido para economia de espaço. Se "GetProgress" for chamado usando operationId de um comando depois que ele tiver sido removido, ele retornará uma FabricException com um ErrorCode de KeyNotFound.

[dl]: https://msdn.microsoft.com/pt-BR/library/azure/mt693569.aspx
[ql]: https://msdn.microsoft.com/pt-BR/library/azure/mt693558.aspx
[rp]: https://msdn.microsoft.com/pt-BR/library/azure/mt645056.aspx
[psdl]: https://msdn.microsoft.com/pt-BR/library/mt697573.aspx
[psql]: https://msdn.microsoft.com/pt-BR/library/mt697557.aspx
[psrp]: https://msdn.microsoft.com/pt-BR/library/mt697560.aspx
[cancel]: https://msdn.microsoft.com/pt-BR/library/azure/mt668910.aspx
[cancelps]: https://msdn.microsoft.com/pt-BR/library/mt697566.aspx
[fte]: https://msdn.microsoft.com/pt-BR/library/azure/system.fabric.fabrictransientexception.aspx

<!---HONumber=AcomDC_0622_2016-->