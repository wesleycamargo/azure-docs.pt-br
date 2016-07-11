<properties
	pageTitle="Preparação do trabalho e limpeza no Lote | Microsoft Azure"
	description="Empregue tarefas de preparação de nível de trabalho para minimizar a transferência de dados para nós de computação do Lote do Azure e libere as tarefas para limpeza de nó na conclusão do trabalho."
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
	ms.date="06/22/2016"
	ms.author="marsma" />

# Executar tarefas de preparação e conclusão do trabalho em nós de computação em lotes do Azure

Os trabalhos do Lote do Azure geralmente exigem algum tipo de instalação antes da execução e, da mesma forma, algum tipo de manutenção pós-trabalho após a conclusão das tarefas do trabalho. O lote fornece mecanismos para essa preparação e manutenção na forma de tarefas opcionais de preparação do trabalho e liberação de trabalho.

Antes da execução das tarefas de um trabalho, a **tarefa de preparação do trabalho** é executada em todos os nós de computação agendados para executar tarefas. Quando o trabalho é concluído, a **tarefa de liberação do trabalho** é executada em cada nó no pool que executou pelo menos uma tarefa. Assim como acontece com tarefas normais de lote, você pode especificar uma preparação de trabalho ou liberar a linha de comando da tarefa para ser invocada quando essa tarefa for executada. Essas tarefas especiais oferecem outros recursos conhecidos como download de arquivo, execução elevada, variáveis de ambiente personalizadas, duração máxima da execução, contagem de repetição e tempo de retenção de arquivo.

Nas seções a seguir, você descobrirá como usar esses dois tipos especiais de tarefa usando as classes [JobPreparationTask][net_job_prep] e [JobReleaseTask][net_job_release] na API [.NET do Lote][api_net].

> [AZURE.TIP] As tarefas de preparação e de liberação são especialmente úteis em ambientes de "pool compartilhado" – esses ambientes em que um pool de nós de computação persiste entre as execuções de trabalho e é compartilhado entre vários trabalhos diferentes.

## Quando usar tarefas de preparação e liberação do trabalho

Qualquer momento em que você precisar preparar nós com configurações ou dados específicos aos trabalhos (e limpar ou persistir dados de resultado da tarefa) é um bom momento para usar as tarefas de preparação e liberação de trabalho. São exemplos de tais situações:

**Transferência de dados de tarefas comuns**

Os trabalhos do Lote geralmente exigem um conjunto comum de dados como entrada para as tarefas do trabalho. Por exemplo, em cálculos diários de análise de riscos, os dados de mercado são específico do trabalho, porém, comuns a todas as tarefas no trabalho. Esses dados de mercado, geralmente com vários gigabytes, devem ser baixados para cada nó de computação apenas uma vez, para que qualquer tarefa que seja executada em um nó possa usá-los. Use uma **tarefa de preparação de trabalho** para baixar os dados para cada nó antes da execução de outras tarefas do trabalho.

**Exclusão de dados do trabalho**

Em um ambiente de pool compartilhado no qual os nós de computação de um pool não são encerrados entre os trabalhos, você pode precisar excluir os dados do trabalho entre as execuções a fim de conservar espaço em disco nos nós ou atender às políticas de segurança de sua organização. Use uma **tarefa de liberação de trabalho** para excluir dados baixados por uma tarefa de preparação de trabalho ou gerados durante a execução da tarefa.

**Retenção de log**

Convém manter uma cópia dos arquivos de log gerados pelas tarefas ou talvez arquivos de despejo de memória que podem ser gerados pelos aplicativos que falharam. Use uma **tarefa de liberação de trabalho** nesses casos para compactar e carregar esses dados para uma conta de [Armazenamento do Azure][azure_storage].

## Tarefa de preparação de trabalho

Antes da execução de tarefas de um trabalho, a tarefa de preparação de trabalho é executada em cada nó de computação agendado para executar uma tarefa. Por padrão, o serviço Lote aguardará a conclusão da tarefa de preparação do trabalho antes de executar as tarefas agendadas para execução no nó. No entanto, você pode configurar o serviço para não aguardar. A tarefa de preparação do trabalho será executada novamente em um nó de computação se o nó for reiniciado, mas você também pode desativar esse comportamento.

A tarefa de preparação de trabalho é executada apenas em nós programados para executar uma tarefa. Isso impede a execução desnecessária de uma tarefa de preparação em um nó que não recebeu uma tarefa. Isso pode ocorrer quando o número de tarefas de um trabalho é menor do que o número de nós em um pool. Também se aplica quando a [execução de tarefas simultâneas](batch-parallel-node-tasks.md) é habilitada, o que deixará alguns nós ociosos se a contagem de tarefas for inferior ao total de possíveis tarefas simultâneas. A não execução da tarefa de preparação do trabalho em nós ociosos economiza encargos de transferência de dados.

> [AZURE.NOTE] [JobPreparationTask][net\_job\_prep\_cloudjob] é diferente de [CloudPool.StartTask][pool_starttask], visto que JobPreparationTask é executado no início de cada trabalho, enquanto StartTask é executado apenas quando um nó de computação ingressa em um pool pela primeira vez ou é reiniciado.

## Tarefa de liberação do trabalho

Quando um trabalho é marcado como concluído, a tarefa de liberação do trabalho é executada em cada nó no pool que executou pelo menos uma tarefa. Marcar uma tarefa como concluída emitindo uma solicitação de encerramento. Em seguida, o serviço do Lote define o estado do trabalho como *encerrando*, encerra quaisquer tarefas em execução ou ativas associadas ao trabalho e executa a tarefa de liberação do trabalho. Em seguida, o trabalho é movido para o estado *concluído*.

> [AZURE.NOTE] A exclusão do trabalho também executa a tarefa de liberação do trabalho. No entanto, se um trabalho já tiver sido encerrado, a tarefa de liberação não será executada uma segunda vez quando o trabalho for excluído posteriormente.

## Tarefas de preparação e liberação de trabalho com o Lote .NET

Para usar uma tarefa de preparação de trabalho, crie e configure um objeto [JobPreparationTask][net_job_prep] e atribua-o à propriedade [CloudJob.JobPreparationTask][net_job_prep_cloudjob] do seu trabalho. Da mesma forma, inicialize uma [JobReleaseTask][net_job_release] e atribua-a à propriedade [CloudJob.JobReleaseTask][net_job_prep_cloudjob] do seu trabalho para definir a tarefa de liberação do trabalho.

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

Conforme mencionado acima, a tarefa de liberação é executada quando um trabalho é encerrado ou excluído. O encerramento de um trabalho com a API .NET do Lote é realizado ao chamar [JobOperations.TerminateJobAsync][net_job_terminate]. A exclusão do trabalho é realizada com [JobOperations.DeleteJobAsync][net_job_delete]. Ambas as ações geralmente são executadas quando as tarefas de um trabalho são concluídas ou quando um tempo limite que você definiu é atingido.

		// Terminate the job to mark it as Completed; this will initiate the Job Release Task on any node
		// that executed job tasks. Note that the Job Release Task is also executed when a job is deleted,
		// thus you need not call Terminate if you typically delete your jobs upon task completion.
		await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");

## Exemplo de código no GitHub

Confira o projeto de exemplo de [JobPrepRelease][job_prep_release_sample] no GitHub para verificar as tarefas de preparação e liberação do trabalho em ação. Esse aplicativo de console faz o seguinte:

1. Cria um pool com dois nós "pequenos".
2. Cria um trabalho com tarefas de preparação, de liberação e padrão de trabalho.
3. Executa a tarefa de preparação de trabalho, que primeiro grava a ID do nó em um arquivo de texto no diretório "compartilhado" do nó.
4. Executa uma tarefa em cada nó que grava sua ID de tarefa no mesmo arquivo de texto.
5. Depois que todas as tarefas forem concluídas (ou que o tempo limite for atingido), imprime o conteúdo do arquivo de texto de cada nó no console.
6. Quando o trabalho é concluído, executa a tarefa de liberação de trabalho para excluir o arquivo do nó.
6. Imprime os códigos de saída das tarefas de preparação e liberação de trabalho para cada nó no qual elas são executadas.
7. Pausa a execução para permitir a confirmação da exclusão do trabalho e/ou pool.

A saída do aplicativo de exemplo é semelhante ao seguinte:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

>[AZURE.NOTE] Devido ao tempo variável de criação e início dos nós em um novo pool (alguns nós ficam prontos para tarefas antes de outros), você pode ver uma saída diferente. Especificamente, como as tarefas são concluídas rapidamente, um dos nós do pool pode executar todas as tarefas do trabalho. Se isso ocorrer, você observará que as tarefas de preparação e liberação do trabalho não existem para o nó que não executou nenhuma tarefa.

### Inspecionar as tarefas de preparação e liberação do trabalho no Portal do Azure

Quando estiver executando o aplicativo de exemplo acima, você pode usar o [portal do Azure][portal] para exibir as propriedades do trabalho e suas tarefas, ou até mesmo baixar o arquivo de texto compartilhado que é modificado pelas tarefas do trabalho.

A captura de tela abaixo mostra a **Folha de tarefas de preparação** no portal do Azure após uma execução do aplicativo de exemplo. Navegue até as propriedades *JobPrepReleaseSampleJob* após a conclusão das tarefas (mas antes de excluir seu trabalho e pool) e clique em **Tarefas de preparação** ou **Tarefas de liberação** para exibir suas propriedades.

![Propriedades de preparação de trabalho no portal do Azure][1]

## Próximas etapas

### Pacotes de aplicativos

Além da tarefa de preparação de trabalho, você também pode usar o recurso de [pacotes de aplicativos](batch-application-packages.md) do Lote para preparar nós de computação para execução da tarefa. Esse recurso é especialmente útil para implantação de aplicativos que não exigem a execução de um instalados, aplicativos que contêm muitos arquivos (mais de 100) ou aplicativos que exigem um controle de versão estrito.

### Instalação de aplicativos e o preparo de dados

Confira a postagem [Installing applications and staging data on Batch compute nodes (Instalação de aplicativos e o preparo de dados em nós de computação do Batch)][forum_post] postagem no Fórum do Lote do Azure para ter uma visão geral dos vários métodos de preparo de seus nós para execução de tarefas. Escrita por um dos membros da equipe do Lote do Azure, esta postagem traz uma bos descrição geral das diferentes maneiras de levar os arquivos (incluindo dados de entrada de tarefas e aplicativos) aos seus nós de computação, bem como e algumas considerações especiais para levar em conta com relação a cada método.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/pt-BR/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net\_job\_prep\_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
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

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png

<!---HONumber=AcomDC_0629_2016-->