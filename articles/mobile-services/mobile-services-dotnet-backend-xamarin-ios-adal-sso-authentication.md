<properties 
	pageTitle="Autenticar o seu aplicativo com Logon Único da biblioteca de autenticação do Active Directory (Xamarin.iOS) | Mobile Dev Center" 
	description="Saiba como autenticar usuários para logon único com ADAL em seu aplicativo iOS Xamarin." 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="dwrede" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/19/2015" 
	ms.author="mahender"/>

# Autenticar o seu aplicativo com Logon Único da biblioteca de autenticação do diretório ativo

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

##Visão geral

Neste tutorial, adicione a autenticação ao projeto de início rápido usando uma Biblioteca de autenticação do diretório ativo.

Para poder autenticar os usuários, deve-se registrar o seu aplicativo com o Azure Active Directory (AAD). Isso é feito em duas etapas. Primeiro, deve-se registrar o seu serviço móvel e expor nele as permissões. Em segundo lugar, você deve registrar seu aplicativo Xamarin.iOS e conceder acesso com essas permissões


>[AZURE.NOTE]O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem efetuar autenticação do Active Directory do Azure com logon único para os aplicativos Xamarin.iOS. Se esta for sua primeira experiência com os Serviços Móveis, conclua o tutorial [Introdução aos serviços móveis].

##Pré-requisitos

Este tutorial exige o seguinte:

* XCode 4.5 e iOS 6.0 (ou versões posteriores) 
* Visual Studio com a [extensão Xamarin] ou [Xamarin Studio] no OS X
* Conclusão do tutorial [Introdução aos Serviços Móveis] ou [Introdução aos Dados].
* SDK de Serviços Móveis do Microsoft Azure
* Uma [associação do Xamarin à Biblioteca de Autenticação do Active Directory para iOS].

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-service](../../includes/mobile-services-dotnet-adal-register-service.md)]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../../includes/mobile-services-dotnet-adal-register-client.md)]

##Configurar o serviço móvel para requerer autenticação

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

##Adicionar o código de autenticação ao aplicativo do cliente

1. Adicionar sua associação do Xamarin à Biblioteca de Autenticação do Active Directory para seu projeto Xamarin.iOS. No Visual Studio 2013, clique como botão direito do mouse em **Referências** e selecione **Adicionar Referências**. Em seguida, navegue para sua biblioteca associada e clique em **Adicionar**. Não se esqueça de adicionar também storyboards da fonte ADAL.

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

6. No código para o método `AuthenticateAsync` acima, substitua **INSERIR-AUTORIDADE-AQUI** pelo nome do locatário em que você provisionou seu aplicativo, o formato deve ser https://login.windows.net/tenant-name.onmicrosoft.com. Este valor pode ser copiado da guia Domínio no Azure Active Directory no [Portal de Gerenciamento do Azure].

7. No código para o método `AuthenticateAsync` acima, substitua **INSIRA-RECURSO-URI-AQUI** com o **URI do ID do aplicativo** para o seu serviço móvel. Se seguiu o tópico [Como se registrar com o Active Directory do Azure], sua URI da ID do aplicativo deverá ser semelhante a https://todolist.azure-mobile.net/login/aad.

8. No código, para o método `AuthenticateAsync` acima, substitua **INSIRA-ID-DO-CLIENTE-AQUI** com o ID do cliente copiado do aplicativo do cliente nativo.

9. No código para o método `AuthenticateAsync` acima, substitua **INSIRA-REDIRECIONAR-URI-AQUI** pelo ponto de extremidade/logon/conclusão de seu serviço móvel. Deve ser semelhante a https://todolist.azure-mobile.net/login/done.


3. No QSTodoListViewController, modifique **ViewDidLoad** adicionando o seguinte código logo antes da chamada para RefreshAsync();

        if (QSTodoService.DefaultService.User == null)
        {
            await QSTodoService.DefaultService.Authenticate();
        }

##Testar o cliente usando autenticação

1. No menu Executar, clique em Executar para iniciar o aplicativo 
2. Você receberá uma solicitação para fazer logon no seu Active Directory do Azure.  
3. O aplicativo autentica e retorna os itens todo.

   ![](./media/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication/mobile-services-app-run.png)



<!-- URLs. -->
[Introdução aos Dados]: mobile-services-ios-get-started-data.md
[Introdução aos serviços móveis]: mobile-services-dotnet-backend-xamarin-ios-get-started.md
[Como se registrar com o Active Directory do Azure]: mobile-services-how-to-register-active-directory-authentication.md
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[associação do Xamarin à Biblioteca de Autenticação do Active Directory para iOS]: https://github.com/AzureADSamples/NativeClient-Xamarin-iOS
[extensão Xamarin]: http://xamarin.com/visual-studio
[Xamarin Studio]: http://xamarin.com/download

<!---HONumber=August15_HO6-->