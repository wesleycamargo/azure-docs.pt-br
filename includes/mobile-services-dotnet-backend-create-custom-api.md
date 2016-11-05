

1. No Visual Studio, clique com o botão direito do mouse na pasta Controladores, expanda **Adicionar** e clique em **Novo Item Scaffold**. Isso exibe a caixa de diálogo Adicionar Scaffold.
2. Expanda **Serviços Móveis do Azure**, clique em **Controlador Personalizado de Serviços Móveis do Azure**, clique em **Adicionar**, forneça um **Nome do controlador** de `CompleteAllController` e clique em **Adicionar** novamente.
   
    ![Caixa de diálogo API da Web adiciona Scaffold.](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)
   
    Isso cria uma nova classe de controlador vazia chamada **CompleteAllController**.
   
   > [!NOTE]
   > Se a caixa de diálogo não tiver scaffolds específicos de Serviços Móveis, crie um novo **Controlador de API Web - Vazio**. Nessa nova classe de controlador, adicione uma propriedade pública **Serviços**, que retorna o tipo **ApiServices**. Essa propriedade é usada para acessar as configurações específicas do servidor dentro do controlador.
   > 
   > 
3. Em **CompleteAllController.cs**, adicione as seguintes instruções **de uso**. Substitua `todolistService` pelo namespace do projeto do serviço móvel, que deve ser o nome do serviço móvel anexado a `Service`.
   
        using System.Threading.Tasks;
        using todolistService.Models;
4. Em **CompleteAllController.cs**, adicione a seguinte classe para encapsular a resposta enviada ao cliente.
   
        // We use this class to keep parity with other Mobile Services
        // that use the JavaScript backend. This way the same client
        // code can call either type of Mobile Service backend.
        public class MarkAllResult
        {
            public int count;
        }
5. Adicione o seguinte código ao novo controlador. Substitua `todolistContext` com o nome do DbContext para seu modelo de dados, que deve ser o nome do serviço móvel anexado com `Context`. Da mesma forma, substitua o nome do esquema na instrução UPDATE com o nome do seu serviço móvel. Esse código usa a [Classe Banco de Dados](http://msdn.microsoft.com/library/system.data.entity.database.aspx) para acessar a tabela **TodoItems** diretamente para definir a marcação concluído em todos os itens. Esse método dá suporte a uma solicitação POST, e o número de linhas alteradas é retornado ao cliente como um valor inteiro.

        // POST api/completeall
        public async Task<MarkAllResult> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE todolist.TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";

                var result = new MarkAllResult();
                result.count = await database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.",
                    result.count.ToString()));

                return result;
            }
        }

    > [AZURE.TIP]Com as permissões padrão, qualquer pessoa com a chave do aplicativo pode chamar a API personalizada. No entanto, a chave de aplicativo não é considerada uma credencial segura porque ela não pode ser distribuída ou armazenada com segurança. Considere a possibilidade de restringir o acesso somente para usuários autenticados para obter segurança adicional.

<!---HONumber=Oct15_HO3-->