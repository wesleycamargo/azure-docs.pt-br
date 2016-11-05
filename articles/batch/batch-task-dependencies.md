---
title: Dependências de tarefas no Lote do Azure | Microsoft Docs
description: Crie tarefas que dependem da conclusão bem-sucedida de outras tarefas para o processamento em estilo MapReduce e cargas de trabalho de big data semelhantes no Lote do Azure.
services: batch
documentationcenter: .net
author: mmacy
manager: timlt
editor: ''

ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 09/28/2016
ms.author: marsma

---
# <a name="task-dependencies-in-azure-batch"></a>Dependências de tarefa no Lote do Azure
O recurso de dependências de tarefa de Lote do Azure é uma boa opção se você quer processar:

* Cargas de trabalho de estilo MapReduce na nuvem.
* Trabalhos cujas tarefas de processamento de dados podem ser expressas como um DAG (gráfico acíclico dirigido).
* Qualquer outro trabalho no qual tarefas downstream dependem da saída das tarefas upstream.

Esse recurso permite que você crie tarefas que são agendadas para execução em nós de computação somente após a conclusão bem-sucedida de uma ou mais outras tarefas. Por exemplo, você pode criar um trabalho que processa cada quadro de um filme 3D com tarefas paralelas separadas. A tarefa final, a "tarefa de mesclagem", mescla os quadros renderizados no filme completo somente depois que todos os quadros são gerados com êxito.

Você pode criar tarefas que dependem de outras tarefas em uma relação um para um ou um para muitos. Você pode até mesmo criar uma dependência de intervalo em que uma tarefa depende da conclusão bem-sucedida de um grupo de tarefas em um intervalo específico de IDs de tarefa. Você pode combinar esses três cenários básicos para criar relações muitos-para-muitos.

## <a name="task-dependencies-with-batch-.net"></a>Dependências de tarefas com o .NET do Lote
Neste artigo, discutimos como configurar dependências de tarefas usando a biblioteca [.NET do Lote][net_msdn]. Primeiro mostramos como [habilitar a dependência de tarefa](#enable-task-dependencies) nos trabalhos. Em seguida, demonstramos brevemente como [configurar uma tarefa com dependências](#create-dependent-tasks). Finalmente, discutiremos os [cenários de dependência](#dependency-scenarios) aos quais o Lote dá suporte.

## <a name="enable-task-dependencies"></a>Habilitar dependências de tarefas
Para usar as dependências de tarefas no aplicativo do Lote, primeiro você deve informar ao serviço de Lote que o trabalho usa dependências de tarefas. No .NET do Lote, habilite-o no [CloudJob][net_cloudjob] definindo a propriedade [UsesTaskDependencies][net_usestaskdependencies] como `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

No trecho de código anterior, "batchClient" é uma instância da classe [BatchClient][net_batchclient].

## <a name="create-dependent-tasks"></a>Criar tarefas dependentes
Para criar uma tarefa que depende da conclusão bem-sucedida de uma ou mais outras tarefas, você informa ao Lote que a tarefa "depende" das outras tarefas. No .NET do Lote, configure a propriedade [CloudTask][net_cloudtask].[DependsOn][net_dependson] com uma instância da classe [TaskDependencies][net_taskdependencies]:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Esse trecho de código cria uma tarefa com a ID de "Flowers" que será agendada para ser executada em um nó de computação apenas depois que as tarefas com IDs de "Rain" e "Sun" forem concluídas com êxito.

> [!NOTE]
> Uma tarefa é considerada concluída quando está no estado **Concluído** e seu **código de saída** é `0`. No .NET do Lote, isso significa que o valor da propriedade [CloudTask][net_cloudtask].[State][net_taskstate] e o valor da propriedade `Completed` and the CloudTask's [TaskExecutionInformation][net_taskexecutioninformation].[ExitCode][net_exitcode] é `0`.
> 
> 

## <a name="dependency-scenarios"></a>Cenários de dependência
Há três cenários de dependência de tarefas básicos que você pode usar no Lote do Azure: um-para-um, um-para-muitos e dependência de intervalo de IDs de tarefas. Eles podem ser combinados para fornecer um quarto cenário, muitos-para-muitos.

| Cenário&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exemplo |  |
|:---:| --- | --- |
|  [Um-para-um](#one-to-one) |*tarefaB* depende de *tarefaA* <p/> A *tarefaB* não será agendada para execução até que a *tarefaA* seja concluída com êxito |![Diagrama: dependência de tarefa de um-para-um][1] |
|  [Um-para-muitos](#one-to-many) |A *tarefaC* depende da *tarefaA* e da *tarefaB* <p/> A *tarefaC* não será agendada para execução até que a *tarefaA* e a *tarefaB* sejam concluídas com êxito |![Diagrama: dependência de tarefa de um-para-muitos][2] |
|  [Intervalo de IDs de tarefa](#task-id-range) |A *taskD* depende de diversas tarefas <p/> A *tarefaD* não será agendada para execução até que as tarefas com as IDs *1* a *10* sejam concluídas com êxito |![Diagrama: dependência de intervalo de ids de tarefas][3] |

> [!TIP]
> Você pode criar relações **muitos para muitos**, como uma em que as tarefas C, D, E e F dependem das tarefas A e B. Isso é útil, por exemplo, em cenários de pré-processamento em paralelo em que as tarefas downstream dependem da saída de várias tarefas upstream.
> 
> 

### <a name="one-to-one"></a>Um-para-um
Para criar uma tarefa que tenha uma dependência referente à conclusão bem-sucedida de outra tarefa, forneça uma única ID de tarefa ao método estático [TaskDependencies][net_taskdependencies].[OnId][net_onid] ao popular a propriedade [DependsOn][net_dependson] de [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Um-para-muitos
Para criar uma tarefa que tenha uma dependência referente à conclusão bem-sucedida de várias tarefas, forneça uma coleção de IDs de tarefas ao método estático [TaskDependencies][net_taskdependencies].[OnId][net_onid] ao popular a propriedade [DependsOn][net_dependson] de [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

### <a name="task-id-range"></a>Intervalo de IDs de tarefa
Para criar uma tarefa que tenha uma dependência referente à conclusão bem-sucedida de um grupo de tarefas cujas IDs estão um intervalo, forneça as IDs da primeira e da última tarefa no intervalo ao método estático [TaskDependencies][net_taskdependencies].[OnIdRange][net_onidrange] ao popular a propriedade [DependsOn][net_dependson] de [CloudTask][net_cloudtask].

> [!IMPORTANT]
> Quando você usa intervalos de IDs de tarefas para dependências, as IDs de tarefas no intervalo *devem* ser representações de cadeia de caracteres de valores inteiros. Além disso, todas as tarefas no intervalo devem ser concluídas com êxito para que a tarefa dependente seja agendada para execução.
> 
> 

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="code-sample"></a>Exemplo de código
O projeto de exemplo [TaskDependencies][github_taskdependencies] é um dos códigos de exemplo do [Azure Batch code samples][github_samples] no GitHub. Essa solução do Visual Studio 2015 demonstra como habilitar a dependência de tarefas em um trabalho, criar tarefas que dependem de outras tarefas e executar essas tarefas em um pool de nós de computação.

## <a name="next-steps"></a>Próximas etapas
### <a name="application-deployment"></a>Implantação do aplicativo
O recurso de [pacotes de aplicativos](batch-application-packages.md) do lote fornece uma maneira fácil de implantar e controlar a versão dos aplicativos que as tarefas executam em nós de computação.

### <a name="installing-applications-and-staging-data"></a>Instalação de aplicativos e preparação de dados
Confira a postagem [Instalação de aplicativos e preparação de dados em nós de computação do Lote][forum_post] no Fórum do Lote do Azure para ter uma visão geral dos vários métodos de preparação de nós para execução de tarefas. Escrita por um dos membros da equipe do Lote do Azure, esta postagem é um bom guia sobre as diferentes maneiras de incluir arquivos (incluindo aplicativos e dados de entrada de tarefa) em seus nós de computação.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagrama: dependência de um-para-um"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagrama: dependência de um-para-muitos"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagrama: dependência de intervalo de ids de tarefas"



<!--HONumber=Oct16_HO2-->


