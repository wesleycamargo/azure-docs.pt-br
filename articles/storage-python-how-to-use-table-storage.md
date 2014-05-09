<properties linkid="develop-python-table-service" urlDisplayName="Serviço Tabela" pageTitle="Como usar o armazenamento de tabela (Python) | Microsoft Azure" metaKeywords="tabela Python do Azure, criando tabela do Azure, excluindo tabela do Azure, inserindo tabela do Azure, consultando tabela do Azure" description="Aprenda como usar o serviço Tabela com Python para criar e excluir uma tabela e inserir, excluir e consultar a tabela." metaCanonical="" services="storage" documentationCenter="Python" title="Como usar o serviço de armazenamento Tabela com Python" authors="" solutions="" manager="" editor="" />





# Como usar o serviço de armazenamento Tabela com Python
Este guia mostra como executar cenários comuns usando o
serviço de armazenamento Tabela do Azure. Os exemplos foram escritos usando a API do Python. Os cenários abordados incluem **criar e excluir uma
tabela, inserir e consultar entidades em uma tabela**. Para obter mais
informações sobre tabelas, consulte a seção [Próximas etapas][].

## Sumário

[O que é o serviço Tabela?][]   
 [Conceitos][]   
 [Criar uma conta de Armazenamento do Azure][]   
 [Como criar uma tabela][]   
 [Como adicionar uma entidade a uma tabela][]   
 [Como atualizar uma entidade][]   
 [Como alterar um grupo de entidades][]   
 [Como consultar uma entidade][]   
 [Como consultar um conjunto de entidades][]   
 [Como consultar um subconjunto de propriedades de entidade][]   
 [Como excluir uma entidade][]   
 [Como excluir uma tabela][]   
 [Próximas etapas][]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <a name="create-account"> </a>Criar uma conta de armazenamento do Azure
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

**Observação:** se você precisar instalar o Python ou as bibliotecas de cliente, consulte o [Guia de Instalação do Python (a página pode estar em inglês)](../python-how-to-install/).


## <a name="create-table"> </a>Como criar uma tabela

O objeto **TableService** permite que você trabalhe com serviços de tabela. O código a seguir cria um objeto **TableService**. Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseje acessar o Armazenamento di Azure programaticamente:

	from azure.storage import *

O código a seguir cria um objeto **TableService** usando o nome da conta de armazenamento e a chave da conta.  Substitua 'Minha conta' e 'mykey' com a conta real e a chave.

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## <a name="add-entity"> </a>Como adicionar uma entidade a uma tabela

Para adicionar uma entidade, primeiro crie um dicionário que defina os nomes e os valores da propriedade de sua entidade. Observe que, para cada entidade, você deve
especificar um **PartitionKey** e um **RowKey**. Eles são os identificadores exclusivos
das suas eentidades e são valores que poderão ser consultados muito mais rápido
do que as suas outras propriedades. O sistema usa **PartitionKey** para
distribuir automaticamente as entidades da tabela por vários nós de armazenamento.
As entidades com o mesmo **PartitionKey** são armazenadas no mesmo nó. O
**RowKey** é o ID exclusivo da entidade na partição à qual pertence.

Para adicionar uma entidade à sua tabela, transmita um objeto de dicionário
para o método **insert\_entity**.

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
	table_service.insert_entity('tasktable', task)

Você também pode transmitir uma instância da classe **Entity** para o método **insert\_entity**.

	task = Entity()
	task.PartitionKey = 'tasksSeattle'
	task.RowKey = '2'
	task.description = 'Wash the car'
	task.priority = 100
	table_service.insert_entity('tasktable', task)

## <a name="update-entity"> </a>Como atualizar uma entidade

Este código mostra como substituir a versão antiga de uma entidade existente por uma versão atualizada.

	task = {'description' : 'Take out the garbage', 'priority' : 250}
	table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

Se a entidade que está sendo atualizada não existir, a operação de atualização falhará. Se quiser armazenar uma entidade, independentemente de ela já existir, use **insert\_or\_replace_entity**. 
No exemplo a seguir, a primeira chamada substituirá a entidade existente. A segunda chamada irá inserir uma nova entidade, contanto que não exista na tabela nenhuma entidade com o **PartitionKey** e o **RowKey** especificados.

	task = {'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

	task = {'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## <a name="change-entities"> </a>Como alterar um grupo de entidades

Às vezes, convém enviar várias operações juntas em um
lote para garantir o processamento atômico pelo servidor. Para fazer isso, você deve usar o método **begin\_batch** em **TableService** e, em seguida, chamar a série de operações como sempre. Quando desejar enviar o lote, chame **commit\_batch**. Observe que todas as entidades devem estar na mesma partição para que sejam alteradas como um lote. O exemplo a seguir adiciona duas entidades juntas em um lote.

	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	table_service.begin_batch()
	table_service.insert_entity('tasktable', task10)
	table_service.insert_entity('tasktable', task11)
	table_service.commit_batch()

## <a name="query-for-entity"> </a>Como consultar uma entidade

Para consultar uma entidade em uma tabela, use o método **get\_entity**, transmitindo **PartitionKey** e **RowKey**.

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## <a name="query-set-entities"> </a>Como consultar um conjunto de entidades

Este exemplo localiza todas as tarefas em Seattle com base no **PartitionKey**.

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## <a name="query-entity-properties"> </a>Como consultar um subconjunto de propriedades de entidade

Uma consulta a uma tabela pode recuperar apenas algumas propriedades de uma entidade.
Essa técnica, chamada *projeção*, reduz a largura de banda e pode melhorar
o desempenho da consulta, principalmente para grandes entidades. Use o parâmetro **select** e transmita os nomes das propriedades que deseja trazer para o cliente.

A consulta no código a seguir retorna apenas as **descrições** das entidades na tabela.

*Observe que o trecho de código a seguir funciona apenas com o serviço de armazenamento em nuvem, e isso não tem suporte no Emulador de Armazenamento.*

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
	for task in tasks:
		print(task.description)

## <a name="delete-entity"> </a>Como excluir uma entidade

Você pode excluir uma entidade usando suas chaves de partição e de linha.

	table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## <a name="delete-table"> </a>Como excluir uma tabela

O código a seguir exclui uma tabela de uma conta de armazenamento.

	table_service.delete_table('tasktable')

## <a name="next-steps"> </a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de tabela, siga estes links para aprender a fazer tarefas de armazenamento mais complexas.

-  Consulte a referência do MSDN: [Armazenando e acessando dados no Azure][]
-   [Acesse o Blog da Equipe de Armazenamento do Azure][]

  [Próximas etapas]: #next-steps
  [O que é o serviço Tabela?]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #create-account
  [Como criar uma tabela]: #create-table
  [Como adicionar uma entidade a uma tabela]: #add-entity
  [Como atualizar uma entidade]: #update-entity
  [Como alterar um grupo de entidades]: #change-entities
  [Como consultar uma entidade]: #query-for-entity
  [Como consultar um conjunto de entidades]: #query-set-entities
  [Como consultar um subconjunto de propriedades de entidade]: #query-entity-properties
  [Como excluir uma entidade]: #delete-entity
  [Como excluir uma tabela]: #delete-table
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Acesse o Blog da Equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/

