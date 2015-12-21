<properties 
	pageTitle="Introdução à autenticação para aplicativos móveis no Xamarin Android" 
	description="Aprenda a usar os aplicativos móveis para autenticar usuários de seu aplicativo Xamarin Android por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="mahender"/>

# Adicione autenticação ao aplicativo Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Este tópico mostra como autenticar usuários de um aplicativo móvel em seu aplicativo cliente. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Aplicativos Móveis do Azure. Após ser autenticado e autorizado com sucesso no aplicativo móvel, o valor da ID de usuário é exibido.

Este tutorial baseia-se no início rápido do aplicativo móvel. Você também deve primeiro concluir o tutorial [Criar um aplicativo Xamarin.Android]. Se você não usar o projeto baixado de início rápido do servidor, deve adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register"></a>Registrar seu aplicativo para autenticação e configurar os Serviços de Aplicativos

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

No Visual Studio ou Xamarin Studio, execute o projeto cliente em um dispositivo ou emulador. Verifique se uma exceção não tratada com um código de status 401 (Não autorizado) é gerada após o aplicativo ser iniciado. Isso acontece porque o aplicativo tenta acessar o back-end do aplicativo móvel como um usuário não autenticado. A tabela *TodoItem* agora exige autenticação.

Em seguida, você atualizará o aplicativo do cliente para solicitar recursos do back-end do aplicativo móvel com um usuário autenticado.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

O aplicativo é atualizado para exigir que os usuários toquem no botão **Entrar** e se autentiquem para que os dados sejam exibidos.

1. Adicione o seguinte código à classe **TodoActivity**:

	    // Define a authenticated user.
	    private MobileServiceUser user;
	    private async Task<bool> Authenticate()
	    {
	            var success = false;
	            try
	            {
	                // Sign in with Facebook login using a server-managed flow.
	                user = await client.LoginAsync(this,
	                    MobileServiceAuthenticationProvider.Facebook);
	                CreateAndShowDialog(string.Format("you are now logged in - {0}",
	                    user.UserId), "Logged in!");
	
	                success = true;
	            }
	            catch (Exception ex)
	            {
	                CreateAndShowDialog(ex, "Authentication failed");
	            }
	            return success;
	    }

        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds. 
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;

                // Load the data.
                OnRefreshItemsSelected();
            }
        }

    Isso cria um novo método para autenticar um usuário e um manipulador de método para um novo botão **Entrar**. O usuário no código de exemplo acima é autenticado usando um logon do Facebook. Uma caixa de diálogo é usada para exibir a ID de usuário após a autenticação.

    > [AZURE.NOTE]Se estiver usando um provedor de identidade que não seja o Facebook, altere o valor transmitido para **LoginAsync** acima para um dos seguintes: _MicrosoftAccount_, _Twitter_, _Google_ ou _WindowsAzureActiveDirectory_.

3. No método **OnCreate**, exclua ou comente a linha de código a seguir:

		OnRefreshItemsSelected ();

4. No arquivo Activity\_To\_Do.axml, adicione a definição do botão *LoginUser* a seguir antes do botão *AddItem* existente:

      	<Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />

5. Adicione o elemento a seguir no arquivo de recursos de Strings.xml:

		<string name="login_button_text">Sign in</string> 

6. No Visual Studio ou Xamarin Studio, execute o projeto cliente em um dispositivo ou emulador e entre com o seu provedor de identidade preferido.

   	Quando você entrar com êxito, o aplicativo exibirá o sua ID de logon e a lista de itens de tarefas e você poderá fazer atualizações aos dados.


<!-- URLs. -->
[Criar um aplicativo Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
 

<!---HONumber=AcomDC_1210_2015-->