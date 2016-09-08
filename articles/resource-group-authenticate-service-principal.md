<properties
   pageTitle="Criar entidade de serviço do Azure com o PowerShell | Microsoft Azure"
   description="Descreve como usar o Azure PowerShell para criar um aplicativo do Active Directory e uma entidade de serviço, e conceder acesso a recursos por meio do controle de acesso baseado em função. Ele mostra como autenticar um aplicativo com uma senha ou certificado."
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
   ms.date="07/18/2016"
   ms.author="tomfitz"/>

# Usar o Azure PowerShell para criar uma entidade de serviço a fim de acessar recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI do Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)

Quando você tiver um aplicativo ou script que precisa acessar recursos, provavelmente não desejará executar esse processo com as credenciais de um usuário. Esse usuário pode ter permissões diferentes que você gostaria de atribuir ao processo e as responsabilidades de trabalho do usuário podem ser alteradas. Em vez disso, você pode criar uma identidade para o aplicativo que inclui as credenciais de autenticação e as atribuições de função. Seu aplicativo fará logon com essa identidade sempre que for executado. Este tópico mostra como usar o [Azure PowerShell](powershell-install-configure.md) para configurar tudo que você precisa para um aplicativo ser executado com suas próprias credenciais e identidade.

Neste artigo, você criará dois objetos: o aplicativo do Active Directory (AD) e a entidade de serviço. O aplicativo do AD contém as credenciais (uma id do aplicativo e uma senha ou certificado). A entidade de serviço contém a atribuição da função. A partir do aplicativo do AD, você pode criar várias entidades de serviço. Este tópico foca em um aplicativo com um único locatário no qual o aplicativo se destina a ser executado dentro de uma só organização. Você normalmente usa os aplicativos com um único locatário para os aplicativos da linha de negócios executados em sua organização. Você também pode criar aplicativos multilocatários quando seu aplicativo precisa ser executado em muitas organizações. Em geral, você usa os aplicativos multilocatários para os aplicativos do software como serviço (SaaS). Para configurar um aplicativo multilocatário, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).

Há muitos conceitos a entender ao trabalhar com o Active Directory. Para obter uma explicação mais detalhada de aplicativos e entidades de serviço, consulte [Objetos de aplicativo e de entidade de serviço](./active-directory/active-directory-application-objects.md). Para obter mais informações sobre a autenticação do Active Directory, consulte [Cenários de autenticação do Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Com o PowerShell, você tem duas opções para autenticar seu aplicativo do AD:

 - Senha
 - certificado

Se, depois de configurar seu aplicativo do AD, você pretende fazer logon no Azure a partir de outra estrutura de programação (como Python, Ruby ou Node.js), a autenticação de senha pode ser a melhor opção. Antes de decidir se você deve usar uma senha ou um certificado, consulte a seção [Aplicativos de exemplo](#sample-applications) para obter exemplos de autenticação em diferentes estruturas.

## Obter a ID do locatário

Sempre que você entrar como uma entidade de serviço, precisará fornecer a ID do locatário do diretório para seu aplicativo do AD. Um locatário é uma instância do Active Directory. Como você precisará desse valor para a autenticação da senha ou do certificado, obteremos esse valor agora.

1. Entre na sua conta.

        Add-AzureRmAccount

2. Se você tiver apenas uma assinatura, poderá usar:

        $tenant = (Get-AzureRmSubscription).TenantId
    
     Se você tiver mais de uma assinatura, especifique a assinatura que usará para o aplicativo do AD. Selecione a assinatura onde reside o Active Directory. Para obter mais informações, consulte [Administrar seu diretório do AD do Azure](./active-directory/active-directory-administer.md).

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

Agora, vá para uma seção abaixo para ver uma autenticação da [senha](#create-service-principal-with-password) ou do [certificado](#create-service-principal-with-certificate).

## Criar a entidade de serviço com a senha

Nesta seção, você executará as etapas para criar o aplicativo do AD e a entidade de serviço com uma senha.

1. Crie um novo aplicativo do Active Directory fornecendo um nome de exibição para seu aplicativo, o URI para uma página que descreve o aplicativo (o link não é verificado), os URIs que identificam seu aplicativo e a senha para a identidade do seu aplicativo.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     Examine o novo objeto de aplicativo.

        $azureAdApplication
        
     Observe especificamente a propriedade **ApplicationId**, que é necessária para criar as entidades de serviço, as atribuições de função e adquirir o token de acesso.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}


     A partir de seu aplicativo do AD, você deve criar uma entidade de serviço e atribuir uma função a ela.

2. Crie uma entidade de serviço para seu aplicativo passando a id do aplicativo do Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

3. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo você concederá à entidade de serviço a permissão para ler todos os recursos na assinatura. Para o parâmetro **ServicePrincipalName**, forneça **ApplicationId** ou **IdentifierUris** usado ao criar o aplicativo. Para saber mais sobre o controle de acesso baseado em função, confira [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md) Para atribuir uma função, você deve ter o acesso `Microsoft.Authorization/*/Write` que é concedido por meio da função [Proprietário](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrador de Acesso do Usuário](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

É isso! Seu aplicativo do AD e a entidade de serviço estão configurados. A próxima seção mostra como fazer logon com as credenciais por meio do PowerShell. No entanto, se você quiser usar a credencial em seu aplicativo de código, não precisará continuar com este tópico. Pode ir para os [aplicativos de amostra](#sample-applications) para ter exemplos de logon com sua ID do aplicativo e senha.

### Fornecer as credenciais por meio do PowerShell

Agora, você precisa fazer logon como o aplicativo para executar operações.

1. Crie um novo objeto **PSCredential** que contenha suas credenciais ao executar o comando **Get-Credential**. Será necessário a **ApplicationId** ou **IdentifierUris** antes de executar esse comando para assegurar que você tem isso disponível para colar.

        $creds = Get-Credential

2. Será solicitado que você insira suas credenciais de conta do Azure. Como nome de usuário, use um dos parâmetros **ApplicationId** ou **IdentifierUris** que você usou ao criar o aplicativo. Como senha, use aquela especificada ao criar a conta.

     ![inserir as credenciais](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

4. Faça logon como a entidade de serviço especificando que essa conta é uma entidade de serviço e fornecendo o objeto das credenciais. Você precisará da ID do locatário recuperada em [Obter ID do locatário](#get-tenant-id).

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory criado.

5. Para usar o token de acesso atual em uma sessão posterior, você pode salvar o perfil.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Você pode abrir o perfil e examinar seu conteúdo. Observe que ele contém um token de acesso.
        
6. Em vez de fazer logon manualmente de novo na próxima vez em que você quiser executar o código como a entidade de serviço, basta carregar o perfil.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] O token de acesso expirará, portanto, usar um perfil salvo funcionará somente se o token for válido.
        
## Criar a entidade de serviço com o certificado

Nesta seção, você executará as etapas para criar o aplicativo do AD e a entidade de serviço com um certificado.

1. Crie um certificado autoassinado. Se você tiver o **Windows 10 ou o Windows Server 2016 Technical Preview**, execute o seguinte comando:

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
     Sua variável contém informações sobre o certificado, incluindo a impressão digital.
     
        Directory: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

        Thumbprint                                Subject
        ----------                                -------
        724213129BD2B950BB3F64FAB0C877E9348B16E9  CN=exampleapp

     Se você **não** tiver o Windows 10 ou o Windows Server 2016 Technical Preview, não terá o cmdlet **New-SelfSignedCertificate**. Em vez disso, baixe o script do PowerShell [Gerador de certificado autoassinado](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6) e execute os seguintes comandos para gerar um certificado. Essa etapa não será necessária se você já criou o certificado no exemplo anterior.
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\New-SelfSignedCertificateEx.ps1
        New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
        $cert = Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp

2. Recupere o valor da chave a partir do certificado.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

4. Crie um aplicativo no diretório.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
        
    Examine o novo objeto de aplicativo.

        $azureAdApplication

    Observe a propriedade **ApplicationId**, que é necessária para criar as entidades de serviço, as atribuições de função e adquirir os tokens de acesso.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}    


5. Crie uma entidade de serviço para seu aplicativo passando a id do aplicativo do Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

6. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo você concederá à entidade de serviço a permissão para ler todos os recursos na assinatura. Para o parâmetro **ServicePrincipalName**, forneça **ApplicationId** ou **IdentifierUris** usado ao criar o aplicativo. Para saber mais sobre o controle de acesso baseado em função, confira [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md) Para atribuir uma função, você deve ter o acesso `Microsoft.Authorization/*/Write` que é concedido por meio da função [Proprietário](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrador de Acesso do Usuário](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

É isso! Seu aplicativo do AD e a entidade de serviço estão configurados. A próxima seção mostra como fazer logon com um certificado por meio do PowerShell.

### Fornecer certificado por meio do script PowerShell automatizado

Para autenticar em seu script, especifique que a conta é uma entidade de serviço e forneça a impressão digital do certificado, a id do aplicativo e a id do locatário. Você já tem esses valores nas variáveis **$azureAdApplication.ApplicationId**, **$cert.Thumbprint** e **$tenant**. Para automatizar o script, você pode armazenar esses valores como variáveis de ambiente e recuperá-los durante a execução, ou pode incluí-los em seu script.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint 000000 -ApplicationId 000000 -TenantId 0000000

Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory criado.

Se você precisar recuperar a ID do aplicativo mais tarde, use:

    (Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example").ApplicationId
        
Se precisar recuperar a impressão digital do certificado mais tarde, use:

    (Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp).Thumbprint

Se precisar recuperar a ID do locatário mais tarde, consulte [Obter ID do locatário](#get-tenant-id).

## Aplicativos de exemplo

Os aplicativos de exemplo a seguir mostram como fazer logon como a entidade de serviço.

**.NET**

- [Implantar uma VM Habilitada para SSH com um Modelo com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Gerenciar recursos e grupos de recursos do Azure com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Introdução aos Recursos - Implantar Usando o Modelo do Azure Resource Manager - em Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Introdução aos Recursos - Gerenciar o Grupo de Recursos - em Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Implantar uma VM Habilitada para SSH com um Modelo no Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Gerenciando o Recurso e Grupos de Recursos do Azure com o Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [Implantar uma VM Habilitada para SSH com um Modelo no Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Gerenciar recursos e grupos de recursos do Azure com Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Implantar uma VM Habilitada para SSH com um Modelo no Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Gerenciando o Recurso e Grupos de Recursos do Azure com Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## Próximas etapas
  
- Para ver as etapas detalhadas sobre como integrar um aplicativo no Azure para gerenciar os recursos, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).

<!---HONumber=AcomDC_0824_2016-->