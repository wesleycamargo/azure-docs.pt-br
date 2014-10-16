<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### O que aconteceu?

###### Referências adicionadas

A biblioteca de Serviços Móveis do Microsoft Azure foi adicionada ao seu projeto na forma de um arquivo MobileServices.js.

###### Valores de cadeia de conexão para Serviços Móveis

Na pasta services\\mobileServices\\settings, foi gerado um novo arquivo JavaScript (.js) com um MobileServiceClient contendo a URL e a chave do aplicativo do serviço móvel selecionado.

### Introdução aos Serviços Móveis

###### Obtenha referência de uma tabela

O objeto do cliente já foi adicionado ao seu projeto. Seu nome é o nome do seu serviço móvel com "Client" anexado a ele. O código a seguir obtém uma referência de uma tabela que contém dados para um TodoItem, que pode ser usada em operações posteriores para ler e atualizar a tabela de dados.

    var todoTable = yourMobileServiceClient.getTable('TodoItem');

###### Adicionar entrada

Inserir um novo item em uma tabela de dados. Um id (um GUID da cadeia de caracteres do tipo) é criada automaticamente como a chave primária para a nova linha. Não altere o tipo da coluna do ID, pois ele é usado pela infraestrutura de serviços móveis.

    var todoTable = client.getTable('TodoItem');
    var todoItems = new WinJS.Binding.List();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            todoItems.push(item);
        });
    };

###### Ler/consultar tabela

O código a seguir consulta uma tabela para todos os itens, atualiza uma coleção local e vincula o resultado ao elemento da interface de usuário listItems.

        // This code refreshes the entries in the list view 
        // by querying the TodoItems table.
        todoTable.where()
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });

Você pode usar o onde o método deverá modificar a consulta. Veja um exemplo que filtra itens concluídos.

    todoTable.where(function () {
        return (this.complete === false && this.createdAt !== null);
    })
    .read()
    .done(function (results) {
        todoItems = new WinJS.Binding.List(results);
        listItems.winControl.itemDataSource = todoItems.dataSource;
    });

Para ver mais exemplos de consultas que podem ser usadas, consulte o [objeto de consulta](http://msdn.microsoft.com/library/azure/jj613353.aspx).

###### Atualizar entrada

Atualizar uma linha em uma tabela de dados. Neste exemplo, o todoItem é o item atualizado e o item é o mesmo que o retornado do serviço móvel. Quando o serviço móvel responde, o item é atualizado na lista todoItems local usando o método [splice](http://msdn.microsoft.com/library/windows/apps/Hh700810.aspx). Chame o método [done]() em um objeto [Promise]() retornado para obter uma cópia do objeto inserido e tratar quaisquer erros existentes.

        todoTable.update(todoItem).done(function (item) {
            todoItems.splice(todoItems.indexOf(item), 1, item);
        });

###### Excluir entrada

Excluir uma linha em uma tabela de dados. Chame o método [done]() em um objeto [Promise]() existentes.

    todoTable.delete(todoItem).done(function (item) {
        todoItems.splice(todoItems.indexOf(item), 1);
    }


