<properties
	pageTitle="Consultas de lista eficientes no lote do Azure | Microsoft Azure"
	description="Aumente o desempenho reduzindo a quantidade de dados retornados ao consultar entidades do Lote do Azure, como pools, trabalhos, tarefas e nós de computação."
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
	
# Consultar o serviço do Lote do Azure com eficiência

Neste artigo, você aprenderá a aumentar o desempenho de seu aplicativo do Lote do Azure reduzindo a quantidade de dados retornados durante uma consulta ao serviço de Lote usando a biblioteca [.NET do Lote][api_net].

O Lote do Azure oferece funcionalidades de computação em grande escala e, em um ambiente de produção, entidades como trabalhos, tarefas e nós de computação podem chegar a milhares. Obter informações sobre esses itens pode, portanto, gerar uma grande quantidade de dados que devem ser transferidos do serviço para seu aplicativo em cada consulta. Ao limitar o número de itens e o tipo de informações retornadas para cada um deles, você poderá aumentar a velocidade de suas consultas e, portanto, o desempenho de seu aplicativo.

Praticamente todo aplicativo que usa o Lote do Azure executará algum tipo de monitoramento ou outra operação que consulta o serviço de Lote, geralmente em intervalos regulares. Por exemplo, para determinar a capacidade e o status de um pool, você deve consultar todos os nós no pool. Para determinar se qualquer uma das tarefas de um trabalho ainda estão em fila, você deve consultar cada tarefa do trabalho. Este artigo explica como executar esses tipos de consultas da forma mais eficiente.

Esse trecho de código da API [.NET do Lote][api_net] recupera todas as tarefas associadas a um trabalho, juntamente com o conjunto *completo* de propriedades das tarefas:

```
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks = batchClient.JobOperations.ListTasks("job-001");
```

No entanto, uma consulta de lista muito mais eficiente pode ser executada. Faça isso fornecendo um objeto [ODATADetailLevel][odata] para o método [JobOperations.ListTasks][net_list_tasks]. Este trecho de código retorna apenas a ID, a linha de comando e as propriedades de informações do nó de computação das tarefas concluídas:

```
// Configure an ODATADetailLevel specifying a subset of tasks and their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks = batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

No cenário do exemplo acima, se houver milhares de tarefas no trabalho, os resultados da segunda consulta serão retornados normalmente muito mais rapidamente do que os da primeira. Mais informações sobre como usar ODATADetailLevel ao listar itens com a API de Lote .NET são apresentadas abaixo.

> [AZURE.IMPORTANT]
Recomendamos que você *sempre* forneça um objeto ODATADetailLevel para as chamadas de lista de API .NET para garantir a máxima eficiência e desempenho do seu aplicativo. Ao especificar um nível de detalhe, você ajuda a reduzir os tempos de resposta do serviço Lote, a melhorar a utilização da rede e a minimizar o uso da memória por aplicativos clientes.

## Ferramentas de consulta eficiente

As APIs [.NET do Lote][api_net] e [REST do Lote][api_rest] permitem reduzir o número de itens retornados em uma lista e a quantidade de informações retornadas por cada um. Faça isso especificando as cadeias de caracteres **filter**, **select** e **expand** ao executar consultas de lista.

### Filtro
A cadeia de caracteres filter é uma expressão que reduz o número de itens retornados. Por exemplo, liste somente as tarefas em execução para um trabalho, ou liste apenas nós de computação que estejam prontos para executar tarefas.

- A cadeia de caracteres filter consiste em uma ou mais expressões, em que uma expressão consiste em um nome de propriedade, um operador e um valor. As propriedades que podem ser especificadas são específicas a cada tipo de entidade consultado, assim como os operadores com suporte para cada propriedade.
- Várias expressões podem ser combinadas usando operadores lógicos `and` e `or`.
- Esse exemplo de cadeia de caracteres filter lista apenas tarefas de “renderização” em execução: `(state eq 'running') and startswith(id, 'renderTask')`.

### Selecionar
A cadeia de caracteres select limita os valores de propriedade retornados para cada item. Especifique uma lista de nomes de propriedade e apenas os valores dessas propriedades retornarão para os itens nos resultados da consulta.

- A cadeia de caracteres de seleção consiste em uma lista separada por vírgulas de nomes de propriedade. Você pode especificar qualquer uma das propriedades para o tipo de entidade que você está consultando.
- Esse exemplo de cadeia de caracteres select especifica que apenas três propriedades devem retornar para cada tarefa: `id, state, stateTransitionTime`

### Expanda
A cadeia de caracteres expand reduz o número de chamadas de API necessárias para obter determinadas informações. Quando você usa uma cadeia de caracteres expand, é possível obter mais informações sobre cada item com uma única chamada de API. Em vez de obter primeiro a lista de entidades para depois solicitar informações para cada item na lista, você pode usar uma cadeia de caracteres expand para obter as mesmas informações em uma única chamada de API. Menos chamadas de API significam um desempenho melhor.

- Assim como a cadeia de caracteres de seleção, a cadeia de caracteres de expansão controla se determinados dados são incluídos nos resultados da consulta de lista.
- A cadeia de caracteres de expansão tem suporte apenas quando ela é usada na listagem de trabalhos, agendas de trabalho, tarefas e pools. Atualmente, dá suporte apenas a informações de estatísticas.
- Quando todas as propriedades forem necessárias e nenhuma cadeia de seleção tiver sido especificada, a cadeia de expansão *terá* que ser usada para obter informações estatísticas. Se uma cadeia de caracteres de seleção for usada para obter um subconjunto de propriedades, `stats` pode ser especificado na cadeia de caracteres de seleção, e a cadeia de caracteres de expansão não precisa ser especificada.
- Esse exemplo de cadeia de caracteres expand especifica que as informações de estatísticas devem retornar para cada item na lista: `stats`

> [AZURE.NOTE] Ao criar qualquer um dos três tipos de cadeias de caracteres de consulta (filter, select e expand), você deve garantir que os nomes de propriedade e caso correspondam aos seus equivalentes de elemento da API REST. Por exemplo, ao trabalhar com a classe [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) do .NET, você deve especificar **state** em vez de **State**, embora a propriedade .NET seja [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Consulte as tabelas abaixo para ver mapeamentos de propriedade entre o .NET e APIs REST.

### Especificações das cadeia de caracteres de filtro, seleção e expansão

- As propriedades especificadas nas cadeias de caracteres de filtro, seleção e expansão correspondem aos nomes de propriedades que aparecem na API [REST do Lote][api_rest], mesmo ao usar a biblioteca [.NET do Lote][api_net].
- Todos os nomes de propriedade diferenciam maiúsculas de minúsculas, mas valores de propriedade não diferenciam maiúsculas de minúsculas
- As cadeias de caracteres de data/hora podem ser de dois formatos e devem ser precedidas por `DateTime`
  - Exemplo de formato W3C-DTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - Exemplo de formato RFC 1123: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`.
- Cadeias de caracteres boolianas são `true` ou `false`
- Se uma propriedade ou operador inválida forem especificados, o resultado será um erro `400 (Bad Request)`

## Consulta eficiente no .NET de Lote

Dentro da API[.NET do Lote][api_net], a classe [ODATADetailLevel][odata] é usada para fornecimento de cadeias de caracteres filter, select e expand para operações de lista. A classe ODataDetailLevel tem três propriedades de cadeia de caracteres públicas que podem ser especificadas no construtor ou definidas diretamente. Em seguida, você passa o objeto ODataDetailLevel como um parâmetro para as várias operações de lista, por exemplo, [ListPools][net_list_pools], [ListJobs][net_list_jobs] e [ListTasks][net_list_tasks].

- [ODATADetailLevel.FilterClause][odata_filter]\: limita o número de itens retornados.
- [ODATADetailLevel.SelectClause][odata_select]\: especifica quais valores de propriedade retornam com cada item.
- [ODATADetailLevel.ExpandClause][odata_expand]\: recupera dados de todos os itens em uma única chamada de API, em vez de chamadas separadas para cada item.

O trecho de código abaixo usa a API de Lote .NET para consultar o serviço de Lote com eficiência a fim de obter as estatísticas de um conjunto específico de pools. Nesse cenário, o usuário de Lote tem grupos de teste e de produção. As IDs do pool de teste são prefixadas com "test", e as IDs do grupo de produção são prefixadas com "prod". No trecho de código, *myBatchClient* é uma instância inicializada apropriadamente da classe [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient).

```
// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
// clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned by using a
// FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to limit the
// properties that are returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned by specifying the
// detail level that we configured above
List<CloudPool> testPools = await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [AZURE.TIP] Uma instância de [ODATADetailLevel][odata] configurada com as cláusulas Select e Expand também pode ser passada para os métodos Get apropriados, como [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), para limitar a quantidade de dados retornados.

## Mapeamentos REST Batch para API .NET

Os nomes de propriedade nas cadeias de caracteres de filtro, seleção e expansão *devem* refletir os seus equivalentes da API REST, no nome e no caso. As tabelas a seguir fornecem os mapeamentos entre os equivalentes API .NET e REST.

### Mapeamentos para cadeias de caracteres de filtro

- **MÉTODOS DE LISTA .NET** - cada um dos métodos da API .NET nesta coluna aceitam um objeto [ODATADetailLevel][odata] como um parâmetro.
- **Solicitações de lista REST** - cada página de API REST associada a essa coluna contém uma tabela especificando as propriedades e operações permitidas em cadeias de caracteres de *filtro*. Você usará esses nomes e operações de propriedades ao construir uma cadeia de caracteres [ODATADetailLevel.FilterClause][odata_filter].

| Métodos de lista .NET | Solicitações de lista REST |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [Lista os certificados em uma conta][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [Lista os arquivos associados a uma tarefa][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [Listar o status das tarefas de preparação e liberação de trabalho para um determinado trabalho][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [Listar os trabalhos em uma conta][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [Lista os arquivos em um nó][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [Lista as tarefas associadas a um trabalho][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [Lista os planos de trabalho em uma conta][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [Lista os trabalhos associados a um plano de trabalho][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [Listar os nós de computação em um pool][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [Listar os pools em uma conta][rest_list_pools]

### Mapeamentos para cadeias de caracteres de seleção

- **Tipos de .NET do Lote** - tipos de API .NET do Lote
- **Entidades da API REST** - cada página nesta coluna contém uma ou mais tabelas que listam os nomes de propriedade da API REST para o tipo. Esses nomes de propriedade são usados ao construir cadeias de caracteres *select*. Você usará esses mesmos nomes de propriedade ao construir uma cadeia de caracteres [ODATADetailLevel.SelectClause][odata_select].

| Tipos de Lote .NET | Entidades da API REST |
|---|---|
| [Certificado][net_cert] | [Obter informações sobre um certificado][rest_get_cert] |
| [CloudJob][net_job] | [Obter informações sobre um trabalho][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [Obter informações sobre um plano de trabalho][rest_get_schedule] |
| [ComputeNode][net_node] | [Obter informações sobre um nó][rest_get_node] |
| [CloudPool][net_pool] | [Obter informações sobre um pool][rest_get_pool] |
| [CloudTask][net_task] | [Obter informações sobre uma tarefa][rest_get_task] |

### Exemplo: construindo uma cadeia de caracteres de filtro

Ao construir uma cadeia de caracteres filter para um [ODATADetailLevel.FilterClause][odata_filter], consulte a tabela acima em “Mapeamentos para cadeias de caracteres filter” a fim de localizar a página de documentação da API REST correspondente à operação de lista que você deseja executar. Você encontrará as propriedades e os operadores com suporte na primeira tabela com várias linhas nessa página. Se desejar recuperar todas as tarefas cujo código de saída era diferente de zero, por exemplo, essa linha em [Lista as tarefas associadas a um trabalho][rest_list_tasks] especifica a cadeia de caracteres de propriedade aplicável e operadores permitidos:

| Propriedade | Operações permitidas | Tipo |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

Assim, a cadeia de caracteres de filtro para listar todas as tarefas com um código de saída diferente de zero deve ser:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

### Exemplo: construir uma cadeia de caracteres de seleção

Para construir um [ODATADetailLevel.SelectClause][odata_select], consulte a tabela acima em Mapeamentos para cadeias de caracteres de seleção e navegue até a página da API REST correspondente ao tipo de entidade listada. Você encontrará as propriedades selecionáveis e os operadores com suporte na primeira tabela de várias linha nessa página. Se quiser recuperar apenas a ID e a linha de comando de cada tarefa em uma lista, por exemplo, você encontrará essas linhas na tabela aplicável em [Obter informações sobre uma tarefa][rest_get_task]\:

| Propriedade | Tipo | Observações |
| :--- | :--- | :--- |
| `id` | `String` | `The ID of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

A cadeia de caracteres de seleção para incluir somente a ID e a linha de comando com cada tarefa listada seria:

`id, commandLine`

## Próximas etapas

Confira o exemplo de projeto [EfficientListQueries][efficient_query_sample] no GitHub para ver como uma consulta de lista eficaz pode afetar o desempenho de um aplicativo. Esse aplicativo de console em C# cria e adiciona um grande número de tarefas a um trabalho. Em seguida, ele faz várias chamadas para o método [JobOperations.ListTasks][net_list_tasks] e passa objetos [ODATADetailLevel][odata] que são configurados com valores de propriedade diferentes para variar a quantidade de dados a serem retornados. Ele produz uma saída semelhante à seguinte:

		Adding 5000 tasks to job jobEffQuery...
		5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

		4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
		0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
		59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
		5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
		5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
		5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

		Sample complete, hit ENTER to continue...

Como é mostrado nas informações de tempo decorrido, você pode diminuir bastante os tempos de resposta limitando as propriedades e o número de itens que são retornados. Você pode encontrar esse e outros exemplos de projetos no repositório [azure-batch-samples][github_samples] no GitHub.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

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

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

<!---HONumber=AcomDC_0128_2016-->