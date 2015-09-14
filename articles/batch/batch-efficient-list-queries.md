<properties
	pageTitle="Consultas de lista eficientes no lote do Azure | Microsoft Azure"
	description="Aprenda a reduzir a quantidade de dados retornados e aumentar o desempenho ao consultar trabalhos, tarefas, nós de computação, pools do Lote do Azure e muito mais."
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="Batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="08/27/2015"
	ms.author="davidmu;v-marsma"/>

# Consultas de lista de lote eficientes

O Lote do Azure é computação em grande escala e, em um ambiente de produção, entidades como trabalhos, tarefas e nós de computação podem chegar a milhares. Obter informações sobre esses itens pode, portanto, gerar uma grande quantidade de dados que devem ser transferidos em cada consulta. Limitar o número de itens e o tipo de informações retornadas para cada um deles aumentará a velocidade de suas consultas e, portanto, o desempenho do seu aplicativo.

Os métodos de API [.NET de Lote](https://msdn.microsoft.com/library/azure/mt348682.aspx) a seguir são exemplos de operações que praticamente todos os aplicativos que usam o Lote do Azure devem executar, geralmente com frequência:

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx)
- [ListComputeNodes](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx)

O monitoramento é um caso de uso comum; determinar a capacidade e o status de um pool requer que todos os nós de computação (VMs) em um pool sejam consultados, por exemplo. Outro exemplo é consultar as tarefas de um trabalho para determinar se alguma dessas tarefas ainda está na fila. Em alguns casos, um conjunto avançado de dados é necessário, mas em outros, será necessária apenas uma contagem do número total de itens ou de uma coleção de itens em um determinado estado.

É importante observar que ambos o número de itens retornados e o tamanho dos dados necessários para representar esses itens podem ser muito grandes. Simplesmente consultar muitos itens que resultam em respostas grandes pode levar a uma série de problemas:

- Os tempos de resposta da API do Batch podem se tornar muito lentos. Quanto maior o número de itens maior o tempo de consulta necessário para o serviço em Lotes. Grandes números de itens devem ser divididos em partes e, portanto, é possível que a biblioteca do cliente precise fazer várias chamadas à API para o serviço a fim de obter todos os itens de uma única lista.
- O processamento da API pelo aplicativo que chama o Lote levará mais tempo conforme houver mais itens a serem processados.
- Mais memória será consumida pelo aplicativo que chama o Lote quando há mais itens e/ou itens maiores.
- Mais itens e/ou itens maiores resultarão em maior tráfego de rede. Isso levará mais tempo para transferir e, dependendo da arquitetura do aplicativo, pode resultar em cobranças de rede maiores para dados transferidos fora da região da conta do Batch.

Para todas as APIs de Lote, o seguinte se aplica:

- Cada nome de propriedade é uma cadeia de caracteres que é mapeada para a propriedade do objeto
- Todos os nomes de propriedade diferenciam maiúsculas de minúsculas, mas valores de propriedade não diferenciam maiúsculas de minúsculas
- Maiúsculas e minúsculas e os nomes de propriedades são do modo que os elementos aparecem na API REST de Lote
- Cadeias de caracteres de data/hora podem ser especificadas em um de dois formatos e precisam ser precedidas de DateTime
	- W3CDTF (por exemplo, *creationTime gt DateTime'2011-05-08T08:49:37Z'*)
	- RFC1123 (por exemplo, *creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'*)
- Cadeias de caracteres boolianas são "verdadeiro" ou "falso"
- Especificar uma propriedade ou operador inválido resultará em um erro "400 (Solicitação Incorreta)"

## Consulta eficiente no .NET de Lote

A API .NET de Lote fornece a capacidade de reduzir o número de itens retornados em uma lista, bem como de reduzir a quantidade de informações retornadas para cada item, especificando o [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) de uma consulta. DetailLevel é uma classe base abstrata, e um objeto [ODATADetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx) realmente precisa ser criado e passado como o parâmetro para os métodos apropriados.

Um objeto ODataDetailLevel tem três propriedades de cadeia de caracteres públicas que podem ser especificadas no construtor ou definidas diretamente:

- [FilterClause](#filter) – filtrar e possivelmente reduzir o número de itens retornados
- [SelectClause](#select) – especificar um subconjunto de valores de propriedade a serem retornados para cada item, reduzindo o tamanho do item e da resposta
- [ExpandClause](#expand) – retornar todos os dados necessários em uma única chamada em vez de várias chamadas

> [AZURE.TIP]Uma instância de DetailLevel configurada com as cláusulas Select e Expand também pode ser passada para os métodos Get apropriados como [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx) para limitar a quantidade de dados retornados.

### <a id="filter"></a> FilterClause

O número de itens retornados pode ser reduzido por uma cadeia de caracteres de filtro. Um ou mais valores de propriedade com qualificadores podem ser especificados para garantir que apenas os itens relevantes à sua consulta sejam retornados. Por exemplo, talvez você queira listar somente as tarefas em execução para um trabalho, ou listar apenas os nós de computação que estão prontos para executar tarefas.

 Um [FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) é uma cadeia de caracteres que consiste em uma ou mais expressões, em que uma expressão consiste em um *nome de propriedade*, um *operador* e um *valor*. As propriedades que podem ser especificadas são específicas para cada chamada de API, assim como os operadores com suporte para cada propriedade. Várias expressões podem ser combinadas usando operadores lógicos **and** e **or**.

Por exemplo, essa cadeia de caracteres de filtro retorna apenas tarefas em execução cujo *displayName* começa com "MyTask":

	startswith(displayName, 'MyTask') and (state eq 'Running')

Cada artigo de API REST de Lote abaixo contém uma tabela especificando as propriedades com suporte e operações nessas propriedades para as diferentes operações de lista.

- [Listar os pools em uma conta](https://msdn.microsoft.com/library/azure/dn820101.aspx)
- [Listar os nós de computação em um pool](https://msdn.microsoft.com/library/azure/dn820159.aspx)
- [Listar os trabalhos em uma conta](https://msdn.microsoft.com/library/azure/dn820117.aspx)
- [Listar o status das tarefas de preparação e liberação de trabalho para um determinado trabalho](https://msdn.microsoft.com/library/azure/mt282170.aspx)
- [Lista os planos de trabalho em uma conta](https://msdn.microsoft.com/library/azure/mt282174.aspx)
- [Lista os trabalhos associados a um plano de trabalho](https://msdn.microsoft.com/library/azure/mt282169.aspx)
- [Lista as tarefas associadas a um trabalho](https://msdn.microsoft.com/library/azure/dn820187.aspx)
- [Lista os arquivos associados a uma tarefa](https://msdn.microsoft.com/library/azure/dn820142.aspx)
- [Lista os certificados em uma conta](https://msdn.microsoft.com/library/azure/dn820154.aspx)
- [Lista os arquivos em um nó](https://msdn.microsoft.com/library/azure/dn820151.aspx)

> [AZURE.IMPORTANT]Ao especificar propriedades em qualquer um dos três tipos de cláusula, certifique-se de que o nome da propriedade e as maiúsculas e minúsculas correspondem àqueles das suas contrapartes de elementos da API REST de Lote. Por exemplo, ao trabalhar com o [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) do .NET, você deve especificar **state** em vez de **State**, embora a propriedade .NET seja [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Para verificar o nome e as maiúsculas e minúsculas apropriados para a propriedade **state**, por exemplo, você deve verificar o nome do elemento em [Obter informações sobre uma tarefa](https://msdn.microsoft.com/library/azure/dn820133.aspx), na documentação da API REST de Lote.

### <a id="select"></a> SelectClause

Os valores de propriedade retornados para cada item podem ser limitados por meio de uma cadeia de caracteres de seleção. Uma lista de propriedades de um item pode ser especificada e, em seguida, somente esses valores de propriedade serão retornados.

Um [SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) é uma cadeia de caracteres que consiste em uma lista separada por vírgulas de nomes de propriedade. Qualquer combinação de propriedades disponíveis para um item retornado por uma operação de lista pode ser especificada.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

O número de chamadas à API pode ser reduzido com uma cláusula de expansão. Informações mais detalhadas sobre cada item de lista podem ser obtidas com uma única chamada à API, em vez de obter a lista e, em seguida, fazer uma chamada para cada item na lista.

Uma [ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) é semelhante à cláusula select, no sentido em que controla se determinados dados são retornados nos resultados. A cláusula expand tem suporte apenas para lista de trabalhos, lista de tarefas e lista de pools; atualmente, ela dá suporte apenas a informações estatísticas. Quando todas as propriedades forem necessárias e nenhuma cláusula select tiver sido especificada, a cláusula de expansão deverá ser usada para obter informações estatísticas. Se uma cláusula select for usada para obter um subconjunto de propriedades, o valor “stats” também poderá ser especificado na cláusula select e a cláusula expand poderá ser deixada como nula.

## Exemplo de consulta eficiente

Abaixo você encontrará um trecho de código que usa a API .NET de Lote para consultar o serviço de Lote com eficiência a fim de obter as estatísticas de um conjunto específico de pools. Nesse cenário, o usuário do Lote tem tanto pools de teste quanto de produção, sendo que as IDs dos pools de teste têm o prefixo "test" e as IDs dos pools de produção têm o prefixo "prod". No trecho de código, *myBatchClient* é uma instância de [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) corretamente inicializada.

	// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
	// clause strings
	ODATADetailLevel detailLevel = new ODATADetailLevel();

	// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
	// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
	// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
	detailLevel.ExpandClause = "stats";

	// We want to pull only the "test" pools, so we limit the items returned by using a Filterclause and
	// specifying that the pool IDs must start with "test"
	detailLevel.FilterClause = "startswith(id, 'test')";

	// To further limit the data that crosses the wire, configure the SelectClause to limit the
	// properties returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
	detailLevel.SelectClause = "id, stats";

	// Now get our collection of pools, minimizing the amount of data returned by specifying the
	// detail level we configured above
	List<CloudPool> testPools = myBatchClient.PoolOperations.ListPools(detailLevel).ToList();

> [AZURE.TIP]É recomendável que você *sempre* use cláusulas filter e select para suas chamadas à API de lista para garantir o máximo de eficiência e o melhor desempenho para o seu aplicativo.

## Próximas etapas

1. Se você ainda não fez isso, certifique-se de verificar a documentação da API do Lote relevante para o seu cenário de desenvolvimento
    - [REST do Lote](https://msdn.microsoft.com/library/azure/dn820158.aspx)
    - [.NET do Lote](https://msdn.microsoft.com/library/azure/dn865466.aspx)
2. Pegue os [Exemplos de Lote do Azure](https://github.com/Azure/azure-batch-samples) no GitHub e aprofunde-se no código

<!---HONumber=September15_HO1-->