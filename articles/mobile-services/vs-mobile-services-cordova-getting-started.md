<properties 
	pageTitle="" 
	description="Descreve os primeiros passos para começar a usar os Serviços Móveis do Azure em um projeto Cordova" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="patshea123"/>

# Introdução aos serviços móveis (Projetos Cordova)

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-cordova-getting-started.md)
> - [What Happened](vs-mobile-services-cordova-what-happened.md)

A primeira etapa necessária para seguir o código nesses exemplos depende de a qual tipo de serviço móvel você está conectado.

- Para obter um serviço móvel de back-end JavaScript, crie uma tabela chamada TodoItem. Para criar uma tabela, localize o serviço móvel sob o nó Azure no Gerenciador de servidores, clique com o botão direito no nó do serviço móvel para abrir o menu de contexto e escolha **Criar Tabela**. Digite "TodoItem" como o nome da tabela.

- Se você tiver um serviço móvel de back-end .NET, já existe uma tabela TodoItem no modelo do projeto padrão que o Visual Studio criou para você, mas será necessário publicá-la no Azure. Para publicá-lo, abra o menu de contexto do projeto de serviço móvel no Gerenciador de servidores e escolha **Publicar na Web**. Aceite os padrões e escolha o botão **Publicar**.



#####Criar uma referência para uma tabela

O código a seguir obtém uma referência de uma tabela que contém dados para um TodoItem, que pode ser usada em operações posteriores para ler e atualizar a tabela de dados. A tabela TodoItem é criada automaticamente ao criar um serviço móvel.

    var todoTable = mobileServiceClient.getTable('TodoItem');

Para que esses exemplos funcionem, as permissões na tabela devem ser definidas para **Qualquer um com uma chave de aplicativo**. Posteriormente, você poderá configurar a autenticação. Consulte [Introdução à autenticação](mobile-services-html-get-started-users.md).

#####Adicionar um item a uma tabela

Inserir um novo item em uma tabela de dados. Uma id (um GUID da cadeia de caracteres do tipo) é criada automaticamente como a chave primária para a nova linha. Chame o método `done()` no objeto [Promise](https://msdn.microsoft.com/library/dn802826.aspx) retornado para obter uma cópia do objeto inserido e tratar os erros existentes.

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }
    
    var items = new Array();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            items.push(item)
        });
    };

#####Ler ou consultar uma tabela

O código a seguir consulta uma tabela para todos os itens, ordenados pelo campo de texto. Você pode adicionar um código para processar os resultados da consulta no manipulador de êxito. Nesse caso, uma matriz local dos itens será atualizada.

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
        });

Você pode usar o onde o método deverá modificar a consulta. Veja um exemplo que filtra itens concluídos.

    todoTable.where(function () {
            return (this.complete === false);
        })
        .read().done(function (results) {
            items = results.slice();
        });

Para ver mais exemplos de consultas que podem ser usadas, consulte o objeto de [consulta](http://msdn.microsoft.com/library/azure/jj613353.aspx).

#####Atualizar um item da tabela

Atualizar uma linha em uma tabela de dados. Neste código, o item será removido da lista quando o serviço móvel responder. Chame o método `done()` no objeto [Promise](https://msdn.microsoft.com/library/dn802826.aspx) retornado para obter uma cópia do objeto inserido e tratar os erros existentes.

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

#####Excluir um item da tabela

Exclua uma linha em uma tabela de dados usando o método **del**. Chame o método `done()` no objeto [Promise](https://msdn.microsoft.com/library/dn802826.aspx) retornado para obter uma cópia do objeto inserido e tratar os erros existentes.

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });

[Saiba mais sobre serviços móveis](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=July15_HO2-->