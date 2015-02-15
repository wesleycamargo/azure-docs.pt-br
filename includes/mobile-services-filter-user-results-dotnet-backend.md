

Agora que a autenticação é obrigatória para acessar dados na tabela TodoItem, é possível usar o valor userID atribuído por Serviços Móveis para filtrar dados retornados.

>[AZURE.NOTE]Os métodos abaixo devem ter o atributo **AuthorizeLevel** aplicado ao **Authorizationlevel** do **Usuário**. Isso restringe o acesso de tabela apenas a usuários autenticados.

1. No Visual Studio 2013, abra o projeto do serviço móvel, expanda a pasta DataObjects e abra o arquivo de projeto TodoItem.cs.

	A classe TodoItem define o objeto de dados, e você precisa adicionar uma propriedade UserId a ser usada na filtragem.

2. Adicione a nova propriedade UserId a seguir à classe **TodoItem**:

		public string UserId { get; set; }

	>[AZURE.NOTE] Ao usar o inicializador de banco de dados padrão, o Entity Framework vai remover e recriar o banco de dados sempre que detectar uma alteração no modelo de dados na definição do modelo Code First. Para fazer com que esse modelo de dados altere e mantenha os dados existentes no banco de dados, você deve usar as Migrações Code First. O inicializador padrão não pode ser usado em um Banco de Dados SQL do Azure. Para obter mais informações, consulte [Como usar as Migrações Code First para atualizar o modelo de dados](/pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations).

3. No Gerenciador de Soluções, expanda a pasta Controllers, abra o arquivo de projeto TodoItemController.cs e adicione a seguinte instrução **using**:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

	A classe **TodoItemController** implementa o acesso a dados da tabela TodoItem. 
 
4. Localize o método **PostTodoItem** e adicione o seguinte código no começo do método:

		// Get the logged-in user.
	    var currentUser = User as ServiceUser;
	
	    // Set the user ID on the item.
	    item.UserId = currentUser.Id;

    Esse código adiciona um valor UserId para o item que é a ID de usuário autenticado, antes que ele seja inserido na tabela TodoItem. 
	

5. Localize o método **GetAllTodoItems** e substitua a instrução **return** existente pela seguinte linha de código:

        // Get the logged-in user.
        var currentUser = User as ServiceUser;

        return Query().Where(todo => todo.UserId == currentUser.Id);

   	Essa consulta filtra os objetos de TodoItem retornados para que cada usuário receba apenas os itens inseridos por ele. 

6. Republique o projeto de serviço móvel no Azure.

<!--HONumber=42-->
