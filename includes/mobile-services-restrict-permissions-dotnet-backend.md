

Por padrão, todas as solicitações de recursos dos serviços móveis são restritas aos clientes que apresentam a chave do aplicativo, que não protege estritamente o acesso aos recursos. Para proteger seus recursos, você deve restringir o acesso a apenas clientes autenticados.

1. No Visual Studio, abra o projeto de serviço móvel, expanda a pasta Controladores e abra **TodoItemController.cs**. A classe **TodoItemController** implementa o acesso a dados da tabela TodoItem. Adicione a instrução `using` a seguir:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

2. Aplique o seguinte atributo _AuthorizeLevel_ à classe **TodoItemController**: Isso garante que todas as operações em relação a tabela _TodoItem_ exigem um usuário autenticado.

		[AuthorizeLevel(AuthorizationLevel.User)]

	>[AZURE.NOTE]Aplique o atributo AuthorizeLevel a métodos individuais para definir níveis de autorização específicos nos métodos expostos pelo controlador.

3. (Opcional) Se desejar depurar a autenticação localmente, expanda a pasta `App_Start`, abra o **WebApiConfig.cs** e adicione o seguinte código ao método **Registrar**.

		config.SetIsHosted(true);

	Isso informa ao projeto do serviço móvel local para executar como se estivesse sendo hospedado no Azure, inclusive respeitando as configurações de *AuthorizeLevel*. Sem essa configuração, todas as solicitações HTTP para localhost são permitidas sem autenticação, apesar da configuração de *AuthorizeLevel*. Quando você habilita o modo de hospedagem interna, também precisa definir um valor para a chave de aplicativo local.

4. (Opcional) No arquivo web.config do projeto, defina um valor em cadeia de caracteres para a configuração `MS_ApplicationKey` do aplicativo. Esta é a senha que você usa (sem nenhum nome de usuário) para testar as páginas de ajuda de API, quando executar o serviço localmente.

	[AZURE.NOTE]Esse valor de cadeia de caracteres não é usado pelo site ao vivo no Azure, e não precisa usar a chave de aplicativo real. Qualquer valor de cadeia de caracteres válida funcionará.
 
4. Republique seu projeto.

<!--HONumber=54-->