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
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# Usar o Azure PowerShell para criar uma entidade de serviço a fim de acessar recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI do Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)

Quando você tiver um aplicativo ou script que precisa acessar recursos, provavelmente não desejará executar esse processo com suas próprias credenciais. Você pode ter permissões diferentes que deseja para o aplicativo e não quer que o aplicativo continue usando suas credenciais se alterar suas responsabilidades. Em vez disso, você cria uma identidade para o aplicativo que inclua as credenciais de autenticação e as atribuições de função. Sempre que o aplicativo for executado, ele se autenticará com essas credenciais. Este tópico mostra como usar o [Azure PowerShell](powershell-install-configure.md) para configurar tudo que você precisa para um aplicativo ser executado com suas próprias credenciais e identidade.

Com o PowerShell, você tem duas opções para autenticar seu aplicativo do AD:

 - Senha
 - certificado

Este tópico mostra como usar as duas opções no PowerShell. Se você pretende fazer logon no Azure a partir de uma estrutura de programação (como o Python, Ruby ou Node.js), a autenticação de senha poderá ser a melhor opção. Antes de decidir se você deve usar uma senha ou um certificado, consulte a seção [Aplicativos de exemplo](#sample-applications) para obter exemplos de autenticação em diferentes estruturas.

## Conceitos do Active Directory

Neste artigo, você cria dois objetos: o aplicativo do Active Directory (AD) e a entidade de serviço. O aplicativo do AD é a representação global do seu aplicativo. Ele contém as credenciais (uma id do aplicativo e uma senha ou certificado). A entidade de serviço é a representação local de seu aplicativo em um Active Directory. Ele contém a atribuição de função. Este tópico foca em um aplicativo com um único locatário no qual o aplicativo se destina a ser executado dentro de uma só organização. Você normalmente usa os aplicativos com um único locatário para os aplicativos da linha de negócios executados em sua organização. Em um aplicativo de locatário único, você tem um aplicativo AD e uma entidade de serviço.

Você pode estar imaginando: Por que preciso dos dois objetos? Essa abordagem faz mais sentido quando você considera os aplicativos multilocatários. Normalmente, você usa aplicativos multilocatários para o software como serviço (aplicativos SaaS), no qual o aplicativo é executado em várias assinaturas diferentes. Para os aplicativos multilocatários, você tem um aplicativo AD e várias entidades de serviço (uma em cada Active Directory que concede acesso ao aplicativo). Para configurar um aplicativo multilocatário, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).

## Permissões necessárias

Para concluir este tópico, você deve ter permissões suficientes no Azure Active Directory e em sua assinatura do Azure. Especificamente, você deve ser capaz de criar um aplicativo no Active Directory e atribuir a entidade de serviço a uma função.

No Active Directory, sua conta deve ser um administrador (como o **Administrador Global** ou o **Usuário Admin**). Se sua conta for atribuída à função **Usuário**, será necessário pedir que um administrador eleve suas permissões.

Em sua assinatura, sua conta deve ter o acesso `Microsoft.Authorization/*/Write`, que é concedido por meio da função [Proprietário](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrador de Acesso do Usuário](./active-directory/role-based-access-built-in-roles.md#user-access-administrator). Se sua conta for atribuída à função **Colaborador**, você receberá um erro ao tentar atribuir a entidade de serviço a uma função. Novamente, o administrador da assinatura deve conceder acesso suficiente.

Agora, vá para uma seção para ver uma autenticação da [senha](#create-service-principal-with-password) ou do [certificado](#create-service-principal-with-certificate).

## Criar a entidade de serviço com a senha

Nesta seção, você deve executar as etapas para:

- criar um aplicativo do AD com uma senha
- criar a entidade de serviço
- atribuir a função Leitor à entidade de serviço

Para executar essas etapas rapidamente, use os três cmdlets a seguir.

     $app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
     New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
     New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Vejamos essas etapas com mais cuidado para assegurar que você entende o processo.

1. Entre na sua conta.

        Add-AzureRmAccount

1. Crie um novo aplicativo do Active Directory fornecendo um nome de exibição, URI que descreve o aplicativo, URIs que identificam seu aplicativo e senha para a identidade do seu aplicativo.

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "<Your_Password>"

     Para os aplicativos de locatário único, os URIs não são validados.
     
     Se sua conta não tiver as [permissões necessárias](#required-permissions) no Active Directory, você verá uma mensagem de erro indicando "Authentication\_Unauthorized" ou "Nenhuma assinatura encontrada no contexto".

1. Examine o novo objeto de aplicativo.

        $app
        
     Observe especificamente a propriedade **ApplicationId**, que é necessária para criar as entidades de serviço, atribuições de função e adquirir o token de acesso.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}

2. Crie uma entidade de serviço para seu aplicativo passando a id do aplicativo do Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

3. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo, você adiciona a entidade de serviço à função **Leitor**, que concede permissão para ler todos os recursos na assinatura. Para ver outras funções, consulte [RBAC: funções internas](./active-directory/role-based-access-built-in-roles.md). Para ver o parâmetro **ServicePrincipalName**, forneça a **ApplicationId** que você usou ao criar o aplicativo.

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Se sua conta não tiver permissões suficientes para atribuir uma função, verá uma mensagem de erro. A mensagem informa que sua conta **não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' no escopo '/subscriptions/{guid}'**.

É isso! Seu aplicativo do AD e a entidade de serviço estão configurados. A próxima seção mostra como fazer logon com as credenciais por meio do PowerShell. Se você quiser usar a credencial em seu aplicativo de código, poderá ir para os [Aplicativos de exemplo](#sample-applications).

### Fornecer as credenciais por meio do PowerShell

Agora, você precisa fazer logon como o aplicativo para executar as operações.

1. Crie um objeto **PSCredential** que contenha suas credenciais executando o comando **Get-Credential**. Será necessário a **ApplicationId** antes de executar esse comando para assegurar que você terá isso disponível para colar.

        $creds = Get-Credential

2. Será solicitado que você insira suas credenciais. Como nome de usuário, use o parâmetro **ApplicationId** que você usou ao criar o aplicativo. Como senha, use aquela especificada ao criar a conta.

     ![inserir as credenciais](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

2. Sempre que você entrar como uma entidade de serviço, precisará fornecer a ID do locatário do diretório para seu aplicativo do AD. Um locatário é uma instância do Active Directory. Se você tiver apenas uma assinatura, poderá usar:

        $tenant = (Get-AzureRmSubscription).TenantId
    
     Se você tiver mais de uma assinatura, especifique a assinatura onde o Active Directory reside. Para saber mais sobre, confira [Como as assinaturas do Azure são associadas ao Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

4. Faça logon como a entidade de serviço especificando que essa conta é uma entidade de serviço e fornecendo o objeto das credenciais.

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory criado.

### Salvar o token de acesso para simplificar o logon

Para evitar fornecer as credenciais de serviço principal toda vez que precisar fazer logon, você poderá salvar o token de acesso.

1. Para usar o token de acesso atual em uma sessão posterior, salve o perfil.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Abra o perfil e examine seu conteúdo. Observe que ele contém um token de acesso.
        
2. Em vez fazer o logon manualmente de novo, basta carregar o perfil.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] O token de acesso expira, portanto, usar um perfil salvo funcionará somente se o token for válido.
        
## Criar a entidade de serviço com o certificado

Nesta seção, você deve executar as etapas para:

- criar um certificado autoassinado
- criar um aplicativo do AD com certificado
- criar a entidade de serviço
- atribuir a função Leitor à entidade de serviço

Para executar rapidamente essas etapas com o Azure PowerShell 2.0 no Windows 10 ou no Windows Server 2016 Technical Preview, consulte os seguintes cmdlets.

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Vejamos essas etapas com mais cuidado para assegurar que você entende o processo. Este artigo também mostra como realizar as tarefas ao usar as versões anteriores do Azure PowerShell ou dos sistemas operacionais.

### Criar um certificado autoassinado

A versão do PowerShell disponível com o Windows 10 e o Windows Server 2016 Technical Preview tem um cmdlet **New-SelfSignedCertificate** atualizado para gerar um certificado autoassinado. Os sistemas operacionais anteriores têm o cmdlet New-SelfSignedCertificate, mas ele não oferece os parâmetros necessários para este tópico. Em vez disso, você precisa importar um módulo para gerar o certificado. Este tópico mostra as duas abordagens para gerar o certificado com base no sistema operacional que você tem.

- Se você tiver o **Windows 10 ou Windows Server 2016 Technical Preview**, execute o seguinte comando para criar um certificado autoassinado:

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
- Se você **não tiver o Windows 10 ou o Windows Server 2016 Technical Preview**, precisará baixar o [Gerador de certificado autoassinado](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) no Script Center da Microsoft. Extraia seu conteúdo e importe o cmdlet necessário.
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
    
     Em seguida, gere o certificado.
    
        $cert = New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"

Você tem seu certificado e pode continuar a criar seu aplicativo do AD.

### Criar o aplicativo do Active Directory e a entidade de serviço

1. Recupere o valor da chave a partir do certificado.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Entre na sua conta do Azure.

        Add-AzureRmAccount

3. Crie um novo aplicativo do Active Directory fornecendo um nome de exibição, URI que descreve o aplicativo, URIs que identificam seu aplicativo e senha para a identidade do seu aplicativo.

     Se você tiver o Azure PowerShell 2.0 (agosto de 2016 ou posterior), use o seguinte cmdlet:

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Se tiver o Azure PowerShell 1.0, use o seguinte cmdlet:
    
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Para os aplicativos de locatário único, os URIs não são validados.
    
    Se sua conta não tiver as [permissões necessárias](#required-permissions) no Active Directory, você verá uma mensagem de erro indicando "Authentication\_Unauthorized" ou "Nenhuma assinatura encontrada no contexto".
        
    Examine o novo objeto de aplicativo.

        $app

    Observe a propriedade **ApplicationId**, que é necessária para criar as entidades de serviço, as atribuições de função e adquirir os tokens de acesso.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}


5. Crie uma entidade de serviço para seu aplicativo passando a id do aplicativo do Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

6. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo, você adiciona a entidade de serviço à função **Leitor**, que concede permissão para ler todos os recursos na assinatura. Para ver outras funções, consulte [RBAC: funções internas](./active-directory/role-based-access-built-in-roles.md). Para ver o parâmetro **ServicePrincipalName**, forneça a **ApplicationId** que você usou ao criar o aplicativo.

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Se sua conta não tiver permissões suficientes para atribuir uma função, verá uma mensagem de erro. A mensagem informa que sua conta **não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' no escopo '/subscriptions/{guid}'**.

É isso! Seu aplicativo do AD e a entidade de serviço estão configurados. A próxima seção mostra como fazer logon com um certificado por meio do PowerShell.

### Fornecer certificado por meio do script PowerShell automatizado

Sempre que você entrar como uma entidade de serviço, precisará fornecer a ID do locatário do diretório para seu aplicativo do AD. Um locatário é uma instância do Active Directory. Se você tiver apenas uma assinatura, poderá usar:

    $tenant = (Get-AzureRmSubscription).TenantId
    
Se você tiver mais de uma assinatura, especifique a assinatura onde o Active Directory reside. Para obter mais informações, consulte [Administrar seu diretório do AD do Azure](./active-directory/active-directory-administer.md).

    $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

Para autenticar em seu script, especifique que a conta é uma entidade de serviço e forneça a impressão digital do certificado, a id do aplicativo e a id do locatário. Para automatizar o script, você pode armazenar esses valores como variáveis de ambiente e recuperá-los durante a execução, ou pode incluí-los em seu script.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant

Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory criado.

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
- Para obter uma explicação mais detalhada de aplicativos e entidades de serviço, consulte [Objetos de aplicativo e de entidade de serviço](./active-directory/active-directory-application-objects.md).
- Para obter mais informações sobre a autenticação do Active Directory, consulte [Cenários de autenticação do Azure AD](./active-directory/active-directory-authentication-scenarios.md).

<!---HONumber=AcomDC_0914_2016-->