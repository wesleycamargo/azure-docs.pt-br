<properties
   pageTitle="Autenticação de uma entidade de serviço com o Gerenciador de Recursos do Azure"
   description="Descreve como conceder acesso a uma entidade de serviço por meio do controle de acesso baseado em função e autenticá-la. Mostra como executar essas tarefas com o PowerShell e CLI do Azure."
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
   ms.date="08/14/2015"
   ms.author="tomfitz"/>

# Autenticação de uma entidade de serviço com o Gerenciador de Recursos do Azure

Este tópico mostra como permitir que uma entidade de serviço (como um processo automatizado, aplicativo ou serviço) acesse outros recursos em sua assinatura. Com o Gerenciador de Recursos do Azure, você pode usar o controle de acesso baseado em função para conceder ações permitidas para uma entidade de serviço e autenticar essa entidade de serviço. Este tópico mostra como usar o PowerShell e CLI do Azure para atribuir uma função à entidade de serviço e autenticar essa entidade.

Ele mostra como autenticar com um nome de usuário e uma senha ou um certificado.

Você pode usar o Azure PowerShell ou a CLI do Azure para Mac, Linux e Windows. Se você não tem o Azure PowerShell instalado, consulte [Como instalar e configurar o Azure PowerShell](./powershell-install-configure.md). Se você não tiver a CLI do Azure instalada, consulte [Instalar e configurar a CLI do Azure](xplat-cli-install.md).

## Conceitos
1. AAD (Active Directory do Azure) - um serviço de gerenciamento de identidades e acesso para a nuvem. Para obter mais informações, consulte [O que é o Active Directory do Azure](active-directory/active-directory-whatis.md)
2. Entidade de serviço - uma instância de um aplicativo em um diretório que precisa acessar outros recursos.
3. Aplicativo do AD - registro de diretório que identifica um aplicativo ao AAD. Para obter mais informações, consulte [Noções básicas de autenticação no AD do Azure](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).

## Autenticar a entidade de serviço com senha — PowerShell

Nesta seção, você executará as etapas para criar uma entidade de serviço para um aplicativo do Active Directory do Azure, atribuir uma função à entidade de serviço e autenticar como a entidade de serviço ao fornecer o identificador do aplicativo e a senha.

1. Criar um novo aplicativo AAD executando o comando **New-AzureADApplication**. Forneça um nome de exibição para seu aplicativo, o URI para uma página que descreve o aplicativo (o link não é verificado), os URIs que identificam seu aplicativo e a senha para a identidade do seu aplicativo.

        PS C:\> $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

     Examine o novo objeto de aplicativo. A propriedade **ApplicationId** é necessária para criar entidades de serviço, atribuições de função e aquisição de tokens JWT.

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}


2. Crie uma entidade de serviço para seu aplicativo.

        PS C:\> New-AzureADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     Você criou uma entidade de serviço no diretório, mas o serviço não tem quaisquer permissões ou escopo atribuídos. Você precisará conceder explicitamente à entidade de serviço permissões para executar operações em algum escopo.

3. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo você concederá à entidade de serviço a permissão para ler todos os recursos na assinatura. Para o parâmetro **ServicePrincipalName**, forneça um dos parâmetros **ApplicationId** ou **IdentifierUris** que você usou ao criar o aplicativo. Para obter mais informações sobre controle de acesso baseado em função, consulte [Gerenciando e auditando o acesso a recursos](azure-portal/resource-group-rbac.md)

        PS C:\> New-AzureRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

4. Obtenha a assinatura na qual a atribuição de função foi criada. Essa assinatura será usada posteriormente para obter a **TenantId** do locatário no qual reside a atribuição de função da entidade de serviço.

        PS C:\> $subscription = Get-AzureSubscription | where { $_.IsCurrent }

     Se você criou a atribuição de função em uma assinatura que não seja a assinatura selecionada, você pode especificar os parâmetros **SubscriptoinId** ou **SubscriptionName** para recuperar uma assinatura diferente.

5. Crie um novo objeto **PSCredential** que contenha suas credenciais ao executar o comando **Get-Credential**.

        PS C:\> $creds = Get-Credential

     Será solicitado que você insira suas credenciais de conta do Azure.

     ![][1]

     Como nome de usuário, use um dos parâmetros **ApplicationId** ou **IdentifierUris** que você usou ao criar o aplicativo. Como senha, use aquela especificada ao criar a conta.

6. Use as credenciais que você inseriu como uma entrada para o cmdlet **Add-AzureAccount**, que fará o logon da entidade de serviço:

        PS C:\> Add-AzureAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId

     Agora, você deve ser autenticado como a entidade de serviço para o aplicativo AAD que você criou.


## Autenticar a entidade de serviço com certificado - PowerShell

Nesta seção, você executará as etapas para criar uma entidade de serviço para um aplicativo do Active Directory do Azure, atribuir uma função à entidade de serviço e autenticar como a entidade de serviço ao fornecer um certificado. Este tópico pressupõe que um certificado tenha sido emitido para você.

Ele mostra duas maneiras de trabalhar com certificados - credenciais de chave e valores de chave. Você pode usar uma das duas abordagens.

Primeiramente, você deve configurar alguns valores no PowerShell que usará mais tarde ao criar o aplicativo.

1. Em ambas as abordagens, crie um objeto X509Certificate do seu certificado e recupere o valor da chave. Use o caminho para seu certificado e a senha do certificado.

        $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Se estiver usando credenciais de chave, crie o objeto de credenciais de chave e defina seu valor como o `$keyValue` da etapa anterior.

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

        $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    Ou use o segundo exemplo para atribuir credenciais de chave.

         $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyCredentials $keyCredential

    Examine o novo objeto de aplicativo. A propriedade **ApplicationId** é necessária para criar entidades de serviço, atribuições de função e aquisição de tokens JWT.

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : 76fa8d97-f07e-4b9a-b871-a57a7acd777a
        ApplicationObjectId     : c36b7b57-a949-4401-b381-18a5210aff10
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in
                          user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "9f13c6c6-35ba-43d6-b8b3-6a87aa641388",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}

4. Crie uma entidade de serviço para seu aplicativo.

        PS C:\> New-AzureADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    Você criou uma entidade de serviço no diretório, mas o serviço não tem quaisquer permissões ou escopo atribuídos. Você precisará conceder explicitamente à entidade de serviço permissões para executar operações em algum escopo.

5. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo você concederá à entidade de serviço a permissão para ler todos os recursos na assinatura. Para o parâmetro **ServicePrincipalName**, forneça um dos parâmetros **ApplicationId** ou **IdentifierUris** que você usou ao criar o aplicativo. Para obter mais informações sobre controle de acesso baseado em função, consulte [Gerenciando e auditando o acesso a recursos](azure-portal/resource-group-rbac.md)

        PS C:\> New-AzureRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

6. Para autenticar de um aplicativo, inclua o código a seguir. Depois de recuperar o cliente, você poderá acessar recursos na assinatura.

        string clientId = "<Client ID for your AAD app>"; 
        var subscriptionId = "<Your Azure SubscriptionId>"; 
        string tenant = "<AAD tenant name>.onmicrosoft.com"; 

        var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

        X509Certificate2 cert = null; 
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
        string certName = "examplecert"; 
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
        

## Autenticar a entidade de serviço com senha — CLI do Azure

Você começará criando uma entidade de serviço. Para fazer isso, devemos usar a opção criar um aplicativo no diretório. Esta seção nos guiará pela criação de um novo aplicativo no diretório.

1. Crie um novo aplicativo AAD executando o comando **azure ad app create**. Forneça um nome de exibição para seu aplicativo, o URI para uma página que descreve o aplicativo (o link não é verificado), os URIs que identificam seu aplicativo e a senha para a identidade do seu aplicativo.

        azure ad app create --name "<Your Application Display Name>" --home-page "<https://YourApplicationHomePage>" --identifier-uris "<https://YouApplicationUri>" --password <Your_Password>
        
    O aplicativo do Azure AD é retornado. A propriedade ApplicationId é necessária para criar entidades de serviço, atribuições de função e aquisição de tokens JWT.

        info:    Executing command ad app create
        + Creating application exampleapp                                                
        data:    Application Id:          b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Application Object Id:   d5c519e2-6149-447e-b323-88d2c4ea27de
        data:    Application Permissions:  
        data:                             claimValue:  user_impersonation
        data:                             description:  Allow the application to access exampleapp on behalf of the signed-in user.
        ...
        info:    ad app create command OK

2. Crie uma entidade de serviço para seu aplicativo. Forneça a ID do aplicativo que foi retornada na etapa anterior.

        azure ad sp create b57dd71d-036c-4840-865e-23b71d8098ec
        
    A nova entidade de serviço será retornada. A ID de objeto é necessária ao conceder permissões.
    
        info:    Executing command ad sp create
        + Creating service principal for application b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Object Id:               47193a0a-63e4-46bd-9bee-6a9f6f9c03cb
        data:    Display Name:            exampleapp
        ...
        info:    ad sp create command OK

    Você criou uma entidade de serviço no diretório, mas o serviço não tem quaisquer permissões ou escopo atribuídos. Você precisará conceder explicitamente à entidade de serviço permissões para executar operações em algum escopo.

3. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo você concederá à entidade de serviço a permissão para ler todos os recursos na assinatura. Para o parâmetro **ServicePrincipalName**, forneça um dos parâmetros **ApplicationId** ou **IdentifierUris** que você usou ao criar o aplicativo. Para obter mais informações sobre controle de acesso baseado em função, consulte [Gerenciando e auditando o acesso a recursos](azure-portal/resource-group-rbac.md)

        azure role assignment create --objectId 47193a0a-63e4-46bd-9bee-6a9f6f9c03cb -o Reader -c /subscriptions/{subscriptionId}/

4. Determinar a **TenantId** do locatário em que a entidade de serviço está atribuição de função reside listando as contas e procurando a propriedade **TenantId** na saída.

        azure account list

5. Entre usando a entidade de serviço como sua identidade. Como nome de usuário, use o parâmetro **ApplicationId** que você usou ao criar o aplicativo. Como senha, use aquela especificada ao criar a conta.

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

    Agora, você deve ser autenticado como a entidade de serviço para o aplicativo AAD que você criou.

## Próximas etapas
  
- Para obter uma visão geral sobre o controle de acesso baseado em função, consulte [Gerenciando e auditando o acesso a recursos](azure-portal/resource-group-rbac.md)  
- Para saber mais sobre como usar o portal com entidades de serviço, consulte [Criar uma nova entidade de serviço do Azure usando o portal do Azure](./resource-group-create-service-principal-portal.md)  
- Para obter orientações sobre como implantar recursos de segurança com o Gerenciador de Recursos do Azure, consulte [Considerações de segurança do Gerenciador de Recursos do Azure](best-practices-resource-manager-security.md)


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=August15_HO8-->