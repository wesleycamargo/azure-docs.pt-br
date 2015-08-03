<properties 
	pageTitle="" 
	description="Como começar a trabalhar com os Serviços Móveis em um projeto JavaScript no Visual Studio" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# Introdução aos Serviços Móveis

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-javascript-getting-started.md)
> - [What Happened](vs-mobile-services-javascript-what-happened.md)

A primeira etapa necessária para seguir o código nesses exemplos depende de a qual tipo de serviço móvel você está conectado.

Para obter um serviço móvel de back-end JavaScript, crie uma tabela chamada TodoItem. Para criar uma tabela, localize o serviço móvel sob o nó Azure no Gerenciador de servidores, clique com o botão direito no nó do serviço móvel para abrir o menu de contexto e escolha **Criar Tabela**. Digite "TodoItem" como o nome da tabela.

Se ao invés de você ter um serviço móvel de back-end .NET, já houver uma tabela TodoItem no modelo do projeto padrão que o Visual Studio criou para você, será necessário publicá-lo no Azure. Para publicá-lo, abra o menu de contexto do projeto de serviço móvel no Gerenciador de servidores e escolha **Publicar na Web**. Aceite os padrões e escolha o botão **Publicar**.

#####Obtenha referência de uma tabela

O objeto do cliente já foi adicionado ao seu projeto. Seu nome é o nome do seu serviço móvel com "Client" anexado a ele. O código a seguir obtém uma referência de uma tabela que contém dados para um TodoItem, que pode ser usada em operações posteriores para ler e atualizar a tabela de dados.

	var todoTable = yourMobileServiceClient.getTable('TodoItem');

#####Adicionar entrada 

Inserir um novo item em uma tabela de dados. Um id (um GUID da cadeia de caracteres do tipo) é criada automaticamente como a chave primária para a nova linha. Não altere o tipo da coluna do ID, pois ele é usado pela infraestrutura de serviços móveis.

    var todoTable = client.getTable('TodoItem');
    var todoItems = new WinJS.Binding.List();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            todoItems.push(item);
        });
    };

#####Ler/consultar tabela

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

#####Atualizar entrada

Atualizar uma linha em uma tabela de dados. Neste exemplo, o todoItem é o item atualizado e o item é o mesmo que o retornado do serviço móvel. Quando o serviço móvel responde, o item é atualizado na lista todoItems local usando o método [splice](http://msdn.microsoft.com/library/windows/apps/Hh700810.aspx). Chame o método [done]() em um objeto [Promise]() retornado para obter uma cópia do objeto inserido e tratar quaisquer erros existentes.

        todoTable.update(todoItem).done(function (item) {
            todoItems.splice(todoItems.indexOf(item), 1, item);
        });

#####Excluir entrada

Excluir uma linha em uma tabela de dados. Chame o método [done]() em um objeto [Promise]() retornado para obter uma cópia do objeto inserido e tratar quaisquer erros existentes.

	todoTable.del(todoItem).done(function (item) {
	    todoItems.splice(todoItems.indexOf(item), 1);
    }



[Saiba mais sobre serviços móveis](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=July15_HO4-->