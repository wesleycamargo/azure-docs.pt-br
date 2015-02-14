
1. No arquivo MainPage.xaml.cs, adicione ou remova o comentário dos seguintes usando instruções: 

		using Microsoft.WindowsAzure.MobileServices;

2. Substitua a definição da classe TodoItem pelo seguinte código: 

	    public class TodoItem
	    {
	        public string Id { get; set; }
	
	        [Newtonsoft.Json.JsonProperty(PropertyName = "text")]  
	        public string Text { get; set; }
	
	        [Newtonsoft.Json.JsonProperty(PropertyName = "complete")]  
	        public bool Complete { get; set; }
	    }
	
	O **JsonPropertyAttribute** é usado para definir o mapeamento entre os nomes das propriedades no tipo de cliente para nomes de coluna na tabela de dados subjacente.

	>[AZURE.NOTE] Em um projeto universal do Windows, a classe TodoItem é definida no arquivo de código separado na pasta DataModel compartilhada.

3. No MainPage.xaml.cs, remova o comentário ou exclua a linha que define a coleção de itens existentes e remova os comentários ou adicione as seguintes linhas, substituindo _&lt;yourClient&gt;_ pelo campo  `MobileServiceClient` adicionado ao arquivo App.xaml.cs quando você conecta seu projeto ao serviço móvel: 

		private MobileServiceCollection<TodoItem, TodoItem> items;
		private IMobileServiceTable<TodoItem> todoTable = 
		    App.<yourClient>.GetTable<TodoItem>();
		  
	Esse código cria uma coleção de ligação com reconhecimento de serviços móveis (itens) e uma classe de proxy para a tabela de banco de dados (todoTable). 

4. No método **InsertTodoItem**, remova a linha de código que define a propriedade **TodoItem.Id**, adicione o modificador **async** ao método e remova o comentário da seguinte linha de código: 

		await todoTable.InsertAsync(todoItem);


	Esse código insere um novo item na tabela. 

5. Substitua o método **RefreshTodoItems** pelo seguinte código: 

		private async void RefreshTodoItems()
        {
            MobileServiceInvalidOperationException exception = null;
            try
            {
                // Query that returns all items.   
                items = await todoTable.ToCollectionAsync();             
            }
            catch (MobileServiceInvalidOperationException e)
            {
                exception = e;
            }
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Error loading items").ShowAsync();
            }
            else
            {
                ListItems.ItemsSource = items;
                this.ButtonSave.IsEnabled = true;
            }    
        }

	Isso define a associação à coleção de itens em  `todoTable`, que contém todos os objetos **TodoItem** retornados do serviço móvel. Se existe um problema ao executar uma consulta, uma caixa de mensagem é destacada para exibir os erros. 

6. No método **UpdateCheckedTodoItem**, adicione o modificador **async** ao método e remova o comentário da linha de código a seguir: 

		await todoTable.UpdateAsync(item);

	Isso envia uma atualização de item para o serviço móvel. 

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, é hora de testar o aplicativo com os Serviços Móveis.<!--HONumber=42-->
