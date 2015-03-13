<properties
	pageTitle="Autorização de serviço de usuários nos Serviços Móveis com o Backend .NET | Centro de Desenvolvimento Móvel"
	description="Saiba como autorizar usuários no backend .NET dos Serviços Móveis do Azure."
	services="mobile-services"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.topic="article"
	ms.date="2/18/2015"
	ms.author="krisragh"/>

# Autorização de serviço de usuários nos serviços móveis

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Qualquer | .NET)](/pt-br/documentation/articles/mobile-services-dotnet-backend-service-side-authorization/)
- [(Qualquer | JavaScript)](/pt-br/documentation/articles/mobile-services-javascript-backend-service-side-authorization/)

Este tópico mostra como usar a lógica de servidor para autorizar usuários.  Neste tutorial, você modifica os métodos de acesso de dados no .NET, filtra consultas com base nas IDs de usuário e concede acesso aos usuários apenas para seus próprios dados.

Esse tutorial se baseia o Início Rápido dos Serviços Móveis e se baseia no tutorial [Adicionar autenticação ao aplicativo de serviços móveis existentes]. Preencha [Adicionar autenticação ao aplicativo de serviços móveis existente] primeiro.

## <a name="register-scripts"></a>Modificar os métodos de acesso de dados

1. No Visual Studio, abra o projeto móvel, expanda a pasta DataObjects e abra **TodoItem.cs**. A classe **TodoItem** define o objeto de dados, e você precisa adicionar uma propriedade **UserId** a ser usada na filtragem. Adicione a nova propriedade UserId a seguir à classe **TodoItem**:

		public string UserId { get; set; }

	>[AZURE.NOTE] Para fazer com que esse modelo de dados altere e mantenha os dados existentes no banco de dados, você deve usar as [Migrações Code First](/pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations).

2. No Visual Studio, expanda a pasta Controladores e abra **TodoItemController.cs**. Localize o método **PostTodoItem** e adicione o seguinte código no começo do método. Esse código adiciona a ID de usuário do usuário autenticado para o item, antes que ele seja inserido na tabela TodoItem.

			// Get the logged in user
			var currentUser = User as ServiceUser;

			// Set the user ID on the item
			item.UserId = currentUser.Id;

3. Localize o método **GetAllTodoItems** e substitua a instrução **return** existente com a seguinte linha de código: Essa consulta filtra os objetos de TodoItem retornados para que cada usuário receba apenas os itens inseridos por ele.

				// Get the logged in user
				var currentUser = User as ServiceUser;

				return Query().Where(todo => todo.UserId == currentUser.Id);

4. Republique o projeto de serviço móvel no Azure.


## <a name="test-app"></a>Testar o aplicativo

1. Observe que, quando você executar agora seu aplicativo do cliente, embora já existam itens no banco de dados de tutoriais anteriores, nenhum item é retornado. Isso acontece porque os itens anteriores foram inseridos sem a coluna ID de usuário e agora têm valores nulos.

2. Se você tiver contas de logon adicionais, verifique se os usuários podem ver apenas seus próprios dados fechando e excluindo o aplicativo e executando novamente. Quando a caixa de diálogo de credenciais de logon for exibida, insira um logon diferente e verifique se os itens inseridos no logon anterior não são exibidos.



<!-- Anchors. -->
[Registrar scripts de servidor]: #register-scripts
[Próximas etapas]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Adicionar autenticação ao aplicativo de serviços móveis existentes]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push

[Referência conceitual do tutorial de .NET de Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/

<!--HONumber=45--> 
