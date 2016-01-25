<properties
	pageTitle="Como usar a biblioteca de cliente de Aplicativos Móveis Android"
	description="Como usar o SDK do cliente Android para Aplicativos Móveis do Azure."
	services="app-service\mobile"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="01/05/2016" 
	ms.author="ricksal"/>


# Como usar a biblioteca de cliente Android para Aplicativos Móveis

[AZURE.INCLUDE [App-Service-Mobile-Selector-Client-Library](../../includes/app-service-mobile-selector-client-library.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Este guia mostra como usar o SDK do cliente Android para Aplicativos Móveis na implementação de cenários comuns, como consulta de dados (inserção, atualização e exclusão), autenticação de usuários, tratamento de erros e personalização do cliente. Ele também faz uma análise aprofundada do código do cliente comum usado na maioria dos aplicativos móveis.

Este guia destaca o SDK do Android no lado do cliente. Para saber mais sobre os SDKs no servidor para Aplicativos Móveis, confira [Trabalhar com o SDK de back-end do .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) ou [Como usar o SDK de back-end Node.js](app-service-mobile-node-backend-how-to-use-server-sdk.md).


<!---You can find the Javadocs API reference for the Android client library [here](http://go.microsoft.com/fwlink/p/?LinkId=298735).-->

## Configuração e pré-requisitos

O SDK dos Serviços Móveis para Android oferece suporte ao Android versão 2.2 ou posterior, mas sugerimos que a criação se baseie na versão 4.2 ou posterior.

Conclua o tutorial [Mobile Apps quickstart](app-service-mobile-android-get-started.md), que garantirá que você tenha instalado o Android Studio; ele ajudará você a configurar sua conta e a criar seu primeiro back-end de Aplicativo Móvel. Se você já fez isso, ignore o restante desta seção.

Se decidir não concluir o tutorial de início rápido e quiser conectar um aplicativo Android a um back-end de Aplicativo Móvel, você precisará fazer o seguinte:

- [Criar um back-end de Aplicativo Móvel](app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend) para usar com o aplicativo Android (a menos que o aplicativo já tenha um)
- No Android Studio, [atualizar os arquivos de criação Gradle](#gradle-build) e
- [Habilitar a permissão de Internet](#enable-internet)

Depois disso, você precisará concluir as etapas descritas na seção Detalhamento.

###<a name="gradle-build"></a>Atualizar o arquivo de criação Gradle 

Altere ambos os arquivos **build.gradle**:

1. Adicione este código ao arquivo **build.gradle** do nível *Project* dentro da marca *buildscript*:
 
		buildscript {
		    repositories {
		        jcenter()
		    }
		} 

2. Adicione este código ao arquivo **build.gradle** do nível *Module app* dentro da marca *dependencies*:

		compile 'com.microsoft.azure:azure-mobile-android:3.0'

	Atualmente, a versão mais recente é a 3.0. As versões com suporte estão listadas [aqui](http://go.microsoft.com/fwlink/p/?LinkID=717034).

###<a name="enable-internet"></a>Habilitar a permissão de Internet
Para acessar o Azure, o aplicativo deve ter a permissão INTERNET habilitada. Se ela ainda não estiver habilitada, adicione a seguinte linha de código ao arquivo **AndroidManifest.xml**:

	<uses-permission android:name="android.permission.INTERNET" />

## Detalhamento das noções básicas  

Esta seção aborda alguns dos códigos no aplicativo Início Rápido. Se você não concluiu o Início Rápido, será preciso adicionar este código ao aplicativo.

> [AZURE.NOTE]A cadeia de caracteres "MobileServices" ocorre frequentemente no código: de fato, o código faz referência ao SDK dos Aplicativos Móveis; trata-se apenas de um reaproveitamento temporário do passado.


###<a name="data-object"></a>Definir as classes de dados do cliente

Para acessar dados de tabelas do SQL Azure, defina as classes de dados do cliente que correspondem às tabelas no back-end de aplicativo móvel. Os exemplos deste tópico pressupõem uma tabela denominada *ToDoItem*, que tem as seguintes colunas:

- ID
- text
- concluído

O objeto do lado do cliente com o tipo correspondente é o seguinte:

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}

O código residirá em um arquivo chamado **ToDoItem.java**.

Se a tabela do SQL Azure contiver mais colunas, adicione os campos correspondentes a essa classe.

Por exemplo, se ela tivesse uma coluna Priority de inteiros, você poderia adicionar este campo, com seus métodos getter e setter:

		private Integer priority;

	    /**
	     * Returns the item priority
	     */
	    public Integer getPriority() {
	        return mPriority;
	    }
	
	    /**
	     * Sets the item priority
	     *
	     * @param priority
	     *            priority to set
	     */
	    public final void setPriority(Integer priority) {
	        mPriority = priority;
	    }

Para saber como criar mais tabelas em seu back-end de Aplicativos Móveis, confira [Como definir um controlador de tabela](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller) (back-end do .NET) ou [Como definir tabelas usando um esquema dinâmico](app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) (back-end do Node.js). Para um back-end Node.js, você também pode usar a configuração **Tabelas fáceis** no [portal do Azure].

###<a name="create-client"></a>Como criar o contexto do cliente

Este código cria o objeto **MobileServiceClient** que é usado para acessar o back-end do Aplicativo Móvel. O código entra no método `onCreate` da classe **Activity** especificada em *AndroidManifest.xml* como uma ação **MAIN** e categoria **LAUNCHER**. No código de Início Rápido, ele entra no arquivo **ToDoActivity.java**.

		MobileServiceClient mClient = new MobileServiceClient(
				"MobileAppUrl", // Replace with the above Site URL
				this)

Nesse código, substitua `MobileAppUrl` pela URL do back-end do Aplicativo Móvel, que pode ser encontrada no [portal do Azure](https://portal.azure.com), na folha do back-end do Aplicativo Móvel. Para que essa linha de código seja compilada, você também precisa adicionar a instrução **import** a seguir:

	import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>Como criar uma referência de tabela

A maneira mais fácil de consultar ou modificar dados no back-end é usando o *modelo de programação tipado*, pois o Java é uma linguagem fortemente tipada (posteriormente, discutiremos o modelo *não tipado*). Esse modelo fornece perfeita serialização e desserialização de JSON usando a biblioteca [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) ao enviar dados entre objetos e tabelas do cliente no SQL do Azure no back-end: o desenvolvedor não precisa fazer nada, a estrutura cuida de tudo.

Para acessar uma tabela, primeiramente crie um objeto [MobileServiceTable](http://go.microsoft.com/fwlink/p/?LinkId=296835) chamando o método **getTable** no [**MobileServiceClient**](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html). Esse método tem duas sobrecargas:

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

No código a seguir, *mClient* é uma referência ao objeto MobileServiceClient.

A [primeira sobrecarga](http://go.microsoft.com/fwlink/p/?LinkId=296839) é usada onde o nome da classe e o nome da tabela são iguais, e é a usada no Início Rápido:

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


A [segunda sobrecarga](http://go.microsoft.com/fwlink/p/?LinkId=296840) é usada quando o nome da tabela é diferente do nome da classe: o primeiro parâmetro é o nome da tabela.

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>Como associar dados à interface do usuário

A associação de dados envolve três componentes:

- A fonte de dados
- O layout da tela
- O adaptador que vincula os dois.

Em nosso código de exemplo, retornamos os dados da tabela *ToDoItem* do SQL Azure dos Aplicativos Móveis em uma matriz. Esse é um padrão muito comum para aplicativos de dados: as consultas de banco de dados muitas vezes retornam uma coleção de linhas que o cliente obtém em uma lista ou matriz. Neste exemplo, a matriz é a fonte de dados.

O código especifica um layout de tela que define a exibição dos dados que serão exibidos no dispositivo.

E os dois são associados juntos com um adaptador, que, nesse código, é uma extensão da classe *ArrayAdapter&lt;ToDoItem&gt;*.

#### <a name="layout"></a>Como definir o layout

O layout é definido por vários trechos de código XML. Com base em um layout existente, vamos supor que o código a seguir representa a **ListView** que queremos preencher com nossos dados de servidor.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>


No código acima, o atributo *listitem* especifica a id do layout para uma linha individual na lista. Este é o código, que especifica uma caixa de seleção e seu texto associado. Isso será instanciado uma vez para cada item da lista. Esse layout não exibe o campo **ID**, e um layout mais complexo especificaria campos adicionais na exibição. Este código está no arquivo **row\_list\_to\_do.xml**.

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


#### <a name="adapter"></a>Como definir o adaptador

Como a fonte de dados da nossa exibição é uma matriz de *ToDoItem*, podemos criar uma subclasse do nosso adaptador de uma classe *ArrayAdapter&lt;ToDoItem&gt;*. Esta subclasse produzirá uma exibição para cada *ToDoItem* usando o layout *row\_list\_to\_do*.

No nosso código, definimos a seguinte classe que é uma extensão da classe *ArrayAdapter&lt;ToDoItem&gt;*:

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {


Você deve substituir o método *getView* do adaptador. Este código de exemplo mostra como fazer isso: os detalhes variarão de acordo com o seu aplicativo.

    @Override
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

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


		return row;
	}

Criamos uma instância dessa classe em nossa atividade, da seguinte forma:

	ToDoItemAdapter mAdapter;
	mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

Observe que o segundo parâmetro para o construtor ToDoItemAdapter é uma referência ao layout. A chamada para o construtor é seguida pelo código a seguir que primeiro obtém uma referência à **ListView** e, em seguida, chama *setAdapter* para se configurar e usar o adaptador que acabamos de criar:

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>A estrutura API

As operações da tabela de Aplicativos Móveis e as chamadas à API personalizadas são assíncronas, de modo que você usa os objetos [Future](http://developer.android.com/reference/java/util/concurrent/Future.html) e [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) em todos os métodos assíncronos que envolvem consultas e inserções, atualizações e exclusões. Isso facilita a execução de várias operações em um thread em segundo plano sem ter que lidar com vários retornos de chamadas aninhados.

Para saber como essas APIs assíncronas são usadas no aplicativo Android e como os dados são exibidos na interface de usuário, analise o arquivo **ToDoActivity.java** no projeto de início rápido do Android no [portal do Azure].


#### <a name="use-adapter"></a>Como usar o adaptador

Agora você está pronto para usar a associação de dados. O código a seguir mostra como obter os itens na tabela de serviços móveis, limpar o adaptador e chamar o método *add* do adaptador para preenchê-lo com os itens retornados.


**TBD**: teste este código!!

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
		runAsyncTask(task);
    }

Você também deverá chamar o adaptador sempre que modificar a tabela *ToDoItem* se desejar exibir os resultados desse procedimento. Como as modificações são feitas de registro em registro, você estará lidando com uma única linha, em vez de uma coleção. Ao inserir um item, você chama o método *add* no adaptador e, ao excluir, você chama o método *remove*.

##<a name="querying"></a>Como consultar dados no back-end do Aplicativo Móvel

Esta seção descreve como emitir consultas ao back-end do Aplicativo Móvel, que inclui as seguintes tarefas:

- [Retornar todos os itens]
- [Filtrar dados retornados]
- [Classificar dados retornados]
- [Retornar dados em páginas]
- [Selecionar colunas específicas]
- [Concatenar métodos de consulta](#chaining)

### <a name="showAll"></a>Retornar todos os itens de uma tabela

A consulta a seguir retorna todos os itens na tabela *ToDoItem*.

	List<ToDoItem> results = mToDoTable.execute().get();             

A variável *results* retorna o conjunto de resultados da consulta como uma lista.

### <a name="filtering"></a>Como filtrar dados retornados

A execução da consulta a seguir retorna todos os itens da tabela *ToDoItem*, em que *complete* é igual a *false*. Esse é o código que já está no Início Rápido.

	List<ToDoItem> result = mToDoTable.where()
								.field("complete").eq(false)
								.execute().get();

*mToDoTable* é a referência à tabela do serviço móvel que criamos anteriormente.

Defina um filtro usando a chamada ao método **where** na referência de tabela. Isso é seguido por uma chamada ao método **field** seguida por uma chamada ao método que especifica o predicado lógico. Os possíveis métodos de predicado incluem **eq** (igual), **ne** (diferente), **gt** (maior que), **ge** (maior que ou igual a), **lt** (menor que), **le** (menor que ou igual a), etc. Esses métodos permitem que você compare os campos de número e cadeia de caracteres com valores específicos.

Você pode filtrar por datas. Os métodos a seguir permitem comparar o campo de data inteira ou partes da data: **year**, **month**, **day**, **hour**, **minute** e **second**. O exemplo a seguir adiciona um filtro para itens cuja *data de vencimento* é igual a 2013.

	mToDoTable.where().year("due").eq(2013).execute().get();

Os métodos a seguir oferecem suporte a filtros complexos nos campos de cadeia de caracteres: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim** e **length**. O exemplo a seguir filtra linhas de tabela em que a coluna *text* começa com "PRI0".

	mToDoTable.where().startsWith("text", "PRI0").execute().get();

Os métodos de operador a seguir têm suporte em campos de número: **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling** e **round**. O exemplo a seguir filtra linhas de tabela em que *duration* é um número par.

	mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

É possível combinar predicados com esses métodos lógicos: **and**, **or** e **not**. O exemplo a seguir combina dois dos exemplos acima.

	mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
				.execute().get();

E você pode agrupar e aninhar operadores lógicos desta forma:

	mToDoTable.where()
				.year("due").eq(2013)
					.and
				(startsWith("text", "PRI0").or().field("duration").gt(10))
				.execute().get();

Para ver uma discussão mais detalhada e exemplos de filtragem, confira [Exploring the richness of the Android client query model](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="sorting"></a>Como classificar dados retornados

O código a seguir retorna todos os itens de uma tabela *ToDoItem* classificada em ordem crescente pelo campo *text*. *mToDoTable* é a referência à tabela de back-end que você criou anteriormente:

	mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

O primeiro parâmetro do método **orderBy** é uma cadeia de caracteres igual ao nome do campo pelo qual classificar.

O segundo parâmetro usa a enumeração **QueryOrder** para especificar se a classificação será feita em ordem crescente ou decrescente.

Observe que, se você estiver filtrando usando o método ***where***, o método ***where*** deverá ser invocado antes do método ***orderBy***.

### <a name="paging"></a>Como retornar dados em páginas

O primeiro exemplo mostra como selecionar os 5 primeiros itens de uma tabela. A consulta retorna os itens de uma tabela *ToDoItems*. *mToDoTable* é a referência à tabela de back-end que você criou anteriormente:

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


Veja uma consulta que ignora os 5 primeiros itens e retorna os 5 seguintes:

	mToDoTable.skip(5).top(5).execute().get();


### <a name="selecting"></a>Como selecionar colunas específicas

O código a seguir ilustra como retornar todos os itens de uma tabela *ToDoItems*, mas exibe apenas os campos *complete* e *text*. *mToDoTable* é a referência à tabela de back-end que criamos anteriormente.

	List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();


Aqui, os parâmetros para a função select são os nomes de cadeia de caracteres das colunas da tabela que você deseja retornar.

O método **select** precisa seguir métodos como **where** e **orderBy**, se eles estiverem presentes. Ele pode ser seguido por métodos de paginação como **top**.

### <a name="chaining"></a>Como concatenar métodos de consulta

Como já foi visto, os métodos usados nas tabelas de back-end de consulta podem ser concatenados. Isso lhe permitirá executar tarefas como selecionar colunas específicas ou linhas filtradas que são classificadas e paginadas. Você pode criar filtros lógicos bastante complexos.

O que faz isso funcionar é que os métodos de consulta usados retornam objetos **MobileServiceQuery&lt;T&gt;**, que, por sua vez, podem ter métodos adicionais invocados neles. Para encerrar a série de métodos e realmente executar a consulta, chame o método **execute**.

Veja um exemplo de código em que *mToDoTable* é uma referência à tabela *ToDoItem*.

	mToDoTable.where().year("due").eq(2013)
					.and().startsWith("text", "PRI0")
					.or().field("duration").gt(10)
				.select("id", "complete", "text", "duration")
				.orderBy(duration, QueryOrder.Ascending).top(20)
				.execute().get();

O principal requisito no encadeamento de métodos é que o método *where* e os predicados precisam vir primeiro. Depois disso, você poderá chamar métodos subsequentes na ordem que melhor atenda às necessidades do seu aplicativo.


##<a name="inserting"></a>Como inserir dados no back-end

O código a seguir mostra como inserir uma nova linha em uma tabela.

Primeiro, crie uma instância da classe *ToDoItem* e defina suas propriedades.

	ToDoItem item = new ToDoItem();
	item.text = "Test Program";
	item.complete = false;

Em seguida, execute o código a seguir:

	ToDoItem entity = mToDoTable.insert(item).get();

A entidade retornada corresponde aos dados inseridos na tabela de back-end, inclusive a ID e outros valores definidos no back-end.

Os Aplicativos Móveis exigem que cada tabela tenha uma coluna chamada **id**, que é usada para indexar a tabela. Por padrão, essa coluna é um tipo de dados de cadeia de caracteres, que é necessário para oferecer suporte à sincronização offline. O valor padrão da coluna ID é um GUID, mas você pode fornecer outros valores exclusivos, como endereços de email ou nomes de usuário. Quando um valor de ID de cadeia de caracteres não é fornecido para um registro inserido, o back-end gera um novo valor de GUID.

Os valores de ID de cadeia de caracteres proporcionam as seguintes vantagens:

+ As IDs podem ser geradas sem que seja preciso fazer uma varredura no banco de dados.
+ Os registros são mais fáceis de mesclar a partir de tabelas ou bancos de dados diferentes.
+ Os valores de ID integram-se melhor à lógica de um aplicativo.

##<a name="updating"></a>Como atualizar dados em um aplicativo móvel

O código a seguir mostra como atualizar dados em uma tabela.

    mToDoTable.update(item).get();

Neste exemplo, *item* é uma referência a uma linha na tabela *ToDoItem*, a qual sofreu algumas alterações.

##<a name="deleting"></a>Como excluir dados em um aplicativo móvel

O código a seguir mostra como excluir dados de uma tabela especificando o objeto de dados.

	mToDoTable.delete(item);

Você também pode excluir um item especificando o **id** da linha a ser excluída.

	String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
   	mToDoTable.delete(myRowId);
                    

##<a name="lookup"></a>Como pesquisar um item específico

Este código mostra como pesquisar um item com uma *id* específica.

	ToDoItem result = mToDoTable
						.lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
						.get();

##<a name="untyped"></a>Como trabalhar com dados não tipados

O modelo de programação não tipado oferece um controle exato sobre a serialização JSON, e há alguns cenários em que você pode querer usá-lo; por exemplo, se a sua tabela de back-end contiver um grande número de colunas e você só precisa fazer referência a algumas delas. O uso do modelo tipado requer que você defina todas as colunas da tabela de aplicativos móveis na classe de dados. Mas, com o modelo não tipado, você define apenas as colunas que precisa usar.

A maioria das chamadas API para acessar dados são semelhante às chamadas de programação tipadas. A principal diferença é que o modelo não tipado você invocar métodos no objeto **MobileServiceJsonTable**, em vez do objeto **MobileServiceTable**.


### <a name="json_instance"></a>Como criar uma instância de uma tabela não tipada

Semelhante ao modelo tipado, você começa obtendo uma referência de tabela, mas, nesse caso, é um objeto**MobileServicesJsonTable**. Você obtém a referência chamando o método **getTable** em uma instância do cliente, como esta:

    private MobileServiceJsonTable mJsonToDoTable;
	//...
    mJsonToDoTable = mClient.getTable("ToDoItem");

Após criar uma instância de **MobileServiceJsonTable**, você poderá chamar praticamente todos os métodos possíveis que ela contém com o modelo de programação tipado. No entanto, em alguns casos, os métodos usam um parâmetro não tipado, como podemos ver nos exemplos a seguir.

### <a name="json_insert"></a>Como inserir em uma tabela não tipada

O código a seguir mostra como fazer uma inserção. A primeira etapa consiste em criar[**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html), que faz parte da biblioteca [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801).

	JsonObject jsonItem = new JsonObject();
	jsonItem.addProperty("text", "Wake up");
	jsonItem.addProperty("complete", false);

A próxima etapa consiste em inserir o objeto.

    mJsonToDoTable.insert(jsonItem).get();                   


Se você precisar obter a ID do objeto inserido, use esta chamada de método:

	jsonItem.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>Como excluir de uma tabela não tipada

O código a seguir mostra como excluir uma instância, neste caso, a mesma instância de um **JsonObject** criado no exemplo *insert* anterior. Observe que o código é igual ao do caso tipado, mas o método tem uma assinatura diferente, uma vez que ele faz referência a um **JsonObject**.


         mToDoTable.delete(jsonItem);


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



##<a name="custom-api"></a>Chamar uma API personalizada

Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

Em um cliente Android, você chama o método **invokeApi** para chamar o ponto de extremidade da API personalizada. O exemplo a seguir mostra como chamar um ponto de extremidade da API chamado *completeAll*, que retorna uma classe de coleção chamada MarkAllResult.

	public void completeItem(View view) {
	    
	    ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class ); 
	    	
	    	Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
	    		@Override
	    		public void onFailure(Throwable exc) {
	    			createAndShowDialog((Exception) exc, "Error");
	    		}
	    		
	    		@Override
	    		public void onSuccess(MarkAllResult result) {
	    			createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
	                refreshItemsFromTable();	
	    		}
	    	});
	    }
	
O método **invokeApi** é chamado no cliente, que envia uma solicitação POST para a nova API personalizada. O resultado retornado pela API personalizada é exibido em uma caixa de diálogo de mensagem, se houver erros. Outras versões de **invokeApi** permitem que você, se desejar, envie um objeto no corpo da solicitação, especifique o método HTTP e envie parâmetros de consulta com a solicitação. Versões não tipadas de **invokeApi** também são fornecidas.

##<a name="authentication"></a>Como adicionar autenticação ao aplicativo

Os tutoriais já descrevem detalhadamente como adicionar esses recursos.

O Serviço de Aplicativo oferecer suporte à [autenticação de usuários do aplicativo](mobile-services-android-get-started-users.md) usando uma variedade de provedores de identidade externos: Facebook, Google, Conta da Microsoft, Twitter e Active Directory do Azure. Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização no seu back-end.

Dois fluxos de autenticação têm suporte: um fluxo de *servidor* e um fluxo de *cliente*. O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende da interface de autenticação da web do provedor. O fluxo de cliente permite uma integração mais profunda a recursos específicos do dispositivo, como o logon único, uma vez que ele depende de SDKs específicos de dispositivos específicos do provedor, além de exigir que você codifique isso.

Três etapas são necessárias para habilitar a autenticação no seu aplicativo:

- Registrar o aplicativo para autenticação com um provedor e configurar o back-end do Aplicativo Móvel.
- Restringir as permissões de tabela somente aos usuários autenticados.
- Adicionar código de autenticação ao aplicativo.

Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar o SID de um usuário autenticado para modificar solicitações.

Essas duas primeiras tarefas são feitas no [portal do Azure](https://portal.azure.com/). Para obter mais informações, consulte [Comece a usar a autenticação].

### <a name="caching"></a>Como adicionar código de autenticação para seu aplicativo

O código a seguir inicia o processo de logon do fluxo do servidor usando o provedor Google:

	MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

Você pode obter a ID do usuário conectado de um **MobileServiceUser** usando o método **getUserId**. Para ver um exemplo de como usar Futures para chamar as APIs de logon assíncronas, confira [Get started with authentication].


### <a name="caching"></a>Como armazenar tokens de autenticação em cache

Armazenar em cache os tokens de autenticação exige que você armazene uma ID de usuário e o token de autenticação localmente no dispositivo. Na próxima vez que o aplicativo iniciar, você verificará o cache e, se esses valores estiverem presentes, poderá ignorar o procedimento de logon e reidratar o cliente com esses dados. No entanto, esses dados são confidenciais e, para segurança, devem ser armazenados criptografados caso o telefone seja roubado.

Você pode ver um exemplo completo de como armazenar em cache tokens de autenticação na seção [Armazenar em cache tokens de autenticação no cliente](app-service-mobile-android-get-started-users.md#cache-tokens).

Ao tentar usar um token expirado, você obterá uma resposta *401 unauthorized*. O usuário deverá fazer logon obter novos tokens. Você pode evitar ter que escrever código para lidar com isso em cada local no seu aplicativo que chamar os serviços móveis usando filtros, o que lhe permite interceptar chamadas de entrada e respostas dos Serviços Móveis. O código do filtro testará a resposta para um 401, disparará o processo de logon se necessário e retomará a solicitação que gerou o 401. Também é possível inspecionar o token para verificar a expiração.

## Como adicionar notificação por push ao aplicativo

Você pode [ler uma visão geral](notification-hubs-overview.md/#integration-with-app-service-mobile-apps) que descreve como os Hubs de Notificações do Microsoft Azure oferecem suporte a uma ampla variedade de notificações por push.

[Neste tutorial](app-service-mobile-android-get-started-push.md), toda vez que um registro é inserido, uma notificação por push é enviada.

## Como adicionar sincronização offline ao aplicativo
O tutorial Início Rápido contém código que implementa a sincronização offline. Procure o código prefixado com comentários como este:

	// Offline Sync

Ao remover comentários das linhas de código a seguir, você pode implementar a sincronização offline e adicionar código semelhante a outro código dos Aplicativos Móveis.

##<a name="customizing"></a>Como personalizar o cliente

Há várias maneiras de personalizar o comportamento padrão do cliente.

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

O cliente pressupõe que os nomes de tabela, nomes de coluna e tipos de dados no back-end correspondem exatamente aos objetos de dados definidos no cliente. Porém, há vários motivos para que não haja correspondência dos nomes de servidor com os nomes de cliente. No seu cenário, você pode querer fazer os seguintes tipos de personalização:

- Os nomes de coluna usados na tabela de serviço móvel não correspondem aos nomes que você está usando no cliente.
- Usar uma tabela de serviço móvel que tenha um nome diferente da classe para a qual ela é mapeada no cliente.
- Ativar a capitalização automática de propriedade.
- Adicionar propriedades complexas a um objeto.

### <a name="columns"></a>Como mapear nomes diferentes de cliente e servidor

Suponha que o seu código de cliente Java use nomes de estilo Java padrão para as propriedades do objeto *ToDoItem*, como a apresentada a seguir.

- mId
- mText
- mComplete
- mDuration


Você precisa serializar os nomes de cliente em nomes JSON que correspondam aos nomes de coluna da tabela*ToDoItem* no servidor. O código a seguir, que usa a biblioteca [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) faz isso.

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;

	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>Como mapear diferentes nomes de tabela entre o cliente e o back-end

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
[Retornar todos os itens]: #showAll
[Filtrar dados retornados]: #filtering
[Classificar dados retornados]: #sorting
[Retornar dados em páginas]: #paging
[Selecionar colunas específicas]: #selecting
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
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[portal do Azure]: https://portal.azure.com
[Get started with authentication]: app-service-mobile-android-get-started-users.md
[Comece a usar a autenticação]: app-service-mobile-android-get-started-users.md

<!---HONumber=AcomDC_0114_2016-->