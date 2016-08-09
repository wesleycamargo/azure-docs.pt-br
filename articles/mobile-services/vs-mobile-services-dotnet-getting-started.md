<properties
	pageTitle="Introdução com um projeto de serviços móveis do Visual Studio .NET (Serviços Conectados) | Microsoft Azure"
	description="Como começar a trabalhar com os Serviços Móveis do Azure em um projeto .NET do Visual Studio"
	services="mobile-services"
	documentationCenter=""
	authors="mlhoop"
	manager="douge"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="mlearned"/>

# Introdução aos serviços móveis (Projetos .NET)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

A primeira etapa necessária para seguir o código nesses exemplos depende de a qual tipo de serviço móvel você está conectado.

- Para obter um serviço móvel de back-end JavaScript, crie uma tabela chamada TodoItem. Para criar uma tabela, localize o serviço móvel sob o nó Azure no Gerenciador de servidores, clique com o botão direito no nó do serviço móvel para abrir o menu de contexto e escolha **Criar Tabela**. Digite "TodoItem" como o nome da tabela.

- Se você tiver um serviço móvel de back-end .NET, já existe uma tabela TodoItem no modelo do projeto padrão que o Visual Studio criou para você, mas será necessário publicá-la no Azure. Para publicá-lo, abra o menu de contexto do projeto de serviço móvel no Gerenciador de servidores e escolha **Publicar na Web**. Aceite os padrões e escolha o botão **Publicar**.

##Obter uma referência para uma tabela

O código a seguir cria uma referência para uma tabela (`todoTable`) que contém dados para um TodoItem, que pode ser usada em operações posteriores para ler e atualizar a tabela de dados. Você precisará da classe TodoItem com atributos preparados para interpretar o JSON enviado pelo serviço móvel em resposta às suas consultas.

	public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

	IMobileServiceTable<TodoItem> todoTable = App.<yourClient>.GetTable<TodoItem>();

Este código funcionará se sua tabela possuir permissões definidas para **Qualquer um com uma chave de aplicativo**. Se você alterar as permissões para proteger seu serviço móvel, precisará adicionar suporte à autenticação de usuário. Consulte [Introdução à autenticação](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md).

##Adicionar um item de tabela

Inserir um novo item em uma tabela de dados.

	TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
	await todoTable.InsertAsync(todoItem);

##Ler ou consultar uma tabela

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


##Atualizar um item da tabela

Atualizar uma linha em uma tabela de dados. O item do parâmetro é o objeto TodoItem a ser atualizado.

	await todoTable.UpdateAsync(item);

##Excluir um item da tabela

Excluir uma linha no banco de dados. O item do parâmetro é o objeto TodoItem a ser excluído.

	await todoTable.DeleteAsync(item);


[Saiba mais sobre serviços móveis](https://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0727_2016-->