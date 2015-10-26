<properties
	pageTitle="Autenticar seu aplicativo com logon único da biblioteca de autenticação do Active Directory (Windows Store) | Microsoft Azure"
	description="Saiba como autenticar usuários para logon único com ADAL em seu aplicativo da Windows Store."
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/18/2015" 
	ms.author="wesmc"/>

# Autenticar o seu aplicativo com Logon Único da biblioteca de autenticação do diretório ativo

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

##Visão geral

Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando a biblioteca de autenticação do Active Directory para dar suporte a [operações de logon direcionadas pelo cliente](http://msdn.microsoft.com/library/azure/jj710106.aspx) com o Active Directory do Azure. Para dar suporte a [operações de logon direcionadas pelo serviço](http://msdn.microsoft.com/library/azure/dn283952.aspx) com o Active Directory do Azure, comece pelo tutorial [Adicionar autenticação ao aplicativo de Serviços Móveis](../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md).

Para poder autenticar os usuários, deve-se registrar o seu aplicativo com o Azure Active Directory (AAD). Isso é feito em duas etapas. Primeiro, deve-se registrar o seu serviço móvel e expor nele as permissões. Segundo, deve-se registrar o seu aplicativo Windows Store e liberar o acesso a essas permissões.


>[AZURE.NOTE]Este tutorial destina-se a ajudá-lo a compreender melhor como os serviços móveis permitem um logon único de autenticação no Active Directory do Azure para aplicativos da Windows Store usando uma [operação de logon direcionada pelo cliente](http://msdn.microsoft.com/library/azure/jj710106.aspx). Se esta for sua primeira experiência com os Serviços Móveis, conclua o tutorial [Introdução aos serviços móveis].


##Pré-requisitos

Este tutorial exige o seguinte:

* Visual Studio 2013 em execução no Windows 8.1.
* Conclusão do tutorial [Introdução aos Serviços Móveis]
* Pacote NuGet do SDK de Serviços Móveis do Microsoft Azure
* Pacote NuGet da biblioteca de autenticação do diretório ativo

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-service](../../includes/mobile-services-dotnet-adal-register-service.md)]

##Registrar o seu aplicativo com o Azure Active Directory

Para registrar o aplicativo com o Azure Active Directory, deve-se associá-lo ao Windows Store e possuir um identificador de segurança de pacote (SID) para o aplicativo. O pacote SID é registrado com as configurações do aplicativo nativo no Azure Active Directory.


###Associar o aplicativo ao novo nome do app store

1. No Visual Studio, clique com o botão direito do mouse no projeto do aplicativo do cliente e clique em **Store** e **Associar o aplicativo ao Store**

    ![][1]

2. Entre na sua conta do Dev Center.

3. Digite o nome do aplicativo que deseja reservar para o aplicativo e clique em **Reservar**.

    ![][2]

4. Selecione o novo nome do aplicativo e clique em **Avançar**.

5. Clique em **Associar** para associar o aplicativo ao nome do repositório.


###Recupere o pacote SID para o seu aplicativo.

Agora é necessário recuperar o seu pacote SID que será configurado com as configurações do aplicativo nativo.

1. Faça logon no [Painel do Dev Center do Windows] e clique em **Editar** no aplicativo.

    ![][3]

2. Clique em **Serviços**

    ![][4]

3. Em seguida, clique em **Site de serviços dinâmicos**.

    ![][5]

4. Copie o seu pacote SID na parte superior da pagina.

    ![][6]

###Crie o registro do aplicativo nativo

1. Navegue até **Active Directory** no [Portal de Gerenciamento do Azure] e clique no seu diretório.

    ![][7]

2. Clique na guia **Aplicativos** na parte superior e clique para **ADICIONAR** um aplicativo.

    ![][8]

3. Clique em **Adicionar um aplicativo que a minha organização está desenvolvendo**.

4. No Assistente para Adicionar aplicativo, insira um **Nome** para o seu aplicativo e clique no tipo **Aplicativo Cliente Nativo**. Em seguida, clique em continuar.

    ![][9]

5. Na caixa **Redirecionar URI**, cole a SID do pacote do aplicativo que você copiou anteriormente e clique para concluir o registro do aplicativo nativo.

    ![][10]

6. Clique na guia **Configurar** para o aplicativo nativo e copie a **ID do Cliente**. Você precisará dessas informações posteriormente.

    ![][11]

7. Role a página para baixo até a seção **permissões para outros aplicativos** e dê acesso completo ao aplicativo do serviço móvel registrado anteriormente. Em seguida, clique em **Salvar**

    ![][12]

O seu serviço móvel agora está configurado no AAD para receber logons únicos do seu aplicativo.



##Configurar o serviço móvel para requerer autenticação

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

##Adicionar o código de autenticação ao aplicativo do cliente

1. Abra o seu projeto do aplicativo do cliente na Windows Store no Visual Studio.

[AZURE.INCLUDE [mobile-services-dotnet-adal-install-nuget](../../includes/mobile-services-dotnet-adal-install-nuget.md)]

4. Na janela Gerenciador de Soluções do Visual Studio, abra o arquivo MainPage.xaml.cs e adicione o seguinte usando as instruções.

        using Windows.UI.Popups;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Newtonsoft.Json.Linq;


5. Adicione o código a seguir para a classe MainPage, que declara o método `AuthenticateAsync`.

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
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceURI, clientID, (Uri) null);
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

6. No código para o método `AuthenticateAsync` acima, substitua **INSERIR-AUTORIDADE-AQUI** pelo nome do locatário em que você provisionou seu aplicativo, o formato deve ser https://login.windows.net/tenant-name.onmicrosoft.com. Este valor pode ser copiado da guia Domínio no Azure Active Directory no [Portal de Gerenciamento do Azure].

7. No código para o método `AuthenticateAsync` acima, substitua **INSIRA-RECURSO-URI-AQUI** com o **URI do ID do aplicativo** para o seu serviço móvel. Se seguiu o tópico [Como se registrar com o Active Directory do Azure], sua URI da ID do aplicativo deverá ser semelhante a https://todolist.azure-mobile.net/login/aad.

8. No código para o método `AuthenticateAsync` acima, substitua **INSERIR-ID-DO-CLIENTE-AQUI** pela ID do cliente copiada do aplicativo cliente nativo.

9. Na janela Gerenciador de Soluções do Visual Studio, abra o arquivo Package.appxmanifest no projeto do cliente. Clique na guia **Recursos** e habilite **Aplicativo corporativo** e **Redes particulares (cliente e servidor)**. Salve o arquivo.

    ![][14]

10. No arquivo MainPage.cs, atualize o manipulador de eventos `OnNavigatedTo` para chamar o método `AuthenticateAsync` como mostrado a seguir.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            await RefreshTodoItems();
        }


##Testar o cliente usando autenticação

1. No Visual Studio, execute o aplicativo do cliente.
2. Você receberá uma solicitação para fazer logon no seu Active Directory do Azure.  
3. O aplicativo autentica e retorna os itens todo.

    ![][15]




<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-app-manage-manifest.png
[1]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-vs-associate-app.png
[2]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-vs-reserve-store-appname.png
[3]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-edit.png
[4]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-services.png
[5]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-live-services-site.png
[6]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-package-sid.png
[7]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-select-aad.png
[8]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-aad-applications-tab.png
[9]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-selection.png
[10]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-sid-redirect-uri.png
[11]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-client-id.png
[12]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-add-permissions.png
[14]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-package-appxmanifest.png
[15]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-app-run.png

<!-- URLs. -->
[Como se registrar com o Active Directory do Azure]: mobile-services-how-to-register-active-directory-authentication.md
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Introdução aos serviços móveis]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Painel do Dev Center do Windows]: http://go.microsoft.com/fwlink/p/?LinkID=266734

<!---HONumber=Oct15_HO3-->