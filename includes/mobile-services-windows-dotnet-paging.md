

1. No Visual Studio, abra o projeto que você modificou quando concluiu o tutorial **Introdução aos dados**.

2. Pressione a tecla **F5** para executar o aplicativo e, em seguida, digite texto em **Inserir um TodoItem** e clique em **Salvar**.

3. Repita a etapa anterior, pelo menos três vezes, para que você tenha mais de três itens armazenados na tabela TodoItem.

2. No arquivo MainPage.xaml.cs, substitua o método **RefreshTodoItems** pelo seguinte código:

        private async void RefreshTodoItems()
        {
            // Define a filtered query that returns the top 3 items.
            IMobileServiceTableQuery<TodoItem> query = todoTable
                            .Where(todoItem => todoItem.Complete == false)
                           .Take(3);
            items = await query.ToCollectionAsync();
            ListItems.ItemsSource = items;
        }

  	Essa consulta, quando executada durante a associação de dados, retorna os três primeiros itens que não estiverem marcados como concluídos.

3. Pressione a tecla **F5** para executar o aplicativo.

  	Observe que apenas os três primeiros resultados da tabela TodoItem são exibidos.

4. (Opcional) Exiba o URI da solicitação enviada ao serviço móvel usando software de inspeção de mensagem, como as ferramentas de desenvolvedor do navegador ou o [Fiddler].

   	Observe que o método `Take(3)` foi movido para a opção de consulta `$top=3` no URI da consulta.

5. Atualize o método **RefreshTodoItems** mais uma vez com o código a seguir:
            
        private async void RefreshTodoItems()
        {
            // Define a filtered query that skips the first 3 items and 
            // then returns the next 3 items.
            IMobileServiceTableQuery<TodoItem> query = todoTable
                           .Where(todoItem => todoItem.Complete == false)
                           .Skip(3)
                           .Take(3);
            items = await query.ToCollectionAsync();
            ListItems.ItemsSource = items;
        }

   	Essa consulta ignora os três primeiros resultados e retorna os três seguintes. Essa é efetivamente a segunda "página" de dados, onde o tamanho da página é de três itens.

    >[AZURE.NOTE]Este tutorial usa um cenário simplificado passando valores de paginação embutidos em código para os métodos <strong>Take</strong> e <strong>Skip</strong>. Em um aplicativo do mundo real, você pode usar consultas semelhantes às mencionadas acima com um controle de paginação ou interface do usuário comparável para permitir que os usuários naveguem para páginas anteriores e posteriores. Você também pode chamar o método <strong>IncludeTotalCount</strong> para obter a contagem total dos itens disponíveis no servidor, juntamente com os dados paginados.

6. (Opcional) Novamente, você pode ver o URI da solicitação enviada ao serviço móvel.

   	Observe que o método `Skip(3)` foi movido para a opção de consulta `$skip=3` no URI da consulta.

<!-- URLs -->
[Fiddler]: http://go.microsoft.com/fwlink/?LinkID=262412

<!---HONumber=July15_HO2-->