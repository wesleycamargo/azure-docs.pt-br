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
> - [Introdução](vs-mobile-services-dotnet-getting-started.md)
> - [O que aconteceu](vs-mobile-services-dotnet-what-happened.md)

## Introdução aos serviços móveis (Projetos .NET)

A primeira etapa necessária para seguir o código nesses exemplos depende de a qual tipo de serviço móvel você está conectado.

Para obter um serviço móvel de back-end JavaScript, crie uma tabela chamada TodoItem.  Para criar uma tabela, localize o serviço móvel sob o nó Azure no Gerenciador de servidores, clique com o botão direito no nó do serviço móvel para abrir o menu de contexto e escolha **Criar Tabela**. Digite "TodoItem" como o nome da tabela.

Se ao invés de você ter um serviço móvel de back-end .NET, já houver uma tabela TodoItem no modelo do projeto padrão que o Visual Studio criou para você, será necessário publicá-lo no Azure. Para publicá-lo, abra o menu de contexto do projeto de serviço móvel no Gerenciador de servidores e escolha **Publicar na Web**. Aceite os padrões e escolha o botão **Publicar**.

#####Obtenha referência para uma tabela

O código a seguir obtém uma referência de uma tabela que contém dados para um TodoItem, que pode ser usada em operações posteriores para ler e atualizar a tabela de dados. Você precisará da classe TodoItem com atributos preparados para interpretar o JSON enviado pelo serviço móvel em resposta às suas consultas.

	public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

	IMobileServiceTable<TodoItem> todoTable = App.<yourClient>.GetTable<TodoItem>();

Este código funcionará se sua tabela possuir permissões definidas para **Qualquer um com uma chave de aplicativo**. Se você alterar as permissões para proteger seu serviço móvel, precisará adicionar suporte à autenticação de usuário. Consulte [Introdução à autenticação](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/).

#####Adicionar entrada 

Inserir um novo item em uma tabela de dados.

	TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
	await todoTable.InsertAsync(todoItem);

#####Ler/consultar tabela 

O código a seguir consulta uma tabela para todos os itens. Observe que ele retorna somente a primeira página de dados, que possui 50 itens por padrão. Você pode usar o tamanho de página que desejar, pois este é um parâmetro opcional.

    List<TodoItem> items;
    try
    {
        // Query that returns all items.   
        items = await todoTable.ToListAsync();             
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }


#####Atualizar entrada

Atualizar uma linha em uma tabela de dados. O item do parâmetro é o objeto TodoItem a ser atualizado.

	await todoTable.UpdateAsync(item);

#####Excluir entrada

Excluir uma linha no banco de dados. O item do parâmetro é o objeto TodoItem a ser excluído.

	await todoTable.DeleteAsync(item);


[Saiba mais sobre serviços móveis](http://azure.microsoft.com/documentation/services/mobile-services/)

<!--HONumber=42-->
