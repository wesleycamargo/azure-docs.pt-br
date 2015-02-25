<properties 
	pageTitle="Autenticar o seu aplicativo com Logon Único da biblioteca de autenticação do Active Directory (Xamarin.iOS) | Mobile Dev Center" 
	description="Saiba como autenticar usuários para logon único com ADAL em seu aplicativo iOS Xamarin." 
	documentationCenter="xamarin" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="wesmc,mahender"/>

# Autenticar o seu aplicativo com Logon Único da biblioteca de autenticação do diretório ativo

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../includes/mobile-services-selector-adal-sso.md)]

Neste tutorial, adicione a autenticação ao projeto de início rápido usando uma Biblioteca de autenticação do Active Directory. 

Para autenticar usuários, registre seu aplicativo com o Active Directory do Azure (AAD). Isso é feito em duas etapas. Primeiro, deve-se registrar o seu serviço móvel e expor nele as permissões. Em segundo lugar, você deve registrar seu aplicativo Xamarin.iOS e conceder acesso com essas permissões


>[AZURE.NOTE] O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem efetuar autenticação do Active Directory do Azure com logon único para aplicativos Xamarin.iOS. Se esta for sua primeira experiência com os Serviços Móveis, conclua o tutorial [Introdução aos Serviços Móveis].

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Registrar o seu serviço móvel com o Active Directory do Azure]
2. [Registrar seu aplicativo com o Active Directory do Azure] 
3. [Configurar o serviço móvel para exigir autenticação]
4. [Adicionar o código de autenticação ao aplicativo do cliente]
5. [Testar o cliente usando autenticação]

Este tutorial exige o seguinte:

* XCode 4.5 e iOS 6.0 (ou versões posteriores) 
* Visual Studio com a extensão [Xamarin] ou [Xamarin Studio] no OS X
* Conclusão do tutorial [Introdução aos Serviços Móveis] ou [Introdução aos Dados].
* SDK de Serviços Móveis do Microsoft Azure
* Uma associação do Xamarin [ à Biblioteca de Autenticação do Active Directory para iOS].

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../includes/mobile-services-dotnet-adal-register-client.md)]

## <a name="require-authentication"></a>Configurar o serviço móvel para exigir autenticação

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>Adicionar o código de autenticação ao aplicativo do cliente

1. Adicionar sua associação do Xamarin para a Biblioteca de Autenticação do Active Directory ao projeto Xamarin.iOS. No Visual Studio 2013, clique como botão direito do mouse em **Referências** e selecione **Adicionar Referências**. Em seguida, navegue até sua biblioteca associada e clique em **Adicionar**. Não se esqueça de adicionar também storyboards da fonte ADAL.

2. Adicione a seguinte classe QSTodoService: 

        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

        public async Task Authenticate()
        {
            var aadAuthority = @"<INSERT-AUTHORITY-HERE>";
            var aadResource = @"<INSERT-RESOURCE-URI-HERE>";
            var aadClientId = @"<INSERT-CLIENT-ID-HERE>";
            var aadRedirect = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError error;
            var aadContext = ADAuthenticationContext.AuthenticationContextWithAuthority(aadAuthority, out error);
            if (error != null)
            {
                throw new InvalidOperationException("Error creating the AAD context: " + error.ErrorDetails);
            }

            var tcs = new TaskCompletionSource<string>();
            aadContext.AcquireTokenWithResource(aadResource, aadClientId, new NSUrl(aadRedirect), result =>
                {
                    if (result.Status == ADAuthenticationResultStatus.SUCCEEDED)
                    {
                        tcs.SetResult(result.AccessToken);
                    }
                    else
                    {
                        string errorDetails;
                        if (result.Status == ADAuthenticationResultStatus.USER_CANCELLED)
                        {
                            errorDetails = "Authentication cancelled by user";
                        }
                        else
                        {
                            errorDetails = result.Error.ErrorDetails;
                        }

                        tcs.SetException(new InvalidOperationException("Error during authentication: " + errorDetails));
                    }
                });
            string accessToken = await tcs.Task;

            var token = new JObject(new JProperty("access_token", accessToken));

            try
            {
                user = await this.client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, token);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

6. No código para o método `AuthenticateAsync` acima, substitua **INSERT-AUTHORITY-HERE** pelo nome do locatário em que você provisionou seu aplicativo, o formato deve ser https://login.windows.net/tenant-name.onmicrosoft.com. Esse valor pode ser copiado do guia de domínio Active Directory do Azure no [Portal de Gerenciamento do Azure].

7. No código para o método `AuthenticateAsync` acima, substitua **INSERT-RESOURCE-URI-HERE** pelo **App ID URI** para o serviço móvel. Se o tópico [Como se registrar com o Active Directory do Azure] foi seguido, sua URI da ID do aplicativo deverá ser semelhante a https://todolist.azure-mobile.net/login/aad.

8. No código, para o método `AuthenticateAsync` acima, substitua **INSERT-CLIENT-ID-HERE** pela ID do cliente copiada do aplicativo do cliente nativo.

9. No código para o método `AuthenticateAsync` acima, substitua **INSERT-REDIRECT-URI-HERE** pelo /login/done endpoint de seu serviço móvel. Esse deve ser semelhante a https://todolist.azure-mobile.net/login/done.


3. Em QSTodoListViewController, modifique **ViewDidLoad** adicionando o seguinte código antes da chamada para RefreshAsync();

        if (QSTodoService.DefaultService.User == null)
        {
            await QSTodoService.DefaultService.Authenticate();
        }

## <a name="test-client"></a>Testar o cliente usando autenticação

1. No menu Executar, clique em Executar para iniciar o aplicativo 
2. Você receberá uma solicitação para fazer logon no seu Active Directory do Azure.  
3. O aplicativo autentica e retorna os itens todo.

   ![](./media/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication/mobile-services-app-run.png)

<!-- Anchors. -->
[Registrar o seu serviço móvel com o Active Directory do Azure]: #register-mobile-service-aad
[Registrar seu aplicativo com o Active Directory do Azure]: #register-app-aad
[Configurar o serviço móvel para exigir autenticação]: #require-authentication
[Adicionar código de autenticação ao aplicativo cliente]: #add-authentication-code
[Testar o cliente usando autenticação]: #test-client

<!-- URLs. -->
[Introdução aos dados]: /en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
[Como se registrar com o Active Directory do Azure]: /en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Uma associação do Xamarin à Biblioteca de Autenticação do Active Directory para iOS]: https://github.com/AzureADSamples/NativeClient-Xamarin-iOS
[Extensão Xamarin]: http://xamarin.com/visual-studio
[Xamarin Studio]: http://xamarin.com/download\n<!--HONumber=42-->
