<properties linkid="develop-mobile-tutorials-sso-with-adal" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc" />

# Autenticar o seu aplicativo com Logon Único da biblioteca de autenticação do diretório ativo

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="C# da Windows Store" class="current">C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" >iOS</a>
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS">Xamarin.iOS</a>
</div>

Neste tutorial, adicione a autenticação ao projeto de início rápido usando uma Biblioteca de autenticação do diretório ativo.

Para poder autenticar os usuários, deve-se registrar o seu aplicativo com o Azure Active Directory (AAD). Isso é feito em duas etapas. Primeiro, deve-se registrar o seu serviço móvel e expor nele as permissões. Segundo, deve-se registrar o seu aplicativo Windows Store e liberar o acesso a essas permissões.

> [WACOM.NOTE] O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem efetuar autenticação do Azure Active Directory em logon único para os aplicativos da Windows Store. Se esta for sua primeira experiência com os Serviços Móveis, conclua o tutorial [Introdução aos serviços móveis][Introdução aos serviços móveis].

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Registrar o seu serviço móvel com o Active Directory do Azure][Registrar o seu serviço móvel com o Active Directory do Azure]
2.  [Registrar o seu aplicativo com o Azure Active Directory][Registrar o seu aplicativo com o Azure Active Directory]
3.  [Configurar o serviço móvel para requerer autenticação][Configurar o serviço móvel para requerer autenticação]
4.  [Adicionar o código de autenticação ao aplicativo do cliente][Adicionar o código de autenticação ao aplicativo do cliente]
5.  [Testar o cliente usando autenticação][Testar o cliente usando autenticação]

Este tutorial exige o seguinte:

-   Visual Studio 2013 em execução no Windows 8.1.
-   Conclusão do tutorial [Introdução aos Serviços Móveis][Introdução aos serviços móveis] ou [Introdução aos Dados][Introdução aos Dados].
-   Pacote NuGet do SDK de Serviços Móveis do Microsoft Azure
-   Pacote NuGet da biblioteca de autenticação do diretório ativo

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

## <a name="register-app-aad"></a>Registrar o seu aplicativo com o Azure Active Directory

Para registrar o aplicativo com o Azure Active Directory, deve-se associá-lo ao Windows Store e possuir um identificador de segurança de pacote (SID) para o aplicativo. O pacote SID é registrado com as configurações do aplicativo nativo no Azure Active Directory.

### Associar o aplicativo ao novo nome do app store

1.  No Visual Studio, clique com o botão direito do mouse no projeto do aplicativo do cliente e clique em **Store** e **Associar o aplicativo ao Store**

    ![][0]

2.  Entre na sua conta do Dev Center.

3.  Digite o nome do aplicativo que deseja reservar para o aplicativo e clique em **Reservar**.

    ![][1]

4.  Selecione o novo nome do aplicativo e clique em **Avançar**.

5.  Clique em **Associar** para associar o aplicativo ao nome do repositório.

### Recupere o pacote SID para o seu aplicativo.

Agora é necessário recuperar o seu pacote SID que será configurado com as configurações do aplicativo nativo.

1.  Faça logon no [Painel do Dev Center do Windows][Painel do Dev Center do Windows] e clique em **Editar** no aplicativo.

    ![][2]

2.  Clique em **Serviços**

    ![][3]

3.  Em seguida, clique em **Site de serviços dinâmicos**.

    ![][4]

4.  Copie o seu pacote SID na parte superior da pagina.

    ![][5]

### Crie o registro do aplicativo nativo

1.  Navegue até **Active Directory** no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure] e clique no seu diretório.

    ![][6]

2.  Clique na guia **Aplicativos** na parte superior e clique para **ADICIONAR** um aplicativo.

    ![][7]

3.  Clique em **Adicionar um aplicativo que a minha organização está desenvolvendo**.

4.  No Assistente para Adicionar aplicativo, insira um **Nome** para o seu aplicativo e clique no tipo **Aplicativo Cliente Nativo**. Em seguida, clique em continuar.

    ![][8]

5.  Na caixa **Redirecionar URI**, cole a SID do pacote do aplicativo que você copiou anteriormente e clique para concluir o registro do aplicativo nativo.

    ![][9]

6.  Clique na guia **Configurar** para o aplicativo nativo e copie a **ID do Cliente**. Você precisará dessas informações posteriormente.

    ![][10]

7.  Role a página para baixo até a seção **permissões para outros aplicativos** e dê acesso completo ao aplicativo do serviço móvel registrado anteriormente. Em seguida, clique em **Salvar**

    ![][11]

O seu serviço móvel agora está configurado no AAD para receber logons únicos do seu aplicativo.

## <a name="require-authentication"></a>Configurar o serviço móvel para requerer autenticação

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="require-authentication"></a>Adicionar o código de autenticação ao aplicativo do cliente

1.  Abra o seu projeto do aplicativo do cliente na Windows Store no Visual Studio.

[WACOM.INCLUDE [mobile-services-dotnet-adal-install-nuget](../includes/mobile-services-dotnet-adal-install-nuget.md)]

1.  Na janela Gerenciador de Soluções do Visual Studio, abra o arquivo MainPage.xaml.cs e adicione o seguinte usando as instruções.

        using Windows.UI.Popups;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Newtonsoft.Json.Linq;

2.  Adicione os códigos a seguir para a classe MainPage, que declara o método `AuthenticateAsync`.

        private MobileServiceUser user; 
        private async Task AuthenticateAsync()
        {
            string authority = "<INSERT-AUTHORITY-HERE>";
            string resourceURI = "<INSERT-RESOURCE-URI-HERE>";
            string clientID = "<INSERT-CLIENT-ID-HERE>"; 
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceURI, clientID);
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                  message = "You must log in. Login Required";
                } 
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            } 
        }

3.  No código para o método acima `AuthenticateAsync`, substitua **INSIRA-AUTORIDADE-AQUI** pelo o nome do locatário no qual o seu aplicativo foi provisionado. O formato deve ser <https://login.windows.net/tenant-name.onmicrosoft.com>. Este valor pode ser copiado da guia Domínio no Azure Active Directory no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

4.  No código para o método `AuthenticateAsync` acima, substitua **INSIRA-RECURSO-URI-AQUI** com o **URI do ID do aplicativo** para o seu serviço móvel. Se o tópico [Como registrar-se com o Azure Active Directory][Como registrar-se com o Azure Active Directory] foi seguido, a sua URI do ID do aplicativo deve ser semelhante a <https://todolist.azure-mobile.net/login/aad>.

5.  No código, para o método `AuthenticateAsync` acima, substitua **INSIRA-ID-DO-CLIENTE-AQUI** com o ID do cliente copiado do aplicativo do cliente nativo.

6.  Na janela Gerenciador de Soluções do Visual Studio, abra o arquivo Package.appxmanifest no projeto do cliente. Clique na guia **Recursos** e habilite **Aplicativo corporativo** e **Redes particulares (cliente e servidor)**. Salve o arquivo.

    ![][12]

7.  No arquivo MainPage.cs, atualize o manipulador de eventos `OnNavigatedTo` para chamar o método `AuthenticateAsync` como mostrado a seguir.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (user == null)
                await AuthenticateAsync();

            RefreshTodoItems();
        }

## <a name="test-client"></a>Testar o cliente usando autenticação

1.  No Visual Studio, execute o aplicativo do cliente.
2.  Você receberá uma solicitação para fazer logon no seu Azure Active Directory.
3.  O aplicativo autentica e retorna os itens todo.

    ![][13]


<!-- Images -->


  [Introdução aos serviços móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Registrar o seu serviço móvel com o Active Directory do Azure]: #register-mobile-service-aad
  [Registrar o seu aplicativo com o Azure Active Directory]: #register-app-aad
  [Configurar o serviço móvel para requerer autenticação]: #require-authentication
  [Adicionar o código de autenticação ao aplicativo do cliente]: #add-authentication-code
  [Testar o cliente usando autenticação]: #test-client
  [Introdução aos Dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  [0]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-associate-app.png
  [1]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-reserve-store-appname.png
  [Painel do Dev Center do Windows]: http://go.microsoft.com/fwlink/p/?LinkID=266734
  [2]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-edit.png
  [3]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-services.png
  [4]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-live-services-site.png
  [5]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-package-sid.png
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [6]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-select-aad.png
  [7]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-applications-tab.png
  [8]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-selection.png
  [9]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-sid-redirect-uri.png
  [10]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-client-id.png
  [11]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-add-permissions.png
  [Como registrar-se com o Azure Active Directory]: /pt-br/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [12]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-package-appxmanifest.png
  [13]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-app-run.png
