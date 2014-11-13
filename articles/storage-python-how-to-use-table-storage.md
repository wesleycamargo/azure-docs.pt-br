<properties urlDisplayName="Table Service" pageTitle="Como usar o armazenamento de tabela (Python) | Microsoft Azure" metaKeywords="Azure table Python, creating table Azure, deleting table Azure, inserting table Azure, querying table Azure" description="Saiba como usar o servi&ccedil;o Tabela do Python para criar e excluir tabelas e inserir, excluir e consultar a tabela." metaCanonical="" services="storage" documentationCenter="Python" title="Como usar o Servi&ccedil;o de Armazenamento de Tabela no Python" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="huvalo" />

# Como usar o Serviço de Armazenamento de Tabela no Python

Este guia mostra como executar cenários comuns usando o serviço de armazenamento de Tabela do Windows
Azure. As amostras foram escritas usando a
API do Python. Os cenários abrangidos incluem **criar e excluir uma
tabela, inserir e consultar entidades em uma tabela**. Para obter mais
informações sobre tabelas, consulte a seção [Próximas etapas][Próximas etapas].

## Sumário

[O que é o serviço Tabela?][O que é o serviço Tabela?]
 [Conceitos][Conceitos]
 [Criar uma conta de armazenamento do Azure][Criar uma conta de armazenamento do Azure]
 [Como: Criar uma tabela][Como: Criar uma tabela]
[Como: Adicionar uma entidade a uma tabela][Como: Adicionar uma entidade a uma tabela]
 [Como: Atualizar uma entidade][Como: Atualizar uma entidade]
 [Como: Alterar um grupo de entidades][Como: Alterar um grupo de entidades]
 [Como: Consultar uma entidade][Como: Consultar uma entidade]
 [Como: Consultar um conjunto de entidades][Como: Consultar um conjunto de entidades]
 [Como: Consultar um subconjunto de propriedades de entidade][Como: Consultar um subconjunto de propriedades de entidade]
 [Como: Excluir uma entidade][Como: Excluir uma entidade]
 [Como: Excluir uma tabela][Como: Excluir uma tabela]
 [Próximas etapas][Próximas etapas]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <a name="create-account"> </a> Criar uma conta de armazenamento do Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

**Observação:** se você precisar instalar o Python ou as bibliotecas de cliente, consulte o [Guia de instalação do Python (a página pode estar em inglês)][Guia de instalação do Python (a página pode estar em inglês)].

## <a name="create-table"> </a>Como criar uma tabela

O objeto **TableService** permite que você trabalhe com serviços de tabela. O
código a seguir cria um objeto **TableService**. Adicione o seguinte código próximo
à parte superior de qualquer arquivo Python no qual você deseja acessar o Armazenamento do Azure programaticamente:

    from azure.storage import TableService, Entity

O código a seguir cria um objeto **TableService** usando o nome da conta de armazenamento e a chave da conta. Substitua 'Minha conta' e 'mykey' com a conta real e a chave.

    table_service = TableService(account_name='myaccount', account_key='mykey')

    table_service.create_table('tasktable')

## <a name="add-entity"> </a>Como adicionar uma entidade a uma tabela

Para adicionar uma entidade, primeiro crie um dicionário que defina os nomes e os valores da propriedade
de sua entidade. Observe que, para cada entidade, você deve
especificar um **PartitionKey** e um **RowKey**. Esses são os identificadores
exclusivos das entidades e são os valores que podem ser consultados muito
mais rápido que as outras propriedades. O sistema usa a **PartitionKey** para
distribuir automaticamente as entidades da tabela por vários nós de armazenamento.
Entidades com a mesma **PartitionKey** são armazenadas no mesmo nó. A
**RowKey** é a ID exclusiva da entidade na partição à
qual pertence.

Para adicionar uma entidade à sua tabela, passe o objeto de dicionário
para o método **insert\_entity**.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
    table_service.insert_entity('tasktable', task)

Você também pode passar uma instância da classe **Entity** para o método **insert\_entity**.

    task = Entity()
    task.PartitionKey = 'tasksSeattle'
    task.RowKey = '2'
    task.description = 'Wash the car'
    task.priority = 100
    table_service.insert_entity('tasktable', task)

## <a name="update-entity"> </a>Como atualizar uma entidade

Este código mostra como substituir a versão antiga de uma entidade existente
por uma versão atualizada.

    task = {'description' : 'Take out the garbage', 'priority' : 250}
    table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

Se a entidade que está sendo atualizada não existir, a operação
de atualização falhará. Se quiser armazenar uma entidade
independentemente de ela já existir, use **insert\_or\_replace\_entity**.
No exemplo a seguir, a primeira chamada substituirá a entidade existente. A segunda chamada irá inserir uma nova entidade, contanto que não exista na tabela nenhuma entidade com o **PartitionKey** e o **RowKey** especificados.

    task = {'description' : 'Take out the garbage again', 'priority' : 250}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

    task = {'description' : 'Buy detergent', 'priority' : 300}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## <a name="change-entities"> </a>Como alterar um grupo de entidades

Às vezes, faz sentido enviar várias operações em conjunto em um
lote para assegurar o processamento atômico pelo servidor. Para isso, você
deve usar o método **begin\_batch** em **TableService** e chamar a
série de operações, como de costume. Quando você desejar enviar o
lote, chame **commit\_batch**. Observe que todas as entidades devem estar na mesma partição para que sejam alteradas como um lote. O exemplo a seguir adiciona duas entidades juntas em um lote.

    task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
    task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
    table_service.begin_batch()
    table_service.insert_entity('tasktable', task10)
    table_service.insert_entity('tasktable', task11)
    table_service.commit_batch()

## <a name="query-for-entity"> </a>Como consultar uma entidade

Para consultar uma entidade em uma tabela, use o método **get\_entity**, transmitindo
**PartitionKey** e **RowKey**.

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
Essa técnica, chamada *projeção*, reduz a largura de banda e pode melhorar o desempenho da consulta
, especialmente para grandes entidades. Use o parâmetro **select**
e transmita os nomes das propriedades que gostaria de trazer para
o cliente.

A consulta no código a seguir retorna apenas as **Descrições** das
entidades na tabela.

*Observe que o trecho de código a seguir funciona apenas com o serviço de armazenamento
de nuvem, e o Emulador
de Armazenamento não dá suporte para isso.*

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

Agora que você aprendeu os conceitos básicos do armazenamento de tabela, siga estes links
para aprender como fazer tarefas de armazenamento mais complexas.

-   Consulte a referência de MSDN: [Armazenando e acessando dados no Azure][Armazenando e acessando dados no Azure]
-   [Visite o Blog da Equipe de Armazenamento do Azure][Visite o Blog da Equipe de Armazenamento do Azure]

  [Próximas etapas]: #next-steps
  [O que é o serviço Tabela?]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #create-account
  [Como: Criar uma tabela]: #create-table
  [Como: Adicionar uma entidade a uma tabela]: #add-entity
  [Como: Atualizar uma entidade]: #update-entity
  [Como: Alterar um grupo de entidades]: #change-entities
  [Como: Consultar uma entidade]: #query-for-entity
  [Como: Consultar um conjunto de entidades]: #query-set-entities
  [Como: Consultar um subconjunto de propriedades de entidade]: #query-entity-properties
  [Como: Excluir uma entidade]: #delete-entity
  [Como: Excluir uma tabela]: #delete-table
  [Guia de instalação do Python (a página pode estar em inglês)]: ../python-how-to-install/
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Visite o Blog da Equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
