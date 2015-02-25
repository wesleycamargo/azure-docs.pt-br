<properties 
	pageTitle="" 
	description="" 
	services="mobile-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/8/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Introdução](/documentation/articles/vs-mobile-services-cordova-getting-started/)
> - [O que aconteceu](/documentation/articles/vs-mobile-services-cordova-what-happened/)

## Introdução aos serviços móveis (Projetos Cordova)

A primeira etapa necessária para seguir o código nesses exemplos depende de a qual tipo de serviço móvel você está conectado.

Para obter um serviço móvel de back-end JavaScript, crie uma tabela chamada TodoItem.  Para criar uma tabela, localize o serviço móvel sob o nó Azure no Gerenciador de servidores, clique com o botão direito no nó do serviço móvel para abrir o menu de contexto e escolha **Criar Tabela**. Digite "TodoItem" como o nome da tabela.

Se ao invés de você ter um serviço móvel de back-end .NET, já houver uma tabela TodoItem no modelo do projeto padrão que o Visual Studio criou para você, será necessário publicá-lo no Azure. Para publicá-lo, abra o menu de contexto do projeto de serviço móvel no Gerenciador de servidores e escolha **Publicar na Web**. Aceite os padrões e escolha o botão **Publicar**.
  
>[AZURE.NOTE] **Em projetos de Cordova que são criados usando o Visual Studio 2015 Preview, use [solução](http://go.microsoft.com/fwlink/?LinkId=518765) para trabalhar com serviços móveis do Azure. A solução não é necessária para projetos que usam versões mais recentes do Visual Studio 2015.**

#####Obtenha referência para uma tabela

O código a seguir obtém uma referência de uma tabela que contém dados para um TodoItem, que pode ser usada em operações posteriores para ler e atualizar a tabela de dados. A tabela TodoItem é criada automaticamente ao criar um serviço móvel.

	var todoTable = mobileServiceClient.getTable('TodoItem');

Para que esses exemplos funcionem, as permissões na tabela devem ser definidas para **Qualquer um com uma chave de aplicativo**. Posteriormente, você poderá configurar a autenticação. Consulte [Introdução à autenticação](http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-html-get-started-users/).

#####Adicionar entrada 

Inserir um novo item em uma tabela de dados. Uma id (um GUID da cadeia de caracteres do tipo) é criada automaticamente como a chave primária para a nova linha. Chame o método [done]() no objeto [Promise]() retornado para obter uma cópia do objeto inserido e manipular os erros.

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    todoTable.insert(todoItem).done(function (item) {
        items.push(item)
        });
    };

#####Ler/consultar tabela 

O código a seguir consulta uma tabela para todos os itens, ordenados pelo campo de texto. Você pode adicionar um código para processar os resultados da consulta no manipulador de êxito. Nesse caso, uma matriz local dos itens será atualizada.

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
            });
        });

Você pode usar o onde o método deverá modificar a consulta. Veja um exemplo que filtra itens concluídos.

	todoTable.where(function () {
                 return (this.complete === false);
              })
             .read().done(function (results) {
                items = results.slice();
             });

Para ver mais exemplos de consultas que podem ser usadas, consulte o objeto [query](http://msdn.microsoft.com/library/azure/jj613353.aspx).

#####Atualizar entrada

Atualizar uma linha em uma tabela de dados. Neste código, o item será removido da lista quando o serviço móvel responder. Chame o método [done]() no objeto [Promise]() retornado para obter uma cópia do objeto inserido e manipular os erros.

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

#####Excluir entrada

Excluir uma linha em uma tabela de dados usando o método **del**. Chame o método [done]() no objeto [Promise]() retornado para obter uma cópia do objeto inserido e manipular os erros.

	todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
	});

[Saiba mais sobre serviços móveis](http://azure.microsoft.com/documentation/services/mobile-services/)

<!--HONumber=42-->
