<properties
   pageTitle="Autenticação de uma entidade de serviço com o Azure Resource Manager | Microsoft Azure"
   description="Descreve como conceder acesso a uma entidade de serviço por meio do controle de acesso baseado em funções e autenticá-la. Mostra como executar essas tarefas com o PowerShell e CLI do Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="02/29/2016"
   ms.author="tomfitz"/>

# Autenticação de uma entidade de serviço com o Gerenciador de Recursos do Azure

Este tópico mostra como permitir que uma entidade de serviço (como um processo automatizado, aplicativo ou serviço) acesse outros recursos em sua assinatura. Com o Gerenciador de Recursos do Azure, você pode usar o controle de acesso baseado em função para conceder ações permitidas para uma entidade de serviço e autenticar essa entidade de serviço.

Este tópico mostra como usar o Azure PowerShell ou a CLI do Azure para Mac, Linux e Windows para criar um aplicativo e uma entidade de serviço, atribuir uma função à entidade de serviço e autenticar-se como a entidade de serviço. Se você não tem o Azure PowerShell instalado, consulte [Como instalar e configurar o Azure PowerShell](./powershell-install-configure.md). Se você não tiver a CLI do Azure instalada, consulte [Instalar e configurar a CLI do Azure](xplat-cli-install.md). Para obter informações sobre como usar o portal para executar essas etapas, consulte o aplicativo [Criar Active Directory e entidade de serviço usando o portal](resource-group-create-service-principal-portal.md)

## Conceitos
1. Azure Active Directory - um serviço de gerenciamento de identidades e acessos para a nuvem. Para obter mais informações, consulte [O que é Azure Active Directory](active-directory/active-directory-whatis.md)
2. Entidade de serviço - uma instância de um aplicativo em um diretório que precisa acessar outros recursos.
3. Aplicativo do Active Directory - registro do diretório que identifica um aplicativo para o AAD.

Para obter uma explicação mais detalhada de aplicativos e entidades de serviço, consulte [Objetos de aplicativo e de entidade de serviço](active-directory/active-directory-application-objects.md). Para obter mais informações sobre a autenticação do Active Directory, consulte [Cenários de autenticação do Azure AD](active-directory/active-directory-authentication-scenarios.md).

Este tópico mostra quatro caminhos para criar um aplicativo do Active Directory e autenticar esse aplicativo. Os caminhos variam dependendo se você quiser usar uma senha ou um certificado para a autenticação ou se preferir usar o PowerShell ou a CLI do Azure. Os caminhos são:

- [Autenticar com senha - PowerShell](#authenticate-with-password---powershell)
- [Autenticar com certificado - PowerShell](#authenticate-with-certificate---powershell)
- [Autenticar com senha - CLI do Azure](#authenticate-with-password---azure-cli)
- [Autenticar com certificado - CLI do Azure](#authenticate-with-certificate---azure-cli)

## Autenticar com senha - PowerShell

Nesta seção, você executará as etapas para criar uma entidade de serviço para um aplicativo do Active Directory do Azure, atribuir uma função à entidade de serviço e autenticar como a entidade de serviço ao fornecer o identificador do aplicativo e a senha.

1. Entre na sua conta.

        PS C:\> Login-AzureRmAccount

1. Criar um novo aplicativo do Active Directory executando o comando **New-AzureRmADApplication**. Forneça um nome de exibição para seu aplicativo, o URI para uma página que descreve o aplicativo (o link não é verificado), os URIs que identificam seu aplicativo e a senha para a identidade do seu aplicativo.

        PS C:\> $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     Examine o novo objeto de aplicativo. A propriedade **ApplicationId** é necessária para criar entidades de serviço, atribuições de função e adquirir o token de acesso.

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}

2. Crie uma entidade de serviço para seu aplicativo passando a id do aplicativo do Active Directory.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     Você criou uma entidade de serviço no diretório, mas o serviço não tem quaisquer permissões ou escopo atribuídos. Você precisará conceder explicitamente à entidade de serviço permissões para executar operações em algum escopo.

3. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo você concederá à entidade de serviço a permissão para ler todos os recursos na assinatura. Para o parâmetro **ServicePrincipalName**, forneça **ApplicationId** ou **IdentifierUris** usado ao criar o aplicativo. Para saber mais sobre o controle de acesso baseado em função, confira [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md)

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

Você criou um aplicativo do Active Directory e uma entidade de serviço para esse aplicativo. Você atribuiu a entidade de serviço a uma função. Agora, você precisa fazer logon como a entidade de serviço para executar operações como a entidade de serviço. Este tópico mostra três opções:

- [Fornecer manualmente as credenciais por meio do PowerShell](#manually-provide-credentials-through-powershell)
- [Fornecer credenciais por meio do script PowerShell automatizado](#provide-credentials-through-automated-powershell-script)
- [Fornecer credenciais por meio de código em um aplicativo](#provide-credentials-through-code-in-an-application)

<a id="manually-provide-credentials-through-powershell" />
### Fornecer manualmente as credenciais por meio do PowerShell

Você pode fornecer manualmente as credenciais para a entidade de serviço ao executar o script sob demanda ou comandos.

1. Crie um novo objeto **PSCredential** que contenha suas credenciais ao executar o comando **Get-Credential**.

        PS C:\> $creds = Get-Credential

2. Será solicitado que você insira suas credenciais de conta do Azure. Como nome de usuário, use um dos parâmetros **ApplicationId** ou **IdentifierUris** que você usou ao criar o aplicativo. Como senha, use aquela especificada ao criar a conta.

     ![inserir as credenciais][1]

3. Obtenha a assinatura na qual a atribuição de função foi criada. Essa assinatura será usada para obter a **TenantId** do locatário no qual reside a atribuição de função da entidade de serviço.

        PS C:\> $subscription = Get-AzureRmSubscription

     Se você criou a atribuição de função em uma assinatura diferente da assinatura selecionada atualmente, poderá especificar os parâmetros **SubscriptoinId** ou **SubscriptionName** para recuperar uma assinatura diferente.

4. Faça logon como a entidade de serviço usando o cmdlet **Login-AzureRmAccount**, mas forneça o objeto de credenciais e especifique se essa conta é uma entidade de serviço.

        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId
        Environment           : AzureCloud
        Account               : {guid}
        Tenant                : {guid}
        Subscription          : {guid}
        CurrentStorageAccount :

Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory criado.

<a id="provide-credentials-through-automated-powershell-script" />
### Fornecer credenciais por meio do script PowerShell automatizado

Esta seção mostra como fazer logon como a entidade de serviço sem precisar inserir as credenciais manualmente. Você não precisa fornecer a senha manualmente porque ela é recuperada a partir de um Cofre da Chave.

> [AZURE.NOTE] Incluir diretamente uma senha em seu script do PowerShell não é seguro porque a senha é exposta como texto. Em vez disso, use um serviço como o Cofre da Chave para armazenar a senha e recuperá-la ao executar o script.

Essas etapas pressupõem que você tenha configurado um Cofre da Chave e um segredo que armazena a senha. Para implantar um Cofre da Chave e um segredo por meio de um modelo, consulte [Formato de modelo do Cofre da Chave](). Para saber mais sobre o Cofre da Chave, consulte [Introdução ao Cofre da Chave do Azure](./key-vault/key-vault-get-started.md).

1. Recupere sua senha (no exemplo a seguir, armazenada como segredo com o nome **appPassword**) no Cofre da Chave.

        PS C:\> $secret = Get-AzureKeyVaultSecret -VaultName examplevault -Name appPassword
        
2. Obtenha seu aplicativo do Active Directory. Você usará a id do aplicativo ao fazer logon.

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"

3. Crie um novo objeto **PSCredential** fornecendo a id do aplicativo e a senha como credenciais.

        PS C:\> $creds = New-Object System.Management.Automation.PSCredential ($azureAdApplication.ApplicationId, $secret.SecretValue)
    
4. Obtenha a assinatura na qual a atribuição de função foi criada. Essa assinatura será usada posteriormente para obter a **TenantId** do locatário no qual reside a atribuição de função da entidade de serviço.

        PS C:\> $subscription = Get-AzureRmSubscription

     Se você criou a atribuição de função em uma assinatura diferente da assinatura selecionada atualmente, poderá especificar os parâmetros **SubscriptoinId** ou **SubscriptionName** para recuperar uma assinatura diferente.

5. Faça logon como a entidade de serviço usando o cmdlet **Login-AzureRmAccount**, mas forneça o objeto de credenciais e especifique se essa conta é uma entidade de serviço.
    
        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $subscription.TenantId

Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory criado.

<a id="provide-credentials-through-code-in-an-application" />
### Fornecer credenciais por meio de código em um aplicativo

Para autenticar em um aplicativo .NET, inclua o código a seguir. Você precisará da id do aplicativo para o aplicativo do Active Directory e a senha e a id do locatário para a assinatura. Depois de recuperar o token de acesso, você poderá trabalhar com os recursos na assinatura.

    public static string GetAccessToken()
    {
      var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
      var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
      var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

      if (result == null) {
        throw new InvalidOperationException("Failed to obtain the JWT token");
      }

      string token = result.AccessToken;

      return token;
    }


## Autenticar com certificado - PowerShell

Nesta seção, você executará as etapas para criar uma entidade de serviço para um aplicativo do Active Directory do Azure, atribuir uma função à entidade de serviço e autenticar como a entidade de serviço ao fornecer um certificado. Este tópico pressupõe que um certificado tenha sido emitido para você.

Ele mostra duas maneiras de trabalhar com certificados - credenciais de chave e valores de chave. Você pode usar uma das duas abordagens.

Primeiramente, você deve configurar alguns valores no PowerShell que usará mais tarde ao criar o aplicativo.

1. Entre na sua conta.

        PS C:\> Login-AzureRmAccount

1. Em ambas as abordagens, crie um objeto X509Certificate2 a partir de seu certificado e recupere o valor da chave. Use o caminho para seu certificado e a senha do certificado.

        $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Se estiver usando credenciais de chave, crie o objeto de credenciais de chave e defina seu valor como o `$keyValue` da etapa anterior. O exemplo a seguir inclui chamar `Add-Type` para adicionar um tipo a partir de um assembly. O caminho mostrado no exemplo deve ser semelhante ao seu caminho, mas pode ser um pouco diferente.

        Add-Type -Path 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ResourceManager\AzureResourceManager\AzureRM.Resources\Microsoft.Azure.Commands.Resources.dll'
        $currentDate = Get-Date
        $endDate = $currentDate.AddYears(1)
        $keyId = [guid]::NewGuid()
        $keyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $keyCredential.StartDate = $currentDate
        $keyCredential.EndDate= $endDate
        $keyCredential.KeyId = $keyId
        $keyCredential.Type = "AsymmetricX509Cert"
        $keyCredential.Usage = "Verify"
        $keyCredential.Value = $keyValue

3. Crie um aplicativo no diretório. O primeiro comando mostra como usar os valores de chave.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    Ou use o segundo exemplo para atribuir credenciais de chave.

         $azureAdApplication = New-AzureRmADApplication -DisplayName "example" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyCredentials $keyCredential

    Examine o novo objeto de aplicativo. A propriedade **ApplicationId** é necessária para criar entidades de serviço, atribuições de função e adquirir tokens de acesso.

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}       

4. Crie uma entidade de serviço para seu aplicativo passando a id do aplicativo do Active Directory.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    Você criou uma entidade de serviço no diretório, mas o serviço não tem quaisquer permissões ou escopo atribuídos. Você precisará conceder explicitamente à entidade de serviço permissões para executar operações em algum escopo.

5. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo você concederá à entidade de serviço a permissão para ler todos os recursos na assinatura. Para o parâmetro **ServicePrincipalName**, forneça **ApplicationId** ou **IdentifierUris** usado ao criar o aplicativo. Para saber mais sobre o controle de acesso baseado em função, confira [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md)

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

Você criou um aplicativo do Active Directory e uma entidade de serviço para esse aplicativo. Você atribuiu a entidade de serviço a uma função. Agora, você precisa fazer logon como a entidade de serviço para executar operações como a entidade de serviço. Este tópico mostra duas opções:

- [Fornecer certificado por meio do script PowerShell automatizado](#provide-certificate-through-automated-powershell-script)
- [Fornecer certificado por meio do código em um aplicativo](#provide-certificate-through-code-in-an-application)

<a id="provide-certificate-through-automated-powershell-script" />
### Fornecer certificado por meio do script PowerShell automatizado

1. Obtenha seu aplicativo do Active Directory. Você usará a id do aplicativo no logon

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"
        
2. Obtenha a assinatura na qual a atribuição de função foi criada. Essa assinatura será usada posteriormente para obter a **TenantId** do locatário no qual reside a atribuição de função da entidade de serviço.

        PS C:\> $subscription = Get-AzureRmSubscription

     Se você criou a atribuição de função em uma assinatura diferente da assinatura selecionada atualmente, poderá especificar os parâmetros **SubscriptoinId** ou **SubscriptionName** para recuperar uma assinatura diferente.

3. Obtenha o certificado que será usado para a autenticação.

        PS C:\> $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")

4. Para autenticar no PowerShell, forneça a impressão digital do certificado, a id do aplicativo e a id do locatário.

        PS C:\> Login-AzureRmAccount -CertificateThumbprint $cert.Thumbprint -ApplicationId $azureAdApplication.ApplicationId -ServicePrincipal -TenantId $subscription.TenantId

Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory criado.

<a id="provide-certificate-through-code-in-an-application" />
### Fornecer certificado por meio do código em um aplicativo

Para autenticar em um aplicativo .NET, inclua o código a seguir. Depois de recuperar o cliente, você poderá acessar recursos na assinatura.

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
    try 
    { 
      store.Open(OpenFlags.ReadOnly); 
      var certCollection = store.Certificates; 
      var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
      cert = certs[0]; 
    } 
    finally 
    { 
      store.Close(); 
    }        

    var certCred = new ClientAssertionCertificate(clientId, cert); 
    var token = authContext.AcquireToken("https://management.core.windows.net/", certCred); 
    var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken); 
    var client = new ResourceManagementClient(creds); 
        

## Autenticar com senha - CLI do Azure

Você começará criando uma entidade de serviço. Para fazer isso, devemos usar a opção criar um aplicativo no diretório. Esta seção nos guiará pela criação de um novo aplicativo no diretório.

1. Alterne para o modo do Gerenciador de Recursos do Azure e entre na sua conta.

        azure config mode arm
        azure login

2. Crie um novo aplicativo do Active Directory executando o comando **azure ad app create**. Forneça um nome de exibição para seu aplicativo, o URI para uma página que descreve o aplicativo (o link não é verificado), os URIs que identificam seu aplicativo e a senha para a identidade do seu aplicativo.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    O aplicativo do Active Directory será retornado. A propriedade AppId é necessária para criar entidades de serviço, atribuições de função e adquirir tokens de acesso.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

3. Crie uma entidade de serviço para seu aplicativo. Forneça a ID do aplicativo que foi retornada na etapa anterior.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    A nova entidade de serviço será retornada. A ID de objeto é necessária ao conceder permissões.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK

    Você criou uma entidade de serviço no diretório, mas o serviço não tem quaisquer permissões ou escopo atribuídos. Você precisará conceder explicitamente à entidade de serviço permissões para executar operações em algum escopo.

4. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo você concederá à entidade de serviço a permissão para ler todos os recursos na assinatura. Para saber mais sobre o controle de acesso baseado em função, confira [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md)

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

Você criou um aplicativo do Active Directory e uma entidade de serviço para esse aplicativo. Você atribuiu a entidade de serviço a uma função. Agora, você precisa fazer logon como a entidade de serviço para executar operações como a entidade de serviço. Este tópico mostra três opções:

- [Fornecer manualmente as credenciais por meio da CLI do Azure](#manually-provide-credentials-through-azure-cli)
- [Fornecer credenciais por meio do script CLI do Azure automatizado](#provide-credentials-through-automated-azure-cli-script)
- [Fornecer credenciais por meio de código em um aplicativo](#provide-credentials-through-code-in-an-application-cli)

<a id="manually-provide-credentials-through-Azure-cli" />
### Fornecer manualmente as credenciais por meio da CLI do Azure

Se você quiser entrar manualmente como a entidade de serviço, poderá usar o comando **azure login**. Você deve fornecer a id do locatário, id do aplicativo e a senha. Incluir diretamente a senha em um script não é seguro porque a senha é armazenada no arquivo. Consulte a próxima seção para ver a melhor opção ao executar um script automatizado.

1. Determine a **TenantId** para a assinatura que contém a entidade de serviço. Você deve remover as aspas duplas inicial e final que são retornadas da saída json antes de passá-la como um parâmetro.

        tenantId=$(azure account show -s <subscriptionId> --json | jq '.[0].tenantId' | sed -e 's/^"//' -e 's/"$//')

2. Para o nome de usuário, use a **AppId** que você usou ao criar a entidade de serviço. Se você precisar recuperar a id do aplicativo, use o comando a seguir. Forneça o nome do aplicativo do Active Directory no parâmetro **search**.

        appId=$(azure ad app show --search exampleapp --json | jq '.[0].appId' | sed -e 's/^"//' -e 's/"$//')

3. Faça logon como a entidade de serviço.

        azure login -u "$appId" --service-principal --tenant "$tenantId"

Quando solicitado, forneça a senha especificada ao criar a conta.

    info:    Executing command login
    Password: ********

Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory criado.

<a id="provide-credentials-through-automated-azure-cli-script" />
### Fornecer credenciais por meio do script CLI do Azure automatizado

Esta seção mostra como fazer logon como a entidade de serviço sem precisar inserir as credenciais manualmente.

> [AZURE.NOTE] Incluir uma senha em seu script CLI do Azure não é seguro porque a senha é exposta como texto. Em vez disso, use um serviço como o Cofre da Chave para armazenar a senha e recuperá-la ao executar o script.

Essas etapas pressupõem que você tenha configurado um Cofre da Chave e um segredo que armazena a senha. Para implantar um Cofre da Chave e um segredo por meio de um modelo, consulte [Formato de modelo do Cofre da Chave](). Para saber mais sobre o Cofre da Chave, consulte [Introdução ao Cofre da Chave do Azure](./key-vault/key-vault-get-started.md).

1. Recupere sua senha (no exemplo a seguir, armazenada como segredo com o nome **appPassword**) no Cofre da Chave. Você deve remover as aspas duplas inicial e final que são retornadas da saída json antes de passá-la como o parâmetro da senha.

        secret=$(azure keyvault secret show --vault-name examplevault --secret-name appPassword --json | jq '.value' | sed -e 's/^"//' -e 's/"$//')
    
2. Determine a **TenantId** para a assinatura que contém a entidade de serviço.

        tenantId=$(azure account show -s <subscriptionId> --json | jq '.[0].tenantId' | sed -e 's/^"//' -e 's/"$//')

3. Para o nome de usuário, use a **AppId** que você usou ao criar a entidade de serviço. Se você precisar recuperar a id do aplicativo, use o comando a seguir. Forneça o nome do aplicativo do Active Directory no parâmetro **search**.

        appId=$(azure ad app show --search exampleapp --json | jq '.[0].appId' | sed -e 's/^"//' -e 's/"$//')

4. Faça logon como a entidade de serviço fornecendo a id do aplicativo, senha do Cofre da Chave, id do locatário.

        azure login -u "$appId" -p "$secret" --service-principal --tenant "$tenantId"
    
Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory criado.

<a id="provide-credentials-through-code-in-an-application-cli" />
### Fornecer credenciais por meio de código em um aplicativo

Para autenticar em um aplicativo .NET, inclua o código a seguir. Você precisará da id do aplicativo para o aplicativo do Active Directory e a senha e a id do locatário para a assinatura. Depois de recuperar o token de acesso, você poderá trabalhar com os recursos na assinatura.

    public static string GetAccessToken()
    {
      var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
      var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
      var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

      if (result == null) {
        throw new InvalidOperationException("Failed to obtain the JWT token");
      }

      string token = result.AccessToken;

      return token;
    }

## Autenticar com certificado - CLI do Azure

Nesta seção, você executará as etapas para criar uma entidade de serviço para um aplicativo do Active Directory do Azure que usa um certificado para autenticação. Este tópico pressupõe que você emitiu um certificado e instalou o [OpenSSL](http://www.openssl.org/).

1. Crie arquivo **. PEM** com:

        openssl pkcs12 -in C:\certificates\examplecert.pfx -out C:\certificates\examplecert.pem -nodes

2. Abra o arquivo **. PEM** e copie os dados do certificado. Procure a longa sequência de caracteres entre **-----BEGIN CERTIFICATE-----** e **-----END CERTIFICATE-----**.

3. Crie um novo Aplicativo do Active Directory executando o comando **azure ad app create** e forneça os dados do certificado copiados na etapa anterior como o valor da chave.

        azure ad app create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>

    O aplicativo do Active Directory será retornado. A propriedade AppId é necessária para criar entidades de serviço, atribuições de função e adquirir tokens de acesso.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

4. Crie uma entidade de serviço para seu aplicativo. Forneça a ID do aplicativo que foi retornada na etapa anterior.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    A nova entidade de serviço será retornada. A ID de objeto é necessária ao conceder permissões.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
5. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo você concederá à entidade de serviço a permissão para ler todos os recursos na assinatura. Para saber mais sobre o controle de acesso baseado em função, confira [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md)

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

Você criou um aplicativo do Active Directory e uma entidade de serviço para esse aplicativo. Você atribuiu a entidade de serviço a uma função. Agora, você precisa fazer logon como a entidade de serviço para executar operações como a entidade de serviço. Este tópico mostra duas opções:

- [Fornecer certificado por meio do script CLI do Azure automatizado](#provide-certificate-through-automated-azure-cli-script)
- [Fornecer certificado por meio do código em um aplicativo](#provide-certificate-through-code-in-an-application-cli)

<a id="provide-certificate-through-automated-azure-cli-script" />
### Fornecer certificado por meio do script CLI do Azure automatizado

1. Você precisa recuperar a impressão digital do certificado e remover os caracteres desnecessários.

        cert=$(openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g')
    
     Que retorna um valor de impressão digital semelhante a:

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Determine a **TenantId** para a assinatura que contém a entidade de serviço.

        tenantId=$(azure account show -s <subscriptionId> --json | jq '.[0].tenantId' | sed -e 's/^"//' -e 's/"$//')

3. Para o nome de usuário, use a **AppId** que você usou ao criar a entidade de serviço. Se você precisar recuperar a id do aplicativo, use o comando a seguir. Forneça o nome do aplicativo do Active Directory no parâmetro **search**.

        appId=$(azure ad app show --search exampleapp --json | jq '.[0].appId' | sed -e 's/^"//' -e 's/"$//')

4. Para autenticar na CLI do Azure, forneça a impressão digital do certificado, arquivo do certificado, id do aplicativo e id do locatário.

        azure login --service-principal --tenant "$tenantId" -u "$appId" --certificate-file C:\certificates\examplecert.pem --thumbprint "$cert"

Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory criado.

<a id="provide-certificate-through-code-in-an-application-cli" />
### Fornecer certificado por meio do código em um aplicativo

Para autenticar em um aplicativo .NET, inclua o código a seguir. Depois de recuperar o cliente, você poderá acessar recursos na assinatura.

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
    try 
    { 
      store.Open(OpenFlags.ReadOnly); 
      var certCollection = store.Certificates; 
      var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
      cert = certs[0]; 
    } 
    finally 
    { 
      store.Close(); 
    }        

    var certCred = new ClientAssertionCertificate(clientId, cert); 
    var token = authContext.AcquireToken("https://management.core.windows.net/", certCred); 
    var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken); 
    var client = new ResourceManagementClient(creds); 
       
Para obter mais informações sobre como usar os certificados e a CLI do Azure, consulte [Autenticação baseada em certificados com as Entidades de Serviço do Azure a partir da linha de comando do Linux](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)

## Próximas etapas
  
- Para saber mais sobre como usar o portal com entidades de serviço, consulte [Criar uma nova entidade de serviço do Azure usando o portal do Azure](./resource-group-create-service-principal-portal.md)  
- Para obter orientações sobre como implantar recursos de segurança com o Gerenciador de Recursos do Azure, consulte [Considerações de segurança do Gerenciador de Recursos do Azure](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=AcomDC_0302_2016-->