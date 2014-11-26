<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-android" urlDisplayName="Get Started with Authentication (Xamarin.Android)" pageTitle="Get started with authentication (Xamarin.Android) - Mobile Services" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services Xamarin.Android" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.Android." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with authentication in Mobile Services" services="mobile-services" documentationCenter="Mobile" manager="dwrede" authors="donnam"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam" />

# Introdução à autenticação dos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure do seu aplicativo Xamarin.Android. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1.  [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis][Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]
2.  [Restringir permissões de tabela para usuários autenticados][Restringir permissões de tabela para usuários autenticados]
3.  [Adicionar autenticação ao aplicativo][Adicionar autenticação ao aplicativo]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

Para concluir este tutorial, é necessário ter Xamarin.Android e SDK do Android 4.2 ou uma versão posterior.

## <a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Registrar seu aplicativo para autenticação e configurar os Serviços Móveis

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a><span class="short-header">Restringir permissões</span>Restringir permissões para usuários autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  No Eclipse, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

2.  No menu **Executar**, clique em **Executar** para iniciar o aplicativo. Verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é acionada depois que o aplicativo é iniciado.

    Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

## <a name="add-authentication"></a><span class="short-header">Adicionar autenticação</span>Adicionar autenticação ao aplicativo

1.  Adicione a seguinte propriedade à classe **TodoActivity**:

            private MobileServiceUser user;

2.  Adicione o seguinte método à classe **TodoActivity**:

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

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Se estiver usando um provedor de identidade diferente do Facebook, altere o valor passado para o m&eacute;todo <strong>logon</strong> acima para um dos seguintes: <i>Facebook</i>, <i>Google</i>, <i>Twitter</i> ou <i>WindowsAzureActiveDirectory</i>.</p>
</div>

3.  No método **OnCreate**, adicione a linha de código a seguir após o código que cria uma instância do objeto `MobileServiceClient`.

        await Authenticate();

    Essa chamada inicia o processo de autenticação e a espera de forma assíncrona.

4.  Mova o código restante após `await Authenticate();` no método **OnCreate** para um novo método **CreateTable** parecido com o seguinte:

            private async Task CreateTable()
            {
                // Get the Mobile Service Table instance to use
                todoTable = client.GetTable<TodoItem>();

                textNewTodo = FindViewById<EditText>(Resource.Id.textNewTodo);

                // Create an adapter to bind the items with the view
                adapter = new TodoItemAdapter(this, Resource.Layout.Row_List_To_Do);
                var listViewTodo = FindViewById<ListView>(Resource.Id.listViewTodo);
                listViewTodo.Adapter = adapter;

                // Load the items from the Mobile Service
                await RefreshItemsFromTableAsync();
            }

5.  Chame o novo método **CreateTable** em **OnCreate** depois que a chamada **Autenticar** foi adicionada na etapa 2:

        await CreateTable();

6.  No menu **Executar**, clique em **Executar** para iniciar o aplicativo e entrar com seu provedor de identidade escolhido.

    Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.

## Obtenha o exemplo concluído

Baixe o [o projeto de exemplo concluído][o projeto de exemplo concluído]. Lembre-se de atualizar as variáveis **applicationURL** e **applicationKey** com suas próprias configurações do Azure.

## <a name="next-steps"></a>Próximas etapas

No próximo tutorial, [Autorizar usuários com scripts][Autorizar usuários com scripts], você irá obter o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usar para filtrar os dados retornados pelos Serviços Móveis.

 
 


  [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]: #register
  [Restringir permissões de tabela para usuários autenticados]: #permissions
  [Adicionar autenticação ao aplicativo]: #add-authentication
  [Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-xamarin-android
  [o projeto de exemplo concluído]: http://go.microsoft.com/fwlink/p/?LinkId=331328
  [Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
