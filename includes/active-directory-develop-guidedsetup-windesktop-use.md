
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Usar a MSAL para obter um token para a API do Microsoft Graph

Nesta seção, você usará a MSAL para obter um token da API do Microsoft Graph.

1.  No arquivo *MainWindow.xaml.cs*, adicione a referência da MSAL à classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. Substitua o código de classe `MainWindow` pelo seguinte:

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string _graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] _scopes = new string[] { "user.read" };

        public MainWindow()
        {
            InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;

            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(_scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
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
                ResultText.Text = await GetHttpContentWithToken(_graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Mais informações
#### <a name="get-a-user-token-interactively"></a>Obter um token de usuário interativamente
A chamada ao método `AcquireTokenAsync` resulta em uma janela que solicita a conexão do usuário. Os aplicativos geralmente exigem que os usuários façam logon interativamente na primeira vez que precisarem acessar um recurso protegido. Eles também podem precisar entrar quando uma operação silenciosa para adquirir um token falhar (por exemplo, quando a senha do usuário tiver expirado).

#### <a name="get-a-user-token-silently"></a>Obter um token de usuário no modo silencioso
O método `AcquireTokenSilentAsync` manipula as aquisições e renovações de tokens sem nenhuma interação do usuário. Depois que `AcquireTokenAsync` for executado pela primeira vez, `AcquireTokenSilentAsync` será o método comum usado para obter tokens que acessam recursos protegidos nas próximas chamadas – já que as chamadas para solicitar ou renovar tokens são feitas no modo silencioso.

Por fim, o método `AcquireTokenSilentAsync` falhará. Os motivos da falha podem ser que o usuário se desconectou ou alterou a senha em outro dispositivo. Quando a MSAL detecta que o problema pode ser resolvido com a solicitação de uma ação interativa, ela dispara uma exceção `MsalUiRequiredException`. O aplicativo pode tratar essa exceção de duas maneiras:

* Ele pode fazer uma chamada para `AcquireTokenAsync` imediatamente. Essa chamada resulta na solicitação de entrada do usuário. Esse padrão geralmente é usado nos aplicativos online em que não há nenhum conteúdo offline disponível para o usuário. O exemplo gerado por essa configuração interativa segue esse padrão, que você pode ver em ação na primeira vez que executar o exemplo. 
    * Como nenhum usuário usou o aplicativo, `PublicClientApp.Users.FirstOrDefault()` conterá um valor nulo e uma exceção `MsalUiRequiredException` será lançada. 
    * Em seguida, o código no exemplo trata a exceção chamando `AcquireTokenAsync`, o que resulta na solicitação de entrada do usuário.

* Ele também pode apresentar uma indicação visual aos usuários informando que uma entrada interativa é necessária, para que possam selecionar a hora certa de entrar. O aplicativo também pode tentar novamente `AcquireTokenSilentAsync` mais tarde. Esse padrão é usado com frequência quando os usuários podem usar outras funcionalidades do aplicativo sem interrupções, por exemplo, quando o conteúdo offline está disponível no aplicativo. Nesse caso, os usuários podem decidir quando desejam entrar para acessar o recurso protegido ou para atualizar as informações desatualizadas. Como alternativa, o aplicativo pode optar por repetir `AcquireTokenSilentAsync` quando a rede é restaurada depois de estar temporariamente indisponível.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Chamar a API do Microsoft Graph usando o token obtido recentemente

Adicione o seguinte método a `MainWindow.xaml.cs`. O método é usado para fazer uma solicitação `GET` na API do Graph usando um cabeçalho de autorização:

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
        //Add the token in Authorization header
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
<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre como fazer uma chamada REST em uma API protegida

Neste exemplo de aplicativo, use o método `GetHttpContentWithToken` para fazer uma solicitação HTTP `GET` em um recurso protegido que exige um token e, em seguida, retornar o conteúdo para o chamador. Esse método adiciona o token adquirido no cabeçalho de Autorização HTTP. Para esta amostra, o recurso é o ponto de extremidade *me* da API do Microsoft Graph – que exibe as informações de perfil do usuário.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Adicionar um método para desconectar um usuário

Para desconectar um usuário, adicione o seguinte método ao arquivo `MainWindow.xaml.cs`:

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
<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>Mais informações sobre como desconectar o usuário

O método `SignOutButton_Click` acima remove os usuários do cache de usuário da MSAL, o que diz para a MSAL esquecer o usuário atual, de modo que uma solicitação futura de aquisição de um token seja bem sucedida apenas se for feita para ser interativa.

Embora o aplicativo neste exemplo dê suporte a um único usuário, a MSAL dá suporte a cenários em que várias contas podem estar conectadas ao mesmo tempo. Um exemplo é um aplicativo de email onde um usuário tem várias contas.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Exibir informações básicas de token

Para exibir informações básicas sobre o token, adicione o seguinte método ao arquivo *MainWindow.xaml.cs*:

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
<!--start-collapse-->
### <a name="more-information"></a>Mais informações

Além do token de acesso que é usado para chamar a API do Microsoft Graph, após o usuário se autenticar, a MSAL também obterá um token de ID. Esse token contém um pequeno subconjunto de informações pertinentes aos usuários. O método `DisplayBasicTokenInfo` exibe as informações básicas contidas no token. Por exemplo, ele mostra o nome de exibição do usuário e a ID, bem como a data de expiração do token e a cadeia de caracteres que representa o token de acesso em si. Pressione o botão *Chamar API do Microsoft Graph* várias vezes e veja que o mesmo token foi reutilizado em solicitações posteriores. Veja também a data de expiração que está sendo estendida quando a MSAL decide a hora de renovar o token.
<!--end-collapse-->

