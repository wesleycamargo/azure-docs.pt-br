<properties 
	pageTitle="Introdução à autenticação (Xamarin.Android) - serviços móveis" 
	description="Aprenda a usar a autenticação em seu aplicativo de serviços móveis do Azure para Android Xamarin." 
	services="mobile-services" 
	documentationCenter="xamarin" 
	manager="dwrede" 
	authors="lindydonna" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/14/2015" 
	ms.author="donnam"/>

# Adicionar autenticação ao aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

<p>Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure do seu aplicativo Xamarin.Android. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.</p>

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1. [Registrar seu aplicativo para autenticação e configurar os Serviços Móveis]
2. [Restringir permissões de tabela para usuários autenticados]
3. [Adicionar autenticação ao aplicativo]

Este tutorial baseia-se no início rápido dos Serviços Móveis. Primeiramente, você também deve concluir o tutorial [Introdução aos Serviços Móveis].

Para concluir este tutorial, é necessário ter Xamarin.Android e SDK do Android 4.2 ou uma versão posterior.

##<a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões a usuários autenticados


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. No Eclipse, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis]. 

4. No menu **Executar**, clique em **Executar** para iniciar o aplicativo. Verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é acionada depois que o aplicativo é iniciado.

	 Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela _TodoItem_ agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

1. Adicione a seguinte propriedade à classe **ToDoActivity**:

		private MobileServiceUser user;

2. Adicione o método a seguir à classe **ToDoActivity**:

        private async Task Authenticate()
        {
            try
            {
                user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.MicrosoftAccount);
                CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
            }
            catch (Exception ex)
            {
                CreateAndShowDialog(ex, "Authentication failed");
            }
        }

    Isso cria um novo método para manipular o processo de autenticação. O usuário é autenticado usando um logon da Conta da Microsoft. Será exibida uma caixa de diálogo que exibe a ID do usuário autenticado. Você não pode continuar sem uma autenticação positiva.

    > [AZURE.NOTE]Se você estiver usando um provedor de identidade que não seja da Microsoft, altere o valor passado para o método **login** acima para um dos seguintes: _Facebook_, _Google_, _Twitter_, ou _WindowsAzureActiveDirectory_.

3. No método **OnCreate**, adicione a linha de código a seguir após o código que cria uma instância do objeto `MobileServiceClient`.

		await Authenticate();

	Essa chamada inicia o processo de autenticação e a espera de forma assíncrona.

4. Mova o código restante após `await Authenticate();` no método **OnCreate** para um novo método **CreateTable** parecido com o seguinte:

        private async Task CreateTable()
        {
            
            await InitLocalStoreAsync();

            // Get the Mobile Service Table instance to use
            toDoTable = client.GetTable<ToDoItem>();

            textNewToDo = FindViewById<EditText>(Resource.Id.textNewToDo);

            // Create an adapter to bind the items with the view
            adapter = new ToDoItemAdapter(this, Resource.Layout.Row_List_To_Do);
            var listViewTodo = FindViewById<ListView>(Resource.Id.listViewToDo);
            listViewTodo.Adapter = adapter;

            // Load the items from the Mobile Service
            await RefreshItemsFromTableAsync();
        }

5. Chame o novo método **CreateTable** em **OnCreate** depois que a chamada **Autenticar** foi adicionada na etapa 2:

		await CreateTable();


6. No menu **Executar**, clique em **Executar** para iniciar o aplicativo e entrar com seu provedor de identidade escolhido.

   	Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.

## Obtenha o exemplo concluído
Baixe o [o projeto de exemplo concluído]. Lembre-se de atualizar as variáveis **applicationURL** e **applicationKey** com suas próprias configurações do Azure.

## <a name="next-steps"></a>Próximas etapas

No próximo tutorial, [Autorizar usuários com scripts], você irá obter o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usar para filtrar os dados retornados pelos Serviços Móveis.

<!-- Anchors. -->
[Registrar seu aplicativo para autenticação e configurar os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-service-uri.png

[13]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Autorizar usuários com scripts]: mobile-services-javascript-backend-service-side-authorization.md
[Azure Management Portal]: https://manage.windowsazure.com/
[o projeto de exemplo concluído]: http://go.microsoft.com/fwlink/p/?LinkId=331328
 

<!---HONumber=July15_HO2-->