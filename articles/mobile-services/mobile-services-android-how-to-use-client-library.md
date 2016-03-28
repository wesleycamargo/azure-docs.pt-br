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
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="01/20/2016"
	ms.author="ricksal"/>


# Como usar a biblioteca de cliente Android para os serviços móveis

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para a versão de aplicativos móveis equivalente deste tópico, consulte [Como usar a biblioteca de cliente Android para aplicativos móveis](../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md).
 
Este guia mostra como executar cenários comuns usando o cliente Android para os Serviços Móveis do Azure. Os cenários abordados incluem consulta de dados, inserção, atualização e exclusão de dados, autenticação de usuários, tratamento de erros e personalização do cliente.

Se você for iniciante nos Serviços Móveis, deve primeiro concluir o tutorial de início rápido [Introdução aos Serviços Móveis]. A conclusão com êxito do tutorial garante que você instalou o Android Studio; ele o ajudará a configurar sua conta e criar seu primeiro serviço móvel e instalar o SDK de serviços móveis, que oferece suporte ao Android versão 2.2 ou posterior, mas recomendamos compilar com base no Android versão 4.2 ou posterior.

Você pode encontrar a referência à API do Javadocs para a biblioteca de cliente Android [aqui](http://go.microsoft.com/fwlink/p/?LinkId=298735).

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="setup"></a>Configuração e Pré-requisitos

Vamos pressupor que você criou um serviço móvel e uma tabela. Para obter mais informações, consulte [Criar uma tabela](http://go.microsoft.com/fwlink/p/?LinkId=298592). No código usado neste tópico, supomos que a tabela é denominada *ToDoItem* e tem as seguintes colunas:

- ID
- text
- concluído

O objeto tipado do lado do cliente correspondente é o seguinte:

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}

Quando o esquema dinâmico está habilitado, os Serviços Móveis do Azure geram automaticamente novas colunas com base no objeto da solicitação de inserção ou atualização. Para obter mais informações, consulte [Esquema dinâmico](http://go.microsoft.com/fwlink/p/?LinkId=296271).

##<a name="create-client"></a>Como criar o cliente dos Serviços Móveis
O código a seguir cria o objeto [MobileServiceClient](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html) que é usado para acessar seu serviço móvel. O código entra no método `onCreate` da classe Activity especificada em *AndroidManifest.xml* como uma ação **PRINCIPAL** e de categoria **INICIADOR**.

		MobileServiceClient mClient = new MobileServiceClient(
				"MobileServiceUrl", // Replace with the above Site URL
				"AppKey", 			// replace with the Application Key
				this)

No código acima, substitua `MobileServiceUrl` e `AppKey` pela URL e pela chave do aplicativo do serviço móvel, nessa ordem. Ambas estão disponíveis no Portal clássico do Azure, selecionando seu serviço móvel e clicando em *Painel*.

##<a name="instantiating"></a>Como criar uma referência de tabela

A maneira mais fácil de consultar ou modificar dados no serviço móvel é usando o *modelo de programação tipado*, pois o Java é uma linguagem fortemente tipada (posteriormente, discutiremos o modelo *não tipado*). Esse modelo fornece perfeita serialização e desserialização para JSON usando a biblioteca [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) ao enviar dados entre o cliente e o serviço móvel: o desenvolvedor não precisa fazer nada, a estrutura lida com tudo isso.

A primeira tarefa que você executa para consultar ou modificar dados é criar um objeto [MobileServiceTable](http://go.microsoft.com/fwlink/p/?LinkId=296835), chamando o método **getTable** no [**MobileServiceClient**](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html). Examinaremos duas sobrecargas desse método:

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

No código a seguir, *mClient* é uma referência para seu cliente de serviço móvel.

A [primeira sobrecarga](http://go.microsoft.com/fwlink/p/?LinkId=296839) é usada onde o nome da classe e o nome da tabela são iguais:

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


A [2ª sobrecarga](http://go.microsoft.com/fwlink/p/?LinkId=296840) é usada quando o nome da tabela é diferente do nome do tipo.

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

## <a name="api"></a>A estrutura API

Desde a versão 2.0 da biblioteca de cliente, as operações da tabela de serviços móveis usam os objetos[Futuro](http://developer.android.com/reference/java/util/concurrent/Future.html) e [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) em todas as operações assíncronas, como os métodos que envolvem consultas e operações, como inserções, atualizações e exclusões. Isso torna mais fácil executar várias operações (enquanto estiver em um thread em segundo plano) sem ter que lidar com vários retornos de chamadas aninhados.


##<a name="querying"></a>Como consultar dados de um serviço móvel

Esta seção descreve como emitir consultas para o serviço móvel. As subseções descrevem diferentes aspectos, como classificação, filtragem e paginação. Finalmente, discutiremos como você pode concatenar essas operações juntas.

### <a name="showAll"></a>Retornar todos os itens de uma tabela

O código a seguir retorna todos os itens da tabela*ToDoItem*. Ele os exibe na interface do usuário ao adicionar os itens a um adaptador. Esse código é semelhante ao que está no tutorial de início rápido [Introdução aos Serviços Móveis].

		new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {

					final MobileServiceList<ToDoItem> result = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        }
                    });
               } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
               }
               return result;
            }
		}.execute();


Consultas como essa usam o objeto [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html).

A variável de *resultado* retorna o conjunto de resultados da consulta e o código após a instrução `mToDoTable.execute().get()` mostra como exibir as linhas individuais.


### <a name="filtering"></a>Como filtrar dados retornados

O código a seguir retorna todos os itens da tabela *ToDoItem* cujo campo *complete* é igual a *false*. *mToDoTable* é a referência à tabela de serviços móveis que criamos anteriormente.

        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final MobileServiceList<ToDoItem> result =
						mToDoTable.where().field("complete").eq(false).execute().get();
					for (ToDoItem item : result) {
                		Log.i(TAG, "Read object with ID " + item.id);
					}
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();



Você inicia um filtro com uma chamada do método [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296867) na referência de tabela. Isso é seguido por uma chamada do método [**field**](http://go.microsoft.com/fwlink/p/?LinkId=296869) seguida por uma chamada de método que especifica o predicado lógico. Os métodos de predicado possíveis incluem [**eq**](http://go.microsoft.com/fwlink/p/?LinkId=298461), [**ne**](http://go.microsoft.com/fwlink/p/?LinkId=298462), [**gt**](http://go.microsoft.com/fwlink/p/?LinkId=298463), [**ge**](http://go.microsoft.com/fwlink/p/?LinkId=298464), [**lt**](http://go.microsoft.com/fwlink/p/?LinkId=298465), [**le**](http://go.microsoft.com/fwlink/p/?LinkId=298466) etc.

Isso é suficiente para comparar os campos de número e de cadeia de caracteres com valores específicos. Mas, você pode fazer muito mais.

Por exemplo, você pode filtrar por datas. Você pode comparar todo o campo de data, mas também pode comparar partes da data, com métodos como [**year**](http://go.microsoft.com/fwlink/p/?LinkId=298467), [**month**](http://go.microsoft.com/fwlink/p/?LinkId=298468), [**day**](http://go.microsoft.com/fwlink/p/?LinkId=298469), [**hour**](http://go.microsoft.com/fwlink/p/?LinkId=298470), [**minute**](http://go.microsoft.com/fwlink/p/?LinkId=298471) e [**second**](http://go.microsoft.com/fwlink/p/?LinkId=298472). O código parcial a seguir adiciona um filtro para itens cuja *data de vencimento* é igual a 2013.

		mToDoTable.where().year("due").eq(2013).execute().get();

Você pode usar uma grande variedade de filtros complexos em campos de cadeia de caracteres com métodos como [**startsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298473), [**endsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298474), [**concat**](http://go.microsoft.com/fwlink/p/?LinkId=298475), [**subString**](http://go.microsoft.com/fwlink/p/?LinkId=298477), [**indexOf**](http://go.microsoft.com/fwlink/p/?LinkId=298488), [**replace**](http://go.microsoft.com/fwlink/p/?LinkId=298491), [**toLower**](http://go.microsoft.com/fwlink/p/?LinkId=298492), [**toUpper**](http://go.microsoft.com/fwlink/p/?LinkId=298493), [**trim**](http://go.microsoft.com/fwlink/p/?LinkId=298495) e [**length**](http://go.microsoft.com/fwlink/p/?LinkId=298496). Os filtros de código parcial a seguir são para linhas de tabela onde a coluna *text* começa com "PRI0".

		mToDoTable.where().startsWith("text", "PRI0").execute().get();

Os campos de número também permitem uma grande variedade de filtros mais complexos com métodos como [**add**](http://go.microsoft.com/fwlink/p/?LinkId=298497), [**sub**](http://go.microsoft.com/fwlink/p/?LinkId=298499), [**mul**](http://go.microsoft.com/fwlink/p/?LinkId=298500), [**div**](http://go.microsoft.com/fwlink/p/?LinkId=298502), [**mod**](http://go.microsoft.com/fwlink/p/?LinkId=298503), [**floor**](http://go.microsoft.com/fwlink/p/?LinkId=298505), [**ceiling**](http://go.microsoft.com/fwlink/p/?LinkId=298506) e [**round**](http://go.microsoft.com/fwlink/p/?LinkId=298507). Os filtros de código parcial a seguir são para linhas de tabela onde *duration* é um número par.

		mToDoTable.where().field("duration").mod(2).eq(0).execute().get();


Você pode combinar predicados com métodos como [**and**](http://go.microsoft.com/fwlink/p/?LinkId=298512), [**or**](http://go.microsoft.com/fwlink/p/?LinkId=298514) e [**not**](http://go.microsoft.com/fwlink/p/?LinkId=298515). Este código parcial combina dois dos exemplos acima.

		mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
					.execute().get();

E você pode agrupar e aninhar operadores lógicos, conforme mostrado neste código parcial:

		mToDoTable.where()
					.year("due").eq(2013)
						.and
					(startsWith("text", "PRI0").or().field("duration").gt(10))
					.execute().get();

Para obter uma discussão mais detalhada e exemplos de filtragem, consulte [Explorando a riqueza do modelo de consulta de cliente dos Serviços Móveis do Android](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="sorting"></a>Como classificar dados retornados

O código a seguir retorna todos os itens de uma tabela *ToDoItem* classificada em ordem crescente pelo campo *text*. *mToDoTable* é a referência à tabela de serviços móveis que criamos anteriormente.

		mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

O primeiro parâmetro do método [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=298519) é uma cadeia de caracteres igual ao nome do campo em que será feita a classificação.

O segundo parâmetro usa a enumeração [**QueryOrder**](http://go.microsoft.com/fwlink/p/?LinkId=298521) para especificar se a classificação será em ordem crescente ou decrescente.

Observe que, se você estiver filtrando usando o método ***where***, o método ***where*** deverá ser invocado antes do método ***orderBy***.

### <a name="paging"></a>Como retornar dados em páginas

O primeiro exemplo mostra como selecionar os 5 primeiros itens de uma tabela. A consulta retorna os itens de uma tabela *ToDoItem*. *mToDoTable* é a referência à tabela de serviços móveis criada anteriormente.

       final MobileServiceList<ToDoItem> result = mToDoTable.top(5).execute().get();


Em seguida, definiremos uma consulta que ignorará os 5 primeiros itens e, em seguida, retornará os 5 seguintes.

		mToDoTable.skip(5).top(5).execute().get();


### <a name="selecting"></a>Como selecionar colunas específicas

O código a seguir ilustra como retornar todos os itens de uma tabela *ToDoItem*, mas exibe apenas os campos *complete* e *text*. *mToDoTable* é a referência à tabela de serviços móveis que criamos anteriormente.

		mToDoTable.select("complete", "text").execute().get();


Aqui, os parâmetros para a função select são os nomes de cadeia de caracteres das colunas da tabela que você deseja retornar.

O método [**select**](http://go.microsoft.com/fwlink/p/?LinkId=290689) precisa seguir métodos como [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296296) e [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=296313), se eles estiverem presentes. Ele pode ser seguido por métodos como [**top**](http://go.microsoft.com/fwlink/p/?LinkId=298731).

### <a name="chaining"></a>Como concatenar métodos de consulta

Os métodos usados na consulta de tabelas de serviços móveis podem ser concatenados. Isso lhe permitirá executar tarefas como selecionar colunas específicas ou linhas filtradas que são classificadas e paginadas. Você pode criar filtros lógicos bastante complexos.

O que faz isso funcionar é que os métodos de consulta usados retornam objetos [**MobileServiceQuery&lt;T&gt;**](http://go.microsoft.com/fwlink/p/?LinkId=298551), que, por sua vez, podem ter métodos adicionais invocados neles. Para encerrar a série de métodos e realmente executar a consulta, chame o método [**execute**](http://go.microsoft.com/fwlink/p/?LinkId=298554).

Este é um exemplo de código onde *mToDoTable* é uma referência à tabela *ToDoItem* de serviços móveis.

		mToDoTable.where().year("due").eq(2013)
						.and().startsWith("text", "PRI0")
						.or().field("duration").gt(10)
					.select("id", "complete", "text", "duration")
					.orderBy(duration, QueryOrder.Ascending).top(20)
					.execute().get();

O principal requisito no encadeamento de métodos é que o método *where* e os predicados precisam vir primeiro. Depois disso, você poderá chamar métodos subsequentes na ordem que melhor atenda às necessidades do seu aplicativo.


##<a name="inserting"></a>Como inserir dados em um serviço móvel

O código a seguir mostra como inserir uma nova linha em uma tabela.

Primeiro, crie uma instância da classe *ToDoItem* e defina suas propriedades.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;

 Em seguida, execute o código a seguir:

		// Insert the new item
	    new AsyncTask<Void, Void, Void>() {

	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.insert(item).get();
	                if (!item.isComplete()) {
	                    runOnUiThread(new Runnable() {
	                        public void run() {
	                            mAdapter.add(item);
	                        }
	                    });
	                }
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();


Esse código insere um novo item e o adiciona ao adaptador para que o exiba na interface de usuário.

Os Serviços Móveis oferecem suporte a valores exclusivos e personalizados de cadeia de caracteres para a id da tabela. Isso permite que os aplicativos usem valores personalizados, como endereços de email ou nomes de usuário, para a coluna de id de uma tabela dos Serviços Móveis. Por exemplo, se você quiser identificar cada registro por um endereço de email, poderá usar o seguinte objeto JSON.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.id = "myemail@mydomain.com";
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;

Se um valor de id de cadeia de caracteres não for fornecido ao inserir novos registros em uma tabela, os Serviços Móveis gerarão um valor exclusivo para a id.

O suporte às ids de cadeia de caracteres oferece as seguintes vantagens aos desenvolvedores

+ Os Ids podem ser gerados sem fazer uma varredura no banco de dados.
+ Os registros são mais fáceis de mesclar a partir de tabelas ou bancos de dados diferentes.
+ Os valores de ids podem integrar-se melhor a uma lógica do aplicativo.

Você também pode usar scripts de servidor para definir valores de ids. O exemplo de script a seguir gera um GUID personalizado e o atribui a uma nova id de registro. Esse é semelhante ao valor da id que os Serviços Móveis gerariam se você não passasse um valor para uma id de registro.

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

O valor da `id` deve ser exclusivo e não deve incluir caracteres dos seguintes conjuntos:

+ Caracteres de controle: [0x0000-0x001F] e [0x007F-0x009F]. Para obter mais informações, consulte [Códigos de controle ASCII C0 e C1].
+  Caracteres imprimíveis: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  Os ids "." e ".."

Como alternativa, você pode usar Ids de números inteiros para suas tabelas. Para usar uma Id de número inteiro, você deve criar sua tabela com o comando `mobile table create` usando a opção `--integerId`. Esse comando é usado com a CLI (interface de linha de comando) para Azure. Para saber mais sobre como usar a CLI, confira [CLI para gerenciar tabelas de Serviços Móveis].


##<a name="updating"></a>Como atualizar dados em um serviço móvel

O código a seguir mostra como atualizar dados em uma tabela. Neste exemplo, *item* é uma referência a uma linha na tabela *ToDoItem*, a qual sofreu algumas alterações. O método a seguir atualiza a tabela e o adaptador de interface do usuário.

	private void updateItem(final ToDoItem item) {
	    if (mClient == null) {
	        return;
	    }

	    new AsyncTask<Void, Void, Void>() {

	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.update(item).get();
	                runOnUiThread(new Runnable() {
	                    public void run() {
	                        if (item.isComplete()) {
	                            mAdapter.remove(item);
	                        }
	                        refreshItemsFromTable();
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();
	}

##<a name="deleting"></a>Como excluir dados em um serviço móvel

O código a seguir mostra como excluir dados de uma tabela. Ele exclui um item existente da tabela ToDoItem que teve a caixa de seleção **Concluído** marcada na interface do usuário.

	public void checkItem(final ToDoItem item) {
		if (mClient == null) {
			return;
		}

		// Set the item as completed and update it in the table
		item.setComplete(true);

		new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mToDoTable.delete(item);
                    runOnUiThread(new Runnable() {
                        public void run() {
                            if (item.isComplete()) {
                                mAdapter.remove(item);
                            }
                            refreshItemsFromTable();
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
	}


O código a seguir ilustra uma outra maneira de fazer isso. Ele exclui um item existente da tabela ToDoItem, especificando o valor do campo id da linha para exclusão (pressupondo que seja igual a "2FA404AB-E458-44CD-BC1B-3BC847EF0902"). Em um aplicativo real, você deve pegar a ID de alguma forma e passá-la como uma variável. Aqui, para simplificar o teste, você pode ir para o seu serviço no portal clássico do Azure, clicar em **Dados** e copiar uma ID com a qual deseja testar.

    public void deleteItem(View view) {

        final String ID = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mToDoTable.delete(ID);
                    runOnUiThread(new Runnable() {
                        public void run() {
                            refreshItemsFromTable();
                        }
               });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

##<a name="lookup"></a>Como pesquisar um item específico
Às vezes, você deseja pesquisar um item específico por sua *id*, diferentemente da consulta where, você geralmente obtém uma coleção de itens que atendem a alguns critérios. O código a seguir mostra como fazer isso com um valor de *id* de `0380BAFB-BCFF-443C-B7D5-30199F730335`. Em um aplicativo real, você deve pegar a ID de alguma forma e passá-la como uma variável. Aqui, para simplificar o teste, você pode ir para o seu serviço no portal clássico do Azure, clicar na guia **Dados** e copiar uma ID com a qual deseja testar.

    /**
     * Lookup specific item from table and UI
     */
    public void lookup(View view) {

        final String ID = "0380BAFB-BCFF-443C-B7D5-30199F730335";
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final ToDoItem result = mToDoTable.lookUp(ID).get();
                    runOnUiThread(new Runnable() {
                        public void run() {
                            mAdapter.clear();
                            mAdapter.add(result);
                        }
               });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

##<a name="untyped"></a>Como trabalhar com dados não tipados

O modelo de programação não tipado oferece um controle exato sobre a serialização JSON, e há alguns cenários onde você pode querer usá-lo, por exemplo, se a sua tabela de serviços móveis contiver um grande número de colunas e você só precisa fazer referência a algumas delas. O uso do modelo tipado requer que você defina todas as colunas da tabela de serviços móveis na sua classe de dados. Mas, com o modelo não tipado, você define apenas as colunas que precisa usar.

A maioria das chamadas API para acessar dados são semelhante às chamadas de programação tipadas. A principal diferença é que o modelo não tipado você invocar métodos no objeto **MobileServiceJsonTable**, em vez do objeto **MobileServiceTable**.


### <a name="json_instance"></a>Como criar uma instância de uma tabela não tipada

Semelhante ao modelo tipado, você começa obtendo uma referência de tabela, mas, nesse caso, é um objeto[MobileServicesJsonTable](http://go.microsoft.com/fwlink/p/?LinkId=298733). Você obtém a referência chamando o método [getTable()](http://go.microsoft.com/fwlink/p/?LinkId=298734) em uma instância do cliente de Serviços Móveis.

Primeiro, defina a variável:

    /**
     * Mobile Service Json Table used to access untyped data
     */
    private MobileServiceJsonTable mJsonToDoTable;



Depois de criar uma instância do cliente de Serviços Móveis no método **onCreate **(aqui, a variável *mClient*), você cria uma instância de **MobileServiceJsonTable**, com o código a seguir.


            // Get the Mobile Service Json Table to use
            mJsonToDoTable = mClient.getTable("ToDoItem");

Após criar uma instância de **MobileServiceJsonTable**, você poderá chamar praticamente todos os métodos possíveis que ela contém com o modelo de programação tipado. No entanto, em alguns casos, os métodos usam um parâmetro não tipado, como podemos ver nos exemplos a seguir.

### <a name="json_insert"></a>Como inserir em uma tabela não tipada

O código a seguir mostra como fazer uma inserção. A primeira etapa consiste em criar um [**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html), que faz parte da biblioteca <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>.

		JsonObject item = new JsonObject();
		item.addProperty("text", "Wake up");
		item.addProperty("complete", false);

A próxima etapa consiste em inserir o objeto. A função de retorno de chamada passada para o método [**insert**](http://go.microsoft.com/fwlink/p/?LinkId=298535) é uma instância da classe [**TableJsonOperationCallback**](http://go.microsoft.com/fwlink/p/?LinkId=298532). Observe como o parâmetro do método *insert* é um JsonObject.

        // Insert the new item
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mJsonToDoTable.insert(item).get();
                    refreshItemsFromTable();
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();


Se você precisar obter a ID do objeto inserido, use esta chamada de método:

		        jsonObject.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>Como excluir de uma tabela não tipada

O código a seguir mostra como excluir uma instância, neste caso, a mesma instância de um **JsonObject** criado no exemplo *insert* anterior. Observe que o código é igual ao do caso tipado, mas o método tem uma assinatura diferente, uma vez que ele faz referência a um **JsonObject**.


         mToDoTable.delete(item);


Você também pode excluir uma instância diretamente, usando sua ID:

		 mToDoTable.delete(ID);



### <a name="json_get"></a>Como retornar todas as linhas de uma tabela não tipada

O código a seguir mostra como recuperar uma tabela inteira. Como você está usando uma tabela JSON, pode recuperar seletivamente somente algumas das colunas da tabela.

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

Você pode fazer filtragem, classificação e paginação, concatenando os métodos que têm os mesmos nomes que os usados no modelo de programação tipado.


##<a name="binding"></a>Como associar dados à interface do usuário

A associação de dados envolve três componentes:

- a fonte de dados
- o layout da tela
- e o adaptador que vincula esses dois.

No nosso código de exemplo, retornamos os dados da tabela de serviços móveis *ToDoItem* em uma matriz. Esse é um padrão muito comum para aplicativos de dados: as consultas de banco de dados geralmente retornam uma coleção de linhas que o cliente obtém em uma lista ou uma matriz. Neste exemplo, a matriz é a fonte de dados.

O código especifica um layout de tela que define a exibição dos dados que serão exibidos no dispositivo.

E os dois são associados juntos com um adaptador, que, nesse código, é uma extensão da classe *ArrayAdapter&lt;ToDoItem&gt;*.

### <a name="layout"></a>Como definir o layout

O layout é definido por vários trechos de código XML. Com base em um layout existente, vamos supor que o código a seguir representa a **ListView** que queremos preencher com nossos dados de servidor.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>


No código acima, o atributo *listitem* especifica a id do layout para uma linha individual na lista. Este é o código, que especifica uma caixa de seleção e seu texto associado. Isso será instanciado uma vez para cada item da lista. Um layout mais complexo seria especificar campos adicionais na exibição. Este código está no arquivo *row\_list\_to\_do.xml*.

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


### <a name="adapter"></a>Como definir o adaptador

Como a fonte de dados da nossa exibição é uma matriz de *ToDoItem*, podemos criar uma subclasse do nosso adaptador de uma classe *ArrayAdapter&lt;ToDoItem&gt;*. Esta subclasse produzirá uma exibição para cada *ToDoItem* usando o layout *row\_list\_to\_do*.

No nosso código, definimos a seguinte classe que é uma extensão da classe *ArrayAdapter&lt;ToDoItem&gt;*:

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

Observe que o segundo parâmetro para o construtor ToDoItemAdapter é uma referência ao layout. A chamada para o construtor é seguida pelo código a seguir que primeiro obtém uma referência à **ListView** e, em seguida, chama *setAdapter* para se configurar e usar o adaptador que acabamos de criar:

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);


### <a name="use-adapter"></a>Como usar o adaptador

Agora você está pronto para usar a associação de dados. O código a seguir mostra como obter os itens na tabela de serviços móveis, limpar o adaptador e, em seguida, chamar o método *add* do adaptador para preenchê-lo com os itens retornados.

    public void showAll(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final MobileServiceList<ToDoItem> result = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

Você também deverá chamar o adaptador sempre que modificar a tabela *ToDoItem* se desejar exibir os resultados desse procedimento. Como as modificações são feitas de registro em registro, você estará lidando com uma única linha, em vez de uma coleção. Ao inserir um item, você chama o método *add* no adaptador e, ao excluir, você chama o método *remove*.

##<a name="custom-api"></a>Chamar uma API personalizada

Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON. Para obter um exemplo de como criar uma API personalizada em seu serviço móvel, consulte [Como: definir um ponto de extremidade de API personalizado](mobile-services-dotnet-backend-define-custom-api.md).

[AZURE.INCLUDE [mobile-services-android-call-custom-api](../../includes/mobile-services-android-call-custom-api.md)]


##<a name="authentication"></a>Como autenticar usuários

Os Serviços Móveis oferecem suporte à autenticação e à autorização de usuários de aplicativo, usando vários provedores de identidade externos: Facebook, Google, Conta da Microsoft, Twitter e o Azure Active Directory. Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização no seu back-end. Para obter mais informações, consulte [Comece a usar a autenticação](http://go.microsoft.com/fwlink/p/?LinkId=296316).

Dois fluxos de autenticação têm suporte: um fluxo de *servidor* e um fluxo de *cliente*. O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende da interface de autenticação da web do provedor. O fluxo de cliente permite uma integração mais profunda com recursos específicos do dispositivo, como logon único, uma vez que depende de provedores específicos e SDKs específicos do dispositivo.

Três etapas são necessárias para habilitar a autenticação no seu aplicativo:

- Registrar seu aplicativo para autenticação com um provedor e configurar os Serviços Móveis
- Restringir as permissões de tabela somente aos usuários autenticados
- Adicionar código de autenticação ao seu aplicativo


Os Serviços Móveis oferecem suporte aos seguintes provedores de identidade existentes que você pode usar para autenticar os usuários:

- Conta da Microsoft
- Facebook
- Twitter
- Google
- Azure Active Directory

Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a ID de um usuário autenticado para modificar solicitações.

Essas duas primeiras tarefas são feitas por meio do [portal clássico do Azure](https://manage.windowsazure.com/). Para obter mais informações, consulte [Comece a usar a autenticação](http://go.microsoft.com/fwlink/p/?LinkId=296316).

### <a name="caching"></a>Como adicionar código de autenticação para seu aplicativo

1.  Adicione as instruções de importação a seguir ao arquivo de atividade do seu aplicativo.

		import java.util.concurrent.ExecutionException;
		import java.util.concurrent.atomic.AtomicBoolean;

		import android.content.Context;
		import android.content.SharedPreferences;
		import android.content.SharedPreferences.Editor;

		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. No método **onCreate** da classe de atividade, adicione a linha de código a seguir após o código que cria o objeto `MobileServiceClient`: supomos que a referência ao objeto `MobileServiceClient` seja *mClient*.

	    // Login using the Google provider.

		ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
    		@Override
    		public void onFailure(Throwable exc) {
    			createAndShowDialog((Exception) exc, "Error");
    		}
    		@Override
    		public void onSuccess(MobileServiceUser user) {
    			createAndShowDialog(String.format(
                        "You are now logged in - %1$2s",
                        user.getUserId()), "Success");
    			createTable();
    		}
    	});

    Esse código autentica o usuário usando um logon do Google. Será exibida uma caixa de diálogo que exibe a ID do usuário autenticado. Você não pode continuar sem uma autenticação positiva.

    > [AZURE.NOTE] Se você estiver usando um provedor de identidade diferente do Google, altere o valor passado ao método **login** acima para um destes: _MicrosoftAccount_, _Facebook_, _Twitter_ ou _WindowsAzureActiveDirectory_.


3. Quando você executar o aplicativo, entrar com seu provedor de identidade escolhido.


### <a name="caching"></a>Como armazenar tokens de autenticação em cache

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
			    // Login using the Google provider.
				ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

		    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		    		@Override
		    		public void onFailure(Throwable exc) {
		    			createAndShowDialog("You must log in. Login Required", "Error");
		    		}
		    		@Override
		    		public void onSuccess(MobileServiceUser user) {
		    			createAndShowDialog(String.format(
		                        "You are now logged in - %1$2s",
		                        user.getUserId()), "Success");
		    			cacheUserToken(mClient.getCurrentUser());
		    			createTable();
		    		}
		    	});		}
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


O que acontecerá se o token expirar? Nesse caso, quando você tentar usá-lo para se conectar, receberá a resposta *401 não autorizado*. O usuário deverá fazer logon obter novos tokens. Você pode evitar ter que escrever código para lidar com isso em cada local no seu aplicativo que chamar os serviços móveis usando filtros, o que lhe permite interceptar chamadas de entrada e respostas dos Serviços Móveis. O código do filtro testará a resposta para um 401, disparará o processo de logon se necessário e retomará a solicitação que gerou o 401.


##<a name="customizing"></a>Como personalizar o cliente

Há várias maneiras de personalizar o comportamento padrão do cliente dos Serviços Móveis.

### <a name="headers"></a>Como personalizar cabeçalhos de solicitação

Você talvez queira anexar um cabeçalho personalizado a cada solicitação de saída. Você pode fazer isso configurando um **ServiceFilter** como este:

	private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                	ServiceFilterRequest request,
					NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
	        		request.addHeader("My-Header", "Value");	                }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>Como personalizar a serialização

Os Serviços Móveis pressupõem, por padrão, que os nomes de tabela, os nomes de coluna e os tipos de dados no servidor correspondem exatamente aos existentes no cliente. Porém, há vários motivos para que não haja correspondência dos nomes de servidor com os nomes de cliente. Por exemplo, se você tiver um cliente existente e quiser alterá-lo para que ele use os Serviços Móveis do Azure em vez do produto de um concorrente.

Você talvez quisesse fazer os seguintes tipos de personalizações:

- Os nomes de coluna usados na tabela de serviços móveis não correspondem aos nomes que você está usando no cliente
- Usar uma tabela de serviços móveis que tenha um nome diferente da classe para a qual ela mapeia no cliente
- Ativar a capitalização automática de propriedade
- Adicionar propriedades complexas a um objeto

### <a name="columns"></a>Como mapear nomes diferentes de cliente e servidor

Suponha que o seu código de cliente Java use nomes de estilo Java padrão para as propriedades do objeto *ToDoItem*, como a apresentada a seguir.

- mId
- mText
- mComplete
- mDuration


Você precisa serializar os nomes de cliente em nomes JSON que correspondam aos nomes de coluna da tabela*ToDoItem* no servidor. O código a seguir, que usa a biblioteca <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>, faz isso.

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;

	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>Como mapear nomes de tabela diferentes entre o cliente e os serviços móveis

O mapeamento do nome da tabela de cliente para um nome diferente de tabela de serviços móveis é fácil, apenas usamos uma das substituições da função <a href="http://go.microsoft.com/fwlink/p/?LinkId=296840" target="_blank">getTable()</a> conforme mostrado no código a seguir.

	mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);


### <a name="conversions"></a>Como automatizar os mapeamentos de nome de coluna

O mapeamento de nomes de coluna para uma tabela estreita com apenas algumas colunas não é um grande problema, conforme observado na seção anterior. Mas, suponha que a nossa tabela tenha muitas colunas, digamos 20 ou 30. Acontece que podemos chamar a API <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> e especificar uma estratégia de conversão que se aplicará a todas as colunas e evitar a anotação de cada nome de coluna.

Para isso, usamos a biblioteca <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> que é usada pela biblioteca de cliente Android de forma oculta para serializar objetos Java para dados JSON, que são enviados aos Serviços Móveis do Azure.

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

### <a name="complex"></a>Como armazenar uma propriedade de objeto ou matriz em uma tabela

Até agora, todos os nossos exemplos de serialização envolveram tipos primitivos, como inteiros e cadeias de caracteres que serializam facilmente em JSON e na tabela de serviços móveis. Suponha que desejemos adicionar um objeto complexo ao nosso tipo de cliente, que não serializa automaticamente em JSON e na tabela. Por exemplo, talvez desejemos adicionar uma matriz de cadeias de caracteres ao objeto de cliente. Agora, cabe a nós especificar como fazer a serialização e como armazenar a matriz na tabela de serviços móveis.

Para ver um exemplo de como fazer isso, verifique a postagem de blog <a href="http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson" target="_blank">Personalizando a serialização usando a biblioteca <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> no cliente de Serviços Móveis do Android</a>.

Esse método geral pode ser usado sempre que tivermos um objeto complexo não serializável automaticamente em JSON e na tabela de serviços móveis.

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[Return all Items]: #showAll
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->



<!-- URLs. -->
[Introdução aos Serviços Móveis]: mobile-services-android-get-started.md
[Códigos de controle ASCII C0 e C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set

<!----HONumber=AcomDC_0309_2016-->