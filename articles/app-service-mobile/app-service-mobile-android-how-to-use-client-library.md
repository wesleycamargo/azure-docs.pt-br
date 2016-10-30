<properties
    pageTitle="Como usar a biblioteca de cliente de Aplicativos Móveis Android"
    description="Como usar o SDK do cliente Android para Aplicativos Móveis do Azure."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>



# <a name="how-to-use-the-android-client-library-for-mobile-apps"></a>Como usar a biblioteca de cliente Android para Aplicativos Móveis

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia mostra como usar o SDK de cliente Android para Aplicativos Móveis a fim de implementar cenários comuns, como:

- Consulta de dados (inserir, atualizar e excluir).
- Autenticação.
- Tratamento de erros.
- Personalização do cliente. 

Ele também faz uma análise aprofundada do código do cliente comum usado na maioria dos aplicativos móveis.

Este guia destaca o SDK do Android no lado do cliente.  Para saber mais sobre os SDKs no servidor para Aplicativos Móveis, confira [Trabalhar com o SDK][10] de back-end do .NET ou [Como usar o SDK][11] de back-end Node.js.

## <a name="reference-documentation"></a>Documentação de referência

Você pode encontrar a [referência][12] à API do Javadocs para a biblioteca de cliente Android no GitHub.

## <a name="supported-platforms"></a>Plataformas com suporte

O SDK do Android para Aplicativos Móveis dá suporte a níveis de API de 19 a 24 (KitKat usando Nougat).  

A autenticação de "fluxo de servidor" usa um modo de exibição da Web para a interface do usuário apresentada. Se o dispositivo não for capaz de apresentar uma interface do usuário para modo de exibição da Web, serão necessários outros métodos de autenticação fora do escopo do produto.  Esse SDK não é adequado para relógios ou dispositivos similarmente restritos.

## <a name="setup-and-prerequisites"></a>Configuração e pré-requisitos

Conclua o tutorial [Início rápido de Aplicativos Móveis](app-service-mobile-android-get-started.md) .  Essa tarefa faz com que todos os pré-requisitos para o desenvolvimento de Aplicativos Móveis do Azure sejam atendidos.  O Início Rápido ajuda a configurar sua conta e criar seu primeiro back-end de aplicativo móvel.

Se você decidir não concluir o tutorial de Início Rápido, conclua as seguintes tarefas:

- [Criar um back-end][13] do Aplicativo Móvel para usar com seu aplicativo Android.
- No Android Studio, [atualizar os arquivos de compilação do Gradle](#gradle-build).
- [Habilitar a permissão de Internet](#enable-internet).

###<a name="<a-name="gradle-build"></a>update-the-gradle-build-file"></a><a name="gradle-build"></a>Atualizar o arquivo de compilação do Gradle

Altere ambos os arquivos **build.gradle** :

1. Adicione este código ao arquivo **build.gradle** do nível *Project* dentro da marca *buildscript*:

        buildscript {
            repositories {
                jcenter()
            }
        }

2. Adicione este código ao arquivo **build.gradle** do nível *Module app* dentro da marca *dependencies*:

        compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

    Atualmente, a versão mais recente é a 3.1.0. As versões com suporte estão listadas [aqui][14].

###<a name="<a-name="enable-internet"></a>enable-internet-permission"></a><a name="enable-internet"></a>Habilitar a permissão de Internet

Para acessar o Azure, o aplicativo deve ter a permissão INTERNET habilitada. Se ela ainda não estiver habilitada, adicione a seguinte linha de código ao arquivo **AndroidManifest.xml** :

    <uses-permission android:name="android.permission.INTERNET" />

## <a name="the-basics-deep-dive"></a>Detalhamento das noções básicas

Esta seção aborda alguns dos códigos no aplicativo de Início Rápido que estão relacionados ao uso de Aplicativos Móveis do Azure.  

###<a name="<a-name="data-object"></a>define-client-data-classes"></a><a name="data-object"></a>Definir as classes de dados do cliente

Para acessar dados de tabelas do SQL Azure, defina as classes de dados do cliente que correspondem às tabelas no back-end de aplicativo móvel. Os exemplos deste tópico pressupõem a existência de uma tabela denominada **ToDoItem**com as seguintes colunas:

- ID
- text
- concluído

O objeto do lado do cliente com o tipo correspondente:

    public class ToDoItem {
        private String id;
        private String text;
        private Boolean complete;
    }

O código reside em um arquivo chamado **ToDoItem.java**.

Se a tabela do SQL Azure contiver mais colunas, adicione os campos correspondentes a essa classe.  Por exemplo, se o DTO (objeto de transferência de dados) tivesse uma coluna Priority de inteiros, você poderia adicionar este campo, com seus métodos getter e setter:

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

Para saber como criar mais tabelas em seu back-end de Aplicativos Móveis, confira [Como definir um controlador de tabela][15] (back-end do .NET) ou [Como definir tabelas usando um esquema dinâmico][16] (back-end do Node.js). Para um back-end Node.js, você também pode usar a configuração **Tabelas fáceis** no [portal do Azure].

###<a name="<a-name="create-client"></a>how-to:-create-the-client-context"></a><a name="create-client"></a>Como criar o contexto do cliente

Este código cria o objeto **MobileServiceClient** usado para acessar o back-end do Aplicativo Móvel. O código deve ser colocado no `onCreate`método da classe **Activity** especificada em *AndroidManifest.xml* como uma ação **MAIN** e categoria **LAUNCHER**. No código do Início Rápido, ele deve ser colocado no arquivo **ToDoActivity.java** .

        MobileServiceClient mClient = new MobileServiceClient(
            "MobileAppUrl", // Replace with the Site URL
            this)

Nesse código, substitua `MobileAppUrl` pela URL do back-end do Aplicativo Móvel, que pode ser encontrada no [portal do Azure] , na folha do back-end do Aplicativo Móvel. Para que essa linha de código seja compilada, você também precisa adicionar a instrução **import** a seguir:

    import com.microsoft.windowsazure.mobileservices.*;

###<a name="<a-name="instantiating"></a>how-to:-create-a-table-reference"></a><a name="instantiating"></a>Como criar uma referência de tabela

A maneira mais fácil de consultar ou modificar dados no back-end é usando o *modelo de programação tipado*, pois o Java é uma linguagem fortemente tipada. Esse modelo fornece perfeita serialização e desserialização de JSON usando a biblioteca [gson][3] ao enviar dados entre objetos e tabelas do cliente no SQL do Azure no back-end.

Para acessar uma tabela, primeiramente crie um objeto [MobileServiceTable][8] chamando o método **getTable** no [MobileServiceClient][9].  Esse método tem duas sobrecargas:

    public class MobileServiceClient {
        public <E> MobileServiceTable<E> getTable(Class<E> clazz);
        public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
    }

No código a seguir, **mClient** é uma referência ao objeto MobileServiceClient.  A primeira sobrecarga é usada onde o nome da classe e o nome da tabela são iguais, e é a usada no Início Rápido:

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

A segunda sobrecarga é usada quando o nome da tabela é diferente do nome da classe: o primeiro parâmetro é o nome da tabela.

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="<a-name="binding"></a>how-to:-bind-data-to-the-user-interface"></a><a name="binding"></a>Como associar dados à interface do usuário

A associação de dados envolve três componentes:

- A fonte de dados
- O layout da tela
- O adaptador que vincula os dois.

Em nosso código de exemplo, retornamos os dados da tabela **ToDoItem** do SQL Azure dos Aplicativos Móveis em uma matriz. Essa atividade é um padrão comum para aplicativos de dados.  As consultas de banco de dados geralmente retornam uma coleção de linhas que o cliente obtém em uma lista ou uma matriz. Neste exemplo, a matriz é a fonte de dados.

O código especifica um layout de tela que define a exibição dos dados que são exibidos no dispositivo.  E os dois são associados juntos com um adaptador, que, nesse código, é uma extensão da classe **ArrayAdapter&lt;ToDoItem&gt;**.

#### <a name="<a-name="layout"></a>how-to:-define-the-layout"></a><a name="layout"></a>Como definir o layout

O layout é definido por vários trechos de código XML. Com base em um layout existente, o código a seguir representa a **ListView** que queremos preencher com nossos dados de servidor.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

No código anterior, o atributo *listitem* especifica a id do layout para uma linha individual na lista. Esse código especifica uma caixa de seleção e seu texto associado, e tem uma instância criada para cada item na lista. Esse layout não exibe o campo **ID** , e um layout mais complexo especificaria campos adicionais na exibição. Este código está no arquivo **row_list_to_do.xml**.

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


#### <a name="<a-name="adapter"></a>how-to:-define-the-adapter"></a><a name="adapter"></a>Como definir o adaptador

Como a fonte de dados da nossa exibição é uma matriz de**ToDoItem**, podemos criar a subclasse do nosso adaptador por meio de uma classe **ArrayAdapter&lt;ToDoItem&gt;**. Esta subclasse produz uma exibição para cada **ToDoItem** usando o layout **row_list_to_do**.

No nosso código, definimos a seguinte classe que é uma extensão da classe **ArrayAdapter&lt;E&gt;**:

    public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

Substitua o método **getView** dos adaptadores. Por exemplo:

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

O segundo parâmetro para o construtor ToDoItemAdapter é uma referência ao layout. Agora podemos criar uma instância do **ListView** e atribuir o adaptador ao **ListView**.

    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);

### <a name="<a-name="api"></a>the-api-structure"></a><a name="api"></a>A estrutura API

As operações de tabela de Aplicativos Móveis e as chamadas de API personalizadas são assíncronas. Use os objetos [Future] e [AsyncTask] para os métodos assíncronos que envolvem consultas, inserções, atualizações e exclusões. O uso de futures facilita a execução de várias operações em um thread em segundo plano sem ter que lidar com vários retornos de chamadas aninhados.

Examine o arquivo **Todoactivity** no projeto de início rápido Android no [portal do Azure] para obter um exemplo.

#### <a name="<a-name="use-adapter"></a>how-to:-use-the-adapter"></a><a name="use-adapter"></a>Como usar o adaptador

Agora você está pronto para usar a associação de dados. O código a seguir mostra como obter os itens na tabela e preenche o adaptador local com os itens retornados.

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

Chame o adaptador sempre que modificar a tabela **ToDoItem** . Como as modificações são feitas de registro em registro, você trata de uma única linha em vez de uma coleção. Ao inserir um item, chame o método **add** no adaptador e, ao excluir, chame o método **remove**.

##<a name="<a-name="querying"></a>how-to:-query-data-from-your-mobile-app-backend"></a><a name="querying"></a>Como consultar dados no back-end do Aplicativo Móvel

Esta seção descreve como emitir consultas ao back-end do Aplicativo Móvel, que inclui as seguintes tarefas:

- [Retornar todos os itens]
- [Filtrar dados retornados]
- [Classificar dados retornados]
- [Retornar dados em páginas]
- [Selecionar colunas específicas]
- [Concatenar métodos de consulta](#chaining)

### <a name="<a-name="showall"></a>how-to:-return-all-items-from-a-table"></a><a name="showAll"></a>Retornar todos os itens de uma tabela

A consulta a seguir retorna todos os itens na tabela **ToDoItem** .

    List<ToDoItem> results = mToDoTable.execute().get();

A variável *results* retorna o conjunto de resultados da consulta como uma lista.

### <a name="<a-name="filtering"></a>how-to:-filter-returned-data"></a><a name="filtering"></a>Como filtrar dados retornados

A execução da consulta a seguir retorna todos os itens da tabela **ToDoItem**, em que **complete** é igual a **false**.

    List<ToDoItem> result = mToDoTable.where()
                                .field("complete").eq(false)
                                .execute().get();

**mToDoTable** é a referência à tabela do serviço móvel que criamos anteriormente.

Defina um filtro usando a chamada ao método **where** na referência de tabela. O método **where** é seguido de um método **field** seguido de um método que especifica o predicado lógico. Os possíveis métodos de predicado incluem **eq** (igual),**ne** (diferente), **gt** (maior que), **ge** (maior que ou igual a), **lt** (menor que), **le** (menor que ou igual a). Esses métodos permitem que você compare os campos de número e cadeia de caracteres com valores específicos.

Você pode filtrar por datas. Os métodos a seguir permitem comparar o campo de data inteira ou partes da data:**year**, **month**, **day**, **hour**, **minute** e **second**. O exemplo a seguir adiciona um filtro para itens cuja *data de vencimento* é igual a 2013.

    mToDoTable.where().year("due").eq(2013).execute().get();

Os métodos a seguir oferecem suporte a filtros complexos nos campos de cadeia de caracteres: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim** e **length**. O exemplo a seguir filtra linhas de tabela em que a coluna *text* começa com "PRI0".

    mToDoTable.where().startsWith("text", "PRI0").execute().get();

Os métodos de operador a seguir têm suporte em campos de número: **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling** e **round**. O exemplo a seguir filtra linhas de tabela em que **duration** é um número par.

    mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

É possível combinar predicados com estes métodos lógicos: **and**, **or** e **not**. O exemplo a seguir combina dois dos exemplos anteriores.

    mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
                .execute().get();

Agrupar e aninhar operadores lógicos:

    mToDoTable.where()
                .year("due").eq(2013)
                    .and
                (startsWith("text", "PRI0").or().field("duration").gt(10))
                .execute().get();

Para ver uma discussão mais detalhada e exemplos de filtragem, veja [Explorando a riqueza do modelo de consulta de cliente do Android](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="<a-name="sorting"></a>how-to:-sort-returned-data"></a><a name="sorting"></a>Como classificar dados retornados

O código a seguir retorna todos os itens de uma tabela **ToDoItem** classificada em ordem crescente pelo campo *text* . *mToDoTable* é a referência à tabela de back-end criada anteriormente:

    mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

O primeiro parâmetro do método **orderBy** é uma cadeia de caracteres igual ao nome do campo pelo qual classificar. O segundo parâmetro usa a enumeração **QueryOrder** para especificar se a classificação será feita em ordem crescente ou decrescente.  Se você estiver filtrando usando o método ***where***, o método ***where*** deverá ser invocado antes do método ***orderBy***.

### <a name="<a-name="paging"></a>how-to:-return-data-in-pages"></a><a name="paging"></a>Como retornar dados em páginas

O primeiro exemplo mostra como selecionar os cinco primeiros itens de uma tabela. A consulta retorna os itens de uma tabela **ToDoItems**. **mToDoTable** é a referência à tabela de back-end criada anteriormente:

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


Veja uma consulta que ignora os cinco primeiros itens e retorna os cinco seguintes:

    mToDoTable.skip(5).top(5).execute().get();

### <a name="<a-name="selecting"></a>how-to:-select-specific-columns"></a><a name="selecting"></a>Como selecionar colunas específicas

O código a seguir ilustra como retornar todos os itens de uma tabela de **ToDoItems**, mas exibe apenas os **complete** e **text**. **mToDoTable** é a referência à tabela de back-end criada anteriormente.

    List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();

Os parâmetros para a função select são os nomes de cadeia de caracteres das colunas da tabela que você deseja retornar.

O método **select** precisa seguir métodos como **where** e **orderBy**. Ele pode ser seguido por métodos de paginação como **top**.

### <a name="<a-name="chaining"></a>how-to:-concatenate-query-methods"></a><a name="chaining"></a>Como concatenar métodos de consulta

Os métodos usados nas tabelas de back-end de consulta podem ser concatenados. O encadeamento de métodos de consulta permite a você selecionar colunas específicas ou linhas filtradas que são classificadas e paginadas. Você pode criar filtros lógicos complexos.
Cada método de consulta retorna um objeto de consulta. Para encerrar a série de métodos e realmente executar a consulta, chame o método **execute** . Por exemplo:

    mToDoTable.where()
        .year("due").eq(2013)
        .and().startsWith("text", "PRI0")
        .or().field("duration").gt(10)
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .top(20)
        .execute().get();

Os métodos de consulta encadeados devem ser ordenados da seguinte maneira:

1. Métodos de filtragem (**where**).
2. Métodos de classificação (**orderBy**).
3. Métodos de seleção (**select**).
4. Métodos de paginação (**skip** e **top**).

##<a name="<a-name="inserting"></a>how-to:-insert-data-into-the-backend"></a><a name="inserting"></a>Como inserir dados no back-end

Crie uma instância da classe *ToDoItem* e defina suas propriedades.

    ToDoItem item = new ToDoItem();
    item.text = "Test Program";
    item.complete = false;

Em seguida, use **insert ()** para inserir um objeto:

    ToDoItem entity = mToDoTable.insert(item).get();

A entidade retornada corresponde aos dados inseridos na tabela de back-end, inclusive a ID e outros valores definidos no back-end.

As tabelas de Aplicativos Móveis exigem uma coluna de chave primária denominada **id**. Por padrão, essa coluna é uma cadeia de caracteres. O valor padrão da coluna de ID é um GUID.  Você pode fornecer outros valores exclusivos, como endereços de email ou nomes de usuário. Quando um valor de ID de cadeia de caracteres não é fornecido para um registro inserido, o back-end gera um novo GUID.

Os valores de ID de cadeia de caracteres proporcionam as seguintes vantagens:

+ As IDs podem ser geradas sem que seja preciso fazer uma varredura no banco de dados.
+ Os registros são mais fáceis de mesclar a partir de tabelas ou bancos de dados diferentes.
+ Os valores de ID integram-se melhor à lógica de um aplicativo.

Valores de ID de cadeia de caracteres são **OBRIGATÓRIOS** para suporte de sincronização offline.

##<a name="<a-name="updating"></a>how-to:-update-data-in-a-mobile-app"></a><a name="updating"></a>Como atualizar dados em um aplicativo móvel

Para atualizar dados em uma tabela, passe o novo objeto para o método **update ()** .

    mToDoTable.update(item).get();

Neste exemplo, *item* é uma referência a uma linha na tabela *ToDoItem*, a qual sofreu algumas alterações.
A linha com a mesma **id** é atualizada.

##<a name="<a-name="deleting"></a>how-to:-delete-data-in-a-mobile-app"></a><a name="deleting"></a>Como excluir dados em um aplicativo móvel

O código a seguir mostra como excluir dados de uma tabela especificando o objeto de dados.

    mToDoTable.delete(item);

Você também pode excluir um item especificando a **id** da linha a ser excluída.

    String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
    mToDoTable.delete(myRowId);

##<a name="<a-name="lookup"></a>how-to:-look-up-a-specific-item"></a><a name="lookup"></a>Como pesquisar um item específico

Pesquise um item com determinado campo **id** com o método **lookUp()**:

    ToDoItem result = mToDoTable
                        .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
                        .get();

##<a name="<a-name="untyped"></a>how-to:-work-with-untyped-data"></a><a name="untyped"></a>Como trabalhar com dados não tipados

O modelo de programação não tipado oferece controle rígido sobre a serialização JSON.  Há alguns cenários comuns em que você pode querer usar um modelo de programação não tipado. Por exemplo, se sua tabela de back-end contém muitas colunas e você só precisa fazer referência a um subconjunto das colunas.  O modelo tipado requer que você defina todas as colunas da tabela de aplicativos móveis na classe de dados.  

A maioria das chamadas API para acessar dados são semelhante às chamadas de programação tipadas. A principal diferença é que o modelo não tipado você invocar métodos no objeto **MobileServiceJsonTable**, em vez do objeto **MobileServiceTable**.

### <a name="<a-name="json_instance"></a>how-to:-create-an-instance-of-an-untyped-table"></a><a name="json_instance"></a>Como criar uma instância de uma tabela não tipada

Semelhante ao modelo tipado, você começa obtendo uma referência de tabela, mas, nesse caso, é um objeto **MobileServicesJsonTable** . Obtenha a referência ao chamar o método **getTable** em uma instância do cliente:

    private MobileServiceJsonTable mJsonToDoTable;
    //...
    mJsonToDoTable = mClient.getTable("ToDoItem");

Depois de criar uma instância do **MobileServiceJsonTable**, ela tem praticamente a mesma API disponível que o modelo de programação tipado. Em alguns casos, os métodos usam um parâmetro não tipado em vez de um parâmetro tipado.

### <a name="<a-name="json_insert"></a>how-to:-insert-into-an-untyped-table"></a><a name="json_insert"></a>Como inserir em uma tabela não tipada

O código a seguir mostra como fazer uma inserção. A primeira etapa consiste em criar um [JsonObject][1], que faz parte da biblioteca [gson][3].

    JsonObject jsonItem = new JsonObject();
    jsonItem.addProperty("text", "Wake up");
    jsonItem.addProperty("complete", false);

Em seguida, use **insert()** para inserir o objeto não tipado na tabela.

    mJsonToDoTable.insert(jsonItem).get();

Se você precisa obter a ID do objeto inserido, use o método **getAsJsonPrimitive()** .

    jsonItem.getAsJsonPrimitive("id").getAsInt());

### <a name="<a-name="json_delete"></a>how-to:-delete-from-an-untyped-table"></a><a name="json_delete"></a>Como excluir de uma tabela não tipada

O código a seguir mostra como excluir uma instância, neste caso, a mesma instância de um **JsonObject** criado no exemplo *insert* anterior. O código é igual ao do caso tipado, mas o método tem uma assinatura diferente, uma vez que ele faz referência a um **JsonObject**.

         mToDoTable.delete(jsonItem);

Você também pode excluir uma instância diretamente, usando sua ID:

         mToDoTable.delete(ID);

### <a name="<a-name="json_get"></a>how-to:-return-all-rows-from-an-untyped-table"></a><a name="json_get"></a>Como retornar todas as linhas de uma tabela não tipada

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

O mesmo conjunto de métodos de filtragem, filtragem e paginação que estão disponíveis para o modelo tipado estão disponíveis também para o modelo não tipado.

##<a name="<a-name="custom-api"></a>how-to:-call-a-custom-api"></a><a name="custom-api"></a>Chamar uma API personalizada

Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

Em um cliente Android, você chama o método **invokeApi** para chamar o ponto de extremidade da API personalizada. O exemplo a seguir mostra como chamar um ponto de extremidade de API denominado **completeAll**, que retorna uma classe de coleção chamada **MarkAllResult**.

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

O método **invokeApi** é chamado no cliente, que envia uma solicitação POST para a nova API personalizada. O resultado retornado pela API personalizada é exibido em uma caixa de diálogo de mensagem, se houver erros. Outras versões de **invokeApi** permitem que você, opcionalmente, envie um objeto no corpo da solicitação, especifique o método HTTP e envie parâmetros de consulta com a solicitação. Versões não tipadas de **invokeApi** também são fornecidas.

##<a name="<a-name="authentication"></a>how-to:-add-authentication-to-your-app"></a><a name="authentication"></a>Como adicionar autenticação ao aplicativo

Os tutoriais já descrevem detalhadamente como adicionar esses recursos.

O Serviço de Aplicativo oferece suporte à [autenticação de usuários do aplicativo](app-service-mobile-android-get-started-users.md) usando uma variedade de provedores de identidade externos: Facebook, Google, Conta da Microsoft, Twitter e Active Directory do Azure. Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização no seu back-end.

Dois fluxos de autenticação são suportados: um **server** flow e um **client** flow. O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende da interface Web do provedor de identidade.  Nenhum SDK adicional é necessário para implementar a autenticação de fluxo de servidor. A autenticação de fluxo de servidor não oferece uma integração profunda ao dispositivo móvel e só é recomendada em cenários de prova de conceito.

O fluxo de cliente permite uma integração mais profunda com funcionalidades específicas do dispositivo, como logon único, uma vez que depende de SDKs fornecidas pelo provedor de identidade.  Por exemplo, você pode integrar o SDK do Facebook a seu aplicativo móvel.  O cliente móvel alterna para o aplicativo do Facebook e confirma o logon antes de voltar para seu aplicativo móvel.

Quatro etapas são necessárias para habilitar a autenticação no seu aplicativo:

- Registrar o aplicativo para autenticação com um provedor de identidade.
- Configurar o back-end do Serviço de Aplicativo.
- Restringir permissões de tabela a usuários autenticados somente no back-end do Serviço de Aplicativo.
- Adicionar código de autenticação ao aplicativo.

Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar o SID de um usuário autenticado para modificar solicitações.  Para saber mais, confira [Introdução à autenticação] e a documentação TUTORIAL do SDK do Servidor.

### <a name="<a-name="caching"></a>how-to:-add-authentication-code-to-your-app"></a><a name="caching"></a>Como adicionar código de autenticação para seu aplicativo

O código a seguir inicia um processo de logon do fluxo do servidor usando o provedor Google:

    MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

Obtenha a ID do usuário conectado de um **MobileServiceUser** usando o método **getUserId**. Para ver um exemplo de como usar Futures para chamar as APIs de logon assíncronas, veja [Introdução à autenticação].

### <a name="<a-name="caching"></a>how-to:-cache-authentication-tokens"></a><a name="caching"></a>Como armazenar tokens de autenticação em cache

Armazenar em cache os tokens de autenticação exige que você armazene uma ID de usuário e o token de autenticação localmente no dispositivo. Na próxima vez que o aplicativo iniciar, você verificará o cache e, se esses valores estiverem presentes, poderá ignorar o procedimento de logon e reidratar o cliente com esses dados. No entanto, esses dados são confidenciais e, para segurança, devem ser armazenados criptografados caso o telefone seja roubado.

Veja um exemplo completo de como armazenar em cache os tokens de autenticação na [seção Armazenar em cache os tokens de autenticação][7].

Ao tentar usar um token expirado, você receberá uma resposta *401 não autorizado* . Você pode tratar erros de autenticação usando filtros.  Filtros interceptam as solicitações para o back-end do Serviço de Aplicativo. O código do filtro testa a resposta para um 401, dispara o processo de logon e retoma a solicitação que gerou o 401. 

## <a name="<a-name="adal"></a>how-to:-authenticate-users-with-the-active-directory-authentication-library"></a><a name="adal"></a>Como autenticar usuários com a Biblioteca de Autenticação do Active Directory

Você pode usar a ADAL (Biblioteca de autenticação do Active Directory) para conectar os usuários ao seu aplicativo usando o Active Directory do Azure. Normalmente, é melhor usar um logon de fluxo de cliente em vez dos métodos `loginAsync()` , pois ele fornece uma aparência mais nativa de UX e permite uma maior personalização.

1. Configure o seu back-end de aplicativo móvel para entrada no AAD seguindo o tutorial [Como configurar o Serviço de Aplicativo para logon no Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) . Complete a etapa opcional de registrar um aplicativo cliente nativo.

2. Instale a ADAL modificando o arquivo build.gradle para incluir as seguintes definições:

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

3. Adicione o seguinte código ao seu aplicativo, fazendo as seguintes substituições:

* Substitua **INSERT-AUTHORITY-HERE** pelo nome do locatário no qual o aplicativo foi provisionado. O formato deve ser https://login.windows.net/contoso.onmicrosoft.com. Esse valor pode ser copiado da guia Domínio no Azure Active Directory no [portal clássico do Azure].

* Substitua **INSERT-RESOURCE-ID-HERE** pela ID do cliente do seu back-end de aplicativo móvel. Você pode obter a ID do cliente na guia **Avançadas** em **Configurações do Azure Active Directory** no portal.

* Substitua **INSERT-CLIENT-ID-HERE** pela ID do cliente copiada do aplicativo cliente nativo.

* Substitua **INSERT-REDIRECT-URI-HERE** pelo ponto de extremidade _/.auth/login/done_ do site, usando o esquema HTTPS. Esse valor deve ser similar a _https://contoso.azurewebsites.net/.auth/login/done_.

        private AuthenticationContext mContext;

        private void authenticate() {
            String authority = "INSERT-AUTHORITY-HERE";
            String resourceId = "INSERT-RESOURCE-ID-HERE";
            String clientId = "INSERT-CLIENT-ID-HERE";
            String redirectUri = "INSERT-REDIRECT-URI-HERE";
            try {
                mContext = new AuthenticationContext(this, authority, true);
                mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
            } catch (Exception exc) {
                exc.printStackTrace();
            }
        }

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    Log.d(TAG, "Cancelled");
                } else {
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    Log.d(TAG, "Token is empty");
                } else {
                    try {
                        JSONObject payload = new JSONObject();
                        payload.put("access_token", result.getAccessToken());
                        ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                        Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                            @Override
                            public void onFailure(Throwable exc) {
                                exc.printStackTrace();
                            }
                            @Override
                            public void onSuccess(MobileServiceUser user) {
                                Log.d(TAG, "Login Complete");
                            }
                        });
                    }
                    catch (Exception exc){
                        Log.d(TAG, "Authentication error:" + exc.getMessage());
                    }
                }
            }
        };

        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
            }
        }

## <a name="how-to:-add-push-notification-to-your-app"></a>Como adicionar notificação por push ao aplicativo

Você pode [ler uma visão geral][6] que descreve como os Hubs de Notificações do Microsoft Azure dão suporte a uma ampla variedade de notificações por push.  Neste [tutorial][5], uma notificação por push será enviada a todos os dispositivos sempre que um registro for inserido.

## <a name="how-to:-add-offline-sync-to-your-app"></a>Como adicionar sincronização offline ao aplicativo

O tutorial Início Rápido contém código que implementa a sincronização offline. Procure o código prefixado com comentários:

    // Offline Sync

Ao remover comentários das linhas de código a seguir, você pode implementar a sincronização offline e adicionar código semelhante a outro código dos Aplicativos Móveis.

##<a name="<a-name="customizing"></a>how-to:-customize-the-client"></a><a name="customizing"></a>Como personalizar o cliente

Há várias maneiras de personalizar o comportamento padrão do cliente.

### <a name="<a-name="headers"></a>how-to:-customize-request-headers"></a><a name="headers"></a>Como personalizar cabeçalhos de solicitação

Configure um **ServiceFilter** para adicionar um cabeçalho HTTP personalizado a cada solicitação:

    private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                    ServiceFilterRequest request,
                    NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
                    request.addHeader("My-Header", "Value");                    }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="<a-name="serialization"></a>how-to:-customize-serialization"></a><a name="serialization"></a>Como personalizar a serialização

O cliente pressupõe que os nomes de tabela, nomes de coluna e tipos de dados no back-end correspondem exatamente aos objetos de dados definidos no cliente. Há vários motivos para que não haja correspondência dos nomes de servidor com os nomes de cliente. No seu cenário, você pode querer fazer os seguintes tipos de personalização:

- Os nomes de coluna usados na tabela do Serviço de Aplicativo não correspondem aos nomes que você está usando no cliente.
- Usar uma tabela do Serviço de Aplicativo que tenha um nome diferente da classe para a qual ela é mapeada no cliente.
- Ativar a capitalização automática de propriedade.
- Adicionar propriedades complexas a um objeto.

### <a name="<a-name="columns"></a>how-to:-map-different-client-and-server-names"></a><a name="columns"></a>Como mapear nomes diferentes de cliente e servidor

Suponha que o seu código de cliente Java use nomes de estilo Java padrão para as propriedades do objeto **ToDoItem** , como as seguintes propriedades:

- mId
- mText
- mComplete
- mDuration

Serialize os nomes de cliente em nomes JSON que correspondam aos nomes de coluna da tabela **ToDoItem** no servidor. O código abaixo usa a biblioteca [gson][3] para anotar as propriedades:

    @com.google.gson.annotations.SerializedName("text")
    private String mText;

    @com.google.gson.annotations.SerializedName("id")
    private int mId;

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;

    @com.google.gson.annotations.SerializedName("duration")
    private String mDuration;

### <a name="<a-name="table"></a>how-to:-map-different-table-names-between-the-client-and-the-backend"></a><a name="table"></a>Como mapear diferentes nomes de tabela entre o cliente e o back-end

Mapeie o nome da tabela de cliente para um nome de tabela dos Serviços Móveis diferente usando uma substituição do método [getTable()][4]:

    mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="<a-name="conversions"></a>how-to:-automate-column-name-mappings"></a><a name="conversions"></a>Como automatizar os mapeamentos de nome de coluna

Você pode especificar uma estratégia de conversão que se aplica a todas as colunas usando a API [gson][3]. A biblioteca do cliente Android usa [gson][3] nos bastidores para serializar objetos Java para dados JSON antes que os dados sejam são enviados aos Serviço de Aplicativo do Azure.  O código a seguir usa o método **setFieldNamingStrategy ()** para definir a estratégia. Esse exemplo excluirá o caractere inicial (um "m") e, em seguida, a minúscula do próximo caractere, para cada nome de campo. Por exemplo, ele poderia transformar "mId" em "id".

    client.setGsonBuilder(
        MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(new FieldNamingStrategy() {
            public String translateName(Field field) {
                String name = field.getName();
                return Character.toLowerCase(name.charAt(1))
                    + name.substring(2);
                }
            });

Esse código deve ser executado antes de usar o **MobileServiceClient**.

### <a name="<a-name="complex"></a>how-to:-store-an-object-or-array-property-into-a-table"></a><a name="complex"></a>Como armazenar uma propriedade de objeto ou matriz em uma tabela

Até agora, nossos exemplos de serialização envolveram tipos primitivos, como inteiros e cadeias de caracteres.  Tipos primitivos serializam facilmente em JSON.  Se quisermos adicionar um objeto complexo que não serializa automaticamente em JSON, precisaremos fornecer o método de serialização JSON.  Para ver um exemplo de como fornecer serialização personalizada JSON, examine a postagem de blog [Personalizando a serialização usando a biblioteca gson no cliente de Serviços Móveis do Android][2].

<!-- Anchors. -->

[O que são Serviços Móveis]: #what-is
[Conceitos]: #concepts
[Como criar o cliente dos Serviços Móveis]: #create-client
[Como criar uma referência de tabela]: #instantiating
[A estrutura API]: #api
[Como consultar dados de um Serviço Móvel]: #querying
[Retornar todos os itens]: #showAll
[Filtrar dados retornados]: #filtering
[Classificar dados retornados]: #sorting
[Retornar dados em páginas]: #paging
[Selecionar colunas específicas]: #selecting
[Como concatenar métodos de consulta]: #chaining
[Como associar dados à interface do usuário]: #binding
[How to: Define the layout]: #layout
[Como definir o adaptador]: #adapter
[Como usar o adaptador]: #use-adapter
[Como inserir dados em um serviço móvel]: #inserting
[How to: update data in a mobile service]: #updating
[Como excluir dados em um serviço móvel]: #deleting
[Como pesquisar um item específico]: #lookup
[Como trabalhar com dados não tipados]: #untyped
[Como autenticar usuários]: #authentication
[Tokens de autenticação em cache]: #caching
[Como tratar erros]: #errors
[Como criar testes de unidade]: #tests
[Como personalizar o cliente]: #customizing
[Personalizar cabeçalhos de solicitações]: #headers
[Personalizar a serialização]: #serialization
[Next Steps]: #next-steps
[Configuração e Pré-requisitos]: #setup

<!-- Images. -->

<!-- URLs. -->
[Introdução aos Aplicativos Móveis do Azure]: app-service-mobile-android-get-started.md
[Códigos de controle ASCII C0 e C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[SDK para Serviços Móveis para Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Portal do Azure]: https://portal.azure.com
[Introdução à autenticação]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Futuro]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html


<!--HONumber=Oct16_HO2-->


