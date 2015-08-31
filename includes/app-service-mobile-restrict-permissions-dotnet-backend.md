
Por padrão, APIs em um back-end de aplicativo móvel podem ser chamadas de forma anônima. Em seguida, você precisa restringir o acesso somente aos clientes autenticados.

1. Em seu computador, abra o projeto do servidor no Visual Studio e navegue até **Controladores** > **TodoItemController.cs**.

2. Adicione o `[Authorize]` atributo à classe **TodoItemController**, como segue. Isso requer que todas as operações da tabela TodoItem sejam feitas por um usuário autenticado. Para restringir o acesso somente aos métodos específicos, você também pode aplicar esse atributo apenas aos métodos, em vez de à classe.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>
   
    Isso requer que todas as operações da tabela TodoItem sejam feitas por um usuário autenticado. Para restringir o acesso somente aos métodos específicos, você também pode aplicar esse atributo apenas aos métodos, em vez de à classe.
   
3. Publique novamente o projeto do servidor.

<!---HONumber=August15_HO8-->