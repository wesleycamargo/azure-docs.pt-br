<properties
   pageTitle="Maximizar o uso do nó do Lote com tarefas paralelas | Microsoft Azure"
   description="Aumente a eficiência e reduza os custos usando menos nós de computação durante a execução de tarefas simultâneas em cada nó em um pool do Lote do Azure"
   services="batch"
   documentationCenter=".net"
   authors="mmacy"
   manager="timlt"
   editor=""/>

   <tags
   	ms.service="batch"
   	ms.devlang="multiple"
   	ms.topic="article"
   	ms.tgt_pltfrm="vm-windows"
   	ms.workload="big-compute"
   	ms.date="09/30/2015"
   	ms.author="v-marsma"/>

# Maximizar o uso de recursos de computação do Lote do Azure com tarefas de nó simultâneas

A execução de tarefas paralelas em larga escala é um dos principais recursos do Lote do Azure, e essa capacidade se estende não só para os vários nós de computação que estejam executando suas tarefas, como também às tarefas simultâneas em execução nesses nós. No Lote, a execução de tarefas paralelas é escalonada verticalmente e expandida.

A habilitação da execução de tarefas simultâneas em nós de computação de um pool permite a maximização do uso de recursos em um número menor de nós no pool. Embora alguns cenários se beneficiem da disponibilidade de todos os recursos de um nó para a alocação para uma única tarefa, algumas situações serão beneficiadas quando várias tarefas compartilharem esses recursos:

 - **Minimização da transferência de dados** quando as tarefas puderem compartilhar dados. Nesse cenário, a cópia de dados compartilhados para um número menor de nós e a execução de tarefas em paralelo em cada nó poderá reduzir drasticamente as cobranças de transferência de dados, especialmente se os dados a serem copiados para cada nó tiverem de ser transferidos entre regiões geográficas.

 - **Maximização do uso de memória** quando as tarefas exigirem uma grande quantidade de memória, mas somente durante curtos períodos de tempo, e em momentos variáveis durante a execução. Um número menor de instâncias de nó maiores e com mais memória poderá ser empregado para o controle eficiente de picos, com as tarefas paralelas em execução em cada nó aproveitando a vantagem da memória abundante dos nós em momentos diferentes.

 - **Redução dos limites do número de nós** quando a comunicação entre nós for necessária em um pool. Atualmente, o número de pools configurados para a comunicação entre os nós está limitado a 50 nós e, portanto, um número maior de tarefas poderá ser executado simultaneamente se cada nó em tal pool for capaz de executar tarefas em paralelo.

 - **Replicação de um cluster de computação local**, como na primeira movimentação de um ambiente de computação no Azure. É possível aumentar o número máximo de tarefas de nó para espelhar com mais precisão uma configuração física existente caso essa configuração execute no momento várias tarefas por nó de computação.

## Cenário de exemplo

Para ilustrar os benefícios da execução de tarefas paralelas, digamos que o seu aplicativo de tarefa tenha requisitos de CPU e de memória de forma que o tamanho do nó Standard\_D1 seja adequado, mas para a execução do trabalho no tempo exigido, serão necessários 1000 desses nós. Em vez de usar nós Standard\_D1, você poderia empregar nós Standard\_D14 com 16 núcleos e habilitar a execução de tarefas paralelas. Nesse caso, poderíamos usar *16 vezes menos nós* -- em vez de 1000 nós, somente 63 seriam necessários. Isso melhora muito o tempo de execução e a eficiência do trabalho caso grandes arquivos de aplicativo ou dados de referência sejam exigidos para cada nó.

## Habilitar a execução de tarefas paralelas

A configuração de nós de computação em sua solução do Lote para execução de tarefas paralelas é feita no nível do pool. Quando você trabalhar com a API .NET do Lote, a propriedade [CloudPool.MaxTasksPerComputeNode][maxtasks_net] será definida durante a criação de um pool. Na API REST do Lote, o elemento [maxTasksPerNode][maxtasks_rest] será definido no corpo da solicitação durante a criação do pool.

O Lote do Azure permite um máximo de tarefas por configuração de nó de até quatro vezes (4x) o número de núcleos de nó. Por exemplo, se o pool estiver configurado com nós de tamanho "Grande" (quatro núcleos), `maxTasksPerNode` poderá ser definido como dezesseis. Os detalhes sobre o número de núcleos para cada um dos tamanhos de nó podem ser encontrados em [Tamanhos das máquinas virtuais](../virtual-machines/virtual-machines-size-specs.md) e você poderá encontrar mais informações sobre os limites de serviço em [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).

> [AZURE.TIP]Leve em consideração o valor `maxTasksPerNode` ao criar uma [fórmula de autoescala][enable_autoscaling] para o pool. Por exemplo, uma fórmula que avalia `$RunningTasks` poderia ser drasticamente afetada por um aumento nas tarefas por nó. Consulte [Dimensionar automaticamente nós de computação em um pool do Lote do Azure](batch-automatic-scaling.md) para saber mais.

## Distribuição de tarefas

Quando os nós de computação dentro de um pool puderem executar tarefas simultaneamente, será importante especificar como você gostaria que suas tarefas fossem distribuídas pelos nós no pool.

Usando a propriedade [CloudPool.TaskSchedulingPolicy][task_schedule], você pode especificar que as tarefas deverão ser atribuídas uniformemente em todos os nós do pool ("difusão") ou que a quantidade máxima possível de tarefas deverá ser atribuída a cada nó antes que as tarefas sejam atribuídas a outro nó no pool ("empacotamento").

Como um exemplo de como esse recurso é útil, considere o pool de nós Standard\_D14 no exemplo acima, configurado com um valor 16 em [CloudPool.MaxTasksPerComputeNode][maxtasks_net]. Se a [CloudPool.TaskSchedulingPolicy][task_schedule] for configurada com um [ComputeNodeFillType][fill_type] de *Pack*, ela maximizaria o uso de todos os 16 núcleos de cada nó e permitiria que um [pool de dimensionamento automático](./batch-automatic-scaling.md) aparasse os nós não utilizados no pool (nós sem nenhuma tarefa atribuída), minimizando, assim, o uso de recursos e economizando dinheiro.

## Exemplo de .NET do Lote

Esse trecho de código de API [.NET do Lote][api_net] mostra uma solicitação para criar um pool com quatro nós grandes com um máximo de quatro tarefas por nó e especifica uma política de agendamento de tarefas que preencherá cada nó com tarefas antes de atribuir tarefas a outro nó do pool. Para saber mais sobre como adicionar pools usando a API .NET do Lote, consulte [BatchClient.PoolOperations.CreatePool][poolcreate_net].

        CloudPool pool = batchClient.PoolOperations.CreatePool(poolId: "mypool",
        													osFamily: "2",
        													virtualMachineSize: "large",
        													targetDedicated: 4);
        pool.MaxTasksPerComputeNode = 4;
        pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
        pool.Commit();

## Exemplo REST do Lote

Esse trecho da API [REST do Lote][api_rest] mostra uma solicitação para criar um pool com dois nós grandes com um máximo de quatro tarefas por nó. Para saber mais sobre como adicionar pools usando a API REST, consulte [Adicionar um pool a uma conta][maxtasks_rest].

        {
          "id": "mypool",
          "vmSize": "Large",
          "osFamily": "2",
          "targetOSVersion": "*",
          "targetDedicated": 2,
          "enableInterNodeCommunication": false,
          "maxTasksPerNode": 4
        }

> [AZURE.NOTE]O elemento `maxTasksPerNode` e a propriedade [MaxTasksPerComputeNode][maxtasks_net] só podem ser definidos no momento da criação do pool. Ele não poderá ser modificado após a criação do pool.

## Explorar o projeto de exemplo

Confira o projeto [ParallelNodeTasks][parallel_tasks_sample] no GitHub, um exemplo de código de trabalho que ilustra o uso de [CloudPool.MaxTasksPerComputeNode][maxtasks_net]. Este aplicativo de console do C# usa a biblioteca do [.NET do Lote][api_net] para criar um pool com um ou mais nós de computação e executa um número configurável de tarefas em nós para simular carga variável. A saída do aplicativo detalha quais nós executaram cada tarefa, bem como um resumo dos parâmetros e a duração do trabalho. A parte de resumo da saída de duas execuções diferentes do aplicativo de exemplo é exibida abaixo.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A primeira execução do aplicativo de exemplo mostra que, com um único nó no pool e usando o padrão de uma tarefa por nó, a duração da tarefa será de mais de trinta minutos.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

A segunda execução do exemplo mostra uma redução significativa na duração do trabalho porque o pool foi configurado com quatro tarefas por nó, permitindo a execução paralela de tarefas para a conclusão do trabalho em aproximadamente um quarto do tempo.

> [AZURE.NOTE]As durações de trabalho nos resumos acima não incluem o tempo de criação do pool. Cada um dos trabalhos acima foi enviado para pools criados anteriormente e cujos nós de computação estavam no estado *Ocioso* na hora do envio.

## Mapa de Calor do Explorador do Lote

O [Explorador do Lote][batch_explorer], um dos [aplicativos de exemplo][github_samples] do Lote do Azure, contém um recurso *Mapa de Calor* que fornece a visualização do uso do núcleo do nó dentro em um pool. Ao executar o aplicativo de exemplo [ParallelTasks][parallel_tasks_sample], use o recurso Mapa de Calor para visualizar facilmente a atividade do nó principal.

![Mapa de Calor do Explorador do Lote][1]

*Mapa de Calor do Explorador do Lote mostrando quatro nós com quatro núcleos cada, com cada núcleo executando uma tarefa no momento*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/pt-BR/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[maxtasks_rest]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png

<!---HONumber=Oct15_HO1-->