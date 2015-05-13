<properties 
	pageTitle="Criar um aplicativo Web Java usando o Banco de Dados de Documentos" 
	description="Este tutorial mostra a você como usar o serviço do Banco de Dados de Documentos do Azure para armazenar e acessar dados de um aplicativo Java hospedado nos Sites do Azure." 
	services="documentdb" 
	documentationCenter="java" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.devlang="java" 
	ms.topic="hero-article" 
	ms.tgt_pltfrm="NA" 
	ms.workload="data-services" 
	ms.date="04/29/2015" 
	ms.author="andrl"/>

# Criar um aplicativo Web Java usando o Banco de Dados de Documentos #

Este tutorial mostra a você como usar o serviço do [Banco de Dados de Documentos do Microsoft Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB) para armazenar e acessar dados de um aplicativo Java hospedado nos Sites do Azure. Neste tópico, você aprenderá:

- Como criar um aplicativo básico do JSP no Eclipse.
- Como trabalhar com o serviço do Banco de Dados de Documentos do Azure usando o [SDK Java do Banco de Dados de Documentos](https://github.com/Azure/azure-documentdb-java).

Este tutorial mostra como criar um aplicativo de gerenciamento de tarefas baseado na web que permite criar, recuperar e marcar tarefas como concluídas, conforme mostrado na imagem a seguir. Cada uma das tarefas na lista de tarefas é armazenada como documentos JSON no Banco de Dados de Documentos do Azure.

![Aplicativo Minha lista de tarefas](./media/documentdb-java-application/image1.png)

> [AZURE.TIP]Este tutorial presume que você tenha experiência anterior com o Java. Se você não estiver familiarizado com Java ou as [Ferramentas pré-requisito](#Prerequisites), recomendamos o download completo do projeto [tarefas](https://github.com/Azure/azure-documentdb-java/tree/master/tutorial/todo) no [GitHub](https://github.com/Azure/azure-documentdb-java) e compilá-lo usando [as instruções no final deste artigo](#GetProject). Depois de compilá-lo, você poderá consultar o artigo para obter informações sobre o código no contexto do projeto.

##<a id="Prerequisites"></a>Pré-requisitos ##
Antes de começar este tutorial, você deve ter o seguinte:

- Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](../../pricing/free-trial/).
- [Java Development Kit \(JDK\) 7 +](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
- [Eclipse IDE para desenvolvedores de Java EE.](http://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunasr1)
- [Um site do Azure com um Java runtime environment \(por exemplo, Tomcat ou Jetty\) habilitado.](web-sites-java-get-started.md)

Se você estiver instalando essas ferramentas pela primeira vez, o coreservlets.com fornecerá um passo a passo do processo de instalação na seção de Início rápido do artigo [Tutorial: Instalar TomCat7 e usá-lo com o Eclipse](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html).

##<a id="CreateDB"></a>Etapa 1: criar uma conta de banco de dados do Banco de Dados de Documentos ##
Para provisionar uma conta de banco de dados do Banco de Dados de Documentos no Azure:

1. Se você ainda não tiver uma conta de banco de dados, crie uma seguindo as instruções em [Criar uma conta de banco de dados](documentdb-create-account.md). Se você já tiver uma conta, vá para a etapa 2.
2. Usando a lâmina **Chaves** mostrada na ilustração a seguir, copie o seu ponto de extremidade **URI** e **CHAVE PRIMÁRIA** para a sua área de transferência e as mantenha à mão, pois usaremos esses valores no aplicativo Web que criaremos em seguida.

![Captura de tela do Portal do Azure mostrando uma conta do Banco de Dados de Documentos com o hub ATIVO realçado, o botão Chaves realçado na folha da conta do Banco de Dados de Documentos e os valores de URI, de CHAVE PRIMÁRIA e de CHAVE SECUNDÁRIA realçados na folha Chaves][1]


##<a id="CreateJSP"></a>Etapa 2: Criar o aplicativo JSP ##

Para criar o aplicativo JSP:

1. Primeiro, começaremos criando um projeto Java. Inicie o Eclipse, clique em **Arquivo**, **Novo** e, em seguida, clique em **Projeto Web dinâmico**. Se você não vir o **Projeto Web Dinâmico** listado como um projeto disponível, faça o seguinte: clique em **Arquivo** \> **Novo** \> **Projeto**..., expanda **Web**, clique em **Projeto Web Dinâmico** e clique em **Avançar**. 

	![](./media/documentdb-java-application/image10.png)

2. Digite um nome de projeto na caixa **Nome do projeto** e no menu suspenso **Tempo de execução de destino**, selecione, opcionalmente, um valor \(por exemplo, Apache Tomcat v 7.0\) e, em seguida, clique em **Concluir**. Selecione um tempo de execução de destino que permite que você execute seu projeto localmente por meio do Eclipse.
3. No Eclipse, na exibição do Explorador de Projeto, expanda o seu projeto. Clique com o botão direito do mouse em **WebContent**, clique em **Novo** e, em seguida, clique em **Arquivo JSP**.
4. Na caixa de diálogo **Novo arquivo JSP**, nomeie o arquivo **index.jsp**. Mantenha a pasta pai como **WebContent**, conforme mostrado na ilustração a seguir e clique em **Próximo**.

	![](./media/documentdb-java-application/image11.png)

5. Na caixa de diálogo **Selecionar modelo JSP**, selecione esse tutorial **Novo arquivo JSP \(html\)** e, em seguida, clique em **Concluir**.

6. Quando o arquivo index.jsp for aberto no Eclipse, adicione o texto para exibir **Hello World!** como elemento <body> existente. A atualização <body> do conteúdo deve se parecer com o código a seguir:
    
	    <body>
	        <% out.println("Hello World!"); %>
	    </body>

8. Salve o arquivo index.jsp.
9. Se definir um tempo de execução de destino na etapa 2, você pode clicar no **Projeto** e em **Executar** para executar o aplicativo JSP localmente:

	![](./media/documentdb-java-application/image12.png)

##<a id="InstallSDK"></a>Etapa 3: Instalar o SDK do Java do Banco de Dados de Documentos ##

É a maneira mais fácil de obter o SDK do Java do Banco de Dados de Documentos e suas dependências por meio do [Apache Maven](http://maven.apache.org/).

Para fazer isso, você precisará converter o projeto para um projeto Maven concluindo as etapas a seguir:

1. Clique em seu projeto no Explorador de projeto, clique em **Configurar** e em **Converter em projeto Maven**.
2. Na janela **Criar novo POM**, aceite os padrões e clique em **Concluir**.
3. No **Explorador de projeto**, abra o arquivo pom.xml. 
4. Na guia **Dependências**, no painel **Dependências**, clique em **Adicionar**.
4. Na janela **Selecionar dependência**, faça o seguinte:
 - Na caixa **GroupId**, insira com.microsoft.azure.
 - Na caixa **Id de artefato**, insira azure-documentdb.
 - Na caixa **Versão**, insira 1.0.0.

	![](./media/documentdb-java-application/image13.png)

	Ou adicione a dependência de XML para o GroupId e ArtifactId diretamente no pom.xml por meio de um editor de texto:

	    <dependency>
		    <groupId>com.microsoft.azure</groupId>
		    <artifactId>azure-documentdb</artifactId>
		    <version>1.0.0</version>
	    </dependency>

5. Clique em **Ok** e o Maven instalará o SDK do Java do Banco de Dados de Documentos.
6. Salve o arquivo pom.xml.

##<a id="UseService"></a>Etapa 4: Usando o serviço de Banco de Dados de Documentos em um aplicativo Java ##

1. Primeiro, vamos definir o objeto TodoItem:

	    @Data
	    @Builder
	    public class TodoItem {
		    private String category;
		    private boolean complete;
		    private String id;
		    private String name;
	    }

	Neste projeto, estamos usando [Project Lombok](http://projectlombok.org/) para gerar o construtor, os getters, os setters e um builder. Como alternativa, você pode escrever esse código manualmente ou o IDE pode gerá-lo.

2. Para invocar o serviço do Banco de Dados de Documentos, você deve criar um novo **DocumentClient**. Em geral, é melhor reutilizar o **DocumentClient** - em vez de construir um novo cliente para cada solicitação subsequente. O cliente pode ser reutilizado envolvendo o cliente em uma **DocumentClientFactory**. Aqui também é onde você precisa colar o valor da URI e a CHAVE PRIMÁRIA salva na área de transferência na [etapa 1](#CreateDB). Substitua [YOUR\_ENDPOINT\_HERE] pela sua URI e substitua [YOUR\_KEY\_HERE] pela sua CHAVE PRIMÁRIA.

	    private static final String HOST = "[YOUR_ENDPOINT_HERE]";
	    private static final String MASTER_KEY = "[YOUR_KEY_HERE]";
	
	    private static DocumentClient documentClient;
	
	    public static DocumentClient getDocumentClient() {
	        if (documentClient == null) {
	            documentClient = new DocumentClient(HOST, MASTER_KEY,
	                    ConnectionPolicy.GetDefault(), ConsistencyLevel.Session);
	        }
	
	        return documentClient;
	    }

3. Agora, vamos criar um objeto de acesso de dados \(DAO\) para abstrair mantendo os itens ToDo no Banco de Dados de Documentos.

	Para salvar os itens das tarefas em uma coleção, o cliente precisa saber qual banco de dados e coleção manter \(como referenciado por self-links\). Em geral, é melhor armazenar o banco de dados e a coleção em cache sempre que possível para evitar viagens adicionais ao banco de dados.

	O código a seguir ilustra como recuperar nosso Banco de dados e Coleção, se existir, ou criar um novo se ela não existir:

		public class DocDbDao implements TodoDao {
		    // The name of our database.
		    private static final String DATABASE_ID = "TodoDB";
		
		    // The name of our collection.
		    private static final String COLLECTION_ID = "TodoCollection";
		
		    // The DocumentDB Client
		    private static DocumentClient documentClient = DocumentClientFactory
		            .getDocumentClient();
		
		    // Cache for the database object, so we don't have to query for it to
		    // retrieve self links.
		    private static Database databaseCache;
		
		    // Cache for the collection object, so we don't have to query for it to
		    // retrieve self links.
		    private static DocumentCollection collectionCache;
		
		    private Database getTodoDatabase() {
		        if (databaseCache == null) {
		            // Get the database if it exists
		            List<Database> databaseList = documentClient
		                    .queryDatabases(
		                            "SELECT * FROM root r WHERE r.id='" + DATABASE_ID
		                                    + "'", null).getQueryIterable().toList();
		
		            if (databaseList.size() > 0) {
		                // Cache the database object so we won't have to query for it
		                // later to retrieve the selfLink.
		                databaseCache = databaseList.get(0);
		            } else {
		                // Create the database if it doesn't exist.
		                try {
		                    Database databaseDefinition = new Database();
		                    databaseDefinition.setId(DATABASE_ID);
		
		                    databaseCache = documentClient.createDatabase(
		                            databaseDefinition, null).getResource();
		                } catch (DocumentClientException e) {
		                    // TODO: Something has gone terribly wrong - the app wasn't
		                    // able to query or create the collection.
		                    // Verify your connection, endpoint, and key.
		                    e.printStackTrace();
		                }
		            }
		        }
		
		        return databaseCache;
		    }
		
		    private DocumentCollection getTodoCollection() {
		        if (collectionCache == null) {
		            // Get the collection if it exists.
		            List<DocumentCollection> collectionList = documentClient
		                    .queryCollections(
		                            getTodoDatabase().getSelfLink(),
		                            "SELECT * FROM root r WHERE r.id='" + COLLECTION_ID
		                                    + "'", null).getQueryIterable().toList();
		
		            if (collectionList.size() > 0) {
		                // Cache the collection object so we won't have to query for it
		                // later to retrieve the selfLink.
		                collectionCache = collectionList.get(0);
		            } else {
		                // Create the collection if it doesn't exist.
		                try {
		                    DocumentCollection collectionDefinition = new DocumentCollection();
		                    collectionDefinition.setId(COLLECTION_ID);

		                    // Configure the new collection performance tier to S1.
		                    RequestOptions requestOptions = new RequestOptions();
		                    requestOptions.setOfferType("S1");
		
		                    collectionCache = documentClient.createCollection(
		                            getTodoDatabase().getSelfLink(),
		                            collectionDefinition, requestOptions).getResource();
		                } catch (DocumentClientException e) {
		                    // TODO: Something has gone terribly wrong - the app wasn't
		                    // able to query or create the collection.
		                    // Verify your connection, endpoint, and key.
		                    e.printStackTrace();
		                }
		            }
		        }
		
		        return collectionCache;
		    }
		}

4. A próxima etapa é escrever algum código para manter as TodoItems na coleção. Neste exemplo, usaremos [Gson](https://code.google.com/p/google-gson/) para serializar e desserializar TodoItem Plain Old Java Objects \(POJOs\) para documentos JSON. [Jackson](http://jackson.codehaus.org/) ou seu próprio serializador personalizado são também excelentes alternativas para serializar POJOs.

	    // We'll use Gson for POJO <=> JSON serialization for this example.
	    private static Gson gson = new Gson();

	    @Override
	    public TodoItem createTodoItem(TodoItem todoItem) {
	        // Serialize the TodoItem as a JSON Document.
	        Document todoItemDocument = new Document(gson.toJson(todoItem));
	
	        // Annotate the document as a TodoItem for retrieval (so that we can
	        // store multiple entity types in the collection).
	        todoItemDocument.set("entityType", "todoItem");
	
	        try {
	            // Persist the document using the DocumentClient.
	            todoItemDocument = documentClient.createDocument(
	                    getTodoCollection().getSelfLink(), todoItemDocument, null,
	                    false).getResource();
	        } catch (DocumentClientException e) {
	            e.printStackTrace();
	            return null;
	        }
	
	        return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
	    }



5. Como os bancos de dados e coleções do Bancos de Dados de Documentos, também são referenciados por self-links. A função de auxiliar a seguir nos permite recuperar documentos por outro atributo \(por exemplo, "id"\) em vez de self-links:

	    private Document getDocumentById(String id) {
	        // Retrieve the document using the DocumentClient.
	        List<Document> documentList = documentClient
	                .queryDocuments(getTodoCollection().getSelfLink(),
	                        "SELECT * FROM root r WHERE r.id='" + id + "'", null)
	                .getQueryIterable().toList();
	
	        if (documentList.size() > 0) {
	            return documentList.get(0);
	        } else {
	            return null;
	        }
	    }

6. Podemos usar o método auxiliar na etapa 5 para recuperar um documento TodoItem JSON pela ID e, em seguida, desserializá-lo para um POJO:

	    @Override
	    public TodoItem readTodoItem(String id) {
	        // Retrieve the document by id using our helper method.
	        Document todoItemDocument = getDocumentById(id);
	
	        if (todoItemDocument != null) {
	            // De-serialize the document in to a TodoItem.
	            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
	        } else {
	            return null;
	        }
	    }

7. Também podemos usar o DocumentClient para obter uma coleção ou uma lista de TodoItems usando o SQL do Banco de Dados de Documentos:
	
	    @Override
	    public List<TodoItem> readTodoItems() {
	        List<TodoItem> todoItems = new ArrayList<TodoItem>();
	
	        // Retrieve the TodoItem documents
	        List<Document> documentList = documentClient
	                .queryDocuments(getTodoCollection().getSelfLink(),
	                        "SELECT * FROM root r WHERE r.entityType = 'todoItem'",
	                        null).getQueryIterable().toList();
	
	        // De-serialize the documents in to TodoItems.
	        for (Document todoItemDocument : documentList) {
	            todoItems.add(gson.fromJson(todoItemDocument.toString(),
	                    TodoItem.class));
	        }
	
	        return todoItems;
	    }

8. Há muitas maneiras de atualizar um documento com o DocumentClient. Em nosso aplicativo de lista Todo, queremos poder ativar ou desativar um TodoItem ele for concluído. Isso pode ser feito atualizando o atributo "concluído" dentro do documento:
	
	    @Override
	    public TodoItem updateTodoItem(String id, boolean isComplete) {
	        // Retrieve the document from the database
	        Document todoItemDocument = getDocumentById(id);
	
	        // You can update the document as a JSON document directly.
	        // For more complex operations - you could de-serialize the document in
	        // to a POJO, update the POJO, and then re-serialize the POJO back in to
	        // a document.
	        todoItemDocument.set("complete", isComplete);
	
	        try {
	            // Persist/replace the updated document.
	            todoItemDocument = documentClient.replaceDocument(todoItemDocument,
	                    null).getResource();
	        } catch (DocumentClientException e) {
	            e.printStackTrace();
	            return null;
	        }
	
	        return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
	    }

9. Por fim, queremos ter a capacidade de excluir um TodoItem de nossa lista. Para fazer isso, podemos usar o método auxiliar que escrevemos antes para recuperar self links e depois dizer ao cliente para excluí-lo:
	
	    @Override
	    public boolean deleteTodoItem(String id) {
	        // DocumentDB refers to documents by self link rather than id.
	
	        // Query for the document to retrieve the self link.
	        Document todoItemDocument = getDocumentById(id);
	
	        try {
	            // Delete the document by self link.
	            documentClient.deleteDocument(todoItemDocument.getSelfLink(), null);
	        } catch (DocumentClientException e) {
	            e.printStackTrace();
	            return false;
	        }
	
	        return true;
	    }


##<a id="Wire"></a> Etapa 5: Conectando o restante do aplicativo ##

Agora que concluímos a parte divertida - tudo que restou é criar uma interface de usuário rápida e conectá-la ao nosso DAO.

1. Primeiro, vamos começar com a criação de um Controlador para chamar nosso DAO:

		public class TodoItemController {
		    public static TodoItemController getInstance() {
		        if (todoItemController == null) {
		            todoItemController = new TodoItemController(TodoDaoFactory.getDao());
		        }
		        return todoItemController;
		    }
		
		    private static TodoItemController todoItemController;
		
		    private final TodoDao todoDao;
		
		    TodoItemController(TodoDao todoDao) {
		        this.todoDao = todoDao;
		    }
		
		    public TodoItem createTodoItem(@NonNull String name,
		            @NonNull String category, boolean isComplete) {
		        TodoItem todoItem = TodoItem.builder().name(name).category(category)
		                .complete(isComplete).build();
		        return todoDao.createTodoItem(todoItem);
		    }
		
		    public boolean deleteTodoItem(@NonNull String id) {
		        return todoDao.deleteTodoItem(id);
		    }
		
		    public TodoItem getTodoItemById(@NonNull String id) {
		        return todoDao.readTodoItem(id);
		    }
		
		    public List<TodoItem> getTodoItems() {
		        return todoDao.readTodoItems();
		    }
		
		    public TodoItem updateTodoItem(@NonNull String id, boolean isComplete) {
		        return todoDao.updateTodoItem(id, isComplete);
		    }
		}

	Em um aplicativo mais complexo, o Controlador pode ter uma lógica comercial complicada na parte superior do DAO.

2. Em seguida, criaremos um Servlet para rotear solicitações HTTP para o Controlador:

		public class TodoServlet extends HttpServlet {
			// API Keys
			public static final String API_METHOD = "method";
		
			// API Methods
			public static final String CREATE_TODO_ITEM = "createTodoItem";
			public static final String GET_TODO_ITEMS = "getTodoItems";
			public static final String UPDATE_TODO_ITEM = "updateTodoItem";
		
			// API Parameters
			public static final String TODO_ITEM_ID = "todoItemId";
			public static final String TODO_ITEM_NAME = "todoItemName";
			public static final String TODO_ITEM_CATEGORY = "todoItemCategory";
			public static final String TODO_ITEM_COMPLETE = "todoItemComplete";
		
			public static final String MESSAGE_ERROR_INVALID_METHOD = "{'error': 'Invalid method'}";
		
			private static final long serialVersionUID = 1L;
			private static final Gson gson = new Gson();
		
			@Override
			protected void doGet(HttpServletRequest request,
					HttpServletResponse response) throws ServletException, IOException {
		
				String apiResponse = MESSAGE_ERROR_INVALID_METHOD;
		
				TodoItemController todoItemController = TodoItemController
						.getInstance();
		
				String id = request.getParameter(TODO_ITEM_ID);
				String name = request.getParameter(TODO_ITEM_NAME);
				String category = request.getParameter(TODO_ITEM_CATEGORY);
				boolean isComplete = StringUtils.equalsIgnoreCase("true",
						request.getParameter(TODO_ITEM_COMPLETE)) ? true : false;
		
				switch (request.getParameter(API_METHOD)) {
				case CREATE_TODO_ITEM:
					apiResponse = gson.toJson(todoItemController.createTodoItem(name,
							category, isComplete));
					break;
				case GET_TODO_ITEMS:
					apiResponse = gson.toJson(todoItemController.getTodoItems());
					break;
				case UPDATE_TODO_ITEM:
					apiResponse = gson.toJson(todoItemController.updateTodoItem(id,
							isComplete));
					break;
				default:
					break;
				}
		
				response.getWriter().println(apiResponse);
			}
	
			@Override
			protected void doPost(HttpServletRequest request,
					HttpServletResponse response) throws ServletException, IOException {
				doGet(request, response);
			}
		}

3. Precisaremos de uma Interface de usuário da Web para a exibição ao usuário. Vamos reescrever o ndex.jsp criado anteriormente:

		<html>
		<head>
		  <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
          <meta http-equiv="X-UA-Compatible" content="IE=edge;" />
		  <title>Azure DocumentDB Java Sample</title>
		
		  <!-- Bootstrap -->
		  <link href="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">
		
		  <style>
		    /* Add padding to body for fixed nav bar */
		    body {
		      padding-top: 50px;
		    }
		  </style>
		</head>
		<body>
		  <!-- Nav Bar -->
		  <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
		    <div class="container">
		      <div class="navbar-header">
		        <a class="navbar-brand" href="#">My Tasks</a>
		      </div>
		    </div>
		  </div>
		
		  <!-- Body -->
		  <div class="container">
		    <h1>My ToDo List</h1>
		
		    <hr/>
		
		    <!-- The ToDo List -->
		    <div class = "todoList">
		      <table class="table table-bordered table-striped" id="todoItems">
		        <thead>
		          <tr>
		            <th>Name</th>
		            <th>Category</th>
		            <th>Complete</th>
		          </tr>
		        </thead>
		        <tbody>
		        </tbody>
		      </table>
		
		      <!-- Update Button -->
		      <div class="todoUpdatePanel">
		        <form class="form-horizontal" role="form">
		          <button type="button" class="btn btn-primary">Update Tasks</button>
		        </form>
		      </div>
		
		    </div>
		
		    <hr/>
		
		    <!-- Item Input Form -->
		    <div class="todoForm">
		      <form class="form-horizontal" role="form">
		        <div class="form-group">
		          <label for="inputItemName" class="col-sm-2">Task Name</label>
		          <div class="col-sm-10">
		            <input type="text" class="form-control" id="inputItemName" placeholder="Enter name">
		          </div>
		        </div>
		
		        <div class="form-group">
		          <label for="inputItemCategory" class="col-sm-2">Task Category</label>
		          <div class="col-sm-10">
		            <input type="text" class="form-control" id="inputItemCategory" placeholder="Enter category">
		          </div>
		        </div>
		
		        <button type="button" class="btn btn-primary">Add Task</button>
		      </form>
		    </div>
		
		  </div>
		
		  <!-- Placed at the end of the document so the pages load faster -->
		  <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.js"></script>
		  <script src="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/bootstrap.min.js"></script>
		  <script src="assets/todo.js"></script>
		</body>
		</html>

4. E, finalmente, escrever um Javascript do lado do cliente para unir o servlet e a interface do usuário da web:
	
		var todoApp = {
		  /*
		   * API methods to call Java backend.
		   */
		  apiEndpoint: "api",
		
		  createTodoItem: function(name, category, isComplete) {
		    $.post(todoApp.apiEndpoint, {
		        "method": "createTodoItem",
		        "todoItemName": name,
		        "todoItemCategory": category,
		        "todoItemComplete": isComplete
		      },
		      function(data) {
		        var todoItem = data;
		        todoApp.addTodoItemToTable(todoItem.id, todoItem.name, todoItem.category, todoItem.complete);
		      },
		      "json");
		  },
		
		  getTodoItems: function() {
		    $.post(todoApp.apiEndpoint, {
		        "method": "getTodoItems"
		      },
		      function(data) {
		        var todoItemArr = data;
		        $.each(todoItemArr, function(index, value) {
		          todoApp.addTodoItemToTable(value.id, value.name, value.category, value.complete);
		        });
		      },
		      "json");
		  },
		
		  updateTodoItem: function(id, isComplete) {
		    $.post(todoApp.apiEndpoint, {
		        "method": "updateTodoItem",
		        "todoItemId": id,
		        "todoItemComplete": isComplete
		      },
		      function(data) {},
		      "json");
		  },
		
		  /*
		   * UI Methods
		   */
		  addTodoItemToTable: function(id, name, category, isComplete) {
		    var rowColor = isComplete ? "active" : "warning";
		
		    todoApp.ui_table().append($("<tr>")
		      .append($("<td>").text(name))
		      .append($("<td>").text(category))
		      .append($("<td>")
		        .append($("<input>")
		          .attr("type", "checkbox")
		          .attr("id", id)
		          .attr("checked", isComplete)
		          .attr("class", "isComplete")
		        ))
		      .addClass(rowColor)
		    );
		  },
		
		  /*
		   * UI Bindings
		   */
		  bindCreateButton: function() {
		    todoApp.ui_createButton().click(function() {
		      todoApp.createTodoItem(todoApp.ui_createNameInput().val(), todoApp.ui_createCategoryInput().val(), false);
		      todoApp.ui_createNameInput().val("");
		      todoApp.ui_createCategoryInput().val("");
		    });
		  },
		
		  bindUpdateButton: function() {
		    todoApp.ui_updateButton().click(function() {
		      // Disable button temporarily.
		      var myButton = $(this);
		      var originalText = myButton.text();
		      $(this).text("Updating...");
		      $(this).prop("disabled", true);
		
		      // Call api to update todo items.
		      $.each(todoApp.ui_updateId(), function(index, value) {
		        todoApp.updateTodoItem(value.name, value.value);
		        $(value).remove();
		      });
		
		      // Re-enable button.
		      setTimeout(function() {
		        myButton.prop("disabled", false);
		        myButton.text(originalText);
		      }, 500);
		    });
		  },
		
		  bindUpdateCheckboxes: function() {
		    todoApp.ui_table().on("click", ".isComplete", function(event) {
		      var checkboxElement = $(event.currentTarget);
		      var rowElement = $(event.currentTarget).parents('tr');
		      var id = checkboxElement.attr('id');
		      var isComplete = checkboxElement.is(':checked');
		
		      // Togle table row color
		      if (isComplete) {
		        rowElement.addClass("active");
		        rowElement.removeClass("warning");
		      } else {
		        rowElement.removeClass("active");
		        rowElement.addClass("warning");
		      }
		
		      // Update hidden inputs for update panel.
		      todoApp.ui_updateForm().children("input[name='" + id + "']").remove();
		
		      todoApp.ui_updateForm().append($("<input>")
		        .attr("type", "hidden")
		        .attr("class", "updateComplete")
		        .attr("name", id)
		        .attr("value", isComplete));
		
		    });
		  },
		
		  /*
		   * UI Elements
		   */
		  ui_createNameInput: function() {
		    return $(".todoForm #inputItemName");
		  },
		
		  ui_createCategoryInput: function() {
		    return $(".todoForm #inputItemCategory");
		  },
		
		  ui_createButton: function() {
		    return $(".todoForm button");
		  },
		
		  ui_table: function() {
		    return $(".todoList table tbody");
		  },
		
		  ui_updateButton: function() {
		    return $(".todoUpdatePanel button");
		  },
		
		  ui_updateForm: function() {
		    return $(".todoUpdatePanel form");
		  },
		
		  ui_updateId: function() {
		    return $(".todoUpdatePanel .updateComplete");
		  },
		
		  /*
		   * Install the TodoApp
		   */
		  install: function() {
		    todoApp.bindCreateButton();
		    todoApp.bindUpdateButton();
		    todoApp.bindUpdateCheckboxes();
		
		    todoApp.getTodoItems();
		  }
		};
		
		$(document).ready(function() {
		  todoApp.install();
		});

5. Incrível! Agora tudo o que resta é testar o aplicativo. Executar o aplicativo localmente e adicionar alguns itens de tarefas, preenchendo o nome do item e a categoria e clicando em **Adicionar tarefa**.

6. Quando o item for exibido, você poderá atualizar se concluído, alternar a caixa de seleção e clicar em **Atualizar tarefas**.

##<a id="Deploy"></a>Etapa 6: Implantar seu aplicativo em sites do Azure ##

Sites do Azure tornam a implantação de aplicativos Java tão simples quanto a exportação de seu aplicativo como um arquivo WAR e por carregamento ou por meio do controle de origem \(por exemplo, GIT\) ou FTP.

1. Para exportar seu aplicativo como um WAR, clique com o botão direito em seu projeto no **Explorador de projeto**, clique em **Exportar** e, em seguida, clique em **Arquivo WAR**. 
2. Na janela **Exportar WAR**, faça o seguinte:
 - Na caixa de projeto da Web, insira azure-documentdb-java-sample.
 - Na caixa Destino, escolha um destino para salvar o arquivo WAR.
 - Clique em **Concluir**.

3. Agora que tem um arquivo WAR em mãos, você pode simplesmente carregá-lo no seu diretório **webapps** do site do Azure. Para obter instruções sobre como carregar o arquivo, consulte [Adicionar um aplicativo ao seu site Java no Azure](web-sites-java-add-app.md).

	Uma vez carregado o arquivo WAR na pasta webapps, o ambiente de tempo de execução irá detectar que você o adicionou e o carregará automaticamente.
4. Para exibir seu produto acabado, navegue até http://YOUR\_SITE\_NAME.azurewebsites.net/azure-documentdb-java-sample/ e comece a adicionar suas tarefas!

##<a id="GetProject"></a>Obtenha o projeto do GitHub##

Todos os exemplos neste tutorial são incluídos no projeto [tarefas](https://github.com/Azure/azure-documentdb-java/tree/master/tutorial/todo) no GitHub, que faz parte do repositório [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java). Para importar o projeto de tarefas no Eclipse, certifique-se de ter o software e os recursos listados na seção [pré-requisitos](#Prerequisites) e, em seguida, faça o seguinte:

1. Instalar [Project Lombok](http://projectlombok.org/). Lombok é usado para gerar construtores, getters e setters no projeto. Depois que você baixou o arquivo lombok.jar, clique duas vezes nele para instalá-lo ou instalá-lo a partir da linha de comando. 
2. Se o Eclipse estiver aberto, feche-o e reinicie-o para carregar o Lombok.
3. No Eclipse, no menu **Arquivo**, clique em **Importar**.
4. Na janela **Importar**, clique em **Git**, **Projetos do Git** e, em seguida, clique em **Próximo**. 
5. Na tela **Selecionar origem de repositório**, clique em **Clonar URI**.
6. Na tela **Repositório da origem Git**, na caixa **URI**, insira https://github.com/Azure/azure-documentdb-java.git e, em seguida, clique em **Próximo**.
7. Na tela **Seleção de ramificação**, verifique se **mestre** está selecionado e, em seguida, clique em **Próximo**.
8. Na tela **Destino Local**, clique em **Procurar** para selecionar uma pasta onde o repositório possa ser copiado e, em seguida, clique em **Próximo**.
9. Na tela **Selecionar um assistente a ser usado para importar projetos**, verifique se **Importar projetos existentes** está selecionado e, em seguida, clique em **Próximo**.
10. Na tela **Importar projetos**, desmarque o projeto **Banco de Dados de Documentos** e, em seguida, clique em **Concluir**. O projeto Banco de Dados de Documentos contém o SDK Java do Banco de Dados de Documentos, que adicionaremos como uma dependência em seu lugar.
11. No **Explorador de Projeto**, navegue até azure-documentdb-java-sample\\src\\com.microsoft.azure.documentdb.sample.dao\\DocumentClientFactory.java e substitua os valores HOST e MASTER\_KEY pela URI e a CHAVE PRIMÁRIA para a sua conta do Banco de Dados de Documentos e, em seguida, salve o arquivo. Para obter mais informações, consulte a [Etapa 1. Criar uma conta de banco de dados do Banco de Dados de Documentos](#CreateDB).
12. Em **Explorador de Projeto**, clique com o botão direito do mouse em **azure-documentdb-java-sample**, clique em **Caminho de compilação** e, em seguida, clique em **Configurar caminho de compilação**.
13. Na tela **Caminho de compilação Java**, no painel direito, selecione a guia **Bibliotecas** e, em seguida, clique em **Adicionar JARs Externos**. Navegue até o local do arquivo lombok.jar e clique em **Abrir** e, em seguida, clique em **OK**.
14. Use a Etapa 12 para abrir a janela **Propriedades** novamente e, no painel esquerdo, clique em **Tempos de Execução Direcionados**.
15. Na tela **Tempos de Execução Direcionados**, clique em **Novo**, selecione **Apache Tomcat v7.0** e, em seguida, clique em **OK**.
16. Use a Etapa 12 para abrir a janela **Propriedades** novamente e, no painel esquerdo, clique em **Facetas do Projeto**.
17. Na tela **Facetas do projeto**, selecione **Módulo da Web Dinâmico** e **Java** e, em seguida, clique em **OK**.
18. Na guia **Servidores** na parte inferior da tela, clique com o botão direito em **Tomcat v7.0 Server at localhost** e, em seguida, clique em **Adicionar e remover**.
19. Na janela **Adicionar e Remover**, mova **azure-documentdb-java-sample** para a caixa **Configurado** e, em seguida, clique em **Concluir**. 
20. Na guia **Servidor**, clique com o botão direito do mouse em **Tomcat v7.0 Server at localhost** e, em seguida, clique em **Reiniciar**.
21. Em um navegador, navegue até http://localhost:8080/azure-documentdb-java-sample/ e comece a adicionar à sua lista de tarefas. Observe que, se você alterou os valores da porta padrão, altere a 8080 para o valor selecionado.
22. Para implantar o projeto em um site do Azure, consulte a [Etapa 6. Implante o aplicativo nos Sites do Azures](#Deploy). 

[1]: ../includes/media/documentdb-keys/keys.png

<!--HONumber=52-->
