<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### O que aconteceu?

###### Referências adicionadas

O pacote NuGet de Serviços Móveis do Azure foi adicionado ao seu projeto. Por isso, as referências .NET a seguir foram adicionadas ao projeto: Microsoft.WindowsAzure.Mobile, Microsoft.WindowsAzure.Mobile.Ext, Newtonsoft.Json, System.Net.Http.Extensions e System.Net.Http.Primitives

###### Valores de cadeia de conexão para Serviços Móveis

No arquivo App.xaml.cs, foi criado um objeto MobileServiceClient com a URL e chave do aplicativo do serviço móvel selecionado.

### Introdução aos Serviços Móveis

###### Obtenha referência de uma tabela

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

Este código funcionará se sua tabela possuir permissões definidas para **Qualquer um com uma chave de aplicativo**. Se você alterar as permissões para proteger seu serviço móvel, precisará adicionar suporte à autenticação de usuário. Consulte [Introdução à autenticação][Introdução à autenticação].

###### Adicionar entrada

Inserir um novo item em uma tabela de dados.

    TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
    await todoTable.InsertAsync(todoItem);

###### Ler/consultar tabela

O código a seguir consulta uma tabela para todos os itens. Observe que ele retorna somente a primeira página de dados, que possui 50 itens por padrão. Você pode usar o tamanho de página que desejar, pois este é um parâmetro opcional.

    List<TodoItem> items = await todoTable.ToListAsync();
    try
    {
        // Query that returns all items.   
        items = await todoTable.ToCollectionAsync();             
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }

###### Atualizar entrada

Atualizar uma linha em uma tabela de dados. O item do parâmetro é o objeto TodoItem a ser atualizado.

    await todoTable.UpdateAsync(item);

###### Excluir entrada

Excluir uma linha no banco de dados. O item do parâmetro é o objeto TodoItem a ser excluído.

    await todoTable.DeleteAsync(item);

  [Introdução à autenticação]: http://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/
