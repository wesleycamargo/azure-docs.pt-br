<properties linkid="develop-mobile-tutorials-sso-with-adal-xamarin-ios" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (Xamarin.iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your Xamarin.iOS application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc,mahender" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc,mahender" />

# Autenticar o seu aplicativo com Logon Único da biblioteca de autenticação do diretório ativo

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="C# da Windows Store" >C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" >iOS</a>
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
</div>

Neste tutorial, adicione a autenticação ao projeto de início rápido usando uma Biblioteca de autenticação do diretório ativo.

Para poder autenticar os usuários, deve-se registrar o seu aplicativo com o Active Directory do Azure (AAD). Isso é feito em duas etapas. Primeiro, deve-se registrar o seu serviço móvel e expor nele as permissões. Em segundo lugar, você deve registrar seu aplicativo Xamarin.iOS e conceder acesso com essas permissões

> [WACOM.NOTE] O objetivo deste tutorial é ajudá-lo a compreender melhor como os Serviços Móveis permitem efetuar autenticação do Active Directory do Azure em logon único para os aplicativos Xamarin.iOS. Se esta for sua primeira experiência com os Serviços Móveis, conclua o tutorial [Introdução aos serviços móveis][Introdução aos serviços móveis].

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Registrar o seu serviço móvel com o Active Directory do Azure][Registrar o seu serviço móvel com o Active Directory do Azure]
2.  [Registrar o seu aplicativo com o Active Directory do Azure][Registrar o seu aplicativo com o Active Directory do Azure]
3.  [Configurar o serviço móvel para requerer autenticação][Configurar o serviço móvel para requerer autenticação]
4.  [Adicionar o código de autenticação ao aplicativo do cliente][Adicionar o código de autenticação ao aplicativo do cliente]
5.  [Testar o cliente usando autenticação][Testar o cliente usando autenticação]

Este tutorial exige o seguinte:

-   XCode 4.5 e iOS 6.0 (ou versões posteriores)
-   Visual Studio com a [extensão Xamarin][extensão Xamarin] ou [Xamarin Studio][Xamarin Studio] no OS X
-   Conclusão do tutorial [Introdução aos Serviços Móveis][Introdução aos serviços móveis] ou [Introdução aos Dados][Introdução aos Dados].
-   SDK de Serviços Móveis do Microsoft Azure
-   Uma [associação do Xamarin à Biblioteca de Autenticação do Active Directory para iOS][associação do Xamarin à Biblioteca de Autenticação do Active Directory para iOS].

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-client](../includes/mobile-services-dotnet-adal-register-client.md)]

## <a name="require-authentication"></a>Configurar o serviço móvel para requerer autenticação

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="require-authentication"></a>Adicionar o código de autenticação ao aplicativo do cliente

1.  Adicionar sua associação do Xamarin à Biblioteca de Autenticação do Active Directory para seu projeto Xamarin.iOS. No Visual Studio 2013, clique como botão direito do mouse em **Referências** e selecione **Adicionar Referências**. Em seguida, navegue para sua biblioteca associada e clique em **Adicionar**. Não se esqueça de adicionar também storyboards da fonte ADAL.

2.  Adicione a seguinte classe QSTodoService:

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

3.  No código para o método acima `AuthenticateAsync`, substitua **INSIRA-AUTORIDADE-AQUI** pelo o nome do locatário no qual o seu aplicativo foi provisionado. O formato deve ser <https://login.windows.net/tenant-name.onmicrosoft.com>. Este valor pode ser copiado da guia Domínio no Active Directory do Azure no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

4.  No código para o método `AuthenticateAsync` acima, substitua **INSIRA-RECURSO-URI-AQUI** com o **URI do ID do aplicativo** para o seu serviço móvel. Se o tópico [Como registrar-se com o Active Directory do Azure][Como registrar-se com o Active Directory do Azure] foi seguido, a sua URI do ID do aplicativo deve ser semelhante a <https://todolist.azure-mobile.net/login/aad>.

5.  No código, para o método `AuthenticateAsync` acima, substitua **INSIRA-ID-DO-CLIENTE-AQUI** com o ID do cliente copiado do aplicativo do cliente nativo.

6.  No código para o método `AuthenticateAsync` acima, substitua **INSIRA-REDIRECIONAR-URI-AQUI** pelo ponto de extremidade /login/done de seu serviço móvel. Isso deve ser similar a <https://todolist.azure-mobile.net/login/done>.

7.  No QSTodoListViewController, modifique **ViewDidLoad** adicionando o seguinte código logo antes da chamada para RefreshAsync();

        if (QSTodoService.DefaultService.User == null)
        {
            await QSTodoService.DefaultService.Authenticate();
        }

## <a name="test-client"></a>Testar o cliente usando autenticação

1.  No menu Executar, clique em Executar para iniciar o aplicativo
2.  Você receberá uma solicitação para fazer logon no seu Active Directory do Azure.
3.  O aplicativo autentica e retorna os itens todo.

![][0]

<!-- Anchors. -->
<!-- URLs. -->

  [Introdução aos serviços móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
  [Registrar o seu serviço móvel com o Active Directory do Azure]: #register-mobile-service-aad
  [Registrar o seu aplicativo com o Active Directory do Azure]: #register-app-aad
  [Configurar o serviço móvel para requerer autenticação]: #require-authentication
  [Adicionar o código de autenticação ao aplicativo do cliente]: #add-authentication-code
  [Testar o cliente usando autenticação]: #test-client
  [extensão Xamarin]: http://xamarin.com/visual-studio
  [Xamarin Studio]: http://xamarin.com/download
  [Introdução aos Dados]: /pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
  [associação do Xamarin à Biblioteca de Autenticação do Active Directory para iOS]: https://github.com/AzureADSamples/NativeClient-Xamarin-iOS
  [mobile-services-dotnet-adal-register-service]: ../includes/mobile-services-dotnet-adal-register-service.md
  [mobile-services-dotnet-adal-register-client]: ../includes/mobile-services-dotnet-adal-register-client.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [Como registrar-se com o Active Directory do Azure]: /pt-br/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [0]: ./media/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication/mobile-services-app-run.png
