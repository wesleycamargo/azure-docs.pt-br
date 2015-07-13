<properties 
	pageTitle="Consultas de lista eficientes" 
	description="Saiba como reduzir o número de itens retornados em uma lista, bem como reduzir a quantidade de informações retornadas para cada item" 
	services="batch" 
	documentationCenter="" 
	authors="davidmu1" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-windows" 
	ms.workload="multiple"
	ms.date="05/09/2015" 
	ms.author="davidmu"/>

# Consultas de lista eficientes

Os métodos a seguir são exemplos de operações que praticamente todos os aplicativos que usam o Azure Batch devem executar, geralmente com frequência:

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listjobs.aspx)
- [ListWorkitems](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listworkitems.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.listpools.aspx)
- [ListCertificates](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icertificatemanager.listcertificates.aspx)

O monitoramento é um caso de uso comum; determinar a capacidade e o status de um pool requer que todas as VMs do pool sejam consultadas, por exemplo. Outro exemplo seria consultar as tarefas de um trabalho para determinar se tarefas ainda estão na fila. Em alguns casos, um conjunto avançado de dados é necessário, mas em outros, apenas uma contagem do número total de itens ou de itens em um determinado estado será necessária.

É importante observar que o número de itens que podem ser retornados pode ser muito grande e o tamanho dos dados necessários para representar a lista de itens também pode ser muito grande. Simplesmente consultar muitos itens que resultam em respostas grandes pode levar a uma série de problemas:

- Os tempos de resposta da API do Batch podem se tornar muito lentos. Quanto maior o número de itens, maior o tempo de consulta necessário para o serviço de Batch. Grandes números de itens devem ser divididos em partes e, portanto, várias chamadas de API de serviço podem ser feitas pela biblioteca de cliente para o serviço a fim de obter todos os itens de uma lista.
- O processamento da API pelo aplicativo que chama o Batch levará mais tempo conforme houver mais itens a serem processados.
- Mais memória será consumida no aplicativo que chama o Batch, pois há mais itens e/ou itens maiores.
- Mais itens e/ou itens maiores resultarão em maior tráfego de rede. Isso levará mais tempo para transferir e, dependendo da arquitetura do aplicativo, pode resultar em cobranças de rede maiores para dados transferidos fora da região da conta do Batch.

A API do Batch fornece a capacidade de reduzir o número de itens retornados em uma lista, bem como de reduzir a quantidade de informações retornadas para cada item. Um parâmetro do tipo [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) pode ser especificado para operações de lista. DetailLevel é uma classe base abstrata e um objeto [ODATADetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx) realmente precisa ser criado e passado como parâmetro.

Para todas as API, o seguinte se aplica:

- Cada nome de propriedade é uma cadeia de caracteres que é mapeada para a propriedade do objeto
- Todos os nomes de propriedade diferenciam maiúsculas de minúsculas, mas valores de propriedade não diferenciam maiúsculas de minúsculas
- Cadeias de caracteres de data/hora podem ter um de dois formatos e precisam ser precedidas de DateTime
	- W3CDTF (por exemplo, creationTime gt DateTime’2011-05-08T08:49:37Z’)
	- RFC1123 (por exemplo, creationTime gt DateTime’Sun, 08 May 2011 08:49:37 GMT’)
- Cadeias de caracteres boolianas são "verdadeiro" ou "falso"
- Se uma propriedade ou um operador inválido for especificado, uma exceção será criada com uma exceção interna "400 (Solicitação Incorreta)".
- O parâmetro DetailLevel com cláusulas Select e Expand também pode ser passado para métodos "Get" apropriados; por exemplo, IPoolManager.GetPool()

O objeto ODataDetailLevel tem três propriedades públicas que podem ser especificadas no construtor ou definidas diretamente. As três propriedades são todas cadeias de caracteres:

- [FilterClause](#filter) – filtrar e possivelmente reduzir o número de itens retornados
- [SelectClause](#select) – especificar os valores de propriedade específicos que são retornados, reduzindo o tamanho de item e resposta
- [ExpandClause](#expand) – retornar todos os dados necessários em uma única chamada em vez de várias chamadas

### <a id="filter"></a> FilterClause

O número de itens retornados pode ser reduzido por uma cadeia de caracteres de filtro. Um ou mais valores de propriedade podem ser especificados para garantir que somente os itens necessários sejam retornados. Por exemplo, listar somente itens de trabalho ativos, listar somente tarefas em execução de um trabalho, listar apenas VMs prontas para executar tarefas.

Um [FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) é uma cadeia de caracteres que consiste em uma ou mais expressões, em que uma expressão consiste em um nome de propriedade, um operador e um valor. As propriedades que podem ser especificadas são específicas para cada chamada de API, assim como os operadores com suporte para cada propriedade. Várias expressões podem ser combinadas usando operadores lógicos "e" e "ou".

Por exemplo, um filtro para listar tarefas poderia ser:

	startswith(name, 'MyTask') and (state eq 'Running')

### <a id="select"></a> SelectClause

Os valores de propriedade retornados para cada item podem ser limitados por meio de uma cadeia de caracteres de seleção. Uma lista de propriedades de um item pode ser especificada e, em seguida, somente esses valores de propriedade serão retornados.

Um [SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) é uma cadeia de caracteres que consiste em uma lista separada por vírgulas de nomes de propriedade. Todas as propriedades no item retornado pela operação de lista podem ser especificadas.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

O número de chamadas de API pode ser reduzido com uma cadeia de caracteres de expansão. Informações mais detalhadas sobre cada item de lista podem ser obtidas com uma chamada de API de lista em vez de obter a lista e, em seguida, fazer uma chamada para cada item na lista.

Um [ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) é semelhante à cláusula Select, a cláusula Expand controla se determinados dados são retornados nos resultados. A cláusula Expand tem suporte apenas para lista de itens de trabalho, lista de tarefas, lista de pools e lista de trabalhos; atualmente, ela dá suporte apenas a informações estatísticas. Quando todas as propriedades são necessárias e não houver uma cláusula select, a cláusula de expansão deverá ser usada para obter informações estatísticas. Se uma cláusula select for usada para obter um subconjunto de propriedades, então as estatísticas poderão ser especificadas na cláusula select e a cláusula expand poderá ser deixada como nula.

> [AZURE.NOTE]É recomendável que você sempre use cláusulas filter e select para suas chamadas de API de lista para garantir o máximo de eficiência e o melhor desempenho para o seu aplicativo.

<!---HONumber=62-->