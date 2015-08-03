

Por padrão, os pontos de extremidade definidos em seu Aplicativo Móvel são expostos publicamente. Para proteger seus recursos, é necessário restringir o acesso apenas a clientes autenticados.

1. No Visual Studio, abra o projeto que contém o código do aplicativo móvel. 

2. No Gerenciador de Soluções, expanda a pasta Controllers e abra o arquivo de projeto TodoItemController.cs.

	A classe **TodoItemController** implementa o acesso a dados da tabela TodoItem.

3. Aplique o atributo `Authorize` à classe **TodoItemController**:

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

	Isso garantirá que todas as operações em relação a tabela **TodoItem** exigem um usuário autenticado.

	>[AZURE.NOTE]Aplique o atributo Authorize a métodos individuais para definir níveis de autorização específicos nos métodos expostos pelo controlador.

4. Republique seu projeto de aplicativo móvel.

<!---HONumber=July15_HO4-->