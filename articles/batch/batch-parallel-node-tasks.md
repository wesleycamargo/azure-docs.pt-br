<properties
	pageTitle="Maximizar o uso do nó do Lote com tarefas paralelas | Microsoft Azure"
	description="Aumente a eficiência e reduza os custos usando menos nós de computação durante a execução de tarefas simultâneas em cada nó em um pool do Lote do Azure"
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />
	
<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="01/22/2016"
	ms.author="marsma" />
	
# Maximizar o uso de recursos de computação do Lote do Azure com tarefas de nó simultâneas

Neste artigo, você aprenderá a executar mais de uma tarefa simultaneamente em cada nó de computação de seu pool do Lote do Azure. A habilitação da execução de tarefas simultâneas em nós de computação de um pool permite a maximização do uso de recursos em uma quantidade menor de nós no pool. Para algumas cargas de trabalho, isso pode economizar tempo e dinheiro.

Embora alguns cenários se beneficiem da disponibilidade de todos os recursos de um nó para a alocação para uma única tarefa, algumas situações serão beneficiadas quando várias tarefas compartilharem esses recursos:

 - **Minimização da transferência de dados** quando as tarefas puderem compartilhar dados. Nesse cenário, é possível reduzir drasticamente os encargos de transferência de dados copiando dados compartilhados em uma quantidade menor de nós e executando tarefas em paralelo em cada nó. Isso se aplicará especialmente se os dados que devem ser copiados em cada nó precisarem ser transferidos entre regiões geográficas.

 - **Maximização do uso de memória** quando as tarefas exigirem uma grande quantidade de memória, mas somente durante curtos períodos de tempo, e em momentos variáveis durante a execução. Você pode empregar menos nós de computação, porém maiores, com mais memória para lidar de forma eficiente com esses picos. Esses nós teriam várias tarefas executadas em paralelo em cada nó, mas cada tarefa aproveitaria a grande quantidade de memória dos nós em momentos diferentes.

 - **Redução dos limites do número de nós** quando a comunicação entre nós for necessária em um pool. Atualmente, os pools configurados para comunicação entre nós estão limitados a 50 nós de computação. Portanto, uma quantidade maior de tarefas pode ser executada simultaneamente se cada nó nesse pool for capaz de executar tarefas em paralelo.

 - **Replicação de um cluster de computação local**, como na primeira movimentação de um ambiente de computação para o Azure. Você pode aumentar o número máximo de tarefas de nó para espelhar com mais precisão uma configuração física existente, caso essa configuração execute no momento várias tarefas por nó de computação.

## Cenário de exemplo

Veja um exemplo que ilustra os benefícios da execução de tarefas em paralelo. Vamos supor que seu aplicativo de tarefa tenha requisitos de CPU e de memória que tornem um tamanho de nó Standard\_D1 adequado. Mas, para executar o trabalho no tempo necessário, 1.000 nós desse tipo serão necessários.

Em vez de usar nós Standard\_D1, que têm um núcleo de CPU, você poderia empregar nós Standard\_D14 com 16 núcleos cada um e permitir a execução de tarefas paralelas. Nesse caso, poderíamos usar *16 vezes menos nós* -- em vez de 1,000 nós, somente 63 seriam necessários. Isso melhora muito o tempo de execução e a eficiência do trabalho caso grandes arquivos de aplicativo ou dados de referência sejam exigidos para cada nó.

## Habilitar a execução de tarefas paralelas

A configuração de nós de computação em sua solução do Lote para execução de tarefas em paralelo é feita no nível do pool. Ao usar a biblioteca .NET do Lote, a propriedade [CloudPool.MaxTasksPerComputeNode][maxtasks_net] é definida durante a criação de um pool. Se você estiver usando a API REST do Lote, o elemento [maxTasksPerNode][maxtasks_rest] é definido no corpo da solicitação durante a criação do pool.

O Lote do Azure permite que você configure o número máximo de tarefas por nó até quatro vezes (4x) o número de núcleos de nó. Por exemplo, se o pool estiver configurado com nós de tamanho "Grande" (quatro núcleos), será possível definir `maxTasksPerNode` como 16. Para obter detalhes sobre o número de núcleos para cada um dos tamanhos de nó, confira [Tamanhos para serviços de nuvem](./../cloud-services/cloud-services-sizes-specs.md). Para saber mais sobre limites de serviço, confira [Cotas e limites para o serviço de Lote do Azure](batch-quota-limit.md).

> [AZURE.TIP] Leve em consideração o valor de `maxTasksPerNode` ao criar uma [fórmula de autoescala][enable_autoscaling] para o pool. Por exemplo, uma fórmula que avalia `$RunningTasks` poderia ser drasticamente afetada por um aumento nas tarefas por nó. Consulte [Dimensionar automaticamente nós de computação em um pool do Lote do Azure](batch-automatic-scaling.md) para saber mais.

## Distribuição de tarefas

Quando os nós de computação dentro de um pool puderem executar tarefas simultaneamente, será importante especificar como você deseja distribuir suas tarefas pelos nós no pool.

Usando a propriedade [CloudPool.TaskSchedulingPolicy][task_schedule], você pode especificar as tarefas devem ser atribuídas uniformemente em todos os nós no pool ("difusão"). Ou você pode especificar que o máximo possível de tarefas deve ser atribuído a cada nó antes de as tarefas serem atribuídas a outro nó no pool ("remessa").

Como um exemplo de como esse recurso é útil, considere o pool de nós Standard\_D14 (no exemplo acima) configurados com um valor 16 em [CloudPool.MaxTasksPerComputeNode][maxtasks_net]. Se a [CloudPool.TaskSchedulingPolicy][task_schedule] for configurada com um [ComputeNodeFillType][fill_type] de *Pack*, ela maximizaria o uso de todos os 16 núcleos de cada nó e permitiria que um [pool de dimensionamento automático](./batch-automatic-scaling.md) aparasse os nós não utilizados no pool (nós sem nenhuma tarefa atribuída). Isso minimiza o uso de recursos e economizando dinheiro.

## Exemplo de .NET do Lote

Esse trecho de código da API [.NET do Lote][api_net] mostra uma solicitação para criar um pool com quatro nós grandes, com um máximo de quatro tarefas por nó. Isso especifica uma política de agendamento de tarefas que preencherá cada nó com tarefas antes de atribuir tarefas a outro nó no pool. Para saber mais sobre como adicionar pools usando a API .NET do Lote, confira [BatchClient.PoolOperations.CreatePool][poolcreate_net].

        CloudPool pool = batchClient.PoolOperations.CreatePool(poolId: "mypool",
        													osFamily: "2",
        													virtualMachineSize: "large",
        													targetDedicated: 4);
        pool.MaxTasksPerComputeNode = 4;
        pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
        pool.Commit();

## Exemplo REST do Lote

Esse trecho da API [REST do Lote][api_rest] mostra uma solicitação para criar um pool com dois nós grandes, com um máximo de quatro tarefas por nó. Para saber mais sobre como adicionar pools usando a API REST, confira [Adicionar um pool a uma conta][maxtasks_rest].

        {
          "id": "mypool",
          "vmSize": "Large",
          "osFamily": "2",
          "targetOSVersion": "*",
          "targetDedicated": 2,
          "enableInterNodeCommunication": false,
          "maxTasksPerNode": 4
        }

> [AZURE.NOTE] Você pode definir o elemento `maxTasksPerNode` e a propriedade [MaxTasksPerComputeNode][maxtasks_net] apenas no momento da criação do pool. Eles não poderão ser modificados após a criação do pool.

## Explorar o projeto de exemplo

Confira o projeto [ParallelNodeTasks][parallel_tasks_sample] no GitHub. É um exemplo de código de trabalho que ilustra o uso de [CloudPool.MaxTasksPerComputeNode][maxtasks_net].

Este aplicativo de console C# usa a biblioteca [.NET do Lote][api_net] para criar um pool com um ou mais nós de computação. Ele executa um número configurável de tarefas nesses nós para simular uma carga variável. A saída do aplicativo especifica os nós executados em cada tarefa. O aplicativo também fornece um resumo dos parâmetros do trabalho e a duração. A parte de resumo da saída de duas execuções diferentes do aplicativo de exemplo é exibida abaixo.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A primeira execução do aplicativo de exemplo mostra que com um único nó no pool, e a configuração padrão de uma tarefa por nó, a duração do trabalho será superior a 30 minutos.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

A segunda execução do exemplo mostra uma redução significativa na duração do trabalho. Isso ocorre porque o pool foi configurado com quatro tarefas por nó, o que permite que a execução paralela de tarefas conclua o trabalho em aproximadamente um quarto do tempo.

> [AZURE.NOTE] As durações de trabalho nos resumos acima não incluem o tempo de criação do pool. Cada um dos trabalhos acima foi enviado para pools criados anteriormente e cujos nós de computação estavam no estado *Ocioso* na hora do envio.

## Mapa de Calor do Explorador do Lote

O [Gerenciador de Lote do Azure][batch_explorer], um dos [aplicativos de exemplo][github_samples] do Lote do Azure, contém um recurso *Mapa de Calor* que fornece a visualização da execução de tarefa. Ao executar o aplicativo de exemplo [ParallelTasks][parallel_tasks_sample], use o recurso Mapa de Calor para visualizar facilmente a execução de tarefas paralelas em cada nó.

![Mapa de Calor do Explorador do Lote][1]

*Mapa de Calor do Gerenciador de Lotes que mostra um pool de quatro nós, com cada nó atualmente executando quatro tarefas*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[maxtasks_rest]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png

<!---HONumber=AcomDC_0128_2016-->