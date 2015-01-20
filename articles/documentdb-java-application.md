<properties title="Build a Java web application using DocumentDB" pageTitle="Criar um aplicativo Web Java usando o Banco de Dados de Documentos" description="Este tutorial mostra a você como usar o serviço do Banco de Dados de Documentos do Azure para armazenar e acessar dados de um aplicativo Java hospedado nos Sites do Azure." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, accounts" services="documentdb" solutions="data-management" documentationCenter=""  authors="andrl" manager="monicar" editor="monicar" videoId="" scriptId="" />

# Criar um aplicativo Web Java usando o Banco de Dados de Documentos #

Este tutorial mostra a você como usar o serviço do Banco de Dados de Documentos do Azure para armazenar e acessar dados de um aplicativo Java hospedado nos Sites do Azure. Neste tópico, você aprenderá:

- Como criar um aplicativo básico do JSP no Eclipse
- Como com o serviço do Banco de Dados de Documentos do Azure usando o Java do Banco de Dados de Documentos

Neste tutorial, você criará um aplicativo simples de gerenciamento de tarefas baseado na web que permite criar, recuperar e concluir tarefas. As tarefas serão armazenadas como documentos JSON no Banco de Dados de Documentos do Azure.


![My ToDo List application](./media/documentdb-java-application/image1.png)

> [WACOM.NOTE] Este tutorial supõe que você tenha alguma experiência prévia com o uso do Java.

## Pré-requisitos ##
Antes de seguir as instruções deste artigo, verifique se você possui o seguinte:

- [Java Development Kit (JDK 7 +)](http://www.oracle.com/technetwork/java/index.html)
- [Eclipse IDE para desenvolvedores de Java EE](https://www.eclipse.org/)
- [Um site do Azure com um Java runtime environment (por exemplo, Tomcat ou Jetty) habilitado.](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-java-get-started/)

## Etapa 1: Criar uma conta de banco de dados do Banco de Dados de Documentos ##
Para provisionar uma conta de banco de dados do Banco de Dados de Documentos no Azure:


1. Navegue até [Portal de Gerenciamento do Azure](http://portal.azure.com/) e clique no bloco da **Galeria do Azure** na home page ou clique em **+** no canto inferior esquerdo da tela. Isso abrirá a Galeria do Azure, onde você pode selecionar vários serviços disponíveis do Azure.

	![](./media/documentdb-java-application/image2.png)


2. Na **Galeria**, selecione **Dados, armazenamento e backup** na lista de categorias.

	![](./media/documentdb-java-application/image3.png)

3. Na lâmina **Dados, armazenamento e backup**, selecione **Banco de Dados de Documentos do Azure**.

	![](./media/documentdb-java-application/image4.png)

4. Na lâmina **Conta do Banco de Dados de Documentos**, selecione **Criar** na parte inferior da tela

	![](./media/documentdb-java-application/image5.png)

5. Isso abrirá a lâmina **Novo Banco de Dados de Documentos**, onde você pode especificar o nome, a região, a escala, o grupo de recursos e outras configurações para sua nova conta.

	![](./media/documentdb-java-application/image6.png)

6. Depois de concluir a inserção de valores na conta, clique em **Criar** e o processo de provisionamento começará a criar sua conta de banco de dados. Quando o provisionamento estiver concluído, você verá uma notificação exibida na área de **notificações** do portal, e o bloco na sua tela inicial (se você tiver selecionado para criar uma tela) mudará para mostrar a ação concluída.

	![](./media/documentdb-java-application/image7.png)

7. Depois de concluir o provisionamento, clique no bloco do **Banco de Dados de Documentos** na**tela inicia**l para exibir a lâmina principal dessa conta de Banco de Dados de Documentos recém-criada.

	![](./media/documentdb-java-application/image8.png)
	![](./media/documentdb-java-application/image9.png)

8. Usando o botão **Chaves**, acesse a URL do ponto de extremidade e a Chave Primária, copie-as para sua área de transferência e mantenha-as por perto, pois usaremos esses valores no aplicativo Web que criaremos em seguida.


## Etapa 2: Criar um aplicativo JSP ##

Para criar o aplicativo JSP: 

1. Primeiro, vamos começar com a criação de um projeto Java. Inicie o Eclipse. No Eclipse, no menu, clique em **Arquivo**, clique em **Novo** e, em seguida, clique em **Projeto Web Dinâmico**. (Se você não vir o **Projeto Web Dinâmico** listado como um projeto, faça o seguinte: clique em **Arquivo**, clique em **Novo**, clique em **Projeto**..., expanda **Web**, clique em **Projeto Web Dinâmico** e clique em **Avançar**.) Você verá a caixa de diálogo a seguir: 

	![](./media/documentdb-java-application/image10.png)

2. Clique em **Concluir**. Selecionar um destino em tempo de execução (por exemplo, Apache Tomcat v 7.0) permitirá que você execute seu projeto localmente por meio do Eclipse.

3. Na exibição do Explorador de projeto do Eclipse, expanda seu projeto. Clique com o botão direito do mouse em **WebContent**, clique em **Novo** e, em seguida, clique em **Arquivo JSP**.
4. Na caixa de diálogo **Novo Arquivo JSP**, nomeie o arquivo **index.jsp**. Mantenha a pasta pai como **WebContent**, conforme mostrado a seguir:

	![](./media/documentdb-java-application/image11.png)

5. Clique em **Próximo**.

6. Na caixa de diálogo **Selecionar Modelo JSP**, por causa deste tutorial selecione **Novo Arquivo JSP (html)** e clique em **Concluir**.

7. Quando o arquivo index.jsp for aberto no Eclipse, adicione o texto para exibir **Hello World!** como elemento <body> existente. Seu conteúdo <body> atualizado deve ser semelhante ao seguinte:
    
    <body>
      <b><% out.println("Hello World!"); %></b>
    </body>

8. Salve o index.jsp. 
9. Se você tiver definido anteriormente um tempo de execução de destino, você pode clicar em **Projeto** e em **Executar** para executar o aplicativo JSP localmente:

	![](./media/documentdb-java-application/image12.png)

## Etapa 3: Instalar o SDK do Java do Banco de Dados de Documentos ##

É a maneira mais fácil para obter o SDK do Java do Banco de Dados de Documentos e suas dependências por meio do maven.

Para fazer isso, você precisará converter o projeto em um projeto maven: 

1. Clique o botão direito do mouse no seu projeto no Explorador de Projeto, clique em **Configurar**, clique em **Converter em Projeto Maven**.
2. A caixa de diálogo solicitará que você preencha um groupId e artifactId para seu projeto.
3. Uma vez gerado o pom.xml - abra o arquivo **pom.xml** no Eclipse, clique na guia **Dependências** e, em seguida, clique em **Adicionar** no painel de dependências.
4. Preencha o GroupId e ArtifactId, conforme mostrado no painel:

	![](./media/documentdb-java-application/image13.png)

	Ou adicione a dependência de XML para o GroupId e ArtifactId diretamente no pom.xml por meio de um editor de texto:

	    <dependency>
		    <groupId>com.microsoft.azure</groupId>
		    <artifactId>azure-documentdb</artifactId>
		    <version>0.9.0</version>
	    </dependency>

5. Clique em **Ok** e o Maven instalará o **SDK do Java do Banco de Dados de Documentos**.

## Etapa 4: Usando o serviço do Banco de Dados de Documento em um aplicativo Java ##

1. Primeiro, vamos definir o objeto TodoItem:

	    @Data
	    @Builder
	    public class TodoItem {
		    private String category;
		    private boolean complete;
		    private String id;
		    private String name;
	    }

	Neste exemplo, estamos usando [Lombok](http://projectlombok.org/) para gerar o construtor, getters, setters e um builder. Como alternativa, você pode escrever esse código manualmente ou o IDE pode gerá-lo.

2. Para invocar o serviço do Banco de Dados de Documentos, você deve criar um novo **DocumentClient**. Em geral, é melhor usar novamente o DocumentClient - em vez de construir um novo cliente para cada solicitação subsequente. Podemos usar novamente o cliente envolvendo-o em uma **DocumentClientFactory**:

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

3. Agora, vamos criar um objeto de acesso de dados (DAO) para abstrair mantendo os itens ToDo no Banco de Dados de Documentos.

	Para salvar itens de tarefas em uma coleção, o cliente precisará saber qual banco de dados e coleção para manter (como referenciado por self-links). Em geral, é melhor armazenar o banco de dados e a coleção em cache sempre que possível para evitar viagens adicionais ao banco de dados.

	O código a seguir ilustra como recuperar nosso Banco de dados e Coleção se existir, ou crie um novo se ela não existir:

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
		
		                    collectionCache = documentClient.createCollection(
		                            getTodoDatabase().getSelfLink(),
		                            collectionDefinition, null).getResource();
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

4. A próxima etapa é escrever algum código para manter as TodoItems na coleção. Neste exemplo, usaremos o [Gson](https://code.google.com/p/google-gson/) para serializar e desserializar POJOs TodoItem em documentos JSON. [Jackson](http://jackson.codehaus.org/) ou seu próprio serializador personalizado são também excelentes alternativas para serializar POJOs.

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



5. Como os bancos de dados e coleções do Bancos de Dados de Documentos, também são referenciados por self-links. A função de auxiliar a seguir nos permitirá recuperar documentos por outro atributo (por exemplo, "id") em vez de self-links:

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

6. Podemos usar o método auxiliar acima para recuperar um documento JSON TodoItem por id e desserializá-lo para um POJO: 

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


## Etapa 5: Gravando o restante do aplicativo junto ##

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

	Em um aplicativo mais complexo, o Cntrolador pode ter uma lógica comercial complicada na parte superior do DAO.

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

3. Precisaremos de uma Interface do usuário da Web para a superfície do usuário. Vamos reescrever o ndex.jsp criado anteriormente:

		<html>
		<head>
		  <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
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
		        value.remove();
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
		      var checkboxElement = $(event.toElement);
		      var rowElement = $(event.toElement).parents('tr');
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

5. Incrível! Agora tudo o que resta é testar o aplicativo. Execute o aplicativo localmente e adicione alguns itens de tarefas, preenchendo o nome do item e a categoria e clicando em **Adicionar Tarefa**

6. Quando o item for exibido, você poderá atualizar se concluído alternando a caixa de seleção e clicando em **Atualizar Tarefas**

## Etapa 6: Implantar o aplicativo de Sites do Azure ##

Sites do Azure tornam a implantação de aplicativos Java tão simples quanto a exportação de seu aplicativo como um arquivo WAR e por carregamento ou por meio do controle de origem (por exemplo, GIT) ou FTP.

1. Para exportar seu aplicativo como um WAR: Clique com o botão direito do mouse no explorador de projeto, expanda a opção de menu **Eportar** e clique em **Arquivo WAR**. Escolha um destino para salvar o arquivo WAR e clique em **Concluir**.

2. Agora que você tem um arquivo WAR em mãos, você pode simplesmente carregá-lo no seu diretório de **webapps** do site do Azure.

	Uma vez carregado o arquivo WAR na pasta webapps, o ambiente de tempo de execução irá detectar que você o adicionou e o carregará automaticamente.

<!--HONumber=35.2-->
