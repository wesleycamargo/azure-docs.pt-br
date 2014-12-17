

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse na pasta Controladores do projeto de serviço móvel, expanda **Adicionar** e clique em **Novo Item Scaffold**.

	Isso exibe a caixa de diálogo Adicionar Scaffold.

2. Expanda **Serviços Móveis do Azure** e clique em **Controlador Personalizado de Serviços Móveis do Azure**, então clique em **Adicionar**, forneça um **Nome do controlador** de `CompleteAllController` e clique em **Adicionar** novamente.

	![Web API Add Scaffold dialog](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

Isso cria uma nova classe de controlador vazia chamada **CompleteAllController**.

>[WACOM.NOTE]Se a caixa de diálogo não tiver scaffolds específicos de Serviços Móveis, crie um novo **Controlador de API Web - Vazio**. Nessa nova classe de controlador, adicione uma propriedade pública **Serviços**, que retorna o tipo **ApiServices**. Essa propriedade é usada para acessar as configurações específicas do servidor dentro do controlador.

3. No novo arquivo de projeto CompleteAllController.cs, adicione as seguintes instruções **de uso:**

		using System.Threading.Tasks;
		using todolistService.Models;

No código acima, substitua `todolistService` pelo namespace do projeto do serviço móvel, que deve ser o nome do serviço móvel anexado a `Service`. 

4. Em CompleteAllController.cs, adicione a seguinte definição de classe ao namespace. Essa classe encapsula a resposta enviada ao cliente.

        // We use this class to keep parity with other Mobile Services
        // that use the JavaScript backend. This way the same client
        // code can call either type of Mobile Service backend.
        public class MarkAllResult
        {
            public Int32 count;
        }


5. Adicione o seguinte código ao novo controlador:

	    // POST api/completeall        
        public async Task<MarkAllResult> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE todolistService.TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";

                var result = new MarkAllResult();
                result.count = await database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.", 
                    result.count.ToString()));
                
                return result;
            }
        }

No código acima, substitua `todolistContext` pelo nome do DbContext do modelo de dados, que deve ser o nome do serviço móvel anexado a `Context`. Além disso, substitua o nome de esquema na instrução UPDATE pelo nome de seu serviço móvel. 

Esse código usa a [Classe Banco de Dados](http://msdn.microsoft.com/pt-br/library/system.data.entity.database.aspx) para acessar a tabela **TodoItems** diretamente para definir o sinalizador concluído em todos os itens. Esse método dá suporte a uma solicitação POST, e o número de linhas alteradas é retornado ao cliente como um valor inteiro.

	> [WACOM.NOTE]  As permissões padrão são definidas, o que significa que qualquer usuário do aplicativo pode chamar a API personalizada. No entanto, a chave de aplicativo não é distribuída ou armazenada de forma segura e não pode ser considerada uma credencial segura. Por isso, você deve restringir o acesso somente para usuários autenticados em operações que modificam dados ou afetam o serviço móvel. 

Em seguida, você modificará o aplicativo quickstart para adicionar um novo botão e o código que chama assincronamente a nova API personalizada.

