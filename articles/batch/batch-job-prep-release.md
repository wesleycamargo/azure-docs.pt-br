<properties
	pageTitle="Preparação do trabalho e limpeza no Lote | Microsoft Azure"
	description="Empregue tarefas de preparação de nível de trabalho para minimizar a transferência de dados para nós de computação do Lote do Azure e libere as tarefas para limpeza de nó na conclusão do trabalho."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="10/15/2015"
	ms.author="v-marsma"/>

# Executar tarefas de preparação e conclusão do trabalho em nós de computação em lotes do Azure

Os trabalhos em lote do Azure geralmente exigem algum tipo de instalação antes da execução e, da mesma forma, algum tipo de manutenção pós-trabalho depois da conclusão das tarefas do trabalho. O lote fornece mecanismos para essa preparação e manutenção na forma de tarefas opcionais de *preparação do trabalho* e *liberação de trabalho*.

A tarefa de preparação de trabalho é executada em todos os nós computados e agendados para execução de tarefas, antes de qualquer uma das tarefas do trabalho. Quando o trabalho for concluído, a tarefa de liberação do trabalho será executada em cada nó no pool que tiver executado pelo menos uma tarefa. As tarefas de preparação e liberação de trabalho permitem que você especifique uma linha de comando para ser executada quando a tarefa é invocada e oferecem recursos como o download do arquivo, a execução privilegiada, variáveis de ambiente personalizadas, duração de execução máxima, contagem de repetição e tempo de retenção de arquivo.

Nas seções a seguir, você descobrirá como usar esses dois tipos especiais de tarefas usando o [JobPreparationTask][net_job_prep] e [JobReleaseTask][net_job_release] n a API [.NET de lote][api_net].

> [AZURE.TIP]Tarefas de preparação e liberação são especialmente úteis em ambientes de "pool compartilhado" – esses ambientes em que um pool de nós de computação persiste entre as execuções de trabalho e é compartilhado entre vários trabalhos diferentes.

## Quando usar tarefas de preparação e liberação do trabalho

Uma série de situações se beneficiam de tarefas de preparação e liberação do trabalho. Veja abaixo algumas delas:

- **Transferência de dados de tarefa comuns** - trabalhos em lotes geralmente exigem um conjunto comum de dados como entrada para tarefas do trabalho. Por exemplo, em cálculos de análise de riscos diárias, dados de mercado são específico do trabalho, e ainda comuns a todas as tarefas no trabalho. Esses dados de mercado, geralmente vários gigabytes de tamanho, devem ser baixados para cada nó de computação apenas uma vez para que cada tarefa que seja executada em um nó possa usá-lo. Use uma *tarefa de preparação de trabalho* para baixar os dados para cada nó antes da execução de outras tarefas.
- **Exclusão de dados de trabalho** – em um ambiente de pool compartilhado no qual nós de computação do pool não são encerrados entre os trabalhos, excluir o trabalho de dados entre as execuções pode ser necessário para conservar espaço em disco em nós ou para satisfazer as políticas de segurança de uma organização. Use uma *tarefa de liberação de trabalho* para excluir dados baixados por uma tarefa de preparação de trabalho ou gerados durante a execução da tarefa.
- **Retenção de log** - convém manter uma cópia dos arquivos de log gerados pelas tarefas ou talvez arquivos de despejo corrompidos gerados por aplicativos que falharam. Use uma *tarefa de liberação de trabalho* nesses casos para compactar e carregar esses dados para uma conta de [Armazenamento do Azure][azure_storage].

## Tarefa de preparação de trabalho

Antes da execução de tarefas de um trabalho, a tarefa de preparação de trabalho é executada em cada nó de computação agendado para executar uma tarefa. Por padrão, o serviço de lote aguardará a tarefa de preparação do trabalho ser concluída antes de executar as tarefas agendadas no nó, mas você pode configurar o serviço para não esperar. A tarefa de preparação do trabalho será executada novamente em um nó de computação se o nó for reiniciado, mas você também pode desativar esse comportamento.

A tarefa de preparação de trabalho é executada apenas em nós programados para executar uma tarefa. Isso impede a execução desnecessária de uma tarefa de preparação que um nó não deveria ter atribuído uma tarefa, proporcionando economia nas cobranças de transferência de dados, por exemplo. Essa situação se aplica quando o número de tarefas de um trabalho é menor que o número de nós em um pool ou quando [a execução de tarefas simultâneas](batch-parallel-node-tasks.md) estiver habilitada, deixando alguns nós ociosos se a contagem de tarefas é menor do que o total de tarefas simultâneas possíveis.

> [AZURE.NOTE]A [JobPreparationTask][net_job_prep_cloudjob] é diferente da [CloudPool.StartTask][pool_starttask], a JobPreparationTask é executada no início de cada trabalho, enquanto a StartTask é executada somente quando um nó de computação ingressa pela primeira vez em um pool ou é reiniciado.

## Tarefa de liberação do trabalho

Quando um trabalho for concluído, a tarefa de liberação do trabalho é executada em cada nó no pool que executou pelo menos uma tarefa. Marcar uma tarefa como concluída emitindo uma solicitação de encerramento. Em seguida, o serviço de lote define o estado do trabalho como *encerrando*, encerra quaisquer tarefas em execução ou ativas associadas ao trabalho e executa a tarefa de liberação do trabalho. Em seguida, o trabalho é movido para o estado *concluído*.

> [AZURE.NOTE]A exclusão do trabalho também executa a tarefa de liberação do trabalho. No entanto, se um trabalho foi encerrado anteriormente, a tarefa de liberação não é executada uma segunda vez quando o trabalho é excluído posteriormente.

## Quando usar tarefas de preparação e liberação de trabalho no Lote .NET

A especificação de uma tarefa de preparação do trabalho é feita criando e configurando um [JobPreparationTask][net_job_prep] e atribuindo-o à propriedade [CloudJob.JobPreparationTask][net_job_prep_cloudjob] de seu trabalho. Da mesma forma, inicializar um [JobReleaseTask][net_job_release] e atribuí-lo ao seu trabalho [CloudJob.JobReleaseTask][net_job_prep_cloudjob] propriedade para definir a tarefa de lançamento do trabalho.

Neste trecho de código, `myBatchClient` é uma instância completamente inicializada de [BatchClient][net_batch_client], e `myPool` é um pool existente dentro da conta do lote.

		// Create the CloudJob for CloudPool "myPool"
		CloudJob myJob = myBatchClient.JobOperations.CreateJob("JobPrepReleaseSampleJob",
															   new PoolInformation() { PoolId = "myPool" });

		// Specify the command lines for the job preparation and release tasks
		string jobPrepCmdLine = "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
		string jobReleaseCmdLine = "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

		// Assign the job preparation task to the job
		myJob.JobPreparationTask = new JobPreparationTask { CommandLine = jobPrepCmdLine };

		// Assign the job release task to the job
		myJob.JobReleaseTask = new JobPreparationTask { CommandLine = jobReleaseCmdLine };

		await myJob.CommitAsync();

Conforme mencionado acima, a tarefa de liberação é executada quando um trabalho é encerrado ou excluído. O encerramento de um trabalho com a API .NET do Lote é executado chamando [PoolOperations.TerminateJobAsync][net_job_terminate], e a exclusão do trabalho é executada com [PoolOperations.DeleteJobAsync][net_job_delete], ambas são normalmente executadas quando tarefas de um trabalho foram concluídas ou um tempo limite definido foi atingido.

		// Terminate the job to mark it as Completed; this will initiate the Job Release Task on any node
		// that executed job tasks. Note that the Job Release Task is also executed when a job is deleted,
		// thus you need not call Terminate if you typically delete your jobs upon task completion.
		await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");

## Próximas etapas

### Projeto de exemplo no GitHub

Confira o projeto de exemplo de [JobPrepRelease][job_prep_release_sample] no GitHub para verificar as tarefas de preparação e liberação do trabalho em ação. Esse aplicativo de console faz o seguinte:

1. Cria um pool com dois nós "pequenos"
2. Cria um trabalho com tarefas de trabalho de preparação, liberação e padrão
3. Executa a tarefa de preparação do trabalho que primeiro grava a ID do nó em um arquivo de texto no diretório "compartilhado" do nó
4. Executa uma tarefa em cada nó que grava sua ID de tarefa para o mesmo arquivo de texto
5. Depois de concluir todas as tarefas (ou o tempo limite ser atingido), imprima o conteúdo do arquivo de texto de cada nó no console
6. Quando o trabalho estiver concluído, execute a tarefa de lançamento do trabalho para excluir o arquivo do nó
6. Imprime os códigos de saída das tarefas de preparação e liberação do trabalho para cada nó no qual elas são executadas
7. Pausa a execução para permitir que a confirmação da exclusão do trabalho e/ou do pool

A saída do aplicativo de exemplo é semelhante ao seguinte:

```
Attempting to create pool: JobPrepReleaseSamplePool
The pool already existed when we tried to create it
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-3105992504_1-20151015t150030z:
-------------------------------------------
tvm-3105992504_1-20151015t150030z tasks:
  task001
  task002
  task006
  task007

Contents of shared\job_prep_and_release.txt on tvm-3105992504_2-20151015t150030z:
-------------------------------------------
tvm-3105992504_2-20151015t150030z tasks:
  task003
  task005
  task004
  task008

Waiting for job JobPrepReleaseSampleJob to reach state Completed
....

tvm-3105992504_1-20151015t150030z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-3105992504_2-20151015t150030z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
no

Sample complete, hit ENTER to exit...
```

### Inspecionar as tarefas de preparação e liberação do trabalho com o Gerenciador do Lote

O [Gerenciador do Lote][batch_explorer_article], também encontrado no [repositório de código de exemplo do lote][batch_explorer_project] no GitHub, é uma excelente ferramenta a ser usada no desenvolvimento de soluções com o Lote do Azure. Ao executar o aplicativo de exemplo acima, por exemplo, você pode usar o Gerenciador do Lote para exibir as propriedades do trabalho e suas tarefas, ou até mesmo fazer download do arquivo de texto compartilhado modificado por tarefas do trabalho.

A captura de tela abaixo destaca as propriedades das tarefas de preparação e liberação de trabalho que aparecem no painel Detalhes do Trabalho quando o trabalho *JobPrepReleaseSampleJob* está selecionado na guia Trabalhos.

![Gerenciador do Lote][1]

*Captura de tela do Gerenciador do Lote mostrando tarefas de preparação e liberação do trabalho*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_explorer_article]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/batchexplorer-01.png
[2]: ./media/batch-job-prep-release/batchexplorer-02.png

<!---HONumber=Nov15_HO1-->