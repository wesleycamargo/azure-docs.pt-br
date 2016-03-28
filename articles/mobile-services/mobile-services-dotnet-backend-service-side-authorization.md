<properties
	pageTitle="Autorização de usuários no lado do serviço em um serviço móvel de back-end do .NET | Microsoft Azure"
	description="Aprenda a restringir o acesso para autorizar usuários em um serviço móvel de back-end do .NET"
	services="mobile-services"
	documentationCenter="windows"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.topic="article"
	ms.devlang="dotnet"
	ms.date="03/09/2016"
	ms.author="krisragh"/>

# Autorização de serviço de usuários nos Serviços Móveis
> [AZURE.SELECTOR]
- [Back-end do .NET](mobile-services-dotnet-backend-service-side-authorization.md)
- [Back-end do Javascript](mobile-services-javascript-backend-service-side-authorization.md)

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para a versão equivalente aos Aplicativos Móveis deste tópico, confira [Como restringir o acesso a dados para usuários autorizados](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#authorize) no tópico Trabalhar com o SDK do servidor de back-end do .NET para Aplicativos Móveis do Azure.

Este tópico mostra como usar a lógica de servidor para autorizar usuários. Neste tutorial, você modifica os controladores de tabela, filtra consultas com base nas IDs de usuário e concede acesso aos usuários apenas para seus próprios dados. Filtrar os resultados da consulta do usuário pela ID de usuário é a forma mais básica de autorização. Dependendo do seu cenário específico, você também poderá criar tabelas de Usuários ou Funções para monitorar informações mais detalhadas de autorização do usuário, como quais pontos de extremidade um determinado usuário tem permissão para acessar.

Esse tutorial se baseia no Início Rápido dos Serviços Móveis e elabora o tutorial [Adicionar autenticação ao aplicativo de Serviços Móveis existentes]. Preencha [Adicionar autenticação ao aplicativo de serviços móveis existente] primeiro.

## <a name="register-scripts"></a>Modificar os métodos de acesso a dados

1. No Visual Studio, abra o projeto móvel, expanda a pasta DataObjects e abra **TodoItem.cs**. A classe **TodoItem** define o objeto de dados, e é necessário adicionar uma propriedade **UserId** a ser usada na filtragem. Adicione as novas propriedades UserId a seguir à classe **TodoItem**:

		public string UserId { get; set; }

	>[AZURE.NOTE] Para fazer com que este modelo de dados altere e mantenha os dados existentes no banco de dados, você deve usar as [Migrações Code First](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

2. No Visual Studio, expanda a pasta Controladores, abra **TodoItemController.cs** e adicione a seguinte instrução using:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

3. Localize o método **PostTodoItem** e adicione o código a seguir no começo do método.

		// Get the logged in user
		var currentUser = User as ServiceUser;

		// Set the user ID on the item
		item.UserId = currentUser.Id;

	Esse código adiciona a ID de usuário do usuário autenticado para o item, antes que ele seja inserido na tabela TodoItem.

3. Localize o método **GetAllTodoItems** e substitua a instrução **return** existente pela seguinte linha de código:

		// Get the logged in user
		var currentUser = User as ServiceUser;

		return Query().Where(todo => todo.UserId == currentUser.Id);

	Essa consulta filtra os objetos de TodoItem retornados para que cada usuário receba apenas os itens inseridos por ele.

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
[Adicionar autenticação ao aplicativo de Serviços Móveis existentes]: mobile-services-dotnet-backend-ios-get-started-users.md
[Adicionar autenticação ao aplicativo de serviços móveis existente]: mobile-services-dotnet-backend-ios-get-started-users.md

<!---HONumber=AcomDC_0316_2016-->