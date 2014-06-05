

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse na pasta Controladores do projeto de serviço móvel, expanda **Adicionar** e clique em **Novo Item Scaffold**.

	Isso exibe a caixa de diálogo Adicionar Scaffold.

2. Expanda **Serviços Móveis do Azure** e clique em **Controlador Personalizado de Serviços Móveis do Azure**, em **Adicionar**, forneça um **Nome do controlador** de `CompleteAllController` e clique em **Adicionar** novamente.

	![](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

	Isso cria uma nova classe de controlador vazia chamada **CompleteAllController**.

>[WACOM.NOTE]Se a caixa de diálogo não tiver scaffolds específicos de Serviços Móveis, em vez de criar um novo **Controlador de API Web - Vazio**. Nessa nova classe de controlador, adicione uma propriedade pública **Serviços**, que retorna o tipo **ApiServices**. Essa propriedade é usada para acessar as configurações específicas do servidor dentro do controlador.

3. No novo arquivo de projeto CompleteAllController.cs, adicione as seguintes instruções **using**:

		using System.Threading.Tasks;
		using todolistService.Models;

	No código acima, substitua `todolistService` pelo namespace do projeto do serviço móvel, que deve ser o nome do serviço móvel anexado a `Serviço`. 

4. Adicione o seguinte código ao novo controlador:

	    // POST api/completeall        
        public Task<int> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";
                var result = database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.", 
                    result.ToString()));
                
                return result;
            }
        }

	No código acima, substitua `todolistContext` pelo nome do DbContext do modelo de dados, que deve ser o nome do serviço móvel anexado a `Contexto`. Esse código usa o [Classe do Banco de Dados](http://msdn.microsoft.com/pt-br/library/system.data.entity.database(v=vs.113).aspx) para acessar a tabela **TodoItems** diretamente para definir o sinalizador de conclusão em todos os itens. Esse método dá suporte a uma solicitação POST, e o número de linhas alteradas é retornado ao cliente como um valor inteiro.

	> [WACOM.NOTE] As permissões padrão são definidas, o que significa que qualquer usuário do aplicativo pode chamar a API personalizada. No entanto, a chave de aplicativo não é distribuída ou armazenada de forma segura e não pode ser considerada uma credencial segura. Por isso, você deve restringir o acesso somente para usuários autenticados em operações que modificam dados ou afetam o serviço móvel. 

Em seguida, você modificará o aplicativo quickstart para adicionar um novo botão e o código que chama assincronamente a nova API personalizada.


