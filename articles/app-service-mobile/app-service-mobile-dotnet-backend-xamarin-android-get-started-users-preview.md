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
	ms.date="06/23/2015" 
	ms.author="mahender"/>

# Adicione autenticação ao aplicativo Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Este tópico mostra como autenticar usuários de um aplicativo móvel do Serviço de Aplicativo em seu aplicativo cliente. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade que tenha suporte no Serviço de Aplicativo. Após ser autenticado e autorizado com sucesso pelo aplicativo móvel, o valor da ID de usuário é exibido.

Este tutorial baseia-se no início rápido do aplicativo móvel. Você também deve primeiro concluir o tutorial [Criar um aplicativo Xamarin.Android].

##<a name="review"></a>Examine a configuração do projeto do servidor (opcional)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-auth-preview](../../includes/app-service-mobile-dotnet-backend-enable-auth-preview.md)]

##<a name="register"></a>Registrar seu aplicativo para autenticação e configurar os Serviços de Aplicativos

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

<ol start="5">
<li><p>No Visual Studio ou Xamarin Studio, execute o projeto cliente em um dispositivo ou emulador. Verifique se uma exceção não tratada com um código de status 401 (Não autorizado) é gerada após o aplicativo ser iniciado.</p>
   
   	<p>Isso acontece porque o aplicativo tenta acessar o código do aplicativo móvel como um usuário não autenticado, mas a tabela <em>TodoItem</em> agora exige autenticação.</p></li>
</ol>

Em seguida, você atualizará o aplicativo para autenticar usuários antes de solicitar recursos do seu Serviço de Aplicativo.

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

    Isso cria um novo método para manipular o processo de autenticação. O usuário é autenticado usando um logon do Facebook. Será exibida uma caixa de diálogo que exibe a ID do usuário autenticado.

    > [AZURE.NOTE]Se você estiver usando um provedor de identidade que não seja o Facebook, altere o valor passado para **LoginAsync** acima para um dos seguintes: _MicrosoftAccount_, _Twitter_, _Google_ ou _WindowsAzureActiveDirectory_.

3. No método **OnCreate**, adicione a linha de código a seguir após o código que cria uma instância do objeto `MobileServiceClient`.

		// Get the Mobile App Table instance to use
        toDoTable = client.GetTable <ToDoItem> ();

        await Authenticate(); // add this line

	Essa chamada inicia o processo de autenticação e a espera de forma assíncrona.


4. No menu **Executar**, clique em **Executar** para iniciar o aplicativo e entrar com seu provedor de identidade escolhido.

   	Quando você entrar com êxito, o aplicativo exibirá a lista de itens de tarefas e você poderá fazer atualizações aos dados.


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Criar um aplicativo Xamarin.Android]: app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=August15_HO6-->