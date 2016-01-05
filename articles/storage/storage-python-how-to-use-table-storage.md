<properties
	pageTitle="Como usar o Armazenamento de tabela do Python | Microsoft Azure"
	description="Saiba como usar o serviço Tabela do Python para criar e excluir uma tabela e inserir e consultar uma tabela."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="emgerner"/>


# Como usar o Armazenamento de tabela do Python

[AZURE.INCLUDE [armazenamento-seletor-tabela-include](../../includes/storage-selector-table-include.md)]

## Visão geral

Este guia mostra como executar cenários comuns usando o serviço de armazenamento de Tabela do Azure. Os exemplos são escritos em Python e usam o [pacote de armazenamento do Python Azure][]. Os cenários abrangidos incluem criar e excluir uma tabela, além de inserir e consultar entidades em uma tabela.

[AZURE.INCLUDE [armazenamento-tabela-conceitos-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.NOTE]Se você precisar instalar o Python ou o [pacote do Python Azure][], consulte o [Guia de instalação do Python](../python-how-to-install.md).


## Criar uma tabela

O objeto **TableService** permite que você trabalhe com serviços de tabela. O código a seguir cria um objeto **TableService**. Adicione o código próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Armazenamento do Azure programaticamente:

	from azure.storage.table import TableService, Entity

O código a seguir cria um objeto **TableService** usando o nome da conta de armazenamento e a chave da conta. Substitua 'Minha conta' e 'mykey' com a conta real e a chave.

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## Adicionar uma entidade a uma tabela

Para adicionar uma entidade, primeiro crie um dicionário que defina os nomes e os valores da propriedade de sua entidade. Observe que, para cada entidade, é necessário especificar **PartitionKey** e um **RowKey**. Esses são os identificadores exclusivos das suas entidades. É possível consultar esses valores de forma muito mais rápida do que com as outras propriedades. O sistema usa o **PartitionKey** para distribuir automaticamente as entidades das tabelas por vários nós de armazenamento. As entidades com o mesmo **PartitionKey** são armazenadas no mesmo nó. **RowKey** é o ID exclusivo da entidade na partição à qual pertence.

Para adicionar uma entidade à sua tabela, transmita um objeto de dicionário para o método **insert\_entity**.

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
	table_service.insert_entity('tasktable', task)

Você também pode passar uma instância da classe **Entity** para o método **insert\_entity**.

	task = Entity()
	task.PartitionKey = 'tasksSeattle'
	task.RowKey = '2'
	task.description = 'Wash the car'
	task.priority = 100
	table_service.insert_entity('tasktable', task)

## Atualizar uma entidade

Este código mostra como substituir a versão antiga de uma entidade existente por uma versão atualizada.

	task = {'description' : 'Take out the garbage', 'priority' : 250}
	table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

Se a entidade que está sendo atualizada não existir, a operação de atualização falhará. Se quiser armazenar uma entidade, independentemente de ela já existir, use **insert\_or\_replace\_entity**. No exemplo a seguir, a primeira chamada substituirá a entidade existente. A segunda chamada irá inserir uma nova entidade, contanto que não exista na tabela nenhuma entidade com o **PartitionKey** e o **RowKey** especificados.

	task = {'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

	task = {'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## Alterar um grupo de entidades

Às vezes, convém enviar várias operações juntas em um lote para garantir o processamento atômico pelo servidor. Para isso, você deve usar o método **begin\_batch** em **TableService** e chamar a série de operações, como de costume. Quando você desejar enviar o lote, chame **commit\_batch**. Observe que todas as entidades devem estar na mesma partição para que sejam alteradas como um lote. O exemplo a seguir adiciona duas entidades juntas em um lote.

	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	table_service.begin_batch()
	table_service.insert_entity('tasktable', task10)
	table_service.insert_entity('tasktable', task11)
	table_service.commit_batch()

## Consultar uma entidade

Para consultar uma entidade em uma tabela, use o método **get\_entity**, passando **PartitionKey** e **RowKey**.

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## Consultar um conjunto de entidades

Este exemplo localiza todas as tarefas em Seattle com base em **PartitionKey**.

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## consultar um subconjunto de propriedades da entidade

Uma consulta a uma tabela pode recuperar apenas algumas propriedades de uma entidade. Essa técnica, chamada *projeção*, reduz a largura de banda e pode melhorar o desempenho da consulta, principalmente para grandes entidades. Use o parâmetro **select** e transmita os nomes das propriedades que você deseja trazer para o cliente.

A consulta no código a seguir retorna apenas as descrições das entidades na tabela.

[AZURE.NOTE]O trecho a seguir funciona apenas com o serviço de armazenamento em nuvem. Isso não é compatível com o emulador de armazenamento.

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
	for task in tasks:
		print(task.description)

## Excluir uma entidade

É possível excluir uma entidade usando suas chaves de partição e de linha.

	table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## Excluir uma tabela

O código a seguir exclui uma tabela de uma conta de armazenamento.

	table_service.delete_table('tasktable')

## Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de Tabela, siga estes links para saber mais sobre tarefas de armazenamento mais complexas:

-   Consulte a Referência do MSDN: [Armazenamento do Azure][].
-   Visite o [Blog da equipe de Armazenamento do Azure][].

Para obter mais informações, veja também o [Centro de Desenvolvedores do Python](/develop/python/).

[Blog da equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[pacote do Python Azure]: https://pypi.python.org/pypi/azure
[pacote de armazenamento do Python Azure]: https://pypi.python.org/pypi/azure-storage

<!---HONumber=AcomDC_1217_2015-->