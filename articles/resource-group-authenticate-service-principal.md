<properties
   pageTitle="Criar um aplicativo do AD com o PowerShell | Microsoft Azure"
   description="Descreve como usar o Azure PowerShell para criar um aplicativo do Active Directory e conceder acesso a recursos por meio do controle de acesso baseado em função. Ele mostra como autenticar um aplicativo com uma senha ou certificado."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="05/26/2016"
   ms.author="tomfitz"/>

# Usar o Azure PowerShell para criar um aplicativo do Active Directory para acessar recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI do Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)


Este tópico mostra como usar o [Azure PowerShell](powershell-install-configure.md) a fim de criar um aplicativo AD (Active Directory), como um processo automatizado, aplicativo ou serviço, que possa acessar outros recursos em sua assinatura. Com o Azure Resource Manager, você pode usar o controle de acesso baseado em função para conceder ações permitidas ao aplicativo.

Neste artigo, você criará dois objetos: o aplicativo do AD e a entidade do serviço. O aplicativo do AD reside no locatário onde o aplicativo está registrado e define o processo a ser executado. A entidade de serviço contém a identidade do aplicativo do AD e é usada para atribuir permissões. A partir do aplicativo do AD, você pode criar várias entidades de serviço. Para obter uma explicação mais detalhada de aplicativos e entidades de serviço, consulte [Objetos de aplicativo e de entidade de serviço](./active-directory/active-directory-application-objects.md). Para obter mais informações sobre a autenticação do Active Directory, consulte [Cenários de autenticação do Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Há duas opções para autenticar seu aplicativo:

 - senha - adequada quando um usuário deseja entrar de forma interativa durante a execução
 - certificado - adequado para scripts autônomos que devem ser autenticados sem interação do usuário

## Criar um aplicativo do AD com senha

Nesta seção, você executará as etapas para criar o aplicativo do AD com uma senha.

1. Entre na sua conta.

        Add-AzureRmAccount

1. Crie um novo aplicativo do Active Directory fornecendo um nome de exibição para seu aplicativo, o URI para uma página que descreve o aplicativo (o link não é verificado), os URIs que identificam seu aplicativo e a senha para a identidade do seu aplicativo.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     Examine o novo objeto de aplicativo.

        $azureAdApplication
        
     Observe especificamente a propriedade **ApplicationId**, que é necessária para criar entidades de serviço, atribuições de função e adquirir o token de acesso.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}


### Criar entidade de serviço e atribuir à função

A partir de seu aplicativo do AD, você deve criar uma entidade de serviço e atribuir uma função a ela.

1. Crie uma entidade de serviço para seu aplicativo passando a id do aplicativo do Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

2. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo você concederá à entidade de serviço a permissão para ler todos os recursos na assinatura. Para o parâmetro **ServicePrincipalName**, forneça **ApplicationId** ou **IdentifierUris** usado ao criar o aplicativo. Para saber mais sobre o controle de acesso baseado em função, confira [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md)

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

### Fornecer manualmente as credenciais por meio do PowerShell

Você criou um aplicativo do Active Directory e uma entidade de serviço para esse aplicativo. Você atribuiu a entidade de serviço a uma função. Agora, você precisa fazer logon como o aplicativo para executar operações. Você pode fornecer manualmente as credenciais para o aplicativo ao executar scripts sob demanda ou comandos.

1. Crie um novo objeto **PSCredential** que contenha suas credenciais ao executar o comando **Get-Credential**.

        $creds = Get-Credential

2. Será solicitado que você insira suas credenciais de conta do Azure. Como nome de usuário, use um dos parâmetros **ApplicationId** ou **IdentifierUris** que você usou ao criar o aplicativo. Como senha, use aquela especificada ao criar a conta.

     ![inserir as credenciais](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

3. Obtenha a assinatura na qual a atribuição de função foi criada. Essa assinatura será usada para obter a **TenantId** do locatário no qual reside a atribuição de função da entidade de serviço.

        $subscription = Get-AzureRmSubscription

     Se sua conta estiver vinculada a mais de uma assinatura, forneça um nome de assinatura ou id para obter a assinatura com a qual você deseja trabalhar.
     
        $subscription = Get-AzureRmSubscription -SubscriptionName "Azure MSDN - Visual Studio Ultimate"

4. Faça logon como a entidade de serviço especificando que essa conta é uma entidade de serviço e fornecendo o objeto das credenciais.

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $subscription.TenantId
        
     Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory criado.

5. Para usar o token de acesso atual em uma sessão posterior, você pode salvar o perfil.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Você pode abrir o perfil e examinar seu conteúdo. Observe que ele contém um token de acesso.
        
6. Em vez de fazer logon novamente na próxima vez em que você desejar executar o código como a entidade de serviço, basta carregar o perfil.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] O token de acesso expirará, portanto, usar um perfil salvo funcionará somente se o token for válido. Para executar permanentemente scripts autônomos, use um certificado.
        
## Criar um aplicativo do AD com certificado

Nesta seção, você executará as etapas para criar um aplicativo do AD com um certificado.

1. Crie um certificado autoassinado.

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
     Você receberá informações sobre o certificado, incluindo a impressão digital.
     
        Directory: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

        Thumbprint                                Subject
        ----------                                -------
        724213129BD2B950BB3F64FAB0C877E9348B16E9  CN=exampleapp

2. Recupere o valor da chave a partir do certificado.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

3. Entre na sua conta do Azure.

        Add-AzureRmAccount

4. Crie um aplicativo no diretório.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
        
    Examine o novo objeto de aplicativo.

        $azureAdApplication

    Observe a propriedade **ApplicationId**, que é necessária para criar entidades de serviço, atribuições de função e adquirir tokens de acesso.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}    


### Criar entidade de serviço e atribuir à função

1. Crie uma entidade de serviço para seu aplicativo passando a id do aplicativo do Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

2. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo você concederá à entidade de serviço a permissão para ler todos os recursos na assinatura. Para o parâmetro **ServicePrincipalName**, forneça **ApplicationId** ou **IdentifierUris** usado ao criar o aplicativo. Para saber mais sobre o controle de acesso baseado em função, confira [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md)

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

### Preparar valores para seu script

No script, você passará três valores que são necessários para fazer logon como a entidade de serviço. Você precisará do seguinte:

- id do aplicativo
- ID do locatário 
- Impressão digital do certificado

Você já viu a id do aplicativo e a impressão digital do certificado nas etapas anteriores. No entanto, se você precisar recuperar esses valores posteriormente, os comandos serão mostrados abaixo, junto com o comando para obter a id do locatário.

1. Para recuperar a id do locatário, use:

        (Get-AzureRmSubscription).TenantId 

    Ou, se você tiver mais de uma assinatura, forneça o nome da assinatura:

        (Get-AzureRmSubscription -SubscriptionName "Azure MSDN - Visual Studio Ultimate").TenantId
        
2. Para recuperar a id do aplicativo, use:

        (Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example").ApplicationId
        
3. Para recuperar a impressão digital do certificado, use:

        (Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp).Thumbprint

### Fornecer certificado por meio do script PowerShell automatizado

Você criou um aplicativo do Active Directory e uma entidade de serviço para esse aplicativo. Você atribuiu a entidade de serviço a uma função. Agora, você precisa fazer logon como a entidade de serviço para executar operações como a entidade de serviço.

Para autenticar em seu script, especifique que a conta é uma entidade de serviço e forneça a impressão digital do certificado, a id do aplicativo e a id do locatário.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint {thumbprint} -ApplicationId {applicationId} -TenantId {tenantid}

Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory criado.

## Próximas etapas
  
- Para obter exemplos de autenticação .NET, confira [SDK do Azure Resource Manager para .NET](resource-manager-net-sdk.md).
- Para obter exemplos de autenticação Java, confira [SDK do Azure Resource Manager para Java](resource-manager-java-sdk.md). 
- Para obter exemplos de autenticação Python, confira [Autenticação de Gerenciamento de Recursos para Python](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html).
- Para obter exemplos de autenticação REST, confira [APIs REST do Resource Manager](resource-manager-rest-api.md).
- Para obter etapas detalhadas sobre como integrar um aplicativo do Azure para gerenciar recursos, consulte [Guia do desenvolvedor para autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).

<!---HONumber=AcomDC_0601_2016-->