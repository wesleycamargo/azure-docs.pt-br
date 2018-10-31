---
title: Introdução à UWP no Azure AD v2.0 | Microsoft Docs
description: Como os aplicativos da UWP (Plataforma Universal do Windows) podem chamar uma API que exige tokens de acesso pelo ponto de extremidade do Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4ba4e844ed6bb01204b7a0adf5020aec255147dd
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986535"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Chamar a API do Microsoft Graph de um aplicativo da Plataforma Universal do Windows (XAML)

> [!div renderon="docs"]
> [!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Este guia explica como um aplicativo nativo da UWP (Plataforma Universal do Windows) pode solicitar um token de acesso e, em seguida, chamar a API do Microsoft Graph. O guia também se aplica para outras APIs que requerem tokens de acesso do ponto de extremidade do Azure Active Directory v2.0.

No final deste guia, seu aplicativo chama uma API protegida usando contas pessoais. Os exemplos são outlook.com, live.com e outros. Seu aplicativo também chama contas corporativas e de estudante de qualquer empresa ou organização que use o Azure Active Directory.

>[!NOTE]
> Este guia requer o Visual Studio 2017 com o desenvolvimento da Plataforma Universal do Windows instalado. Consulte [Prepare-se para começar](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) para obter instruções de como baixar e configurar o Visual Studio para desenvolver aplicativos da Plataforma Universal do Windows.

## <a name="how-this-guide-works"></a>Como funciona este guia

![Gráfico Como funciona este guia](./media/tutorial-v2-windows-uwp/uwp-intro.png)

Este guia cria um aplicativo da UWP de exemplo que consulta a API do Microsoft Graph ou uma API Web que aceita tokens do ponto de extremidade do Azure Active Directory v2.0. Para esse cenário, um token é adicionado às solicitações HTTP por meio do cabeçalho de Autorização. A MSAL (Biblioteca de Autenticação da Microsoft) lida com as aquisições e a renovações de tokens.

## <a name="nuget-packages"></a>Pacotes NuGet

Este guia usa os seguintes pacotes NuGet:

|Biblioteca|DESCRIÇÃO|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteca de Autenticação da Microsoft|

## <a name="set-up-your-project"></a>Configurar o seu projeto

Esta seção fornece instruções passo a passo para integrar um aplicativo .NET de área de trabalho do Windows (XAML) com *Entrar com a Microsoft*. Em seguida, ele poderá consultar as APIs da Web que exigem um token, como a API do Microsoft Graph.

Este guia cria um aplicativo que exibe um botão usado para consultar a API do Graph, um botão de saída e caixas de texto que exibem os resultados das chamadas.

> [!NOTE]
> Prefere baixar este projeto de exemplo do Visual Studio? [Baixe um projeto](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) e vá para a etapa [registro do aplicativo](#register-your-application "etapa de registro do aplicativo") para configurar o exemplo de código antes de executá-lo.


### <a name="create-your-application"></a>Criar o aplicativo

1. No Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**.
2. Em **Modelos**, selecione **Visual C#**.
3. Selecione **Aplicativo em Branco (Universal do Windows)**.
4. Nomeie o aplicativo e selecione **OK**.
5. Se solicitado, selecione qualquer versão para **Destino** e **Mínima** e selecione **OK**.

    >![Versões mínima e de destino](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Adicionar a Biblioteca de Autenticação da Microsoft ao projeto
1. No Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes do NuGet** > **Console do Gerenciador de Pacotes**.
2. Copie e cole o seguinte comando na janela **Console do Gerenciador de Pacotes**:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre -Version 1.1.4-preview0002
    ```

> [!NOTE]
> Esse comando instala a [Biblioteca de Autenticação da Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). A MSAL adquire, armazena em cache e atualiza os tokens de usuário que acessam as APIs protegidas pelo Azure Active Directory v2.0.

> [!NOTE]
> Este tutorial ainda não usa a versão mais recente de MSAL.NET, mas estamos trabalhando para atualizá-la.

## <a name="initialize-msal"></a>Inicializar a MSAL
Esta etapa ajuda você a criar uma classe para lidar com a interação com a MSAL, por exemplo, a manipulação de tokens.

1. Abra o arquivo **App.xaml.cs** e adicione a referência da MSAL à classe:

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

Um arquivo **MainPage.xaml** é criado automaticamente como parte de seu modelo de projeto. Abra esse arquivo e, em seguida, siga as instruções:

* Substitua o nó **Grade** do seu aplicativo pelo código a seguir:

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
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Usar a MSAL para obter um token para a API do Microsoft Graph

Esta seção mostra como usar a MSAL para obter um token para a API do Microsoft Graph.

1.  Em **MainPage.xaml.cs**, adicione a referência da MSAL à classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. Substitua o código da sua classe <code>MainPage</code> pelo seguinte código:

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

Uma chamada ao método `AcquireTokenAsync` resulta em uma janela que solicita a entrada do usuário. Os aplicativos geralmente exigem que os usuários façam logon interativamente na primeira vez que precisarem acessar um recurso protegido. Eles também podem precisar entrar quando uma operação silenciosa para adquirir um token falhar. Por exemplo, quando a senha do usuário tiver expirado.

#### <a name="get-a-user-token-silently"></a>Obter um token de usuário no modo silencioso

O método `AcquireTokenSilentAsync` manipula as aquisições e renovações de tokens sem nenhuma interação do usuário. Depois que `AcquireTokenAsync` é executado pela primeira vez e o usuário é solicitado a inserir suas credenciais, o método `AcquireTokenSilentAsync` deve ser usado para solicitar tokens para chamadas subsequentes porque ele adquire tokens silenciosamente. A MSAL tratará do armazenamento em cache e da renovação do token.

Por fim, o método `AcquireTokenSilentAsync` falhará. A falha pode ocorrer por os usuários terem se desconectado ou alterado a senha em outro dispositivo. Quando a MSAL detecta que o problema pode ser resolvido com a solicitação de uma ação interativa, ela dispara uma exceção `MsalUiRequiredException`. O aplicativo pode tratar essa exceção de duas maneiras:

* Ele pode fazer uma chamada para `AcquireTokenAsync` imediatamente. Essa chamada resulta na solicitação de entrada do usuário. Normalmente, esse padrão é usado em aplicativos online em que não há nenhum conteúdo offline disponível para o usuário. O exemplo gerado por essa instalação orientada segue esse padrão. Você pode vê-lo em ação na primeira vez que executar o exemplo. 
    * Como nenhum usuário usou o aplicativo, `PublicClientApp.Users.FirstOrDefault()` conterá um valor nulo e uma exceção `MsalUiRequiredException` será lançada.
    * Em seguida, o código no exemplo manipula a exceção chamando `AcquireTokenAsync`. Essa chamada resulta na solicitação de entrada do usuário.

* Ou, em vez disso, ele apresenta aos usuários uma indicação visual de que uma entrada interativa é necessária. Em seguida, eles podem selecionar o momento certo para entrar. O aplicativo também pode tentar novamente `AcquireTokenSilentAsync` mais tarde. Frequentemente, esse padrão é usado quando os usuários podem usar outras funcionalidades do aplicativo sem interrupções. Um exemplo é quando o conteúdo offline está disponível no aplicativo. Nesse caso, os usuários podem decidir quando desejam entrar para acessar o recurso protegido ou para atualizar as informações desatualizadas. Ou o aplicativo pode optar por repetir `AcquireTokenSilentAsync` quando a rede é restaurada depois de estar temporariamente indisponível.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Chamar a API do Microsoft Graph usando o token obtido recentemente

* Adicione o seguinte novo método a **MainPage.xaml.cs**. Esse método é usado para fazer uma solicitação `GET` na API do Graph usando um cabeçalho [Authorize]:

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

Neste aplicativo de exemplo, o método `GetHttpContentWithToken` é usado para fazer uma solicitação HTTP `GET` em um recurso protegido que exige um token. Depois o método retorna o conteúdo para o chamador. Esse método adiciona o token adquirido no cabeçalho de **Autorização HTTP**. Para este exemplo, o recurso é o ponto de extremidade **me** da API do Microsoft Graph – que exibe as informações de perfil do usuário.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adicionar um método para desconectar o usuário

* Para desconectar o usuário, adicione o seguinte método a **MainPage.xaml.cs**:

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

### <a name="more-information-on-sign-out"></a>Mais informações sobre a saída

O método `SignOutButton_Click` remove o usuário do cache do usuário da MSAL. Esse método informa efetivamente a MSAL para esquecer o usuário atual. Em seguida, uma solicitação futura para adquirir um token só terá êxito se for feita para ser interativa.
O aplicativo neste exemplo oferece suporte a um único usuário. Mas a MSAL oferece suporte a cenários em que mais de uma conta pode ser conectada ao mesmo tempo. Um exemplo é um aplicativo de email em que um usuário tem diversas contas.

## <a name="display-basic-token-information"></a>Exibir informações básicas de token

* Adicione o seguinte método a **MainPage.xaml.cs** para exibir informações básicas sobre o token:

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

Os tokens de ID adquiridos por meio do **OpenID Connect** também contêm um pequeno subconjunto de informações pertinentes ao usuário. `DisplayBasicTokenInfo` exibe informações básicas contidas no token. Entre os exemplos, o nome de exibição do usuário e a ID, a data de expiração do token e a cadeia de caracteres que representa o token de acesso em si. Caso selecione o botão **Chamar API do Microsoft Graph** diversas vezes, você verá que o mesmo token foi reutilizado em solicitações posteriores. Veja também que a data de expiração é estendida quando a MSAL decide que é a hora de renovar o token.

## <a name="register-your-application"></a>Registre seu aplicativo

Agora você precisa registrar seu aplicativo no Portal de Registro de Aplicativos da Microsoft:
1. Acesse o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar um aplicativo.
2. Insira um nome para seu aplicativo.
3. Verifique se a opção para **Configuração Guiada** *não está marcada*.
4. Selecione **Adicionar Plataformas**, depois **Aplicativo Nativo** e, em seguida, **Salvar**.
5. Copie o GUID da **ID do Aplicativo**, volte ao Visual Studio, abra **App.xaml.cs** e substitua `your_client_id_here` pela ID do Aplicativo que você acabou de registrar:

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Habilitar a autenticação integrada em domínios federados (opcional)

Para habilitar a Autenticação Integrada do Windows ao ser usada com um domínio federado do Azure Active Directory, o manifesto do aplicativo deverá habilitar recursos adicionais:

1. Clique duas vezes em **Package.appxmanifest**.
2. Selecione a guia **Recursos** e verifique se as configurações a seguir estão habilitadas:

    - Autenticação de Empresa
    - Redes Privadas (Cliente e Servidor)
    - Certificados Compartilhados de Usuário

3. Abra **App.xaml.cs** e adicione a seguinte linha ao construtor de aplicativo:

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> A Autenticação Integrada do Windows não está configurada por padrão nesse exemplo. Os aplicativos que solicitam recursos de *Autenticação de Empresa* ou *Certificados Compartilhados de Usuário* exigem um nível mais alto de verificação pela Windows Store. Além disso, nem todos os desenvolvedores desejam executar o nível mais alto de verificação. Habilite essa configuração somente se você precisar da Autenticação Integrada do Windows com um domínio federado do Azure Active Directory.

## <a name="test-your-code"></a>Testar seu código

Para testar o aplicativo, selecione F5 para executar o projeto no Visual Studio. A janela principal será exibida:

![Interface do usuário do aplicativo](./media/tutorial-v2-windows-uwp/testapp-ui.png)

Quando estiver pronto para testar, selecione **Chamar a API do Microsoft Graph**. Em seguida, use uma conta organizacional do Microsoft Azure Active Directory ou uma conta da Microsoft, como live.com ou outlook.com, para entrar. Se esta for a primeira vez que você entra, será exibida uma janela solicitando a entrada:

![Página de entrada](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Consentimento
Na primeira vez que você entrar no aplicativo, será apresentada uma tela de consentimento semelhante à a seguir. Selecione **Sim** para consentir explicitamente com o acesso:

![Tela de consentimento de acesso](./media/tutorial-v2-windows-uwp/consentscreen.png)
### <a name="expected-results"></a>Resultados esperados
São exibidas as informações de perfil do usuário retornadas pela chamada à API do Microsoft Graph na tela **Resultados da Chamada à API**:

![Tela Resultados da Chamada à API](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

Também serão exibidas informações básicas sobre o token adquirido por meio de `AcquireTokenAsync` ou `AcquireTokenSilentAsync` na caixa **Informações de Token**:

|Propriedade  |Formatar  |DESCRIÇÃO |
|---------|---------|---------|
|**Nome** |Nome completo do usuário|O nome e sobrenome do usuário.|
|**Nome de Usuário** |<span>user@domain.com</span> |É o nome de usuário que identifica o usuário.|
|**O Token Expira** |Datetime |A hora em que o token expira. MSAL estende a data de validade renovando o token, conforme necessário.|
|**Token de acesso** |Cadeia de caracteres |A cadeia de caracteres do token enviada para solicitações HTTP que requerem um *Cabeçalho de Autorização*.|

#### <a name="see-whats-in-the-access-token-optional"></a>Ver o que há no token de acesso (opcional)
Também é possível copiar o valor em **Token de Acesso** e colá-lo em https://jwt.ms para decodificá-lo e ver a lista de declarações.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo *user.read* para ler um perfil de usuário. Esse escopo é adicionado automaticamente e por padrão a cada aplicativo registrado no Portal de Registro de Aplicativos. Outras APIs do Microsoft Graph e APIs personalizadas do servidor de back-end podem exigir escopos adicionais. A API do Microsoft Graph requer o escopo *Calendars.Read* para listar os calendários do usuário.

Para acessar os calendários do usuário no contexto de um aplicativo, adicione a permissão delegada *Calendars.Read* às informações de registro do aplicativo. Em seguida, adicione o escopo *Calendars.Read* à chamada `acquireTokenSilent`.

> [!NOTE]
> Talvez os usuários precisem fornecer autorizações adicionais à medida que o número de escopos aumenta.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="issue-1"></a>Problema 1
Você recebe uma das seguintes mensagens de erro ao entrar em seu aplicativo em um domínio federado do Azure Active Directory:
 - Nenhum certificado do cliente válido encontrado na solicitação.
 - Nenhum certificado válido encontrado no repositório de certificados do usuário.
 - Tente novamente escolhendo um método de autenticação diferente.

**Causa:** recursos corporativos e de certificado não estão habilitados.

**Solução:** siga as etapas em [autenticação integrada em domínios federados](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problema 2
Você habilitar a [autenticação integrada em domínios federados](#enable-integrated-authentication-on-federated-domains-optional) e tentar usar o Windows Hello em um computador com Windows 10 para entrar em um ambiente com autenticação multifator configurada. A lista de certificados é apresentada. No entanto, se você optar por usar seu PIN, a janela PIN nunca é apresentada.

**Causa:** esse problema é uma limitação conhecida do agente de autenticação da Web em aplicativos da UWP executados na área de trabalho do Windows 10. Ele funciona corretamente no Windows 10 Mobile.

**Solução alternativa:** selecione **Entrar com outras opções**. Depois selecione **Entrar com nome de usuário e senha**. Selecione **Forneça sua senha**. Depois passe pelo processo de autenticação de telefone.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]