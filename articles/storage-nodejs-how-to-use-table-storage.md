<properties linkid="dev-nodejs-how-to-table-services" urlDisplayName="serviço Tabela" pageTitle="Como usar o armazenamento de tabela (Node.js) | Microsoft Azure" metaKeywords="Serviço de armazenamento de tabela do Azure, serviço tabela do Azure Node.js, armazenamento de tabela Node.js" description="Saiba como usar o serviço de armazenamento de tabela no Azure. Os exemplos de código são escritos usando a API Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="Como usar o serviço Tabela no Node.js" authors="" solutions="" manager="" editor="" />





# Como usar o serviço Tabela no Node.js

Este guia mostra como executar cenários comuns usando o
serviço Tabela do Azure. Os exemplos são gravados usando a
API do Node.js. Os cenários abordados incluem **criar e excluir uma
tabela, inserir e consultar entidades em uma tabela**. Para obter mais
informações sobre tabelas, consulte a seção [Próximas etapas][].

## Sumário

* [O que é o serviço Tabela?][]   
* [Conceitos][]   
* [Criar uma conta de Armazenamento do Azure][]   
* [Criar um aplicativo Node.js][]   
* [Configurar seu aplicativo para acessar o armazenamento][]   
* [Configurar uma conexão de armazenamento do Azure][]   
* [Como criar uma tabela][]   
* [Como adicionar uma entidade a uma tabela][]   
* [Como atualizar uma entidade][]   
* [Como trabalhar com grupos de entidades][]   
* [Como consultar uma entidade][]   
* [Como consultar um conjunto de entidades][]   
* [Como consultar um subconjunto de propriedades de entidade][]   
* [Como excluir uma entidade][]   
* [Como excluir uma tabela][]   
* [Próximas etapas][]

## <a name="what-is"> </a>O que é o serviço Tabela?

O serviço Tabela do Azure armazena grandes quantidades de dados estruturados. O serviço aceita chamadas autenticadas de dentro e fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados, não relacionais. Os usos comuns dos serviços Tabela
incluem:

-   Armazenando uma grande quantidade de dados estruturados (muitos TB) que são automaticamente dimensionados para atender às demandas de taxa de transferência
-   Armazenando conjuntos de dados que não exigem junções complexas, chaves estrangeiras ou procedimentos armazenados e que podem ser desnormalizados para acesso rápido
-   Consultando dados rapidamente, como perfis de usuário usando um índice clusterizado

Você pode usar o serviço Tabela para armazenar e consultar grandes conjuntos de
dados estruturados e não relacionais, e suas tabelas serão dimensionadas quando o volume
aumentar.

## <a name="concepts"> </a>Conceitos

O serviço Tabela contém os seguintes componentes:

![Table1][Table1]

-   **Formato de URL:** o código endereça as tabelas de uma conta usando este formato de endereço:
   
	    http://storageaccount.table.core.windows.net/table  
      
    Você pode endereçar as tabelas do Azure diretamente usando esse endereço com o protocolo OData. Para obter mais informações, consulte [OData.org][]

-   **Conta de Armazenamento:** Todo o acesso ao Armazenamento do Azure é feito através de uma conta de armazenamento. O tamanho total do conteúdo do blob, da tabela e da fila
    em uma conta de armazenamento não pode ultrapassar 100 TB.

-   **Tabela**: uma tabela é uma coleção ilimitada de entidades. As tabelas
    não impõem um esquema nas entidades, o que significa que uma única tabela pode
    conter entidades com diferentes conjuntos de propriedades. Uma conta
    pode conter várias tabelas.

-   **Entidade**: uma entidade é um conjunto de propriedades, semelhante a uma linha do banco de dados. Uma entidade pode ser de até 1MB.

-   **Propriedades**: uma propriedade é um par de nome-valor. Cada entidade pode incluir até 252 propriedades para armazenar dados. Cada entidade também tem
    três propriedades de sistema que especificam uma chave de partição, uma chave de linha e
    um carimbo de data/hora. As entidades com a mesma chave de partição podem ser consultadas
    mais rapidamente e inseridas/atualizadas em operações atômicas. A chave de linha
    de uma entidade é seu identificador exclusivo dentro de uma partição.

## <a name="create-account"> </a>Criar uma conta de armazenamento do Azure

Para usar as operações de armazenamento, você precisa de uma conta de armazenamento do Azure. Você
pode criar uma conta de armazenamento seguindo essas etapas. (Você também pode
pode criar uma conta de armazenamento [usando a API REST][].)

1.  Faça logon no [Portal de Gerenciamento do Azure].

2.  Na parte inferior do painel de navegação, clique em **+NOVO**.

	![+novo][plus-new]

3.  Clique em **Conta de Armazenamento** e depois em **Criação Rápida**.

	![Caixa de diálogo Criação rápida][quick-create-storage]

4.  Na URL, digite um nome de subdomínio para usar na URI para a conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas e números. Esse valor se torna o nome do host no URI que é usado para lidar com os recursos Blob, Fila ou Tabela da assinatura.

5.  Escolha uma Região/Grupo de Afinidade no qual deseja localizar o armazenamento. Se você usar o armazenamento de seu aplicativo do Azure, selecione a mesma região onde você implantará seu aplicativo.

6.  Clique em **Criar Conta de Armazenamento**.

## <a name="create-app"> </a>Criar um aplicativo Node.js

Crie um aplicativo do Node.js em branco. Para obter instruções sobre como criar um aplicativo Node.js, consulte [Criar e implantar um aplicativo Node.js em um site do Azure], [Serviço de Nuvem do Node.js] (usando o Windows PowerShell) ou [Site com o WebMatrix].

## <a name="configure-access"> </a>Configurar seu aplicativo para acessar o armazenamento

Para usar o armazenamento do Azure, você precisa baixar e usar o pacote do Azure Node.js, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST do armazenamento.

### Usar o Gerenciador de Pacotes de Nós (NPM) para obter o pacote

1.  Use uma interface de linha de comando, como **PowerShell** (Windows,) **Terminal** (Mac) ou **Bash** (Unix), e vá até a pasta onde você criou a amostra do aplicativo.

2.  Digite **npm install azure** na janela de comando, que deve
    resultar na seguinte saída:

        azure@0.7.5 node_modules\azure
		├── dateformat@1.0.2-1.2.3
		├── xmlbuilder@0.4.2
		├── node-uuid@1.2.0
		├── mime@1.2.9
		├── underscore@1.4.4
		├── validator@1.1.1
		├── tunnel@0.0.2
		├── wns@0.5.3
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Você pode executar o comando **ls** manualmente para verificar se uma pasta
    **node\_modules** foi criada. Dentro dessa pasta você
    encontrará o pacote **azure**, que contém as bibliotecas necessárias para
    acessar o armazenamento.

### Importar o pacote

Usando o bloco de notas ou outro editor de texto, adicione o seguinte à parte superior do
arquivo **server.js** do aplicativo onde você pretende usar o armazenamento:

    var azure = require('azure');

## <a name="setup-connection-string"> </a>Configurar uma conexão de armazenamento do Azure

O módulo do Azure lerá as variáveis de ambiente AZURE\_STORAGE\_ACCOUNT e AZURE\_STORAGE\_ACCESS\_KEY para obter as informações necessárias para se conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta ao chamar **TableService**.

Para obter um exemplo de como definir as variáveis de ambiente em um arquivo de configuração para um Serviço de Nuvem do Azure, consulte [Serviço de Nuvem do Node.js com Armazenamento].

Para obter um exemplo de como definir as variáveis de ambiente no portal de gerenciamento para um Site do Azure, consulte [Aplicativo da Web do Node.js com Armazenamento].

## <a name="create-table"> </a>Como criar uma tabela

O código a seguir cria um objeto **TableService** e usa-o para criar uma nova tabela. Adicione o seguinte próximo à parte superior do **server.js**.

    var tableService = azure.createTableService();

A chamada para **createTableIfNotExists** retornará a tabela especificada se ela existir ou criará uma nova tabela com o nome especificado se ela ainda não existir. O exemplo a seguir criará uma nova tabela denominada 'mytable' se ele ainda não existir:

    tableService.createTableIfNotExists('mytable', function(error){
		if(!error){
			// Table exists or created
		}
	});

###Filtros

É possível aplicar operações de filtragem opcionais às operações executadas usando **TableService**. As operações de filtragem podem incluir log, repetição automática etc. Os filtros são objetos que implementam um método com a assinatura:

		function handle (requestOptions, next)

Após fazer seu pré-processamento nas opções de solicitação, o método precisará chamar "next", passando um retorno de chamada com a assinatura a seguir:

		function (returnObject, finalCallback, next)

Nesse retorno de chamada, e após processar o returnObject (a resposta da solicitação ao servidor), o retorno de chamada precisará invocar next, se ele existir, para continuar processando outros filtros ou simplesmente invocar finalCallback para terminar a invocação de serviço.

Dois filtros que implementam a lógica de repetição estão incluídos no SDK do Azure para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte código cria um objeto **TableService** que usa o **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var tableService = azure.createTableService().withFilter(retryOperations);

## <a name="add-entity"> </a>Como adicionar uma entidade a uma tabela

Para adicionar uma entidade, crie primeiro um objeto que defina as propriedades da
sua entidade e seus tipos de dados. Observe que, para cada entidade, você deve
especificar um **PartitionKey** e um **RowKey**. Eles são os identificadores exclusivos
das suas eentidades e são valores que poderão ser consultados muito mais rápido
do que as suas outras propriedades. O sistema usa **PartitionKey** para distribuir automaticamente as entidades da tabela por vários nós de armazenamento.
As entidades com o mesmo **PartitionKey** são armazenadas no mesmo nó. **RowKey** é a ID exclusiva da entidade na partição à qual pertence. Para adicionar uma entidade à sua tabela, passe o objeto de entidade para o método **insertEntity**.

    var task = {
		PartitionKey : 'hometasks'
		, RowKey : '1'
		, Description : 'Take out the trash'
		, DueDate: new Date(2012, 6, 20)
	};
	tableService.insertEntity('mytable', task, function(error){
		if(!error){
			// Entity inserted
		}
	});

## <a name="update-entity"> </a>Como atualizar uma entidade

Há vários métodos disponíveis para atualizar uma entidade existente:

* **updateEntity** - Atualiza uma entidade existente substituindo-a.

* **mergeEntity** - Atualiza uma entidade existente mesclando novos valores de propriedade à entidade existente.

* **insertOrReplaceEntity** - Atualiza uma entidade existente substituindo-a. Se nenhuma entidade existir, uma nova será inserida.

* **insertOrMergeEntity** - Atualiza uma entidade existente mesclando novos valores de propriedade aos existentes. Se nenhuma entidade existir, uma nova será inserida.

O exemplo a seguir demonstra a atualização de uma entidade usando **updateEntity**:

	var task = {
		PartitionKey : 'hometasks'
		, RowKey : '1'
		, Description : 'Wash Dishes'
	}
	tableService.updateEntity('mytable', task, function(error){
		if(!error){
			// Entity has been updated
		}
	});
    
Com **updateEntity** e **mergeEntity**, se a entidade que está sendo atualizada não existir, haverá falha na operação de atualização. Portanto, se desejar armazenar uma entidade independentemente de sua existência, você deverá usar **insertOrReplaceEntity** ou **insertOrMergeEntity**.

## <a name="change-entities"> </a>Como trabalhar com grupos de entidades

Às vezes, convém enviar várias operações juntas em um
lote para garantir o processamento atômico pelo servidor. Para fazer isso, você deve usar o método **beginBatch** em **TableService** e, em seguida, chamar a série de operações como sempre. A diferença é que as funções de retorno de chamada
desses operadores indicarão que a operação foi em lote, e não enviadas ao servidor. Quando desejar enviar o lote, chame **commitBatch**. O retorno de chamada fornecido para esse método
indicará se o lote inteiro foi enviado com êxito. O exemplo a seguir demonstra o envio de duas entidades em um lote:

    var tasks=[
		{
			PartitionKey : 'hometasks'
			, RowKey : '1'
			, Description : 'Take out the trash.'
			, DueDate : new Date(2012, 6, 20)
		}
		, {
			PartitionKey : 'hometasks'
			, RowKey : '2'
			, Description : 'Wash the dishes.'
			, DueDate : new Date(2012, 6, 20)
		}
	]
	tableService.beginBatch();
	var async=require('async');

	async.forEach(tasks
		, function taskIterator(task, callback){
			tableService.insertEntity('mytable', task, function(error){
				if(!error){
					// Entity inserted
					callback(null);
				} else {
					callback(error);
				}
			});
		}
		, function(error){
			if(!error){
				// All inserts completed
				tableService.commitBatch(function(error){
					if(!error){
						// Batch successfully commited
					}
				});
			}
		});

<div class="dev-callout">
<strong>Observação</strong>
<p>O exemplo acima usa o módulo 'async' para garantir que as entidades tenham sido todas enviadas com êxito antes da chamada de **commitBatch**.</p>
</div>

## <a name="query-for-entity"> </a>Como consultar uma entidade

Para consultar uma entidade em uma tabela, use o método **queryEntity**, passando **PartitionKey** e **RowKey**.

    tableService.queryEntity('mytable'
		, 'hometasks'
		, '1'
		, function(error, entity){
			if(!error){
				// entity contains the returned entity
			}
		});

## <a name="query-set-entities"> </a>Como consultar um conjunto de entidades

Para consultar uma tabela, use o objeto **TableQuery** para criar uma expressão de consulta usando cláusulas, como **select**, **from**, **where** (incluindo cláusulas de conveniência, como **wherePartitionKey**, **whereRowKey**, **whereNextPartitionKey** e **whereNextRowKey**), **and**, **or** e **top**. Em seguida, passe a expressão de consulta para o método **queryEntities**. Você pode usar os resultados em um loop **for** dentro do retorno de chamada.

Este exemplo localiza todas as tarefas em Seattle com base no **PartitionKey**.

    var query = azure.TableQuery
		.select()
		.from('mytable')
		.where('PartitionKey eq ?', 'hometasks');
	tableService.queryEntities(query, function(error, entities){
		if(!error){
			//entities contains an array of entities
		}
	});

## <a name="query-entity-properties"> </a>Como consultar um subconjunto de propriedades de entidade

Uma consulta a uma tabela pode recuperar apenas algumas propriedades de uma entidade.
Essa técnica, chamada *projeção*, reduz a largura de banda e pode melhorar
o desempenho da consulta, principalmente para grandes entidades. Use a cláusula **select**
e passe os nomes das propriedades que você gostaria de trazer para o cliente.

A consulta no código a seguir retorna apenas as **Descriptions** das
entidades na tabela; observe que na saída do programa, a **DueDate** será mostrada como **undefined** por que não foi enviado pelo servidor.

<div class="dev-callout">
<strong>Observação</strong>
<p>Observe que o trecho a seguir funciona apenas com o serviço de armazenamento de nuvem,
a palavra-chave <b>select</b> não tem suporte do Emulador de Armazenamento.</p>
</div>

    var query = azure.TableQuery
		.select('Description')
		.from('mytable')
		.where('PartitionKey eq ?', 'hometasks');
	tableService.queryEntities(query, function(error, entities){
		if(!error){
			//entities contains an array of entities
		}
	});

## <a name="delete-entity"> </a>Como excluir uma entidade

Você pode excluir uma entidade usando suas chaves de partição e de linha. Neste exemplo, o objeto **task1** contém os valores **RowKey** e **PartitionKey** da entidade a ser excluída. Em seguida, o objeto é passado para o método **deleteEntity**.

    tableService.deleteEntity('mytable'
		, {
			PartitionKey : 'hometasks'
			, RowKey : '1'
		}
		, function(error){
			if(!error){
				// Entity deleted
			}
		});

## <a name="delete-table"> </a>Como excluir uma tabela

O código a seguir exclui uma tabela de uma conta de armazenamento.

    tableService.deleteTable('mytable', function(error){
		if(!error){
			// Table deleted
		}
	});

## <a name="next-steps"> </a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de tabela, siga estes links para saber como executar tarefas de armazenamento mais complexas.

-   Consulte a referência do MSDN: [Armazenando e acessando dados no Azure][].
-   [Visite o Blog da Equipe de Armazenamento do Azure][].
-   Visite o repositório [SDK do Azure para o nó] no GitHub.

  [SDK do Azure para o nó]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Próximas etapas]: #next-steps
  [O que é o serviço Tabela?]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #create-account
  [Criar um aplicativo Node.js]: #create-app
  [Configurar seu aplicativo para acessar o armazenamento]: #configure-access
  [Configurar uma conexão de armazenamento do Azure]: #setup-connection-string
  [Como criar uma tabela]: #create-table
  [Como adicionar uma entidade a uma tabela]: #add-entity
  [Como atualizar uma entidade]: #update-entity
  [Como trabalhar com grupos de entidades]: #change-entities
  [Como consultar uma entidade]: #query-for-entity
  [Como consultar um conjunto de entidades]: #query-set-entities
  [Como consultar um subconjunto de propriedades de entidade]: #query-entity-properties
  [Como excluir uma entidade]: #delete-entity
  [Como excluir uma tabela]: #delete-table
  [Table1]: ./media/storage-nodejs-how-to-use-table-storage/table1.png
  [OData.org]: http://www.odata.org/
  [usando a API REST]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh264518.aspx
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com

  [plus-new]: ./media/storage-nodejs-how-to-use-table-storage/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-table-storage/quick-storage.png
  
  
  
  [Serviço de Nuvem do Node.js]: {localLink:2221} "Aplicativo Web com Express"
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Visite o Blog da Equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Site com o WebMatrix]: /pt-br/develop/nodejs/tutorials/web-site-with-webmatrix/
[Serviço de Nuvem do Node.js com Armazenamento]: /pt-br/develop/nodejs/tutorials/web-app-with-storage/
  [Aplicativo da Web do Node.js com Armazenamento]: /pt-br/develop/nodejs/tutorials/web-site-with-storage/
 [Criar e implantar um aplicativo Node.js em um site do Azure]: /pt-br/develop/nodejs/tutorials/create-a-website-(mac)/

