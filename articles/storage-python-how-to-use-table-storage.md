<properties 
	pageTitle="Como usar o Armazenamento de tabela do Python | Microsoft Azure" 
	description="Saiba como usar o serviço Tabela do Python para criar e excluir tabelas e inserir, excluir e consultar a tabela." 
	services="storage" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="huvalo"/>


# Como usar o Armazenamento de tabela do Python

[AZURE.INCLUDE [storage-selector-table-include](../includes/storage-selector-table-include.md)]

## Visão geral

Este guia mostra como executar cenários comuns usando o serviço de armazenamento de Tabela do Azure. Os exemplos são escritos em Python e usam o [Pacote do Python Azure][]. Os cenários abrangidos incluem **criar e excluir uma tabela, inserindo e consultando entidades em uma tabela**.

[AZURE.INCLUDE [storage-table-concepts-include](../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

[AZURE.NOTE] Se você precisar instalar o Python ou o [pacote do Python Azure][], consulte o [Guia de instalação do Python](python-how-to-install.md).


## Como criar uma tabela

O objeto **TableService** permite que você trabalhe com serviços de tabela. O código a seguir cria um objeto **TableService**. Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Armazenamento do Azure programaticamente:

	from azure.storage import TableService, Entity

O código a seguir cria um objeto **TableService** usando o nome da conta de armazenamento e a chave da conta.  Substitua 'myaccount' e 'mykey' pela conta e a chave reais.

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## Como adicionar uma entidade à tabela

Para adicionar uma entidade, primeiro crie um dicionário que defina os nomes e os valores da propriedade de sua entidade. Observe que, para cada entidade, você deve especificar um **PartitionKey** e um **RowKey**. Estes são os identificadores exclusivos das entidades e são os valores que podem ser consultados muito mais rápido que as outras propriedades. O sistema usa a **PartitionKey** para distribuir automaticamente as entidades das tabelas por vários nós de armazenamento.
As entidades com o mesmo **PartitionKey** são armazenadas no mesmo nó. A
**RowKey** é a ID exclusiva da entidade na partição à qual ela pertence.

Para adicionar uma entidade à sua tabela, transmita um objeto de dicionário para o método **insert\_entity**.

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
	table_service.insert_entity('tasktable', task)

Você também pode transmitir uma instância da classe **Entity** para o método **insert\_entity**.

	task = Entity()
	task.PartitionKey = 'tasksSeattle'
	task.RowKey = '2'
	task.description = 'Wash the car'
	task.priority = 100
	table_service.insert_entity('tasktable', task)

## Como atualizar uma entidade

Este código mostra como substituir a versão antiga de uma entidade existente por uma versão atualizada.

	task = {'description' : 'Take out the garbage', 'priority' : 250}
	table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

Se a entidade que está sendo atualizada não existir, a operação de atualização falhará. Se quiser armazenar uma entidade, independentemente de ela já existir, use **insert\_or\_replace_entity**. 
No exemplo a seguir, a primeira chamada substituirá a entidade existente. A segunda chamada irá inserir uma nova entidade, contanto que não exista na tabela nenhuma entidade com o **PartitionKey** e o **RowKey** especificados.

	task = {'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

	task = {'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## Como alterar um grupo de entidades

Às vezes, convém enviar várias operações juntas em um lote para garantir o processamento atômico pelo servidor. Para isso, você deve usar o método **begin\_batch** em **TableService** e chamar a série de operações, como de costume. Quando você desejar enviar o lote, chame **commit\_batch**. Observe que todas as entidades devem estar na mesma partição para que sejam alteradas como um lote. O exemplo a seguir adiciona duas entidades juntas em um lote.

	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	table_service.begin_batch()
	table_service.insert_entity('tasktable', task10)
	table_service.insert_entity('tasktable', task11)
	table_service.commit_batch()

## Como consultar uma entidade

Para consultar uma entidade em uma tabela, use o método **get\_entity**, passando **PartitionKey** e **RowKey**.

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## Como consultar um conjunto de entidades

Este exemplo localiza todas as tarefas em Seattle com base no **PartitionKey**.

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## Como consultar um subconjunto de propriedades de entidade

Uma consulta a uma tabela pode recuperar apenas algumas propriedades de uma entidade.
Essa técnica, chamada *projection*, reduz a largura de banda e pode melhorar o desempenho da consulta, principalmente para grandes entidades. Use o parâmetro **select** e transmita os nomes das propriedades que gostaria de trazer para o cliente.

A consulta no código a seguir retorna apenas as descrições das entidades na tabela.

*Observe que o trecho de código a seguir funciona apenas com o serviço de armazenamento de nuvem, e o Emulador
de Armazenamento não dá suporte para isso.*

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
	for task in tasks:
		print(task.description)

## Como excluir uma entidade

Você pode excluir uma entidade usando suas chaves de partição e de linha.

	table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## Como excluir uma tabela

O código a seguir exclui uma tabela de uma conta de armazenamento.

	table_service.delete_table('tasktable')

## Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de tabela, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

-   Consulte a referência de MSDN: [Armazenando e acessando dados no Azure][]
-   [Visite o Blog da Equipe de Armazenamento do Azure][]

[Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Visite o Blog da Equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Pacote do Python Azure]: https://pypi.python.org/pypi/azure  

<!--HONumber=49-->