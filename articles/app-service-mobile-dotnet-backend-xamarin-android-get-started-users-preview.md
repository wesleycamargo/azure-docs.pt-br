<properties 
	pageTitle="Introdução à autenticação para aplicativos móveis no Xamarin Android" 
	description="Aprenda a usar os aplicativos móveis para autenticar usuários de seu aplicativo Xamarin Android por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/22/2015" 
	ms.author="mahender"/>

# Adicionar autenticação ao seu aplicativo Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../includes/app-service-mobile-selector-get-started-users.md)]

Este tópico mostra como autenticar usuários de um aplicativo móvel do serviço de aplicativo usando o seu aplicativo cliente.  Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade ao qual o Serviço de Aplicativo dá suporte.  Após obter sucesso em ser autenticado e autorizado pelo seu aplicativo móvel, o valor de identificação de usuário é exibido.

Este tutorial se baseia no início rápido do aplicativo móvel.  Você também deve primeiro concluir o tutorial [Criar um aplicativo Xamarin.Android]. 

##<a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços de Aplicativos

[AZURE.INCLUDE [app-service-mobile-register-authentication](../includes/app-service-mobile-register-authentication.md)] 

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

<ol start="7">
<li><p>No Visual Studio ou Xamarin Studio, execute o projeto cliente em um dispositivo ou emulador. Verifique se uma exceção sem tratamento com um código de status 401 (Não autorizado) é gerada após o aplicativo ser iniciado.</p>
   
   	<p>Isso acontece porque o aplicativo tenta acessar o Código de Aplicativo Móvel como um usuário não autenticado, mas a tabela <em>TodoItem</em> agora exige autenticação.</p></li>
</ol>

Em seguida, você irá atualizar o aplicativo para autenticar os usuários antes de solicitar recursos do seu Serviço de Aplicativo.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

1. Adicione a seguinte propriedade à classe **TodoActivity**:

			private MobileServiceUser user;

2. Adicione o seguinte método à classe **TodoActivity**: 

	        private async Task Authenticate()
	        {
	            try
	            {
	                user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.Facebook);
	                CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
	            }
	            catch (Exception ex)
	            {
	                CreateAndShowDialog(ex, "Authentication failed");
	            }
	        }

    Isso cria um novo método para manipular o processo de autenticação.  O usuário é autenticado usando um logon do Facebook.  Será exibida uma caixa de diálogo que exibe a ID do usuário autenticado. 

    > [AZURE.NOTE] Se você estiver usando um provedor de identidade diferente do Facebook, altere o valor passado ao **LoginAsync** acima para um dos seguintes:  _MicrosoftAccount_, _Twitter__, Google_ ou _MicrosoftAzureActiveDirectory_.

3. No método **OnCreate** adicione a linha de código a seguir após o código que cria uma instância do objeto  `MobileServiceClient`.

		// Get the Mobile App Table instance to use
        toDoTable = client.GetTable <ToDoItem> ();

        await Authenticate(); // add this line

	Essa chamada inicia o processo de autenticação e a espera de forma assíncrona.


4. No menu **Executar**, clique em **Executar** para iniciar o aplicativo e entrar com seu provedor de identidade. 

   	Quando você entrar com êxito, o aplicativo exibirá a lista de itens de tarefas e você poderá fazer atualizações aos dados.


<!-- URLs. -->
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Criar um aplicativo Xamarin.Android]: app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md

[Portal de Gerenciamento do Azure]: https://portal.azure.com

<!--HONumber=49-->