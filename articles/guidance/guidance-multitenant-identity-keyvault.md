<properties
   pageTitle="Como usar o Cofre da Chave para proteger segredos do aplicativo | Microsoft Azure"
   description="Como usar o serviço Cofre da Chave para armazenar segredos do aplicativo"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Como usar o Cofre da Chave para proteger segredos do aplicativo

Este artigo faz [parte de uma série]. Há também um [aplicativo de exemplo] completo que acompanha esta série.

## Visão geral

É comum ter configurações de aplicativo confidenciais e que devem ser protegidas, por exemplo:

- Cadeias de conexão de banco de dados
- Senhas
- Chaves de criptografia

Como prática de segurança recomendada, nunca armazene esses segredos no controle do código-fonte. Eles vazam com muita facilidade, mesmo se o repositório de código fonte for privado. E não se trata apenas de guardar os segredos do público geral. Em projetos maiores, talvez você queira restringir quais desenvolvedores e operadores podem acessar os segredos de produção. (As configurações para ambientes de teste ou de desenvolvimento são diferentes).

Uma opção mais segura é armazenar esses segredos no [Cofre da Chave do Azure][KeyVault]. O Cofre da Chave é um serviço hospedado na nuvem para gerenciamento de chaves de criptografia e de outros segredos. Este artigo mostra como usar o Cofre da Chave para armazenar as definições de configuração para seu aplicativo.

No aplicativo [Tailspin Surveys][Surveys], as configurações a seguir são secretas:

- A cadeia de conexão do banco de dados.
- A cadeia de conexão Redis.
- O segredo do cliente para o aplicativo Web.

Para armazenar segredos de configuração no Cofre da Chave, o aplicativo Surveys implementa um provedor de configuração personalizada, que conecta-se ao [sistema de configuração][configuration] do ASP.NET Core 1.0. O provedor personalizado lê as configurações do Cofre da Chave na inicialização.

O aplicativo Surveys carrega configurações dos seguintes locais:

- O arquivo appsettings.json
- O [armazenamento de segredos do usuário][user-secrets] (ambiente somente de desenvolvimento; para teste)
- O ambiente de hospedagem (configurações do aplicativo em aplicativos Web do Azure)
- Cofre da Chave

Cada uma delas substitui a anterior, portanto, as configurações armazenadas no Cofre da Chave têm precedência.

> [AZURE.NOTE] Por padrão, o provedor de configuração do Cofre da Chave está desabilitado. Ele não é necessário para executar o aplicativo localmente. Você poderia habilitá-lo em uma implantação de produção.

> [AZURE.NOTE] Atualmente, não há suporte para o provedor do Cofre da Chave no .NET Core, pois ele exige o pacote [Microsoft.Azure.KeyVault][Microsoft.Azure.KeyVault].

Na inicialização, o aplicativo lê as configurações de cada provedor de configuração registrado, e as usa para preencher um objeto de opções fortemente tipado. (Para saber mais, confira [Como usar opções e objetos de configuração][options]).

## Implementação

A classe [KeyVaultConfigurationProvider][KeyVaultConfigurationProvider] é um provedor de configuração que se conecta ao [sistema de configuração][configuration] do ASP.NET Core 1.0.

Para usar a `KeyVaultConfigurationProvider`, chame o método de extensão `AddKeyVaultSecrets` na classe de inicialização:

```csharp
    var builder = new ConfigurationBuilder()
        .SetBasePath(appEnv.ApplicationBasePath)
        .AddJsonFile("appsettings.json");

    if (env.IsDevelopment())
    {
        builder.AddUserSecrets();
    }
    builder.AddEnvironmentVariables();
    var config = builder.Build();

    // Add key vault configuration:
    builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
        config["KeyVault:Name"],
        config["AzureAd:Asymmetric:CertificateThumbprint"],
        Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
        loggerFactory);
```

Observe que a `KeyVaultConfigurationProvider` exige algumas definições de configuração que precisam ser armazenadas em uma das outras fontes de configuração.

Quando o aplicativo é iniciado, a `KeyVaultConfigurationProvider` enumera todos os segredos no cofre da chave. Para cada segredo, ela procura uma marca chamada “ConfigKey”. O valor da marca é o nome da configuração.

> [AZURE.NOTE] [Tags]As [key-tags] são metadados opcionais armazenados com uma chave. As marcas são usadas aqui porque os nomes de chave não podem conter caracteres dois-pontos (:).

```csharp
var kvClient = new KeyVaultClient(GetTokenAsync);
var secretsResponseList = await kvClient.GetSecretsAsync(_vault, MaxSecrets, token);
foreach (var secretItem in secretsResponseList.Value)
{
    //The actual config key is stored in a tag with the Key "ConfigKey"
    // because ':' is not supported in a shared secret name by Key Vault.
    if (secretItem.Tags != null && secretItem.Tags.ContainsKey(ConfigKey))
    {
        var secret = await kvClient.GetSecretAsync(secretItem.Id, token);
        Data.Add(secret.Tags[ConfigKey], secret.Value);
    }
}
```

> [AZURE.NOTE] Confira [KeyVaultConfigurationProvider.cs].

## Configuração do Cofre da Chave no aplicativo Surveys

Pré-requisitos:

- Instale os [Cmdlets do Azure Resource Manager][azure-rm-cmdlets]
- Configure o aplicativo Surveys conforme descrito em [Execução do aplicativo Surveys][readme].

Etapas de alto nível:

1. Configure um usuário de administração no locatário.
2. Configure um certificado de cliente.
3. Crie um cofre da chave.
4. Adicione definições de configuração para o cofre da chave.
5. Remova o comentário do código que habilita o cofre da chave.
6. Atualize os segredos do usuário do aplicativo.

### Configurar um usuário administrador

> [AZURE.NOTE] Para criar um cofre da chave, você deve usar uma conta que possa gerenciar sua assinatura do Azure. Além disso, qualquer aplicativo que você autorizar para ler o cofre da chave deve ser registrado no mesmo locatário que essa conta.

Nesta etapa, você confirmará se pode criar um cofre da chave enquanto estiver conectado como um usuário no locatário no qual o aplicativo Surveys está registrado.

Primeiro, altere o diretório associado à sua assinatura do Azure.

1. Faça logon no [portal de gerenciamento do Azure][azure-management-portal]

2. Clique em **Configurações**.

    ![Configurações](media/guidance-multitenant-identity/settings.png)

3. Selecione sua assinatura do Azure.

4. Clique em **Editar Diretório** na parte inferior do portal.

    ![Configurações](media/guidance-multitenant-identity/edit-directory.png)

5. Em "Alterar o diretório associado", selecione o locatário do Azure AD onde o aplicativo Surveys está registrado,

    ![Configurações](media/guidance-multitenant-identity/edit-directory2.png)

6. Clique no botão de seta e conclua a caixa de diálogo.

Crie um usuário administrador no locatário do Azure AD onde o aplicativo Surveys está registrado.

1. Faça logon no [portal de gerenciamento do Azure][azure-management-portal].

2. Selecione o locatário do Azure AD onde seu aplicativo está registrado.

3. Clique em **Usuários** > **Adicionar Usuário**.

4. Na caixa de diálogo **Adicionar Usuário**, atribua o usuário à função de Administrador Global.

Adicione o usuário administrador como um coadministrador de sua assinatura do Azure.

1. Faça logon no [portal de gerenciamento do Azure][azure-management-portal].

2. Clique em **Configurações** e selecione sua assinatura do Azure.

3. Clique em **Administradores**

4. Clique em **Adicionar** na parte inferior do portal.

5. Insira o email do usuário administrador que você criou anteriormente.

6. Marque a caixa de seleção da assinatura.

7. Clique no botão de marca de seleção para concluir a caixa de diálogo.

![Adicionar um co-administrador](media/guidance-multitenant-identity/co-admin.png)


### Configurar um certificado de cliente

1. Execute o script PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] da seguinte maneira:
    ```
    .\Setup-KeyVault.ps1 -Subject <<subject>>
    ```
    Para o parâmetro `Subject`, insira qualquer nome, como "surveysapp". O script gera um certificado autoassinado e o armazena no repositório de certificados "Usuário Atual/Pessoal".

2. A saída do script é um fragmento JSON. Adicione isso ao manifesto do aplicativo Web da seguinte maneira:

    1. Faça logon no [portal de gerenciamento do Azure][azure-management-portal] e navegue até o diretório do Azure AD.

    2. Clique em **Aplicativos**.

    3. Selecione o aplicativo Surveys.

    4.	Clique em **Gerenciar Manifesto** e selecione **Baixar Manifesto**.

    5.	Abra o arquivo de manifesto JSON em um editor de texto. Cole a saída do script na propriedade `keyCredentials`. O arquivo deve ser semelhante ao seguinte:
    ```
            "keyCredentials": [
                {
                  "type": "AsymmetricX509Cert",
                  "usage": "Verify",
                  "keyId": "29d4f7db-0539-455e-b708-....",
                  "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
                  "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
                }
              ],
    ```          
    6.	Salve as alterações feitas no arquivo JSON.

    7.	Volte para o portal. Clique em **Gerenciar Manifesto** > **Carregar Manifesto** e carregue o arquivo JSON.

3. Adicione o mesmo fragmento JSON para o manifesto do aplicativo da API Web (Surveys.WebAPI).

4. Execute o comando a seguir para obter a impressão digital do certificado.
    ```
    certutil -store -user my [subject]
    ```
    onde `[subject]` é o valor especificado para o Assunto no script do PowerShell. A impressão digital é listada em "Cert Hash(sha1)". Remova os espaços entre os números hexadecimais.

Posteriormente, você usará a impressão digital.

### Criar um cofre de chave

1. Execute o script PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] da seguinte maneira:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ResourceGroupName <<resource group name>> -Location <<location>>
    ```

    Quando receber a solicitação pelas credenciais, entre como o usuário do Azure AD que você criou anteriormente. O script cria um novo grupo de recursos e um novo cofre da chave dentro desse grupo de recursos.

    Observação: para o parâmetro -Location, você pode usar o seguinte comando do PowerShell para obter uma lista de regiões válidas:

    ```
    Get-AzureRmResourceProvider -ProviderNamespace "microsoft.keyvault" | Where-Object { $_.ResourceTypes.ResourceTypeName -eq "vaults" } | Select-Object -ExpandProperty Locations
    ```

2. Execute SetupKeyVault.ps novamente, com os seguintes parâmetros:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ApplicationIds @("<<web app client ID>>", "<<web API client ID>>")
    ```

    onde

    - nome do cofre da chave = o nome que você deu ao cofre da chave na etapa anterior.
    - ID de cliente do aplicativo Web = a ID do cliente para o aplicativo Web Surveys.
    - ID de cliente da api Web = a ID do cliente para o aplicativo Surveys.WebAPI

    Exemplo:
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName tailspinkv -ApplicationIds @("f84df9d1-91cc-4603-b662-302db51f1031", "8871a4c2-2a23-4650-8b46-0625ff3928a6")
    ```

    > [AZURE.NOTE] Você pode obter as IDs de cliente no [Portal de Gerenciamento do Azure][azure-management-portal]. Selecione o locatário do Azure AD, escolha o aplicativo e clique em **Configurar**.

    Esse script autoriza o aplicativo Web e a API Web a recuperar os segredos de seu cofre da chave. Confira [Introdução ao Cofre da Chave do Azure][authorize-app] para saber mais.

### Adicionar definições de configuração para o cofre da chave

1. Execute SetupKeyVault.ps da seguinte maneira:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName RedisCache -KeyValue "<<Redis DNS name>>.redis.cache.windows.net,password=<<Redis access key>>,ssl=true" -ConfigName "Redis:Configuration"
    ```
    onde

    - nome do cofre da chave = o nome que você deu ao cofre da chave na etapa anterior.
    - nome DNS de Redis = o nome DNS de sua instância de cache Redis.
    - chave de acesso Redis = a chave de acesso da sua instância de cache Redis.

    Esse comando adiciona um segredo ao cofre da chave. O segredo é um par de nome/valor e mais uma marca:

    -	O nome da chave não é usado pelo aplicativo, mas deve ser exclusivo no Cofre da Chave.
    -	O valor é o valor da opção de configuração, neste caso, a cadeia de conexão do Redis.
    -	a marca "ConfigKey" contém o nome da chave de configuração.

2. Agora, convém testar se você armazenou os segredos no cofre da chave com êxito. Execute o seguinte comando do PowerShell:

    ```
    Get-AzureKeyVaultSecret <<key vault name>> RedisCache | Select-Object *
    ```
    O resultado deve mostrar o valor do segredo e mais alguns metadados:

    ![Saída do PowerShell](media/guidance-multitenant-identity/get-secret.png)

3. Execute SetupKeyVault.ps novamente para adicionar a cadeia de conexão do banco de dados:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName ConnectionString -KeyValue <<DB connection string>> -ConfigName "Data:SurveysConnectionString"
    ```

    em que `<<DB connection string>>` é o valor da cadeia de conexão do banco de dados.

    Para testar com o banco de dados local, copie a cadeia de conexão do arquivo Tailspin.Surveys.Web/appsettings.json. Se você fizer isso, lembre-se de alterar as barras duplas invertidas ('\\\') por uma barra invertida. A barra dupla invertida é um caractere de escape no arquivo JSON.

    Exemplo:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName mykeyvault -KeyName ConnectionString -KeyValue "Server=(localdb)\MSSQLLocalDB;Database=Tailspin.SurveysDB;Trusted_Connection=True;MultipleActiveResultSets=true" -ConfigName "Data:SurveysConnectionString"
    ```

### Remover o comentário do código que habilita o Cofre da Chave

1. Abra a solução Tailspin.Surveys.

2. Em [Tailspin.Surveys.Web/Startup.cs][web-startup], localize o seguinte bloco de código e remova o comentário dele.

    ```csharp
    //#if DNX451
    //            _configuration = builder.Build();
    //            builder.AddKeyVaultSecrets(_configuration["AzureAd:ClientId"],
    //                _configuration["KeyVault:Name"],
    //                _configuration["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(_configuration["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

3. Em [Tailspin.Surveys.WebAPI/Startup.cs][web-api-startup], localize o seguinte bloco de código e remova o comentário dele.

    ```csharp
    //#if DNX451
    //            var config = builder.Build();
    //            builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
    //                config["KeyVault:Name"],
    //                config["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

4. Em [Tailspin.Surveys.Web/Startup.cs][web-startup], localize o código que registra o `ICredentialService`. Remova o comentário da linha que usa `CertificateCredentialService` e comente a linha que usa `ClientCredentialService`:

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

    Essa alteração permite que o aplicativo Web use [Declaração de cliente][client-assertion] para obter tokens de acesso OAuth. Com a declaração de cliente, você não precisa de um segredo do cliente OAuth. Como alternativa, você pode armazenar o segredo do cliente no cofre da chave. No entanto, o cofre da chave e a declaração de cliente usam um cliente de certificado, portanto, se você habilitar o cofre da chave, convém habilitar a declaração de cliente também.

### Atualizar os segredos do usuário

No Gerenciador de Soluções, clique com o botão direito no projeto Tailspin.Surveys.Web e selecione **Gerenciar Segredos do Usuário**. No arquivo secrets.json, exclua o JSON existente e cole o seguinte:

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys web app client ID]",
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Surveys.WebAPI application]",
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint. Example: 105b2ff3bc842c53582661716db1b7cdc6b43ec9]",
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "KeyVault": {
        "Name": "[key vault name]"
      }
    }
    ```

Substitua as entradas entre [colchetes] pelos valores corretos.

- `AzureAd:ClientId`: a ID do cliente do aplicativo Surveys.
- `AzureAd:WebApiResourceId`: o URI da ID do Aplicativo que você especificou quando criou o aplicativo Surveys.WebAPI no Azure AD.
- `Asymmetric:CertificateThumbprint`: a impressão digital do certificado que você obteve anteriormente, quando criou o certificado do cliente.
- `KeyVault:Name`: o nome de seu cofre da chave.

> [AZURE.NOTE] `Asymmetric:ValidationRequired` é false, pois o certificado que você criou anteriormente não foi assinado por uma autoridade de certificação raiz. Em produção, use um certificado assinado por uma autoridade de certificação e defina `ValidationRequired` como true.

Salve o arquivo secrets.json atualizado.

Em seguida, no Gerenciador de Soluções, clique com o botão direito do mouse no projeto Tailspin.Surveys.WebApi e selecione **Gerenciar Segredos do Usuário**. Exclua o arquivo JSON existente e cole o seguinte:

```
{
  "AzureAd": {
    "ClientId": "[Surveys.WebAPI client ID]",
    "WebApiResourceId": "https://tailspin5.onmicrosoft.com/surveys.webapi",
    "Asymmetric": {
      "CertificateThumbprint": "[certificate thumbprint]",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "ValidationRequired": "false"
    }
  },
  "KeyVault": {
    "Name": "[key vault name]"
  }
}
```

Substitua as entradas entre [colchetes] e salve o arquivo secrets.json.

> [AZURE.NOTE] Para a API Web, use a ID do cliente para o aplicativo Surveys.WebAPI, não para o aplicativo Surveys.


<!-- Links -->
[authorize-app]: ../key-vault/key-vault-get-started.md/#authorize
[azure-management-portal]: https://manage.windowsazure.com/
[azure-rm-cmdlets]: https://msdn.microsoft.com/library/mt125356.aspx
[client-assertion]: guidance-multitenant-identity-client-assertion.md
[configuration]: https://docs.asp.net/en/latest/fundamentals/configuration.html
[KeyVault]: https://azure.microsoft.com/services/key-vault/
[KeyVaultConfigurationProvider]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[key-tags]: https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_Keytags
[Microsoft.Azure.KeyVault]: https://www.nuget.org/packages/Microsoft.Azure.KeyVault/
[options]: https://docs.asp.net/en/latest/fundamentals/configuration.html#using-options-and-configuration-objects
[readme]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[user-secrets]: http://go.microsoft.com/fwlink/?LinkID=532709
[web-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[web-api-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[parte de uma série]: guidance-multitenant-identity.md
[KeyVaultConfigurationProvider.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[aplicativo de exemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->