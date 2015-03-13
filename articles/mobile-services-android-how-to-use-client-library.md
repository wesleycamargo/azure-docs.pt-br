<properties 
	pageTitle="Trabalhando com a Biblioteca de Cliente Android para Serviços Móveis" 
	description="Saiba como usar um cliente Android para os serviços móveis do Azure." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="Mobile-Android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="10/20/2014" 
	ms.author="ricksal"/>

# Como usar a biblioteca de cliente Android para os Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/pt-br/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/pt-br/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/pt-br/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/pt-br/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android" class="current">Android</a><a href="/pt-br/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


Este guia mostra como executar cenários comuns usando o cliente Android para os Serviços Móveis do Azure.  Os cenários abordados incluem consulta de dados, inserção, atualização e exclusão de dados, autenticação de usuários, tratamento de erros e personalização do cliente. Se for iniciante em Serviços Móveis, deve primeiro concluir o [Início rápido dos Serviços Móveis][Introdução aos Serviços Móveis]. O tutorial de início rápido ajuda a configurar sua conta e criar seu primeiro serviço móvel.

As amostras são escritas em Java e exigem o [SDK dos Serviços Móveis]. Este tutorial também requer o [SDK do Android](https://go.microsoft.com/fwLink/p/?LinkID=280125&clcid=0x409), que inclui IDE (Ambiente de Desenvolvimento Integrado)do Eclipse e o plug-in de ADT (Android Developer Tools). O SDK dos Serviços Móveis dá suporte ao Android versão 2.2 ou posterior, mas recomendamos compilar com base no Android versão 4.2 ou posterior.



## Sumário

- [O que são Serviços Móveis]
- [Conceitos]
- [Configuração e pré-requisitos]
- [Como: Criar o cliente de Serviços Móveis]
- [Como: Criar uma referência de tabela]
	- [A estrutura API]
- [Como: Consultar dados por meio de um serviço móvel]
	- [Filtrar dados retornados]
    - [Classificar dados retornados]
	- [Retornar dados em páginas]
	- [Selecionar colunas específicas]
	- [Como: Concatenar métodos de consulta]
- [Como: Inserir dados em um serviço móvel]
- [Como: Atualizar dados em um serviço móvel]
- [Como: Excluir dados em um serviço móvel]
- [Como: Pesquisar um item específico]
- [Como: Trabalhar com dados não tipados]
- [Como: Associar dados à interface com o usuário]
	- [Como: Definir o layout]
	- [Como: Definir o adaptador]
	- [Como: Usar o adaptador]
- [Como: Autenticar usuários]
	- [Armazenar tokens de autenticação em cache]
- [Como: Tratar erros]
- [Como: Personalizar o cliente]
	- [Personalizar cabeçalhos de solicitação]
	- [Personalizar a serialização]
- [Próximas etapas][]

[AZURE.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]


<h2><a name="setup"></a>Configuração e pré-requisitos</h2>

Suponhamos que você tenha criado um serviço móvel e uma tabela. Para obter mais informações, consulte [Criar uma tabela](http://go.microsoft.com/fwlink/p/?LinkId=298592). No código usado neste tópico, supomos que a tabela é denominada *ToDoItem*, e tem as seguintes colunas:

<ul>
<li>id</li>
<li>texto</li>
<li>concluído</li>
<li>conclusão</li>
<li>duração</li>
</ul>

O objeto tipado do lado do cliente correspondente é o seguinte:

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
		private Date due
		private Integer duration;
	}
	
Quando o esquema dinâmico está habilitado, os Serviços Móveis do Azure geram automaticamente novas colunas com base no objeto da solicitação de inserção ou atualização. Para obter mais informações, consulte [Esquema dinâmico]( http://go.microsoft.com/fwlink/p/?LinkId=296271).

<h2><a name="create-client"></a>Como: Criar o cliente de Serviços Móveis</h2>

O código a seguir cria o objeto [MobileServiceClient](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html) que é usado para acessar seu serviço móvel. 

			MobileServiceClient mClient = new MobileServiceClient(
					"MobileServiceUrl", // Replace with the above Site URL
					"AppKey", 			// replace with the Application Key 
					this)

No código acima, substitua `MobileServiceUrl` e `AppKey` pela URL e pela chave do aplicativo do serviço móvel, nessa ordem. Ambas estão disponíveis no Portal de Gerenciamento do Azure, selecionando seu serviço móvel e clicando em *Dashboard*.

<h2><a name="instantiating"></a>Como: Criar uma referência de tabela</h2>

A maneira mais fácil de consultar ou modificar dados no serviço móvel é usando o *typed programming model*, já que o Java é uma linguagem fortemente tipada (posteriormente, discutiremos o modelo *untyped* não tipado). Esse modelo fornece perfeita serialização e desserialização para JSON usando a biblioteca <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> ao enviar dados entre o cliente e o serviço móvel: o desenvolvedor não precisa fazer nada, a estrutura lida com tudo isso.

A primeira tarefa que você executa para consultar ou modificar dados é criar um objeto [MobileServiceTable](http://go.microsoft.com/fwlink/p/?LinkId=296835) chamando o método **getTable** no [**MobileServiceClient**](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html).  Examinaremos duas sobrecargas desse método:

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

No código a seguir, *mClient* é uma referência para seu cliente de serviço móvel.

A [primeira sobrecarga](http://go.microsoft.com/fwlink/p/?LinkId=296839) é usada onde o nome de classe e o nome da tabela são iguais:

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


A [segunda sobrecarga](http://go.microsoft.com/fwlink/p/?LinkId=296840) é usada quando o nome da tabela é diferente do nome do tipo.

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

 


### <a name="api"></a>A estrutura API

As operações da tabela de serviços móveis usam o modelo de retorno de chamada assíncrono. Os métodos que envolvem consultas e operações, como inserções, atualizações e exclusões, têm todos um parâmetro que é um objeto de retorno de chamada. Este objeto contém sempre um método **OnCompleted**. O método **onCompleted** contém um parâmetro que é um objeto **Exception**, que você pode testar para determinar o êxito da chamada do método. Um objeto nulo **Exception** indica êxito, caso contrário o objeto **Exception** descreverá o motivo da falha.

Há vários objetos diferentes de retorno de chamada, e qual deles usar dependerá se você estiver consultando, modificando ou excluindo dados. Os parâmetros para o método *onCompleted* variam, dependendo do objeto de retorno de chamada do qual ele faz parte.


<h2><a name="querying"></a>Como: Consultar dados por meio de um serviço móvel</h2>

Esta seção descreve como emitir consultas para o serviço móvel. As subseções descrevem diferentes aspectos, como classificação, filtragem e paginação. Finalmente, discutiremos como você pode concatenar essas operações juntas.

O código a seguir retorna todos os itens da tabela *ToDoItem*. 

		mToDoTable.execute(new TableQueryCallback<ToDoItem>() {
				public void onCompleted(List<ToDoItem> result, int count,
					Exception exception, ServiceFilterResponse response) {
					if (exception == null) {
						for (ToDoItem item : result) {
                			Log.i(TAG, "Read object with ID " + item.id);  
						}
					}
				}
			});

Consultas como essa usam o objeto de retorno de chamada [**TableQueryCallback&lt;E&gt;**](http://go.microsoft.com/fwlink/p/?LinkId=296849). 

O parâmetro *result* retorna o conjunto de resultados da consulta, e o código dentro da ramificação de êxito do teste *exception* mostra como analisar linhas individuais.


### <a name="filtering"></a>Como: Filtrar dados retornados

O código a seguir retorna todos os itens da tabela *ToDoItem* cujo campo *complete* é igual a *false*. *mToDoTable* é a referência à tabela de serviços móveis que criamos anteriormente. 

		mToDoTable.where().field("complete").eq(false)
				  .execute(new TableQueryCallback<ToDoItem>() {
						public void onCompleted(List<ToDoItem> result, 
												int count, 
												Exception exception,
												ServiceFilterResponse response) {
				if (exception == null) {
					for (ToDoItem item : result) {
                		Log.i(TAG, "Read object with ID " + item.id);  
					}
				} 
			}
		});

Você inicia um filtro com uma chamada do método [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296867) na referência de tabela. Isso é seguido por uma chamada do método [**field**](http://go.microsoft.com/fwlink/p/?LinkId=296869) seguida por uma chamada de método que especifica o predicado lógico. Métodos de predicado possíveis incluem [**eq**](http://go.microsoft.com/fwlink/p/?LinkId=298461), [**ne**](http://go.microsoft.com/fwlink/p/?LinkId=298462), [**gt**](http://go.microsoft.com/fwlink/p/?LinkId=298463), [**ge**](http://go.microsoft.com/fwlink/p/?LinkId=298464), [**lt**](http://go.microsoft.com/fwlink/p/?LinkId=298465), [**le**](http://go.microsoft.com/fwlink/p/?LinkId=298466) etc.

Isso é suficiente para comparar os campos de número e de cadeia de caracteres com valores específicos. Mas, você pode fazer muito mais.

Por exemplo, você pode filtrar por datas. Você pode comparar o campo de data inteiro, mas também pode comparar partes da data, com métodos como [**year**](http://go.microsoft.com/fwlink/p/?LinkId=298467), [**month**](http://go.microsoft.com/fwlink/p/?LinkId=298468), [**day**](http://go.microsoft.com/fwlink/p/?LinkId=298469), [**hour**](http://go.microsoft.com/fwlink/p/?LinkId=298470), [**minute**](http://go.microsoft.com/fwlink/p/?LinkId=298471) and [**second**](http://go.microsoft.com/fwlink/p/?LinkId=298472). O código parcial a seguir adiciona um filtro a itens cuja *due date* é igual a 2013.

		mToDoTable.where().year("due").eq(2013)

Você pode fazer uma grande variedade de filtros complexos em campos de cadeia de caracteres com métodos como [**startsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298473), [**endsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298474), [**concat**](http://go.microsoft.com/fwlink/p/?LinkId=298475), [**subString**](http://go.microsoft.com/fwlink/p/?LinkId=298477), [**indexOf**](http://go.microsoft.com/fwlink/p/?LinkId=298488), [**replace**](http://go.microsoft.com/fwlink/p/?LinkId=298491), [**toLower**](http://go.microsoft.com/fwlink/p/?LinkId=298492), [**toUpper**](http://go.microsoft.com/fwlink/p/?LinkId=298493), [**trim**](http://go.microsoft.com/fwlink/p/?LinkId=298495), and [**length**](http://go.microsoft.com/fwlink/p/?LinkId=298496). Os filtros de código parcial a seguir são para linhas de tabela onde a coluna *text* começa com "PRI0".

		mToDoTable.where().startsWith("text", "PRI0")

Campos numéricos também permitem uma grande variedade de filtros mais complexos com métodos como [**add**](http://go.microsoft.com/fwlink/p/?LinkId=298497), [**sub**](http://go.microsoft.com/fwlink/p/?LinkId=298499), [**mul**](http://go.microsoft.com/fwlink/p/?LinkId=298500), [**div**](http://go.microsoft.com/fwlink/p/?LinkId=298502), [**mod**](http://go.microsoft.com/fwlink/p/?LinkId=298503), [**floor**](http://go.microsoft.com/fwlink/p/?LinkId=298505), [**ceiling**](http://go.microsoft.com/fwlink/p/?LinkId=298506), and [**round**](http://go.microsoft.com/fwlink/p/?LinkId=298507). Os filtros de código parcial a seguir são para linhas de tabela onde *duration* é um número par.

		mToDoTable.where().field("duration").mod(2).eq(0)


Você pode combinar predicados com métodos como [**and**](http://go.microsoft.com/fwlink/p/?LinkId=298512), [**or**](http://go.microsoft.com/fwlink/p/?LinkId=298514) and [**not**](http://go.microsoft.com/fwlink/p/?LinkId=298515). Este código parcial combina dois dos exemplos acima.

		mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")

E você pode agrupar e aninhar operadores lógicos, conforme mostrado neste código parcial:

		mToDoTable.where()
					.year("due").eq(2013)
						.and
					(startsWith("text", "PRI0").or().field("duration").gt(10))

Para obter uma discussão mais detalhada e exemplos de filtragem, consulte [Explorando a riqueza do modelo de consulta de cliente dos Serviços Móveis do Android](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="sorting"></a>Como: Classificar dados retornados

O código a seguir retorna todos os itens de uma tabela *ToDoItems* classificada em ordem crescente pelo *text* campo. *mToDoTable* é a referência à tabela de serviços móveis que criamos anteriormente.

		mToDoTable.orderBy("text", QueryOrder.Ascending)
			.execute(new TableQueryCallback<ToDoItem>() { 
				/* same implementation as above */ 
			}); 

O primeiro parâmetro do método [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=298519) é uma cadeia de caracteres igual ao nome do campo em que será feita a classificação.

O segundo parâmetro usa a enumeração [**QueryOrder**](http://go.microsoft.com/fwlink/p/?LinkId=298521) para especificar se a classificação será em ordem crescente ou decrescente.

Observe que, se você estiver filtrando usando o método ***where*** , o método ***where*** deve ser chamado antes do método ***orderBy*** .

### <a name="paging"></a>Como: Retornar dados em páginas

O primeiro exemplo mostra como selecionar os 5 primeiros itens de uma tabela. A consulta retorna os itens de uma tabela  *ToDoItems*. *mToDoTable* é a referência à tabela de serviços móveis criada anteriormente.

		mToDoTable.top(5)
	            .execute(new TableQueryCallback<ToDoItem>() {	
	            public void onCompleted(List<ToDoItem> result, 
										int count,
	                    				Exception exception, 
										ServiceFilterResponse response) {
	                if (exception == null) {
	                    for (ToDoItem item : result) {
                			Log.i(TAG, "Read object with ID " + item.id);  
	                    }
	                } 
	            }
	        });

Em seguida, definiremos uma consulta que ignorará os 5 primeiros itens e, em seguida, retornará os 5 seguintes.

		mToDoTable.skip(5).top(5)
	            .execute(new TableQueryCallback<ToDoItem>() {	
	            // implement onCompleted logic here
	        });


### <a name="selecting"></a>Como: Selecionar colunas específicas

TO código a seguir ilustra como retornar todos os itens de uma tabela  *ToDoItems*, mas exibe apenas os campos *complete* e *text*. *mToDoTable* é a referência à tabela de serviços móveis que criamos anteriormente.

		mToDoTable.select("complete", "text")
	            .execute(new TableQueryCallback<ToDoItem>() { 
					/* same implementation as above */ 
			}); 

	
Aqui, os parâmetros para a função select são os nomes de cadeia de caracteres das colunas da tabela que você deseja retornar.

O método [**select**](http://go.microsoft.com/fwlink/p/?LinkId=290689) precisa seguir métodos como [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296296) e [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=296313), se estiverem presentes. Ele pode ser seguido por métodos como [**top**](http://go.microsoft.com/fwlink/p/?LinkId=298731).

### <a name="chaining"></a>Como: Concatenar métodos de consulta 

Os métodos usados na consulta de tabelas de serviços móveis podem ser concatenados. Isso lhe permitirá executar tarefas como selecionar colunas específicas ou linhas filtradas que são classificadas e paginadas. Você pode criar filtros lógicos bastante complexos.

O que faz isso funcionar é que os métodos de consulta usados retornam objetos [**MobileServiceQuery&lt;T&gt;**](http://go.microsoft.com/fwlink/p/?LinkId=298551) que, por sua vez, podem ter métodos adicionais invocados neles. Para encerrar a série de métodos e realmente executar a consulta, chame o método [**execute**](http://go.microsoft.com/fwlink/p/?LinkId=298554).

Este é um exemplo de código onde *mToDoTable* é uma referência à tabela *ToDoItem* de serviços móveis.

		mToDoTable.where().year("due").eq(2013)
						.and().startsWith("text", "PRI0")
						.or().field("duration").gt(10)
					.select("id", "complete", "text", "duration")
					.orderBy(duration, QueryOrder.Ascending).top(20)				
					.execute(new TableQueryCallback<ToDoItem>() { 
						/* code to execute */ 
				});

O principal requisito no encadeamento de métodos é que o método *where* e os predicados precisam vir primeiro. Depois disso, você poderá chamar métodos subsequentes na ordem que melhor atenda às necessidades do seu aplicativo.


<h2><a name="inserting"></a>Como: Inserir dados em um serviço móvel</h2>

O código a seguir mostra como inserir novas linhas em uma tabela.

Primeiro, crie uma instância da classe *ToDoItem* e defina suas propriedades.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;
		mToDoItem.duration = 5; 
		
 Em seguida chame o método [**insert**](http://go.microsoft.com/fwlink/p/?LinkId=296862).

		mToDoTable.insert(mToDoItem, new TableOperationCallback<ToDoItem>() {
			public void onCompleted(ToDoItem entity, 
								Exception exception, 
								ServiceFilterResponse response) {	
				if (exception == null) {
                		Log.i(TAG, "Read object with ID " + entity.id);  
				} 
			}
		});

Pra operações**insert**, o objeto de retorno de chamada é um [**TableOperationCallback&lt;ToDoItem&gt;**](http://go.microsoft.com/fwlink/p/?LinkId=296865).

O parâmetro de entidade do método **onCompleted** contém o objeto recém-inserido. O código bem-sucedido mostra como acessar a *id* da linha inserida.

Os Serviços Móveis dão suporte a valores exclusivos e personalizados de cadeia de caracteres para a ID da tabela. Isso permite que os aplicativos usem valores personalizados, como endereços de email ou nomes de usuário, para a coluna de id de uma tabela dos Serviços Móveis. Por exemplo, se você quiser identificar cada registro por um endereço de email, poderá usar o seguinte objeto JSON.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.id = "myemail@mydomain.com";
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;
		mToDoItem.duration = 5; 

Se um valor de id de cadeia de caracteres não for fornecido ao inserir novos registros em uma tabela, os Serviços Móveis gerarão um valor exclusivo para a id.

O suporte às ids de cadeia de caracteres oferece as seguintes vantagens aos desenvolvedores

+ As Ids podem ser geradas sem fazer uma varredura no banco de dados.
+ Os registros são mais fáceis de mesclar em tabelas ou bancos de dados diferentes.
+ Os valores de ids podem integrar-se melhor a uma lógica do aplicativo.

Você também pode usar scripts de servidor para definir valores de ids. O exemplo de script a seguir gera um GUID personalizado e o atribui a um novo id de registro. Isso é semelhante ao valor de id que os Serviços Móveis gerariam se você não transmitisse um valor para um id de registro.

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


Se um aplicativo fornecer um valor para uma id, os Serviços Móveis irão armazená-lo como está. Isso inclui espaços em branco à direita ou à esquerda. O espaço em branco não será cortado do valor.

O valor de `id` deve ser exclusivo e não deve incluir caracteres dos seguintes conjuntos:

+ Caracteres de controle: [0x0000-0x001F] e [0x007F-0x009F]. Para obter mais informações, consulte [Códigos de controle ASCII C0 e C1].
+  Caracteres de impressão: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  Os ids "." e ".."

Como alternativa, você pode usar Ids de números inteiros para suas tabelas. Para usar uma Id de número inteiro, você deve criar sua tabela com o comando `mobile table create` usando a opção `--integerId` . Esse comando é usado com a CLI (interface de linha de comando) para Azure. Para obter mais informações sobre como usar a CLI, consulte [CLI para gerenciar tabelas de Serviços Móveis].


<h2><a name="updating"></a>Como: Atualizar dados em um serviço móvel</h2>

O código a seguir mostra como atualizar dados em uma tabela. Neste exemplo, *mToDoItem* é uma referência a um item da tabela *ToDoItem* e atualizamos sua propriedade *duration*.

		mToDoItem.duration = 5;
		mToDoTable.update(mToDoItem, new TableOperationCallback<ToDoItem>() {
			public void onCompleted(ToDoItem entity, 
									Exception exception, 
									ServiceFilterResponse response) {
				if (exception == null) {
            			Log.i(TAG, "Read object with ID " + entity.id);  
				} 
			}
		});

Observe que o objeto de retorno de chamada e os parâmetros do método *onCompleted* são os mesmos de quando fazemos uma inserção.

<h2><a name="deleting"></a>Como: Excluir dados em um serviço móvel</h2>

O código a seguir mostra como excluir dados de uma tabela. Ele exclui um item existente da tabela ToDoItem, usando uma referência ao item que, neste caso, é *mToDoItem*.

		mToDoTable.delete(mToDoItem, new TableDeleteCallback() {
		    public void onCompleted(Exception exception,
									ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Object deleted");
		        }
		    }
		});

Observe que no caso *delete*, o objeto de retorno de chamada é um [**TableDeleteCallback**](http://go.microsoft.com/fwlink/p/?LinkId=296858) e o método**onCompleted** é um pouco diferente, nenhuma linha da tabela será retornada.

O código a seguir ilustra uma outra maneira de fazer isso. Ele exclui um item existente da tabela ToDoItem, especificando o valor do campo id da linha para exclusão (pressupondo que seja igual a "37BBF396-11F0-4B39-85C8-B319C729AF6D"). 

		mToDoTable.delete("37BBF396-11F0-4B39-85C8-B319C729AF6D", new TableDeleteCallback() {
		    public void onCompleted(Exception exception, 
		            ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Object deleted");
		        }
		    }
		});

<h2><a name="lookup"></a>Como: Pesquisar um item específico</h2>
Às vezes, você deseja pesquisar um item específico por sua *id*, diferentemente da consulta, onde você geralmente obtém uma coleção de itens que atende a alguns critérios. O código a seguir mostra como fazer isso, para *id* = "37BBF396-11F0-4B39-85C8-B319C729AF6D".

		mToDoTable.lookUp("37BBF396-11F0-4B39-85C8-B319C729AF6D", new TableOperationCallback<ToDoItem>() {
		    public void onCompleted(item entity, Exception exception,
		            ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Read object with ID " + entity.id);    
		        }
		    }
		});


<h2><a name="untyped"></a>Como: Trabalhar com dados não tipados</h2>

O modelo de programação não tipado oferece um controle exato sobre a serialização JSON, e há alguns cenários onde você pode querer usá-lo, por exemplo, se a sua tabela de serviços móveis contiver um grande número de colunas e você só precisa fazer referência a algumas delas. O uso do modelo tipado requer que você defina todas as colunas da tabela de serviços móveis na sua classe de dados. Mas, com o modelo não tipado, você define apenas as colunas que precisa usar.

Semelhante ao modelo tipado, você começa obtendo uma referência de tabela, mas, nesse caso, é um objeto [MobileServicesJsonTable](http://go.microsoft.com/fwlink/p/?LinkId=298733). Você obtém a referência chamando o método [getTable()](http://go.microsoft.com/fwlink/p/?LinkId=298734) em uma instância do cliente de Serviços Móveis.


Use a sobrecarga a seguir desse método, que é usado para trabalhar com os modelos de programação não tipados baseados em JSON:

		public class MobileServiceClient {
		    public MobileServiceJsonTable getTable(String name);
		}

A maioria das chamadas API para acessar dados são semelhante às chamadas de programação tipadas. A principal diferença é que o modelo não tipado você invocar métodos no objeto **MobileServiceJsonTable**, em vez do objeto **MobileServiceTable**. O uso do objeto de retorno de chamada e do método **onCompleted** é muito semelhante ao modelo tipado.


### <a name="json_instance"></a>Como: Criar uma instância de uma tabela não tipada

Após criar uma instância do cliente de Serviços Móveis (aqui, a variável *mClient*), você cria uma instância de uma **MobileServiceJsonTable**, com o código a seguir.

		MobileServiceJsonTable mTable = mClient.getTable("ToDoItem");

Após criar uma instância de **MobileServiceJsonTable**, você poderá chamar praticamente todos os métodos possíveis que ela contém com o modelo de programação tipado. No entanto, em alguns casos, os métodos usam um parâmetro não tipado, como podemos ver nos exemplos a seguir.

### <a name="json_insert"></a>Como: Inserir em uma tabela não tipada

O código a seguir mostra como fazer uma inserção. A primeira etapa é criar um [**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html), que faz parte da biblioteca <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>.

		JsonObject task = new JsonObject();
		task.addProperty("text", "Wake up");
		task.addProperty("complete", false);
		task.addProperty("duration", 5);

A próxima etapa consiste em inserir o objeto. A função de retorno de chamada passada para o método [**insert**](http://go.microsoft.com/fwlink/p/?LinkId=298535) é uma instância da classe [**TableJsonOperationCallback**](http://go.microsoft.com/fwlink/p/?LinkId=298532). Observe como o primeiro parâmetro do método *onCompleted* é um JsonObject.
		 
		mTable.insert(task, new TableJsonOperationCallback() {
		    public void onCompleted(JsonObject jsonObject, 
									Exception exception,
									ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Object inserted with ID " + 
		        jsonObject.getAsJsonPrimitive("id").getAsString());
		        }
		    }
		});


Observe como obtemos a ID do objeto inserido com esta chamada de método:

		        jsonObject.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>Como: Excluir em uma tabela não tipada

O código a seguir mostra como excluir uma instância, neste caso, a mesma instância de um **JsonObject** criado no exemplo *insert* anterior. Observe o objeto de retorno de chamada, **TableDeleteCallback**, é o mesmo usado no modelo de programação tipado e seu método **onCompleted** tem uma assinatura diferente da usada no exemplo **insert**.


		mTable.delete(task, new TableDeleteCallback() {
		    public void onCompleted(Exception exception, 
									ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Object deleted");
		        }
		    }
		});

You can also delete an instance directly by using its ID: 
		
		mTable.delete(task.getAsJsonPrimitive("id").getAsString(), ...)


### <a name="json_get"></a>Como: Retornar todas as linhas de uma tabela não tipada

O código a seguir mostra como recuperar uma tabela inteira. Observe que o modelo de programação não tipado usa um objeto de retorno de chamada diferente: [**TableJsonQueryCallback**](http://go.microsoft.com/fwlink/p/?LinkId=298543).

		mTable.execute(new TableJsonQueryCallback() {
		    public void onCompleted(JsonElement result, 
									int count, 
									Exception exception,
									ServiceFilterResponse response) {
		        if(exception == null){
		            JsonArray results = result.getAsJsonArray();
		            for(JsonElement item : results){
		                Log.i(TAG, "Read object with ID " + 
		            item.getAsJsonObject().getAsJsonPrimitive("id").getAsInt());
		            }
		        }
		    }
		});

Você pode fazer filtragem, classificação e paginação, concatenando os métodos que têm os mesmos nomes que os usados no modelo de programação tipado.


<h2><a name="binding"></a>Como: Associar dados à interface com o usuário</h2>

A associação de dados envolve três componentes:

- a fonte de dados
- o layout da tela
- e o adaptador que vincula esses dois.

Em nosso código de exemplo, retornamos os dados da tabela de serviços móveis *ToDoItem* em uma matriz. Esse é um padrão muito comum para aplicativos de dados: as consultas de banco de dados geralmente retornam uma coleção de linhas que o cliente obtém em uma lista ou uma matriz. Neste exemplo, a matriz é a fonte de dados. 

O código especifica um layout de tela que define a exibição dos dados que serão exibidos no dispositivo. 

E os dois são associados juntos com um adaptador, que, nesse código, é uma extensão da classe *ArrayAdapter&lt;ToDoItem&gt;*.

### <a name="layout"></a>Como: Definir o layout
 
O layout é definido por vários trechos de código XML. Com base em um layout existente, vamos supor que o código a seguir representa a **ListView** que queremos preencher com nossos dados de servidor.

	    <ListView
	        android:id="@+id/listViewToDo"
	        android:layout_width="match_parent"
	        android:layout_height="wrap_content"
	        tools:listitem="@layout/row_list_to_do" >
	    </ListView>
	

No código acima, o atributo *listitem* especifica a id do layout para uma linha individual na lista. Este é o código, que especifica uma caixa de seleção e seu texto associado. Isso será instanciado uma vez para cada item da lista. Um layout mais complexo seria especificar campos adicionais na exibição. Este código está no arquivo *row_list_to_do.xml*.

		<?xml version="1.0" encoding="utf-8"?>
		<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		    android:layout_width="match_parent"
		    android:layout_height="match_parent"
		    android:orientation="horizontal">		    
		    <CheckBox
		        android:id="@+id/checkToDoItem"
		        android:layout_width="wrap_content"
		        android:layout_height="wrap_content"
		        android:text="@string/checkbox_text" />
		</LinearLayout>
		

### <a name="adapter"></a>Como: Definir o adaptador
	
Como a fonte de dados da nossa exibição é uma matriz de *ToDoItem*, criamos a subclasse de nosso adaptador de uma classe *ArrayAdapter&lt;ToDoItem&gt;*. Essa subclasse produzirá uma exibição para cada *ToDoItem* usando o layout *row_list_to_do*.

No nosso código, definimos a seguinte classe que é uma extensão da classe *ArrayAdapter&lt;E&gt;*:

		public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {



Você deve substituir o método *getView* do adaptador. Este código de exemplo mostra como fazer isso: os detalhes variarão de acordo com o seu aplicativo.

	public View getView(int position, View convertView, ViewGroup parent) {
		View row = convertView;

		final ToDoItem currentItem = getItem(position);

		if (row == null) {
			LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
			row = inflater.inflate(R.layout.row_list_to_do, parent, false);
		}

		row.setTag(currentItem);

		final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
		checkBox.setText(currentItem.getText());
		checkBox.setChecked(false);
		checkBox.setEnabled(true);

		return row;
	}

Criamos uma instância dessa classe em nossa atividade, da seguinte forma:

		ToDoItemAdapter mAdapter;
		mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

Observe que o segundo parâmetro para o construtor ToDoItemAdapter é uma referência ao layout. A chamada para o construtor é seguida pelo código a seguir que primeiro obtém uma referência à **ListView**, e, em seguida, chama *setAdapter* para configurar a si próprio e usar o adaptador que acabamos de criar:

		ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
		listViewToDo.setAdapter(mAdapter);


### <a name="use-adapter"></a>Como: Usar o adaptador

Agora você está pronto para usar a vinculação de dados. O código a seguir mostra como obter os itens na tabela de serviços móveis, limpar o adaptador e, em seguida, chamar o método *add* do adaptador para preenchê-lo com os itens retornados.

		mToDoTable.execute(new TableQueryCallback<ToDoItem>() {
			public void onCompleted(List<ToDoItem> result, int count, Exception exception, ServiceFilterResponse response) {
				if (exception == null) {
					mAdapter.clear();
					for (ToDoItem item : result) {
						mAdapter.add(item);
					}
				} 
			}
		});

Você também deverá chamar o adaptador sempre que modificar a tabela *ToDoItem* se quiser exibir os resultados desse procedimento. Como as modificações são feitas de registro em registro, você estará lidando com uma única linha, em vez de uma coleção. Ao inserir um item, você chama o método *add* no adaptador e, ao excluir, você chama o método *remove*.


<h2><a name="authentication"></a>Como: Autenticar usuários</h2>

Os Serviços Móveis dão suporte à autenticação e à autorização de usuários de aplicativos usando uma variedade de provedores de identidade externos: Facebook, Google, Conta da Microsoft, Twitter e Active Directory do Azure. Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização em scripts do servidor. Para obter mais informações, consulte [Introdução à autenticação](http://go.microsoft.com/fwlink/p/?LinkId=296316).

Dois fluxos de autenticação são suportados: um fluxo *server* e um fluxo *client*. O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende da interface de autenticação da web do provedor. O fluxo de cliente permite uma integração mais profunda com recursos específicos do dispositivo, como logon único, uma vez que depende de provedores específicos e SDKs específicos do dispositivo.

Três etapas são necessárias para habilitar a autenticação no seu aplicativo:

<ol>
<li>Registrar seu aplicativo para autenticação com um provedor e configurar os Serviços Móveis</li>
<li>Restringir as permissões de tabela somente aos usuários autenticados</li>
<li>Adicionar código de autenticação ao seu aplicativo</li>
</ol>

Os Serviços Móveis oferecem suporte aos seguintes provedores de identidade existentes que você pode usar para autenticar os usuários:

- Conta da Microsoft
- Facebook
- Twitter
- Google 
- Azure Active Directory

Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a ID de um usuário autenticado para modificar solicitações. 

Essas duas primeiras tarefas são feitas usando o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/). Para obter mais informações, consulte [Introdução à autenticação](http://go.microsoft.com/fwlink/p/?LinkId=296316).

### <a name="caching"></a>Como: Adicionar código de autenticação ao seu aplicativo

1.  Adicione as instruções de importação a seguir ao arquivo de atividade do seu aplicativo.

		import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
		import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2. No método **onCreate** da classe de atividade, adicione a linha de código a seguir após o código que cria o objeto `MobileServiceClient`: supomos que a referência ao objeto `MobileServiceClient` seja *mClient*.
	
			// Login using the Google provider.
			mClient.login(MobileServiceAuthenticationProvider.Google,
					new UserAuthenticationCallback() {
						@Override
						public void onCompleted(MobileServiceUser user,
								Exception exception, ServiceFilterResponse response) {	
							if (exception == null) {
								/* User now logged in, you can get their identity via user.getUserId() */ 
							} else {
								/* Login error */
							}
						}
					});

    Esse código autentica o usuário usando um logon do Google. Será exibida uma caixa de diálogo que exibe a ID do usuário autenticado. Você não pode continuar sem uma autenticação positiva.

    > [AZURE.NOTE] Se você estiver usando um provedor de identidade diferente do Google, altere o valor passado para o método **login** acima para um dos seguintes: _MicrosoftAccount_, _Facebook_, _Twitter_, ou _WindowsAzureActiveDirectory_.
    </div>


3. Quando você executar o aplicativo, entrar com seu provedor de identidade escolhido. 


### <a name="caching"></a>Como: Armazenar tokens de autenticação em cache

Esta seção mostra como armazenar um token de autenticação no cache. Faça isso para impedir que os usuários tenham que se autenticar novamente enquanto o token ainda for válido se o aplicativo estiver "hibernando".

Armazenar em cache os tokens de autenticação exige que você armazene uma ID de usuário e o token de autenticação localmente no dispositivo. Na próxima vez que o aplicativo iniciar, você verificará o cache e, se esses valores estiverem presentes, poderá ignorar o procedimento de logon e reidratar o cliente com esses dados. No entanto, esses dados são confidenciais e, para segurança, devem ser armazenados criptografados caso o telefone seja roubado. 

O seguinte trecho de código demonstra a obtenção de um token para um logon de conta da Microsoft. O token é armazenado em cache e recarregado se o cache for encontrado. 

	private void authenticate() {
		if (LoadCache())
		{
			createTable();
		}
		else
		{
		    // Login using the provider.
		    mClient.login(MobileServiceAuthenticationProvider.MicrosoftAccount,
		            new UserAuthenticationCallback() {
		                @Override
		                public void onCompleted(MobileServiceUser user,
		                        Exception exception, ServiceFilterResponse response) {
		                    if (exception == null) {
		                        createTable();
		                        cacheUser(mClient.getCurrentUser());
		                    } else {
		                        createAndShowDialog("You must log in. Login Required", "Error");
		                    }
		                }
		            });
		}
	}	


	private boolean LoadCache()
	{
		SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
		String tmp1 = prefs.getString("tmp1", "undefined"); 
		if (tmp1 == "undefined")
			return false;
		String tmp2 = prefs.getString("tmp2", "undefined"); 
		if (tmp2 == "undefined")
			return false;
		MobileServiceUser user = new MobileServiceUser(tmp1);
		user.setAuthenticationToken(tmp2);
		mClient.setCurrentUser(user);		
		return true;
	}


	private void cacheUser(MobileServiceUser user)
	{
		SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
		Editor editor = prefs.edit();
		editor.putString("tmp1", user.getUserId());
		editor.putString("tmp2", user.getAuthenticationToken());
		editor.commit();
	}


O que acontecerá se o token expirar? Nesse caso, quando você tenta usá-lo para se conectar, obtém uma resposta *401 não autorizado*. O usuário deverá fazer logon obter novos tokens. Você pode evitar ter que escrever código para lidar com isso em cada local no seu aplicativo que chama os serviços móveis usando filtros, o que lhe permite interceptar chamadas para e respostas dos serviços móveis. Em seguida, o código do filtro testará a resposta para um 401, disparará o processo de logon, se necessário e, em seguida, retomará a solicitação que gerou o 401.


<h2><a name="errors"></a>Como: Tratar erros</h2>

Você pode ver um exemplo de como fazer a validação e tratar qualquer erro <a href="https://www.windowsazure.com/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/" target="_blank">aqui</a>, que implementa a validação por meio de scripts de servidor que retornam exceções de erro e o código do cliente que lida com as exceções.

Outra abordagem é fornecer um *global* manipulador de erro. O código visto que acessa a tabela de serviços móveis envolveu três objetos diferentes de retorno de chamada:

- **TableQueryCallback** / **TableQueryJsonCallback**
- **TableOperationCallback** / **TableJsonOperationCallback**
- **TableDeleteCallback** 

Cada um deles tem um método **OnCompleted** onde o segundo parâmetro é um objeto **java.lang.Exception**. Você pode criar uma subclasse desses objetos de retorno de chamada e implementar seu próprio método **onCompleted** que verificará se o parâmetro de exceção é nulo. Se ele for nulo, não haverá nenhum erro e você simplesmente deverá chamar <b>super.OnCompleted()</b>.

Se o objeto **Exception** não for nulo, execute um tratamento de erro genérico no qual você exibirá informações mais detalhadas sobre o erro. O trecho de código a seguir mostra uma maneira de exibir mais detalhes.

		String msg = exception.getCause().getMessage();



Agora o desenvolvedor pode usar seus retornos de chamada de subclasse e não se preocupar em verificar a exceção, porque ela é tratada em um local central (#2) para todas as instâncias do retorno de chamada.


<h2><a name="customizing"></a>Como: Personalizar o cliente</h2>

### <a name="headers"></a>Como: Personalizar cabeçalhos de solicitação

Você talvez queira anexar um cabeçalho personalizado a cada solicitação de saída. Você pode fazer isso configurando um ServiceFilter como este:

		client = client.withFilter(new ServiceFilter() {
		
		    @Override
		    public void handleRequest(ServiceFilterRequest request,
					NextServiceFilterCallback nextServiceFilterCallback,
		        	ServiceFilterResponseCallback responseCallback) {
		        request.addHeader("My-Header", "Value");      
		        nextServiceFilterCallback.onNext(request, responseCallback);
		    }
		});


### <a name="serialization"></a>Como: Serialização personalizada

Os Serviços Móveis pressupõem, por padrão, que os nomes de tabela, os nomes de coluna e os tipos de dados no servidor correspondem exatamente aos existentes no cliente. Porém, há vários motivos para que não haja correspondência dos nomes de servidor com os nomes de cliente. Por exemplo, se você tiver um cliente existente que queira alterar para que ele use os Serviços Móveis do Azure, em vez do produto de um concorrente.

Você talvez quisesse fazer os seguintes tipos de personalizações:
<ul>
<li>
Os nomes de coluna usados na tabela de serviços móveis não correspondem aos nomes que você está usando no cliente</li>

<li>Usar uma tabela de serviços móveis que tenha um nome diferente da classe para a qual ela mapeia no cliente</li>
<li>Ativar a capitalização automática de propriedade</li>

<li>Adicionar propriedades complexas a um objeto</li>

</ul>

### <a name="columns"></a>Como: Mapear nomes diferentes de cliente e servidor

Suponha que o seu código de cliente Java use nomes de estilo Java padrão para as propriedades do objeto *ToDoItem* como a seguinte. 
<ul>
<li>mId</li>
<li>mText</li>
<li>mComplete</li>
<li>mDuration</li>

</ul>

Você precisa serializar os nomes de cliente em nomes JSON que correspondam aos nomes de coluna da tabela *ToDoItem* no servidor. O código a seguir, que usa a biblioteca <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> faz isso.

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;
 
	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>Como: Mapear nomes de tabela diferentes entre o cliente e os serviços móveis

Mapeando o nome da tabela de cliente para um nome diferente de tabela de serviços móveis é fácil, podemos simplesmente usar uma das substituições da função
A função<a href="http://go.microsoft.com/fwlink/p/?LinkId=296840" target="_blank">getTable()</a>, como mostrada no código a seguir.

		mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);


### <a name="conversions"></a>Como: Automatizar os mapeamentos de nome de coluna

O mapeamento de nomes de coluna para uma tabela estreita com apenas algumas colunas não é um grande problema, conforme observado na seção anterior. Mas, suponha que a nossa tabela tenha muitas colunas, digamos 20 ou 30. Acontece que podemos chamar a API <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> e especificar uma estratégia de conversão que se aplicará a todas as colunas, evitando assim ter de anotar cada nome de coluna.

Para isso, usamos a biblioteca <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> que é usada pela biblioteca do cliente Android em segundo plano para serializar objetos Java para dados JSON, que são enviados aos Serviços Móveis do Azure.

O código a seguir usa o método *setFieldNamingStrategy()*, em que definimos um método *FieldNamingStrategy()*. Esse método diz para excluir o caractere inicial (um "m") e, em seguida, a minúscula do próximo caractere, para cada nome de campo. Este código também permite que uma bela impressão da saída JSON.

	client.setGsonBuilder(
	    MobileServiceClient
	    .createMobileServiceGsonBuilder()
	    .setFieldNamingStrategy(new FieldNamingStrategy() {
	        public String translateName(Field field) {
	            String name = field.getName();
	            return Character.toLowerCase(name.charAt(1))
	                + name.substring(2);
	            }
	        })
	        .setPrettyPrinting());
	


Esse código deve ser executado antes de qualquer chamada de método no objeto de cliente de Serviços Móveis.

### <a name="complex"></a>Como: Armazenar uma propriedade de objeto ou matriz em uma tabela 

Até agora, todos os nossos exemplos de serialização envolveram tipos primitivos, como inteiros e cadeias de caracteres que serializam facilmente em JSON e na tabela de serviços móveis. Suponha que desejemos adicionar um objeto complexo ao nosso tipo de cliente, que não serializa automaticamente em JSON e na tabela. Por exemplo, talvez desejemos adicionar uma matriz de cadeias de caracteres ao objeto de cliente. Agora, cabe a nós especificar como fazer a serialização e como armazenar a matriz na tabela de serviços móveis.

Para ver um exemplo de como fazer isso, verifique a postagem de blog <a href="http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson" target="_blank">Personalizando a serialização usando a biblioteca <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> no cliente de Serviços Móveis do Android</a>.

Esse método geral pode ser usado sempre que tivermos um objeto complexo não serializável automaticamente em JSON e na tabela de serviços móveis.


## <a name="next-steps"></a>Próximas etapas

A referência de Javadocs para a API do cliente Android está em [http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/package-summary.html](http://go.microsoft.com/fwlink/p/?LinkId=298735 "here")

<!-- Anchors. -->

[O que são Serviços Móveis]: #what-is
[Conceitos]: #concepts
[Como: Criar o cliente de Serviços Móveis]: #create-client
[Como: Criar uma referência de tabela]: #instantiating
[A estrutura API]: #api
[Como: Consultar dados por meio de um serviço móvel]: #querying
[Filtrar dados retornados]: #filtering
[Classificar dados retornados]: #sorting
[Retornar dados em páginas]: #paging
[Selecionar colunas específicas]: #selecting
[Como: Concatenar métodos de consulta]: #chaining
[Como: Associar dados à interface com o usuário]: #binding
[Como: Definir o layout]: #layout
[Como: Definir o adaptador]: #adapter
[Como: Usar o adaptador]: #use-adapter
[Como: Inserir dados em um serviço móvel]: #inserting
[Como: atualizar dados em um serviço móvel]: #updating
[Como: Excluir dados em um serviço móvel]: #deleting
[Como: Pesquisar um item específico]: #lookup
[Como: Trabalhar com dados não tipados]: #untyped
[Como: Autenticar usuários]: #authentication
[Armazenar tokens de autenticação em cache]: #caching
[Como: Tratar erros]: #errors
[Como: Projetar testes da unidade]: #tests
[Como: Personalizar o cliente]: #customizing
[Personalizar cabeçalhos de solicitação]: #headers
[Personalizar a serialização]: #serialization
[Próximas etapas]: #next-steps
[Configuração e pré-requisitos]: #setup

<!-- Images. -->


















<!-- URLs. -->
[Get started with Mobile Services]: /pt-br/develop/mobile/tutorials/get-started-android/
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?linkid=280126
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-android/
[Códigos de controle ASCII C0 e C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI para gerenciar tabelas dos Serviços Móveis]: http://azure.microsoft.com/manage/linux/other-resources/command-line-tools/#Mobile_Tables

\<!--HONumber=42-->
