<properties
	pageTitle="Como usar o Armazenamento de Tabela do Azure por meio do Ruby | Microsoft Azure"
	description="Armazene dados estruturados na nuvem usando o Armazenamento de Tabelas do Azure, um armazenamento de dados NoSQL."
	services="storage"
	documentationCenter="ruby"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>
<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="robmcm"/>


# Como usar o Armazenamento de Tabela do Azure por meio do Ruby

[AZURE.INCLUDE [armazenamento-seletor-tabela-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## Visão geral

Este guia mostra como executar cenários comuns usando o serviço Tabela do Azure. Os exemplos de código são gravados com a API do Ruby. Os cenários abrangidos incluem **criar e excluir uma tabela, inserindo e consultando entidades em uma tabela**.

[AZURE.INCLUDE [armazenamento-tabela-conceitos-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Criar um aplicativo Ruby

Para obter instruções sobre como criar um aplicativo Ruby, confira [Aplicativo Web Ruby on Rails em uma VM do Azure](../virtual-machines/virtual-machines-linux-classic-ruby-rails-web-app.md).


## Configurar seu aplicativo para acessar o Armazenamento

Para usar o Armazenamento do Azure, você precisará baixar e usar o pacote do Azure para o Ruby, que inclui um conjunto de bibliotecas de conveniência que se comunicam com os serviços REST do Armazenamento.

### Usar RubyGems para obter o pacote

1. Use uma interface de linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2. Digite **gem install azure** na janela de comandos para instalar a gema e as dependências.

### Importar o pacote

Use seu editor de texto favorito e adicione o seguinte na parte superior do arquivo do Ruby no qual você pretende usar o Armazenamento:

	require "azure"

## Configurar uma conexão do Armazenamento do Azure

O módulo do Azure lerá as variáveis de ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS\_KEY** para obter as informações necessárias para se conectar à sua conta do Armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta antes de usar **Azure::TableService** com o seguinte código:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"

Para obter esses valores de uma conta de armazenamento clássico ou do Resource Manager no Portal do Azure:

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Navegue até a conta de armazenamento que você deseja usar.
3. Na folha Configurações no lado direito, clique em **Chaves de Acesso**.
4. Na folha Acessar chaves exibida, você verá as teclas de acesso 1 e 2. Você pode usar qualquer uma das duas.
5. Clique no ícone de cópia para copiar a chave para a área de transferência.

Para obter esses valores de uma conta de armazenamento clássico no Portal Clássico do Azure:

1. Faça logon no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. Navegue até a conta de armazenamento que você deseja usar.
3. Clique em **GERENCIAR CHAVES DE ACESSO** na parte inferior do painel de navegação.
4. Na caixa de diálogo pop-up, você verá o nome da conta de armazenamento, a chave de acesso primária e a chave de acesso secundária. Para a chave de acesso, você pode usar tanto a primária quanto a secundária.
5. Clique no ícone de cópia para copiar a chave para a área de transferência.

## Criar uma tabela

O objeto **Azure::TableService** permite trabalhar com tabelas e entidades. Para criar uma tabela, use o método **create\_table()**. O exemplo a seguir cria uma tabela ou imprime o erro, se houver algum.

	azure_table_service = Azure::TableService.new
	begin
	  azure_table_service.create_table("testtable")
	rescue
	  puts $!
	end

## Adicionar uma entidade a uma tabela

Para adicionar uma entidade, primeiro crie um objeto hash que defina as propriedades da entidade. É importante lembrar que, para cada entidade, você deve especificar um **PartitionKey** e um **RowKey**. Estes são os identificadores exclusivos das entidades e são os valores que podem ser consultados muito mais rápido que as outras propriedades. O Armazenamento do Azure usa **PartitionKey** para distribuir automaticamente as entidades da tabela entre vários nós de armazenamento. As entidades com o mesmo **PartitionKey** são armazenadas no mesmo nó. O **RowKey** é o ID exclusivo da entidade na partição à qual pertence.

	entity = { "content" => "test entity",
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.insert_entity("testtable", entity)

## Atualizar uma entidade

Há vários métodos disponíveis para atualizar uma entidade existente:

* **update\_entity():** atualiza uma entidade existente substituindo-a.
* **merge\_entity():** atualiza uma entidade existente mesclando novos valores de propriedade a essa entidade.
* **insert\_or\_merge\_entity():** atualiza uma entidade existente ao substituí-la. Se não existir nenhuma entidade, uma nova será inserida:
* **insert\_or\_replace\_entity():** atualiza uma entidade existente mesclando novos valores de propriedade a essa entidade. Se nenhuma entidade existir, uma nova será inserida.

O seguinte exemplo demonstra a atualização de uma entidade usando **update\_entity ()**:

	entity = { "content" => "test entity with updated content",
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.update_entity("testtable", entity)

Com **update\_entity()** e **merge\_entity()**, se a entidade que estiver sendo atualizada não existir, a operação de atualização falhará. Portanto, se você desejar armazenar uma entidade independentemente de já existir, use **insert\_or\_replace\_entity()** ou **insert\_or\_merge\_entity()**.

## Trabalhar com grupos de entidades

Às vezes, convém enviar várias operações juntas em um lote para garantir o processamento atômico pelo servidor. Para fazer isso, você cria primeiro um objeto de **Lote** e usa o método de **execute\_batch()** em **TableService**. O seguinte exemplo demonstra o envio de duas entidades com RowKey 2 e 3 em um lote. Observe que isso funciona apenas em entidades com o mesmo PartitionKey.

	azure_table_service = Azure::TableService.new
	batch = Azure::Storage::Table::Batch.new("testtable",
	  "test-partition-key") do
	  insert "2", { "content" => "new content 2" }
	  insert "3", { "content" => "new content 3" }
	end
	results = azure_table_service.execute_batch(batch)

## Consultar uma entidade

Para consultar uma entidade em uma tabela, use o método **get\_entity()**, transmitindo o nome da tabela, **PartitionKey** e **RowKey**.

	result = azure_table_service.get_entity("testtable", "test-partition-key",
	  "1")

## Consultar um conjunto de entidades

Para consultar um conjunto de entidades em uma tabela, crie um objeto de hash de consulta e use o método **query\_entities()**. O exemplo a seguir demonstra como obter todas as entidades com o mesmo **PartitionKey**:

	query = { :filter => "PartitionKey eq 'test-partition-key'" }
	result, token = azure_table_service.query_entities("testtable", query)

> [AZURE.NOTE] Se o conjunto de resultados for muito grande para ser retornado por uma única consulta, um token de continuação será retornado para que você possa usar para recuperar páginas subsequentes.

## consultar um subconjunto de propriedades da entidade

Uma consulta a uma tabela pode recuperar apenas algumas propriedades de uma entidade. Essa técnica, chamada "projeção", reduz a largura de banda e pode melhorar o desempenho da consulta, principalmente em grandes entidades. Use a cláusula select e transmita os nomes das propriedades que você gostaria de trazer para o cliente.

	query = { :filter => "PartitionKey eq 'test-partition-key'",
	  :select => ["content"] }
	result, token = azure_table_service.query_entities("testtable", query)

## Excluir uma entidade

Para excluir uma entidade, use o método **delete\_entity()**. Você precisa transmitir o nome da tabela que contém a entidade, a PartitionKey e a RowKey da entidade.

		azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## Excluir uma tabela

Para excluir uma tabela, use o método **delete\_table()** e passe o nome da tabela que você deseja excluir.

		azure_table_service.delete_table("testtable")

## Próximas etapas

Para saber mais sobre tarefas complexas de armazenamento, siga estes links:

- [Blog da equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- [SDK do Azure para repositório Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) no GitHub

<!---HONumber=AcomDC_0817_2016--->