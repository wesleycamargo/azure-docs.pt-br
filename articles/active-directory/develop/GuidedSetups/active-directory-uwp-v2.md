---
title: Introdução à UWP no Azure AD v2 | Microsoft Docs
description: Como os aplicativos da Plataforma Universal do Windows (XAML) podem chamar uma API que exige tokens de acesso pelo ponto de extremidade do Azure Active Directory v2
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/20/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 390559922b3b8fb293d1c8b38f36dfd0a1df9ebd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763366"
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Chamar a API do Microsoft Graph de um aplicativo da UWP (Plataforma Universal do Windows)

Este guia demonstra como um aplicativo nativo da Plataforma Universal do Windows (XAML) pode obter um token de acesso e usar esse token de acesso para chamar a API do Microsoft Graph ou outras APIs que exigem tokens de acesso por meio do ponto de extremidade do Azure Active Directory v2.

Ao final deste guia, seu aplicativo poderá chamar uma API protegida usando contas pessoais (incluindo outlook.com, live.com e outras), bem como contas corporativas ou de estudante de qualquer empresa ou organização que tem o Azure Active Directory.  

> Este guia requer o Visual Studio 2017 com o desenvolvimento da Plataforma Universal do Windows instalado. Confira este [artigo](https://docs.microsoft.com/windows/uwp/get-started/get-set-up "Configurar o Visual Studio para UWP") para obter instruções de como baixar e configurar o Visual Studio para desenvolver aplicativos da Plataforma Universal do Windows.

### <a name="how-this-guide-works"></a>Como funciona este guia

![Como funciona este guia](media/active-directory-mobileanddesktopapp-windowsuniversalplatform-introduction/uwp-intro.png)

O aplicativo de exemplo criado por este guia permite que um aplicativo da UWP consulte a API do Microsoft Graph ou uma API Web que aceita tokens do ponto de extremidade do Azure Active Directory v2. Para esse cenário, um token é adicionado às solicitações HTTP por meio do cabeçalho de Autorização. Aquisições e renovações de tokens são manipuladas pela MSAL (Biblioteca de Autenticação da Microsoft).

### <a name="nuget-packages"></a>Pacotes NuGet

Este guia usa os seguintes pacotes NuGet:

|Biblioteca|DESCRIÇÃO|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|MSAL (Biblioteca de Autenticação da Microsoft)|


## <a name="set-up-your-project"></a>Configurar o seu projeto

Esta seção fornece instruções passo a passo de como integrar um aplicativo .NET da Área de Trabalho do Windows (XAML) com a opção *Entrar com uma Conta da Microsoft*, de forma que ele possa consultar APIs Web que exigem um token, como a API do Microsoft Graph.

O aplicativo criado por este guia exibe um botão usado para consultar a API do Graph, um botão de saída e caixas de texto que exibem os resultados das chamadas.

> Prefere baixar este projeto do Visual Studio de exemplo? [Baixe um projeto](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) e vá para a etapa [registro do aplicativo](#register-your-application "etapa de registro do aplicativo") para configurar o exemplo de código antes de executá-lo.


### <a name="create-your-application"></a>Criar o aplicativo
1. No Visual Studio: **Arquivo** > **Novo** > **Projeto**<br/>
2. Em *Modelos*, selecione **Visual C#**
3. Selecione **Aplicativo em Branco (Universal do Windows)**
4. Dê um nome a ele e clique em 'Ok'.
5. Se solicitado, fique à vontade para selecionar qualquer versão para *Destino* e *Mínimo* e clique em 'Ok':<br/><br/>![Versões mínima e de destino](media/active-directory-uwp-v2.md/vs-minimum-target.png)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>Adicionar a MSAL (Biblioteca de Autenticação da Microsoft) ao projeto
1. No Visual Studio: **Ferramentas** > **Gerenciador de Pacotes do NuGet** > **Console do Gerenciador de Pacotes**
2. Copie/cole o seguinte comando na janela do Console do Gerenciador de Pacotes:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> O pacote acima instala a [MSAL (Biblioteca de Autenticação da Microsoft)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). A MSAL manipula a aquisição, o armazenamento em cache e a atualização de tokens do usuário usados para acessar APIs protegidas pelo Azure Active Directory v2.

## <a name="initialize-msal"></a>Inicializar a MSAL
Esta etapa ajuda a criar uma classe para manipular a interação com a Biblioteca MSAL, como a manipulação de tokens.

1. Abra o arquivo **App.xaml.cs** e adicione a referência à biblioteca MSAL à classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Adicione as duas linhas a seguir à classe do aplicativo (dentro de bloco <code>sealed partial class App : Application</code>):

    ```csharp
    // Below is the clientId of your app registration. 
    // You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>Criar a interface do usuário do aplicativo

Um arquivo **MainPage.xaml** deve ser criado automaticamente como parte de seu modelo de projeto. Abra esse arquivo e, em seguida, siga as instruções:

1.  Substitua o nó **<Grid>** de seu aplicativo por:

    ```xml
    <Grid>
        <StackPanel Background="Azure">
            <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
                <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
                <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
            </StackPanel>
            <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
            <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
        </StackPanel>
    </Grid>
    ```
    
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Usar a MSAL (Biblioteca de Autenticação da Microsoft) para obter um token para a API do Microsoft Graph

Esta seção mostra como usar a MSAL para obter um token para a API do Microsoft Graph.

1.  Em **MainPage.xaml.cs**, adicione a referência à biblioteca MSAL à classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. Substitua o código de sua classe <code>MainPage</code> por:

    ```csharp
    public sealed partial class MainPage : Page
    {
        // Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";
    
        // Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };
    
        public MainPage()
        {
            this.InitializeComponent();
        }
    
        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;
    
            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
    
                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }
    
            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    }
    ```

### <a name="more-information"></a>Mais informações
#### <a name="get-a-user-token-interactively"></a>Obter um token de usuário interativamente
A chamada ao método `AcquireTokenAsync` resulta em uma janela que solicita a conexão do usuário. Os aplicativos geralmente exigem que os usuários façam logon interativamente na primeira vez que precisarem acessar um recurso protegido. Eles também podem precisar entrar quando uma operação silenciosa para adquirir um token falhar (por exemplo, quando a senha do usuário tiver expirado).

#### <a name="get-a-user-token-silently"></a>Obter um token de usuário no modo silencioso
O método `AcquireTokenSilentAsync` manipula as aquisições e renovações de tokens sem nenhuma interação do usuário. Depois que `AcquireTokenAsync` for executado pela primeira vez, `AcquireTokenSilentAsync` será o método comum usado para obter tokens que acessam recursos protegidos nas próximas chamadas – já que as chamadas para solicitar ou renovar tokens são feitas no modo silencioso.

Por fim, o método `AcquireTokenSilentAsync` falhará. Os motivos da falha podem ser que o usuário se desconectou ou alterou a senha em outro dispositivo. Quando a MSAL detecta que o problema pode ser resolvido com a solicitação de uma ação interativa, ela dispara uma exceção `MsalUiRequiredException`. O aplicativo pode tratar essa exceção de duas maneiras:

* Ele pode fazer uma chamada para `AcquireTokenAsync` imediatamente. Essa chamada resulta na solicitação de entrada do usuário. Esse padrão normalmente é usado em aplicativos online em que não há nenhum conteúdo offline disponível para o usuário. O exemplo gerado por essa configuração interativa segue esse padrão, que você pode ver em ação na primeira vez que executar o exemplo. 
    * Como nenhum usuário usou o aplicativo, `PublicClientApp.Users.FirstOrDefault()` conterá um valor nulo e uma exceção `MsalUiRequiredException` será lançada. 
    * Em seguida, o código no exemplo trata a exceção chamando `AcquireTokenAsync`, o que resulta na solicitação de entrada do usuário.

* Ele também pode apresentar uma indicação visual aos usuários informando que uma entrada interativa é necessária, para que eles possam selecionar a hora certa de entrar. O aplicativo também pode tentar novamente `AcquireTokenSilentAsync` mais tarde. Esse padrão é usado com frequência quando os usuários podem usar outras funcionalidades do aplicativo sem interrupções, por exemplo, quando o conteúdo offline está disponível no aplicativo. Nesse caso, os usuários podem decidir quando desejam entrar para acessar o recurso protegido ou para atualizar as informações desatualizadas. Como alternativa, o aplicativo pode optar por repetir `AcquireTokenSilentAsync` quando a rede é restaurada depois de estar temporariamente indisponível.

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Chamar a API do Microsoft Graph usando o token obtido recentemente

1. Adicione o seguinte método a **MainPage.xaml.cs**. O método é usado para fazer uma solicitação `GET` na API do Graph usando um cabeçalho de autorização:

    ```csharp
    /// <summary>
    /// Perform an HTTP GET request to a URL using an HTTP Authorization header
    /// </summary>
    /// <param name="url">The URL</param>
    /// <param name="token">The token</param>
    /// <returns>String containing the results of the GET operation</returns>
    public async Task<string> GetHttpContentWithToken(string url, string token)
    {
        var httpClient = new System.Net.Http.HttpClient();
        System.Net.Http.HttpResponseMessage response;
        try
        {
            var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
            // Add the token in Authorization header
            request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
            response = await httpClient.SendAsync(request);
            var content = await response.Content.ReadAsStringAsync();
            return content;
        }
        catch (Exception ex)
        {
            return ex.ToString();
        }
    }
    ```

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre como fazer uma chamada REST em uma API protegida

Neste aplicativo de exemplo, o método `GetHttpContentWithToken` é usado para fazer uma solicitação HTTP `GET` em um recurso protegido que exige um token e, em seguida, retornar o conteúdo para o chamador. Esse método adiciona o token adquirido no *cabeçalho de Autorização HTTP*. Para esta amostra, o recurso é o ponto de extremidade *me* da API do Microsoft Graph – que exibe as informações de perfil do usuário.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adicionar um método para desconectar o usuário

1. Para desconectar o usuário, adicione o seguinte método a **MainPage.xaml.cs**:

    ```csharp
    /// <summary>
    /// Sign out the current user
    /// </summary>
    private void SignOutButton_Click(object sender, RoutedEventArgs e)
    {
        if (App.PublicClientApp.Users.Any())
        {
            try
            {
                App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
                this.ResultText.Text = "User has signed-out";
                this.CallGraphButton.Visibility = Visibility.Visible;
                this.SignOutButton.Visibility = Visibility.Collapsed;
            }
            catch (MsalException ex)
            {
                ResultText.Text = $"Error signing-out user: {ex.Message}";
            }
        }
    }
    ```

### <a name="more-info-on-sign-out"></a>Mais informações sobre a Saída

O método `SignOutButton_Click` remove o usuário do cache de usuário da MSAL – efetivamente, isso informará a MSAL para esquecer o usuário atual; portanto, uma solicitação futura de aquisição de um token terá êxito apenas se for feita para ser interativa.
Embora o aplicativo nesta amostra dê suporte a um único usuário, a MSAL dá suporte a cenários em que várias contas podem estar conectadas ao mesmo tempo – um exemplo é um aplicativo de email no qual um usuário tem várias contas.

## <a name="display-basic-token-information"></a>Exibir informações básicas de token

1. Adicione o seguinte método a **MainPage.xaml.cs** para exibir informações básicas sobre o token:

    ```csharp
    /// <summary>
    /// Display basic information contained in the token
    /// </summary>
    private void DisplayBasicTokenInfo(AuthenticationResult authResult)
    {
        TokenInfoText.Text = "";
        if (authResult != null)
        {
            TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
            TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
            TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
            TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
        }
    }
    ```

### <a name="more-information"></a>Mais informações

Os tokens de ID adquiridos por meio do *OpenID Connect* também contêm um pequeno subconjunto de informações pertinentes ao usuário. `DisplayBasicTokenInfo` exibe informações básicas contidas no token: por exemplo, o nome de exibição do usuário e a ID, bem como a data de expiração do token e a cadeia de caracteres que representa o token de acesso em si. Essas informações são exibidas para sua visualização. Pressione o botão **Chamar API do Microsoft Graph** várias vezes e veja que o mesmo token foi reutilizado em solicitações posteriores. Veja também a data de expiração que está sendo estendida quando a MSAL decide a hora de renovar o token.

## <a name="register-your-application"></a>Registre seu aplicativo

Agora você precisa registrar seu aplicativo no *Portal de Registro de Aplicativos da Microsoft*:
1. Acesse o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar um aplicativo
2. Insira um nome para seu aplicativo 
3. Verifique se a opção Instalação Guiada está desmarcada
4. Clique em **Adicionar Plataformas**, selecione **Aplicativo Nativo** e, em seguida, selecione Salvar
5. Copie o GUID da ID do Aplicativo, volte ao Visual Studio, abra **App.xaml.cs** e substitua `your_client_id_here` pela ID do Aplicativo que você acabou de registrar:

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Habilitar a autenticação integrada em domínios federados (opcional)

Para habilitar a Autenticação Integrada do Windows ao usar um domínio federado do Azure Active Directory, o manifesto do aplicativo deverá habilitar funcionalidades adicionais:

1. Clique duas vezes em **Package.appxmanifest**
2. Selecione a guia **Recursos** e certifique-se de que as configurações a seguir estejam habilitadas:

    - Autenticação de Empresa
    - Redes Privadas (Cliente e Servidor)
    - Certificados Compartilhados de Usuário 

3. Em seguida, abra **App.xaml.cs** e adicione a seguinte linha ao construtor de aplicativo:

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> A Autenticação Integrada do Windows não está configurada por padrão para este exemplo porque aplicativos que solicitam as funcionalidades de *Autenticação de Empresa* ou *Certificados Compartilhados de Usuário* exigem um nível mais elevado de verificação pela Windows Store e nem todos os desenvolvedores desejam realizar o nível mais alto de verificação. Habilite essa configuração somente se você precisar da Autenticação Integrada do Windows com um domínio federado do Azure Active Directory.


## <a name="test-your-code"></a>Testar seu código

Para testar o aplicativo, pressione `F5` para executar o projeto no Visual Studio. A Janela Principal deve ser exibida:

![Interface do usuário do aplicativo](media/active-directory-uwp-v2.md/testapp-ui.png)

Quando você estiver pronto para testar, clique em *Chamar API do Microsoft Graph* e use uma conta do Microsoft Azure Active Directory (conta organizacional) ou uma Conta da Microsoft (live.com, outlook.com) para se conectar. Se esta for a primeira vez, você verá uma janela solicitando que o usuário entre:

![Página de entrada](media/active-directory-uwp-v2.md/sign-in-page.png)

### <a name="consent"></a>Consentimento
Na primeira vez que você entrar no aplicativo, será apresentada uma tela de consentimento semelhante à tela abaixo, na qual você precisa aceitar explicitamente:

![Tela de consentimento](media/active-directory-uwp-v2.md/consentscreen.png)
### <a name="expected-results"></a>Resultados esperados
Você deverá ver as informações de perfil do usuário retornadas pela chamada à API do Microsoft Graph na tela Resultados da Chamada à API:

![Tela de resultados](media/active-directory-uwp-v2.md/uwp-results-screen.PNG)

Você também deverá ver informações básicas sobre o token adquirido por meio de `AcquireTokenAsync` ou `AcquireTokenSilentAsync` na caixa Informações de Token:

|Propriedade  |Formatar  |DESCRIÇÃO |
|---------|---------|---------|
|**Nome** |Nome completo do usuário |O nome e sobrenome do usuário.|
|**Nome de Usuário** |<span>user@domain.com</span> |O nome de usuário que é usado para identificar o usuário.|
|**O Token Expira** |Datetime |A data e hora em que o token expira. MSAL estende a data de validade renovando o token, conforme necessário.|
|**Token de acesso** |Cadeia de caracteres |A cadeia de caracteres do token enviada para solicitações HTTP que requerem um *Cabeçalho de Autorização*.|

#### <a name="see-what-is-in-the-access-token-optional"></a>Veja o que há no token de acesso (opcional)
Você também pode copiar o valor em 'Token de acesso' e colá-lo em https://jwt.ms para decodificá-lo e ver a lista de declarações.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo *user.read* para ler o perfil do usuário. Esse escopo é adicionado automaticamente, por padrão, a cada aplicativo que é registrado no Portal de Registro de Aplicativos. Outras APIs do Microsoft Graph, bem como APIs personalizadas do servidor de back-end, podem exigir escopos adicionais. A API do Microsoft Graph requer o escopo *Calendars.Read* para listar os calendários do usuário.

Para acessar os calendários do usuário no contexto de um aplicativo, adicione a permissão delegada *Calendars.Read* às informações de registro do aplicativo. Em seguida, adicione o escopo *Calendars.Read* à chamada `acquireTokenSilent`. 

> [!NOTE]
> Talvez o usuário precise fornecer autorizações adicionais à medida que o número de escopos aumentar.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="issue-1"></a>Problema 1:
Você poderá receber um dos seguintes erros ao entrar em seu aplicativo em um domínio federado do Azure Active Directory:
 - Nenhum certificado do cliente válido encontrado na solicitação.
 - Nenhum certificado válido encontrado no repositório de certificados do usuário.
 - Tente novamente escolhendo um método de autenticação diferente.

**Causa:** funcionalidades corporativas e de certificado não estão habilitadas

**Solução:** siga as etapas em [autenticação integrada em domínios federados](#enable-integrated-authentication-on-federated-domains-optional)

### <a name="issue-2"></a>Problema 2:
Após você habilitar a [autenticação integrada em domínios federados](#enable-integrated-authentication-on-federated-domains-optional) e tentar usar o Windows Hello em um computador com Windows 10 para entrar em um ambiente com a autenticação multifator configurada, a lista de certificados será apresentada. No entanto, se você optar por usar seu PIN, a janela de PIN não será apresentada.

**Causa:** limitação conhecida do agente de autenticação da Web em aplicativos da UWP em execução na área de trabalho do Windows 10 (funciona bem no Windows 10 Mobile)

**Solução alternativa:** os usuários precisam optar por entrar com outras opções e, em seguida, escolher *Entrar com nome de usuário e senha*, selecionar fornecer sua senha e passar pela autenticação por telefone.

