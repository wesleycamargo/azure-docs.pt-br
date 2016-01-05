## Obter um token do Gerenciador de Recursos

O Active Directory do Azure deve autenticar todas as tarefas que podem ser executadas nos recursos usando o Gerenciador de Recursos do Azure. O exemplo mostrado aqui usa a autenticação de senha; para ver outras abordagens, veja [Autenticando solicitações do Gerenciador de Recursos do Azure][lnk-authenticate-arm].

1. Adicione o seguinte código ao método **Main** em Program.cs para recuperar um token do Azure AD usando a ID e senha do aplicativo.

    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.windows.net/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
    
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```

2. Crie um objeto **ResourceManagementClient** que usa o token adicionando o seguinte código ao final do método **Main**:

    ```
    var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken);
    var client = new ResourceManagementClient(creds);
    ```

3. Crie ou obtenha uma referência para o grupo de recursos que você está usando:

    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdateAsync(rgName,
        new ResourceGroup("East US")).Result;
    if (rgResponse.StatusCode != HttpStatusCode.Created
        && rgResponse.StatusCode != HttpStatusCode.OK)
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx

<!---HONumber=AcomDC_1203_2015-->