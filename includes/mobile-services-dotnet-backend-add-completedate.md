Nesta seção, modificaremos o modelo do nosso banco de dados, adicionando um novo campo de carimbo de data/hora chamado **CompleteDate**. Esse campo gravará a última vez em que o item de tarefa foi concluído. O Entity Framework atualizará o banco de dados com base na mudança de modelo usando uma classe inicializadora do banco de dados padrão derivada de [DropCreateDatabaseIfModelChanges](http://go.microsoft.com/fwlink/?LinkId=394621).

1. No Gerenciador de Soluções do Visual Studio, expanda a pasta **App\_Start** no projeto de serviço da lista de tarefas. Abra o arquivo WebApiConfig.cs.

2. No arquivo WebApiConfig.cs, observe que a classe de inicializador de banco de dados padrão é derivada da classe `DropCreateDatabaseIfModelChanges`. Isso significa que qualquer alteração no modelo resultará na tabela que está sendo descartada e recriada para acomodar o modelo novo. Portanto, os dados da tabela serão perdidos e a tabela será refeita. Modifique o método Seed do inicializador do banco de dados de forma que os dados de semente sejam os da gravação do arquivo WebApiConfig.cs.

    >[AZURE.NOTE]Ao usar o inicializador de banco de dados padrão, o Entity Framework vai remover e recriar o banco de dados sempre que detectar uma alteração no modelo de dados na definição do modelo Code First. Para fazer com que esse modelo de dados altere e mantenha os dados existentes no banco de dados, você deve usar as Migrações Code First. Para obter mais informações, consulte [Como usar as Migrações Code First para atualizar o modelo de dados](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

        List<TodoItem> todoItems = new List<TodoItem>
        {
          new TodoItem { Id = "1", Text = "First seed item", Complete = false },
          new TodoItem { Id = "2", Text = "Second seed item", Complete = false },
        };
     

3. No Gerenciador de Soluções do Visual Studio, expanda a pasta **DataObjects** no projeto de serviço da lista de tarefas. Abra o arquivo TodoItem.cs e atualize a classe TodoItem para incluir o campo CompleteDate da maneira a seguir. Em seguida, salve o arquivo TodoItem.cs.

        public class TodoItem : EntityData
        {
          public string Text { get; set; }
          public bool Complete { get; set; }
          public System.DateTime? CompleteDate { get; set; }
        }

4. No Gerenciador de Soluções do Visual Studio, expanda a pasta **Controladores** no projeto de serviço da lista de tarefas. Abra o arquivo TodoItemController.cs e atualize o método `PatchTodoItem` de modo que ele defina **CompleteDate** quando a propriedade **Complete** estiver sendo alterada de falsa para verdadeira. Em seguida, salve o arquivo TodoItemController.cs.

        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            // If complete is being set to true and was false, set CompleteDate
            if ((patch.GetEntity().Complete == true) &&
                (GetTodoItem(id).Queryable.Single().Complete == false))
            {
                patch.TrySetPropertyValue("CompleteDate", System.DateTime.Now);
            }
            return UpdateAsync(id, patch);
        }


5. Recompile o projeto de serviço de back-end do .NET da lista de tarefas e verifique se não há erros de compilação.

Em seguida, você atualizará o aplicativo cliente para exibir os novos dados de **CompleteDate**.

<!---HONumber=Oct15_HO3-->