<properties 
	pageTitle="Autorização no lado do serviço (Windows Store) | Centro de Desenvolvimento de Serviços Móveis" 
	description="Saiba como autorizar usuários no back-end .NET dos Serviços Móveis do Azure." 
	services="" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="glenga"/>

# Autorização de atendimento a usuários dos Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]


Este tópico mostra como autorizar os usuários autenticados para acessar dados nos Serviços Móveis do Azure em um aplicativo da Windows Store. Neste tutorial, você adiciona código aos métodos de acesso a dados em seu controlador que filtra consultas com base na userId de um usuário autenticado, garantindo que cada usuário possa ver apenas seus próprios dados.

Este tutorial baseia-se no início rápido dos Serviços Móveis e no tutorial anterior [Introdução à autenticação]. Antes de iniciar este tutorial, você deve primeiro concluir o tutorial [Introdução à autenticação].  

## <a name="register-scripts"></a>Modificar os métodos de acesso a dados

[AZURE.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)] 


## Testar o aplicativo

1. No Visual Studio, abra o projeto modificado quando você concluiu o tutorial [Introdução à autenticação].

2. Pressione a tecla F5 para executar o aplicativo e fazer o logon com seu provedor de identidade. 

   	Observe que, desta vez, embora já existam itens na tabela TodoItem de tutoriais anteriores, nenhum item é retornado. Isso acontece porque os itens anteriores foram inseridos sem a coluna userId e agora têm valores nulos.

3. No aplicativo, insira o texto em **Inserir um TodoItem** e clique em **Salvar**.

   	![][3]

   	Isso insere o texto e a userId na tabela TodoItemno serviço móvel. Como o novo item tem o valor userId correto, ele é retornado pelo serviço móvel e exibido na segunda coluna.

6. (Opcional) Se tiver contas de logon adicionais, você poderá verificar se os usuários podem ver apenas seus próprios dados, fechando o aplicativo (Alt + F4) e, em seguida, executando-o novamente. Quando a caixa de diálogo de credenciais de logon for exibida, insira um logon diferente e, em seguida, verifique se os itens inseridos na conta anterior não são exibidos. 

## Próximas etapas

Isso conclui os tutoriais que demonstram os conceitos básicos de como trabalhar com autenticação. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.
  
* [Como usar um cliente HTML/JavaScript para Serviços Móveis do Azure]
  <br/>Saiba mais sobre como usar os Serviços Móveis com JavaScript e HTML.

<!-- Anchors. -->
[Registrar scripts de servidor]: #register-scripts
[Próximas etapas]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started
[Introdução aos dados]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
[Introdução à autenticação]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
[Introdução às notificações por push]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push

[Como usar um cliente HTML/JavaScript para Serviços Móveis do Azure]: /en-us/documentation/articles/mobile-services-html-how-to-use-client-library
\n<!--HONumber=42-->
