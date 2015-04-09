Por padrão, todas as solicitações de recursos dos serviços móveis são restritas aos clientes que apresentam a chave do aplicativo, que não protege estritamente o acesso aos recursos. Para proteger seus recursos, você deve restringir o acesso a apenas clientes autenticados.

1. No Visual Studio, abra o projeto de serviço móvel, expanda a pasta Controladores e abra **TodoItemController.cs**. A classe **TodoItemController** implementa o acesso a dados da tabela TodoItem. Adicione a instrução `using` a seguir:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

2. Aplique o seguinte atributo _AuthorizeLevel_ à classe **TodoItemController**: Isso garante que todas as operações em relação a tabela  _TodoItem_ exigem um usuário autenticado.

		[AuthorizeLevel(AuthorizationLevel.User)]

	>[AZURE.NOTE]Aplique o atributo AuthorizeLevel a métodos individuais para definir níveis de autorização específicos nos métodos expostos pelo controlador.

3. Se desejar depurar a autenticação localmente, expanda a pasta `App_Start`, abra o **WebApiConfig.cs** e adicione o seguinte código ao método **Register**.  

		config.SetIsHosted(true);

	Isso informa o projeto de serviço móvel local para executar como se estivesse sendo hospedado no Azure, incluindo respeitar as configurações *AuthorizeLevel*. Sem essa configuração, todas as solicitações HTTP para localhost são permitidas sem autenticação, apesar da configuração de *AuthorizeLevel*. 

4. Republique seu projeto.

<!--HONumber=49-->