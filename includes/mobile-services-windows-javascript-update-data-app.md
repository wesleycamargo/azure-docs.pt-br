

1. No arquivo de script default.js logo abaixo a linha do código que define a lista todoItems, adicione a seguinte definição de função:
 
        // Add a filter that adds a header to prevent caching. This makes sure that the 
		// latest data is returned when the 'Refresh; button is clicked.        
        var noCachingFilter = function (request, next, callback) {
            if (request.type === 'GET' && !request.headers['If-Modified-Since']) {
                request.headers['If-Modified-Since'] = 'Mon, 27 Mar 1972 00:00:00 GMT';
            }
            next(request, callback);
        };

	Isso define uma função de filtro, que adiciona o cabeçalho  `If-Modified-Since` para impedir o cache no cliente.
 
2. Em seguida, remova os comentários ou adicione a seguinte linha de código e substitua `<yourClient>` com a variável adicionada ao arquivo service.js quando você conecta seu projeto ao serviço móvel:

		var todoTable = <yourClient>.withFilter(noCachingFilter).getTable('TodoItem');

   	This code creates a proxy object (**todoTable**) for the new database table, using the caching filter. 

3. Replace the **InsertTodoItem** function with the following code:

		var insertTodoItem = function (todoItem) {
		    // Inserts a new row into the database. When the operation completes
		    // and Mobile Services has assigned an id, the item is added to the binding list.
		    todoTable.insert(todoItem).done(function (item) {
		        todoItems.push(item);
		    });
		};

	Esse código insere um novo item na tabela.

3. Substitua a função **RefreshTodoItems** pelo seguinte código:

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the table.
            // Results are filtered to remove completed items.
            todoTable.where({ complete: false })
                .read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

   	Isso define a associação à coleção de itens em todoTable, que contém todos os objetos **TodoItem** retornados do serviço móvel. 

4. Substitua a função **UpdateCheckedTodoItem** pelo seguinte código:
        
        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
            // Remove the completed item from the filtered list.
            todoItems.splice(todoItems.indexOf(todoItem), 1);
        };

   	Isso envia uma atualização de item para o serviço móvel.

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, é hora de testar o aplicativo com os Serviços Móveis.
<!--HONumber=42-->
