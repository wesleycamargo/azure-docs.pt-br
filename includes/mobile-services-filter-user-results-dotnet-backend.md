Agora que a autenticação é obrigatória para acessar dados na tabela TodoItem, é possível usar o valor userID atribuído por Serviços Móveis para filtrar dados retornados.

> [WACOM.NOTE]Os métodos embaixo devem ter o atributo **AuthorizeLevel** aplicado no **Authorizationlevel** ou **User**. Isso restringe o acesso de tabela apenas a usuários autenticados.

1.  No Visual Studio 2013, abra o projeto do serviço móvel, expanda a pasta DataObjects e abra o arquivo de projeto TodoItem.cs.

    A classe TodoItem define o objeto de dados, e você precisa adicionar uma propriedade UserId a ser usada na filtragem.

2.  Adicione as novas propriedades UserId a seguir à classe **TodoItem**:

        public string UserId { get; set; }

    > [WACOM.NOTE] Ao usar o inicializador de banco de dados padrão, o Entity Framework eliminará e recriará o banco de dados sempre que detectar uma mudança do modelo de dados na definição de modelo Code First. Para fazer com que esse modelo de dados altere e mantenha os dados existentes no banco de dados, você deve usar as Migrações Code First. O inicializador padrão não pode ser usado em um Banco de Dados SQL do Azure. Para obter mais informações, consulte [Como usar as Migrações Code First para atualizar o modelo de dados][Como usar as Migrações Code First para atualizar o modelo de dados].

3.  No Gerenciador de Soluções do Visual Studio, expanda a pasta Controllers, abra o arquivo do projeto TodoItemController.cs e adicione a seguinte instrução **using**:

        using Microsoft.WindowsAzure.Mobile.Service.Security;

    A classe **TodoItemController** implementa o acesso a dados da tabela TodoItem.

4.  Localize o método **PostTodoItem** e adicione o seguinte código no começo do método:

        // Get the logged-in user.
        var currentUser = User as ServiceUser;

        // Set the user ID on the item.
        item.UserId = currentUser.Id;

    Esse código adiciona um valor UserId para o item que é a ID do usuário autenticado, antes que ele seja inserido na tabela TodoItem.

5.  Localize o método **GetAllTodoItems** e substitua a instrução **return** existente pela seguinte linha de código:

        // Get the logged-in user.
        var currentUser = User as ServiceUser;

        return Query().Where(todo => todo.UserId == currentUser.Id);

    Essa consulta filtra os objetos de TodoItem retornados para que cada usuário receba apenas os itens inseridos por ele. Você também pode

6.  Republique o projeto de serviço móvel no Azure.

  [Como usar as Migrações Code First para atualizar o modelo de dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations
