##<a name="update-app"></a>Atualizar o aplicativo para chamar a API personalizada

1. No Visual Studio, abra o arquivo MainPage.xaml em seu projeto de início rápido, localize o elemento **Botão** chamado `ButtonRefresh` e substitua-o pelo código XAML a seguir: 

        <StackPanel Grid.Row="3" Grid.ColumnSpan="2" Orientation="Horizontal">
            <Button Width="225" Name="ButtonRefresh" 
                Click="ButtonRefresh_Click">Refresh</Button>
            <Button Width="225"  Name="ButtonCompleteAll" 
                Click="ButtonCompleteAll_Click">Complete All</Button>
        </StackPanel>

	Isso adiciona um novo botão à página.

2. Abra o arquivo de código MainPage.xaml.cs e adicione o seguinte código de definição da classe:

	    public class MarkAllResult
	    {
	        public int Count { get; set; }
	    }

	Essa classe é usada para armazenar o valor da contagem de linhas retornado pela API personalizada.

3. Localize o método **RefreshTodoItems** na classe **MainPage** e verifique se a `query` está definida usando o seguinte método **Where**:

        .Where(todoItem => todoItem.Complete == false)

	Isso filtra os itens de forma que os itens concluídos não sejam retornados pela consulta.

3. Na classe **MainPage**, adicione o seguinte método:

		private async void ButtonCompleteAll_Click(object sender, RoutedEventArgs e)
		{
		    string message;
		    try
		    {
		        // Asynchronously call the custom API using the POST method. 
		        var result = await App.MobileService
		            .InvokeApiAsync<MarkAllResult>("completeAll", 
		            System.Net.Http.HttpMethod.Post, null);
		        message =  result.Count + " item(s) marked as complete.";
		        RefreshTodoItems();
		    }
		    catch (MobileServiceInvalidOperationException ex)
		    {
		        message = ex.Message;                
		    }
		
		    MessageBox.Show(message);  
		}

	Esse método identifica o evento **Click** do novo botão. O método **InvokeApiAsync** é chamado no cliente, que envia uma solicitação para a nova API personalizada. O resultado retornado pela API personalizada é exibido em uma caixa de diálogo de mensagem.

## <a name="test-app"></a>Testar o aplicativo

1. No Visual Studio, pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

2. No aplicativo, digite um texto em **Inserir um TodoItem** e toque em **Salvar**.

3. Repita a etapa anterior até que tenha incluído vários itens pendentes na lista.

4. Toque no botão **Tudo Concluído**.

  	![](./media/mobile-services-windows-phone-call-custom-api/mobile-custom-api-windows-phone-completed.png)

	É exibida uma caixa de mensagem que indica o número de itens marcados como concluídos, e a consulta filtrada é executada novamente, o que limpa todos os itens da lista.

<!---HONumber=August15_HO6-->