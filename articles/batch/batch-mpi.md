---
title: Executar aplicativos de MPI no Lote do Azure com tarefas de várias instâncias | Microsoft Docs
description: Saiba como executar aplicativos de MPI (interface de transmissão de mensagens) usando o tipo de tarefa de várias instâncias no Lote do Azure.
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
ms.date: 09/29/2016
ms.author: marsma

---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-(mpi)-applications-in-azure-batch"></a>Usar tarefas de várias instâncias para executar aplicativos de MPI (interface de transmissão de mensagens) no Lote do Azure
As tarefas de várias instâncias permitem que você execute uma tarefa do Lote do Azure em vários nós de computação simultaneamente. Essas tarefas permitem cenários de computação de alto desempenho, como aplicativos MPI (Interface de Transmissão de Mensagens) no Lote. Neste artigo, você aprende a executar tarefas de várias instâncias usando a biblioteca de [Lote do .NET][api_net].

> [!NOTE]
> Embora os exemplos neste artigo se concentrem no Lote do .NET, MS-MPI e nós de computação do Windows, os conceitos de tarefa de várias instâncias discutidos aqui são aplicáveis a outras plataformas e tecnologias (por exemplo, Python e Intel MPI em nós Linux).
> 
> 

## <a name="multi-instance-task-overview"></a>Visão geral da tarefa de várias instâncias
No Lote, cada tarefa normalmente é executada em um nó de computação único: você envia várias tarefas para um trabalho e o serviço do Lote agenda cada tarefa para execução em um nó. No entanto, ao definir as **configurações de várias instâncias**de uma tarefa, você poderá instruir o lote para dividir a tarefa em subtarefas a fim de ser executada em vários nós.

![Visão geral da tarefa de várias instâncias][1]

Quando você envia uma tarefa com as configurações de várias instâncias para um trabalho, o Lote executa várias etapas exclusivas para tarefas de várias instâncias:

1. O serviço do Lote divide a tarefa automaticamente em uma tarefa **principal** e várias **subtarefas**. O número total de tarefas (as principais e todas as subtarefas) corresponde ao número de **instâncias** (nós de computação) que você especificar nas configurações de várias instâncias.
2. O Lote designa um de nós de computação como o **mestre** e agenda para ocorrer nele a execução a tarefa principal. Ele agenda a execução das subtarefas para ocorrer no restante dos nós de computação alocados para a tarefa de várias instâncias, uma subtarefa por nó.
3. As tarefas principais e todas as subtarefas baixam os **arquivos de recurso comum** que você especificar nas configurações de várias instâncias.
4. Depois que os arquivos de recursos comuns tiverem sido baixados, a tarefa principal e as subtarefas executarão o **comando de coordenação** especificado nas configurações de várias instâncias. O comando de coordenação normalmente é usado para preparar nós para executar a tarefa. Isso pode incluir a inicialização de serviços em segundo plano (como o `smpd.exe` do [Microsoft MPI][msmpi_msdn]) e verificar se os nós estão prontos para processar mensagens entre nós.
5. A tarefa principal executará o **comando de aplicativo** no nó mestre *após* o comando de coordenação ter sido concluído com êxito pela tarefa principal e por todas as subtarefas. O comando do aplicativo é a linha de comando da tarefa de várias instâncias em si, que é executada somente pela tarefa principal. Por exemplo, em uma solução com base em [MS-MPI][msmpi_msdn], é aqui que você executa seu aplicativo habilitado por MPI usando `mpiexec.exe`.

> [!NOTE]
> Embora seja funcionalmente distinto, o "tarefa de várias instâncias" não é um tipo de tarefa exclusivo como o [StartTask][net_starttask] ou o [JobPreparationTask][net_jobprep]. A tarefa de várias instâncias é simplesmente uma tarefa do Lote standard ([CloudTask][net_task] no Lote do .NET) cujas configurações de várias instâncias foram configuradas. Neste artigo, nos referimos a isso como a **tarefa de várias instâncias**.
> 
> 

## <a name="requirements-for-multi-instance-tasks"></a>Requisitos para tarefas de várias instâncias
As tarefas de várias instâncias exigem um pool com **comunicação entre nós habilitada** e com a **execução de tarefas simultâneas desabilitada**. Se você tentar executar uma tarefa de várias instâncias em um pool com a comunicação entre nós desabilitada, ou com um valor *maxTasksPerNode* maior que 1, a tarefa nunca será agendada; ela permanecerá indefinidamente no estado "ativo". Este trecho de código mostra a criação de pool usando a biblioteca .NET do Lote.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicated: 3
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

Além disso, as tarefas de várias instâncias poderão ser executadas *somente* em nós nos **pools criados após 14 de dezembro de 2015**.

> [!TIP]
> Quando você escolher um [tamanho compatível com RDMA](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) , como o A9 para os nós de computação em seu pool do Lote, o aplicativo MPI poderá tirar proveito da rede RDMA (acesso remoto direto à memória) de alto desempenho e baixa latência do Azure. Você pode ver a lista completa de tamanhos de nós de computação disponíveis para pools do Lote em [Tamanhos para Serviços de Nuvem](../cloud-services/cloud-services-sizes-specs.md).
> 
> 

### <a name="use-a-starttask-for-mpi-application-installation"></a>Use um StartTask para instalação do aplicativo de MPI
Para executar aplicativos de MPI com uma tarefa de várias instâncias, você primeiro precisará colocar o software MPI nos nós de computação do pool. Esse é um ótimo momento para usar uma [StartTask][net_starttask], que é executada sempre que um nó ingressa em um pool ou é reiniciado. Este trecho de código cria um StartTask que especifica o pacote de instalação do MS-MPI como um [arquivo de recurso][net_resourcefile] e a linha de comando executada depois de baixar o arquivo de recurso para o nó.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    RunElevated = true,
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

> [!NOTE]
> Você não está limitado a usar o MS-MPI ao implementar uma solução MPI com tarefas de várias instâncias no Lote. Você pode usar qualquer implementação de MPI padrão que seja compatível com o sistema operacional que você especificar para os nós de computação em seu pool.
> 
> 

## <a name="create-a-multi-instance-task-with-batch-.net"></a>Criar uma tarefa de várias instâncias com o .NET do Lote
Agora que já abordamos os requisitos de pool e a instalação do pacote MPI, vamos criar a tarefa de várias instâncias. Neste trecho de código, criamos um padrão [CloudTask][net_task] e configuramos sua propriedade [MultiInstanceSettings][net_multiinstance_prop]. Conforme mencionado anteriormente, a tarefa de várias instâncias não é um tipo de tarefa distinto, mas uma tarefa do Lote Standard definida com configuração de várias instâncias.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Tarefa principal e subtarefas
Quando você cria as configurações de várias instâncias de uma tarefa, pode especificar o número de nós de computação que devem executar a tarefa. Ao enviar a tarefa a um trabalho, o serviço de Lote cria uma tarefa **principal** **subtarefas** suficientes para corresponder ao número de nós que você especificou.

Essas tarefas são atribuídas a uma ID de número inteiro no intervalo de 0 a *numberOfInstances* – 1. A tarefa com ID 0 é a tarefa principal e todas as outras IDs são subtarefas. Por exemplo, se você criar as configurações de várias instâncias abaixo para uma tarefa, a tarefa principal terá uma id 0 e as subtarefas terão ids de 1 a 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Nó mestre
Quando você envia uma tarefa de várias instâncias, o serviço de Lote designa um dos nós de computação como o "mestre" e agenda para ocorrer nele a execução a tarefa principal. Ele agenda a execução das subtarefas para ocorrer no restante dos nós alocados para a tarefa de várias instâncias.

## <a name="coordination-command"></a>comando de coordenação
O **comando de coordenação** é executado pela tarefa principal e subtarefas.

A invocação do comando de coordenação está bloqueando. O Lote não executa o comando do aplicativo até que o comando de coordenação retorne com êxito para todas as subtarefas. O comando de coordenação, portanto, deve iniciar todos os serviços em segundo plano necessários, verificar se eles estão prontos para uso e sair. Por exemplo, este comando de coordenação para uma solução com a versão 7 do MS-MPI inicia o serviço SMPD no nó e sai:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Observe o uso de `start` neste comando de coordenação. Isso é necessário porque o aplicativo `smpd.exe` não retorna imediatamente após a execução. Sem usar o comando [iniciar][cmd_start], esse comando de coordenação não retornaria e, portanto, bloquearia a execução do comando do aplicativo.

## <a name="application-command"></a>Comando de aplicativo
Depois que a tarefa principal e todas as subtarefas concluírem a execução do comando de coordenação, a linha de comando da tarefa de várias instâncias é executada *somente*pela tarefa principal. Nós o chamamos de **comando de aplicativo** para distingui-lo do comando de coordenação.

Para os aplicativos MS-MPI, use o comando de aplicativo para executar o aplicativo habilitado para MPI com `mpiexec.exe`. Por exemplo, eis um comando do aplicativo para uma solução usando o MS-MPI versão 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Como o `mpiexec.exe` do MS-MPI usa a variável `CCP_NODES` por padrão (veja [Variáveis de ambiente](#environment-variables)), a linha de comando do aplicativo de exemplo acima a exclui.
> 
> 

## <a name="environment-variables"></a>Variáveis de ambiente
O Lote cria várias [variáveis de ambiente][msdn_env_var] específicas de tarefas de várias instâncias nos nós de computação alocados em uma tarefa de várias instâncias. Suas linhas de comando de coordenação e do aplicativo podem fazer referência a essas variáveis de ambiente, assim como podem fazê-lo os scripts e programas executados por elas.

As variáveis de ambiente a seguir são criadas pelo serviço de Lote para uso por tarefas de várias instâncias:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Para obter detalhes completos sobre essas e outras variáveis de ambiente do nó de computação do Lote, incluindo seu conteúdo e visibilidade, veja [Variáveis de ambiente do nó de computação][msdn_env_var].

> [!TIP]
> O exemplo de código MPI para Linux do Lote contém um exemplo de como várias dessas variáveis de ambiente podem ser usadas. O script de Bash [coordination-cmd][coord_cmd_example] baixa arquivos de entrada e de aplicativos comuns do Armazenamento do Azure, habilita um compartilhamento de NFS (sistema de arquivos de rede) no nó mestre e configura os outros nós alocados para a tarefa de várias instâncias, como clientes NFS.
> 
> 

## <a name="resource-files"></a>Arquivos de recurso
Há dois conjuntos de arquivos de recursos a serem considerados para tarefas de várias instâncias: **arquivos de recurso comuns** que *todas* as tarefas baixam (principal e subtarefas) e **arquivos de recurso** especificados para a própria tarefa de várias instâncias, que é baixado *somente pela tarefa principal*.

Você pode especificar um ou mais **arquivos de recurso comum** nas configurações de várias instâncias de uma tarefa. Esses arquivos de recurso comum são baixados do [Armazenamento do Azure](../storage/storage-introduction.md) no **diretório compartilhado da tarefa** de cada nó pela tarefa principal e por todas as subtarefas. Você pode acessar o diretório compartilhado da tarefa das linhas de comando do aplicativo e de coordenação usando a variável de ambiente `AZ_BATCH_TASK_SHARED_DIR` . O caminho `AZ_BATCH_TASK_SHARED_DIR` é idêntico em todos os nós alocados para a tarefa de várias instâncias, assim você pode compartilhar um único comando de coordenação entre a tarefa principal e todas as subtarefas. O Lote não "compartilha" o diretório no sentido de acesso remoto, mas você pode usá-lo como um ponto de montagem ou compartilhamento conforme mencionado anteriormente na dica sobre variáveis de ambiente.

Os arquivos de recursos que você especificar para a tarefa de várias instâncias em si serão baixados para o diretório de trabalho da tarefa, `AZ_BATCH_TASK_WORKING_DIR` por padrão. Conforme mencionado, em contraste com arquivos de recursos comuns, apenas a tarefa principal baixa arquivos de recurso especificados para a tarefa de várias instâncias em si.

> [!IMPORTANT]
> Sempre use as variáveis de ambiente `AZ_BATCH_TASK_SHARED_DIR` e `AZ_BATCH_TASK_WORKING_DIR` para fazer referência a esses diretórios em suas linhas de comando. Não tente construir os caminhos manualmente.
> 
> 

## <a name="task-lifetime"></a>Tempo de vida da tarefa
O tempo de vida da tarefa principal controla o tempo de vida de toda a tarefa com várias instâncias. Quando a tarefa principal é encerrada, todas as subtarefas são encerradas. O código de saída da tarefa principal é o código de saída da tarefa e, portanto, é usado para determinar o êxito ou a falha da tarefa para fins de repetição.

Se qualquer uma das subtarefas falhar, sair com um código de retorno diferente de zero, por exemplo, toda a tarefa de várias instâncias falhará. A tarefa de várias instâncias é encerrada e repetida até o limite de repetição.

Quando você exclui uma tarefa de várias instâncias, a tarefa principal e todas as subtarefas são também excluídas pelo serviço do Lote. Todos os diretórios de subtarefa e seus arquivos serão excluídos dos nós de computação, como ocorre com uma tarefa padrão.

[TaskConstraints][net_taskconstraints] para uma tarefa de várias instâncias, por exemplo, as propriedades [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock] e [RetentionTime][net_taskconstraint_retention], são respeitadas assim como ocorreria para uma tarefa padrão e se aplicam à tarefa principal e a todas as subtarefas. No entanto, se você alterar a propriedade [RetentionTime][net_taskconstraint_retention] depois de adicionar a tarefa de várias instâncias ao trabalho, essa alteração será aplicada somente à tarefa principal. Todas as subtarefas continuam a usar o [RetentionTime][net_taskconstraint_retention] original.

A lista de tarefas recentes de um nó de computação reflete a id da subtarefa se a tarefa recente fizer parte de uma tarefa de várias instâncias.

## <a name="obtain-information-about-subtasks"></a>Obtenha informações sobre subtarefas
Para obter informações sobre as subtarefas usando a biblioteca .NET do Lote, chame o método [CloudTask.ListSubtasks][net_task_listsubtasks]. Esse método retorna informações sobre todas as subtarefas e sobre o nó de computação que executou as tarefas. Com essas informações, você pode determinar o diretório raiz de cada subtarefa, a id do pool, seu estado atual, o código de saída e muito mais. Você pode usar essas informações em combinação com o método [PoolOperations.GetNodeFile][poolops_getnodefile] para obter arquivos da subtarefa. Observe que esse método não retorna informações sobre a tarefa principal (id 0).

> [!NOTE]
> Salvo indicação em contrário, os métodos .NET do Lote que operam na própria [CloudTask][net_task] de várias instâncias se aplicam *somente* à tarefa principal. Por exemplo, quando você chama o método [CloudTask.ListNodeFiles][net_task_listnodefiles] em uma tarefa de várias instâncias, somente os arquivos da tarefa principal são retornados.
> 
> 

O trecho de código a seguir mostra como obter as informações sobre subtarefas, bem como solicitar o conteúdo do arquivo dos nós em que elas são executadas.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == TaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="next-steps"></a>Próximas etapas
* O blog da Equipe de HPC e do Lote do Azure da Microsoft discute o [suporte do MPI para Linux no Lote do Azure][blog_mpi_linux] e inclui informações sobre como usar o [OpenFOAM][openfoam] com o Lote. Você pode encontrar exemplos de código do Python para o [exemplo do OpenFOAM no GitHub][github_mpi].
* Talvez você queira criar um aplicativo simples do MS-MPI para usar ao testar as tarefas de várias instâncias no Lote. Outro artigo do blog, [Como compilar e executar um programa simples do MS-MPI][msmpi_howto], contém um passo a passo para criar um aplicativo de MPI simples usando o MS-MPI.
* Confira a página [Microsoft MPI][msmpi_msdn] no MSDN para obter as informações mais recentes sobre MS-MPI.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples/mpi
[github_samples]: https://github.com/Azure/azure-batch-samples
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Visão geral de várias instâncias"



<!--HONumber=Oct16_HO2-->


