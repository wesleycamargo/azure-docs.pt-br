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
- [(Any | .NET)](mobile-services-dotnet-backend-service-side-authorization.md)
- [(Any | Javascript)](mobile-services-javascript-backend-service-side-authorization.md)

Este tópico mostra como usar a lógica de servidor para autorizar usuários. Neste tutorial, você modifica os métodos de acesso de dados no .NET, filtra consultas com base nas IDs de usuário e concede acesso aos usuários apenas para seus próprios dados.

Este tutorial se baseia no Início Rápido dos Serviços Móveis e se baseia no tutorial [Adicionar autenticação a um aplicativo de Serviços Móveis existente]. Preencha [Adicionar autenticação ao aplicativo de serviços móveis existente] primeiro.

## <a name="register-scripts"></a>Modificar os métodos de acesso a dados

1. No Visual Studio, abra o projeto móvel, expanda a pasta DataObjects e abra **TodoItem.cs**. A classe **TodoItem** define o objeto de dados, e é necessário adicionar uma propriedade **UserId** a ser usada na filtragem. Adicione as novas propriedades UserId a seguir à classe **TodoItem**:

		public string UserId { get; set; }

	>[AZURE.NOTE]Para fazer com que este modelo de dados altere e mantenha os dados existentes no banco de dados, você deve usar as [Migrações Code First](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

2. No Visual Studio, expanda a pasta Controladores e abra **TodoItemController.cs**. Localize o método **PostTodoItem** e adicione o código a seguir no começo do método. Esse código adiciona a ID de usuário do usuário autenticado para o item, antes que ele seja inserido na tabela TodoItem.

			// Get the logged in user
			var currentUser = User as ServiceUser;

			// Set the user ID on the item
			item.UserId = currentUser.Id;

3. Localize o método **GetAllTodoItems** e substitua a instrução **return** existente pela linha de código a seguir. Essa consulta filtra os objetos de TodoItem retornados para que cada usuário receba apenas os itens inseridos por ele.

				// Get the logged in user
				var currentUser = User as ServiceUser;

				return Query().Where(todo => todo.UserId == currentUser.Id);

4. Republique o projeto de serviço móvel no Azure.


## <a name="test-app"></a>Testar o aplicativo

1. Observe que, quando você executar agora seu aplicativo do cliente, embora já existam itens no banco de dados de tutoriais anteriores, nenhum item é retornado. Isso acontece porque os itens anteriores foram inseridos sem a coluna ID de usuário e agora têm valores nulos.

2. Se você tiver contas de logon adicionais, verifique se os usuários podem ver apenas seus próprios dados fechando e excluindo o aplicativo e executando novamente. Quando a caixa de diálogo de credenciais de logon for exibida, insira um logon diferente e verifique se os itens inseridos no logon anterior não são exibidos.



<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]: #next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Get started with Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Get started with data]: mobile-services-dotnet-backend-ios-get-started-data.md
[Adicionar autenticação a um aplicativo de Serviços Móveis existente]: mobile-services-dotnet-backend-ios-get-started-users.md
[Adicionar autenticação ao aplicativo de serviços móveis existente]: mobile-services-dotnet-backend-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-ios-get-started-push.md

[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md

<!--HONumber=54-->