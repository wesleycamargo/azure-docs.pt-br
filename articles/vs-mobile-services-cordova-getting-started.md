<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### O que aconteceu?

### Referências adicionadas

O plug-in do Cliente do Serviço Móvel do Azure está incluído em todos os Aplicativos Híbridos para Vários Dispositivos habilitados.

### Valores de cadeia de conexão para Serviços Móveis

Na pasta services\\mobileServices\\settings, foi gerado um novo arquivo JavaScript (.js) com um MobileServiceClient contendo a URL e a chave do aplicativo do serviço móvel selecionado. O arquivo contém a inicialização de um objeto de cliente de serviço móvel, semelhante ao código a seguir.

    var mobileServiceClient;
    document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
            "<your mobile service name>.azure-mobile.net",
            "<insert your key>"
        );

### Introdução aos Serviços Móveis

###### Obtenha referência de uma tabela

O código a seguir obtém uma referência de uma tabela que contém dados para um TodoItem, que pode ser usada em operações posteriores para ler e atualizar a tabela de dados. A tabela TodoItem é criada automaticamente ao criar um serviço móvel.

    var todoTable = mobileServiceClient.getTable('TodoItem');

Para que esses exemplos funcionem, as permissões na tabela devem ser definidas para **Qualquer um com uma chave de aplicativo**. Posteriormente, você poderá configurar a autenticação. Consulte [Introdução à autenticação][Introdução à autenticação].

###### Adicionar entrada

Inserir um novo item em uma tabela de dados. Um id (um GUID da cadeia de caracteres do tipo) é criada automaticamente como a chave primária para a nova linha. Chame o método [done]() em um objeto [Promise]() retornado para obter uma cópia do objeto inserido e tratar quaisquer erros existentes.

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    todoTable.insert(todoItem).done(function (item) {
        items.push(item)
        });
    };

###### Ler/consultar tabela

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

Para ver mais exemplos de consultas que podem ser usadas, consulte o objeto de [consulta]().

###### Atualizar entrada

Atualizar uma linha em uma tabela de dados. Neste código, o item será removido da lista quando o serviço móvel responder. Chame o método [done]() em um objeto [Promise]() erros existentes.

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

###### Excluir entrada

Excluir uma linha em uma tabela de dados usando o método del. Chame o método [done]() em um objeto [Promise]() retornado para obter uma cópia do objeto inserido e tratar quaisquer erros existentes.

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });

  [Introdução à autenticação]: http://azure.microsoft.com/en-us/documentation/articles/mobile-services-html-get-started-users/
  [done]: 
  [consulta]: (http://msdn.microsoft.com/library/azure/jj613353.aspx)
