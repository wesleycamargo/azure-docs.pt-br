

1. Em seguida, remova os comentários ou adicione a linha de código a seguir e substitua `<yourClient>` pela variável adicionada ao arquivo service.js quando você conectou seu projeto ao serviço móvel:
   
        var todoTable = <yourClient>.getTable('TodoItem');
   
       Esse código criará um objeto proxy (**todoTable**) para a nova tabela de banco de dados, usando o filtro de cache.
2. Substitua a função **InsertTodoItem** pelo seguinte código:
   
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

<!---HONumber=Oct15_HO3-->