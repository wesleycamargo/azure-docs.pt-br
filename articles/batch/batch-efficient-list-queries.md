<properties 
	pageTitle="Consultas de lista eficiente" 
	description="Aprenda a reduzir o número de itens retornados em uma lista, bem como reduzir a quantidade de informações retornadas para cada item" 
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

# Consultas de lista eficiente

Os métodos a seguir são exemplos de operações que praticamente todos os aplicativos usando o Azure lote deve ser executado e geralmente precisa executar com freqüência:

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listjobs.aspx)
- [ListWorkitems](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listworkitems.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.listpools.aspx)
- [ListCertificates](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icertificatemanager.listcertificates.aspx)

O monitoramento é um comum usar maiúsculas/minúsculas; determinar a capacidade e o status de um pool requer que todo pool VM está a ser consultado, por exemplo. Outro exemplo seria consultar as tarefas de um trabalho determinar se as tarefas ainda estão na fila. Em alguns casos, um rico conjunto de dados é necessário, mas em outros casos, apenas uma contagem do número total de itens ou itens em um determinado estado é necessária.

É importante perceber que o número de itens que podem ser retornadas pode ser muito grande e o tamanho dos dados necessários para representar a lista de itens também pode ser muito grande. Simplesmente consultar vários itens que resulta em respostas grandes pode levar a uma série de problemas:

- Tempos de resposta do lote API podem se tornar muito lentos. Quanto maior que o número de itens mais o tempo de consulta necessário pelo serviço de lote. Grandes números de itens ser dividido em partes e, portanto, várias chamadas de API de serviço podem ser feito pela biblioteca de cliente para o serviço para obter todos os itens de uma lista.
- API pelo aplicativo de que lote de chamada levará mais tempo de processamento são os itens de mais para o processo.
- Mais memória será consumida no aplicativo chamar o lote como há mais itens e/ou itens maiores.
- Mais itens e/ou itens maiores resultará em maior tráfego de rede. Isso levará mais tempo para transferir e, dependendo da arquitetura do aplicativo, pode resultar em encargos de rede maior para dados transferidos fora da região da conta do lote.

A API de lote fornece a capacidade dos reduzir o número de itens retornados em uma lista, bem como reduzir a quantidade de informações retornadas para cada item. Um parâmetro de tipo [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) pode ser especificado para operações de lista. DetailLevel é uma classe base abstrata e um [ODATADetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx) objeto realmente precisa ser criado e passado como o parâmetro.

Para todas as API a seguir se aplicarem:

- Cada nome de propriedade é uma cadeia de caracteres que é mapeado para a propriedade do objeto
- Todos os nomes de propriedade diferenciam maiúsculas de minúsculas, mas valores de propriedade diferenciam maiúsculas de minúsculas
- Data/hora cadeias de caracteres podem ter um dos dois formatos e precisa ser precedido de data e hora
	- W3CDTF (por exemplo, creationTime gt DateTime'2011-05-08T08:49:37Z')
	- RFC1123 (por exemplo, creationTime gt DateTime'Sun, 08 de maio de 2011 08:49:37 GMT')
- Boolean cadeias de caracteres são "true" ou "false"
- Se uma propriedade inválida ou um operador for especificado, uma exceção será criada com um "400 (solicitação incorreta)" exceção interna.
- Parâmetro DetailLevel com cláusulas Select e expandir também pode ser passado para apropriada de métodos "Get"; Por exemplo, IPoolManager.GetPool()

O objeto ODataDetailLevel tem três propriedades públicas que podem ser especificadas no construtor ou definir diretamente. As três propriedades são todas as cadeias de caracteres:

- [FilterClause](#filter) – filtrar e reduzir o número de itens retornados
- [SelectClause](#select) – especifique os valores de propriedade específicos que são retornados, reduzindo o tamanho do item e resposta
- [ExpandClause](#expand) – todos os dados necessários em uma chamada em vez de várias chamadas de retorno

### <a id="filter"></a> FilterClause

O número de itens retornados pode ser reduzido por uma cadeia de caracteres de filtro. Um ou mais valores de propriedade podem ser especificados para garantir que somente os itens necessários são retornados. Por exemplo, listar itens de trabalho ativos somente, somente a execução de tarefas de um trabalho de lista listam apenas da VM que está pronta para executar tarefas.

A [FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) é uma cadeia de caracteres que consiste em uma ou mais expressões, com uma expressão consiste em um nome de propriedade, um operador e um valor. As propriedades que podem ser especificadas são específicas para cada chamada de API que os operadores suportados para cada propriedade. Várias expressões podem ser combinadas usando operadores lógicos "e" e "ou".

Por exemplo, um filtro para a listagem de tarefas poderia ser:

	startswith(name, 'MyTask') and (state eq 'Running')

### <a id="select"></a> SelectClause

Os valores de propriedade retornados para cada item podem ser limitados por meio de uma seqüência de caracteres select. Uma lista de propriedades de um item pode ser especificada e, em seguida, os valores de propriedade são retornados.

A [SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) é uma cadeia de caracteres que consiste em uma lista separada por vírgulas de nomes de propriedade. Todas as propriedades do item retornado pela operação de lista podem ser especificadas.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

O número de chamadas de API pode ser reduzido com uma cadeia de caracteres de expansão. Informações mais detalhadas para cada item da lista podem ser obtidas com a chamada de API de uma lista em vez de obter a lista e, em seguida, fazer uma chamada para cada item na lista.

Um [ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) é semelhante à cláusula Select, os controles de cláusula de expansão se determinados dados são retornados nos resultados. A cláusula de expansão tem suporte apenas para a lista de item de trabalho, lista de tarefas, lista de pool e lista de trabalho; Ele atualmente suporta apenas informações de estatísticas. Quando todas as propriedades são necessárias e não é uma cláusula select, a cláusula de expansão deve ser usada para obter informações de estatísticas. Se uma cláusula select é usada para obter um subconjunto de propriedades, em seguida, as estatísticas podem ser especificadas na cláusula select e a cláusula de expansão pode ser deixada como nula.

> [AZURE.NOTE]É recomendável que você sempre use o filtro e selecione cláusulas para as chamadas de API de lista garantir o máximo de eficiência e o melhor desempenho para o seu aplicativo.

<!---HONumber=GIT-SubDir-->