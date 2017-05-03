---
title: Criar identidade para o aplicativo do Azure com o PowerShell | Microsoft Docs
description: "Descreve como usar o Azure PowerShell para criar um aplicativo do Active Directory do Azure e uma entidade de serviço, e conceder acesso a recursos por meio do controle de acesso baseado em função. Ele mostra como autenticar um aplicativo com uma senha ou certificado."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/03/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 775734ea55d1136e64afc713356b0f0bfc81ea9f
ms.lasthandoff: 04/21/2017


---
# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Usar o Azure PowerShell para criar uma entidade de serviço a fim de acessar recursos
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [CLI do Azure](resource-group-authenticate-service-principal-cli.md)
> * [Portal](resource-group-create-service-principal-portal.md)
> 
> 

Quando você tiver um aplicativo ou script que precisa acessar recursos, poderá configurar uma identidade para o aplicativo e autenticá-lo com suas próprias credenciais. Essa identidade é conhecida como uma entidade de serviço. Essa abordagem permite:

* Atribuir permissões à identidade do aplicativo que são diferentes de suas próprias permissões. Normalmente, essas permissões são restritas a exatamente o que o aplicativo precisa fazer.
* Use um certificado para a autenticação ao executar um script autônomo.

Este tópico mostra como usar o [Azure PowerShell](/powershell/azureps-cmdlets-docs) para configurar tudo que você precisa para um aplicativo ser executado com suas próprias credenciais e identidade.

## <a name="required-permissions"></a>Permissões necessárias
Para concluir este tópico, você deve ter permissões suficientes no Azure Active Directory e em sua assinatura do Azure. Especificamente, você deve ser capaz de criar um aplicativo no Active Directory do Azure e atribuir a entidade de serviço a uma função. 

A maneira mais fácil de verificar se a sua conta tem as permissões adequadas é por meio do portal. Consulte [Verificar permissão necessária](resource-group-create-service-principal-portal.md#required-permissions).

Agora, vá para uma seção para ver uma autenticação da [senha](#create-service-principal-with-password) ou do [certificado](#create-service-principal-with-certificate).

## <a name="create-service-principal-with-password"></a>Criar a entidade de serviço com a senha
O seguinte script cria uma identidade para o aplicativo e atribui a ele a função Colaborador no escopo especificado:

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $Password
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.SubscriptionId
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 # Create Azure Active Directory application with password
 $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $ApplicationDisplayName) -Password $Password

 # Create Service Principal for the AD app
 $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
 }
```

Alguns itens a serem observados sobre o script:

* Para conceder o acesso da identidade à assinatura padrão, não é necessário fornecer os parâmetros ResourceGroup ou SubscriptionId.
* Especifique o parâmetro ResourceGroup somente quando desejar limitar o escopo da atribuição de função a um grupo de recursos.
* Para aplicativos de locatário único, a home page e os URIs do identificador não são validados.
*  Neste exemplo, você adiciona a entidade de serviço à função Colaborador. Para ver outras funções, confira [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md).
* O script fica suspenso 15 segundos para dar tempo à nova entidade de serviço de se propagar pelo Active Directory do Azure. Se o script não esperar tempo suficiente, você verá um erro dizendo: "PrincipalNotFound: a {id} da entidade não existe no diretório”.
* Se precisar conceder o acesso à entidade de serviço a mais assinaturas ou grupos de recursos, execute o cmdlet `New-AzureRMRoleAssignment` novamente com escopos diferentes.


### <a name="provide-credentials-through-powershell"></a>Fornecer as credenciais por meio do PowerShell
Agora, você precisa fazer logon como o aplicativo para executar as operações. Para o nome de usuário, use a `ApplicationId` que você criou para o aplicativo. Como senha, use aquela especificada ao criar a conta. 

```powershell   
$creds = Get-Credential
Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId {tenant-id}
```

A ID de locatário não diferencia maiúsculas de minúsculas e, portanto, você pode inseri-la diretamente no script. Se precisar recuperar a ID de locatário, use:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

## <a name="create-service-principal-with-self-signed-certificate"></a>Criar a entidade de serviço com um certificado autoassinado
Para gerar um certificado autoassinado e uma entidade de serviço com o Azure PowerShell 2.0 no Windows 10 ou no Windows Server 2016 Technical Preview, use o seguinte script:

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.SubscriptionId
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
 $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

 # Use Key credentials
 $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $ApplicationDisplayName) -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore

 $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
 }
```

Alguns itens a serem observados sobre o script:

* Para conceder o acesso da identidade à assinatura padrão, não é necessário fornecer os parâmetros ResourceGroup ou SubscriptionId.
* Especifique o parâmetro ResourceGroup somente quando desejar limitar o escopo da atribuição de função a um grupo de recursos.
* Para aplicativos de locatário único, a home page e os URIs do identificador não são validados.
*  Neste exemplo, você adiciona a entidade de serviço à função Colaborador. Para ver outras funções, confira [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md).
* O script fica suspenso 15 segundos para dar tempo à nova entidade de serviço de se propagar pelo Active Directory do Azure. Se o script não esperar tempo suficiente, você verá um erro dizendo: "PrincipalNotFound: a {id} da entidade não existe no diretório”.
* Se precisar conceder o acesso à entidade de serviço a mais assinaturas ou grupos de recursos, execute o cmdlet `New-AzureRMRoleAssignment` novamente com escopos diferentes.

Se você **não tiver o Windows 10 ou o Windows Server 2016 Technical Preview**, precisará baixar o [Gerador de certificado autoassinado](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) no Script Center da Microsoft. Extraia seu conteúdo e importe o cmdlet necessário.

```powershell  
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```
  
No script, substitua as duas linhas a seguir para gerar o certificado.
  
```powershell
New-SelfSignedCertificateEx  -StoreLocation CurrentUser -StoreName My -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornecer certificado por meio do script PowerShell automatizado
Sempre que você entrar como uma entidade de serviço, precisará fornecer a ID do locatário do diretório para seu aplicativo do AD. Um locatário é uma instância do Active Directory do Azure. Se você tiver apenas uma assinatura, poderá usar:

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertSubject,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match $CertSubject }).Thumbprint
 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

A ID do aplicativo e a ID de locatário não diferenciam maiúsculas de minúsculas e, portanto, você pode inseri-las diretamente no script. Se precisar recuperar a ID de locatário, use:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Se precisar recuperar a ID do aplicativo, use:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Criar a entidade de serviço com um certificado da Autoridade de Certificação
Para usar um certificado emitido por uma Autoridade de Certificação para criar a entidade de serviço, use o seguinte script:

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -SubscriptionId $SubscriptionId

 $KeyId = (New-Guid).Guid
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
 $KeyCredential.StartDate = $PFXCert.NotBefore
 $KeyCredential.EndDate= $PFXCert.NotAfter
 $KeyCredential.KeyId = $KeyId
 $KeyCredential.CertValue = $KeyValue

 # Use Key credentials
 $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

 $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

Alguns itens a serem observados sobre o script:

* O acesso é restrito à assinatura.
* Para aplicativos de locatário único, a home page e os URIs do identificador não são validados.
*  Neste exemplo, você adiciona a entidade de serviço à função Colaborador. Para ver outras funções, confira [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md).
* O script fica suspenso 15 segundos para dar tempo à nova entidade de serviço de se propagar pelo Active Directory do Azure. Se o script não esperar tempo suficiente, você verá um erro dizendo: "PrincipalNotFound: a {id} da entidade não existe no diretório”.
* Se precisar conceder o acesso à entidade de serviço a mais assinaturas ou grupos de recursos, execute o cmdlet `New-AzureRMRoleAssignment` novamente com escopos diferentes.

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornecer certificado por meio do script PowerShell automatizado
Sempre que você entrar como uma entidade de serviço, precisará fornecer a ID do locatário do diretório para seu aplicativo do AD. Um locatário é uma instância do Active Directory do Azure.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

A ID do aplicativo e a ID de locatário não diferenciam maiúsculas de minúsculas e, portanto, você pode inseri-las diretamente no script. Se precisar recuperar a ID de locatário, use:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Se precisar recuperar a ID do aplicativo, use:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Alterar credenciais

Para alterar as credenciais para um aplicativo do AD, por causa de uma violação de segurança ou expiração de credencial, use os cmdlets [Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) e [New-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermadappcredential).

Para remover todas as credenciais de um aplicativo, use:

```powershell
Remove-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -All
```

Para adicionar uma senha, use:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -Password p@ssword!
```

Para adicionar um valor de certificado, crie um certificado autoassinado conforme mostrado neste tópico. Em seguida, use:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
```

## <a name="save-access-token-to-simplify-log-in"></a>Salvar o token de acesso para simplificar o logon
Para evitar fornecer as credenciais de serviço principal toda vez que precisar fazer logon, você poderá salvar o token de acesso.

Para usar o token de acesso atual em uma sessão posterior, salve o perfil.
   
```powershell
Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```
   
Abra o perfil e examine seu conteúdo. Observe que ele contém um token de acesso. Em vez fazer o logon manualmente de novo, basta carregar o perfil.
   
```powershell
Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```

> [!NOTE]
> O token de acesso expira, portanto, usar um perfil salvo funcionará somente se o token for válido.
>  

Como alternativa, você pode invocar operações REST do PowerShell para fazer logon. Na resposta de autenticação, você pode recuperar o token de acesso para uso com outras operações. Para obter um exemplo de como recuperar o token de acesso invocando operações REST, consulte [Gerar um token de acesso](resource-manager-rest-api.md#generating-an-access-token).

## <a name="debug"></a>Depurar

Você pode receber os seguintes erros ao criar uma entidade de serviço:

* **"Authentication_Unauthorized"** ou **"Nenhuma assinatura encontrada no contexto".** – Você verá esse erro quando sua conta não tiver as [permissões necessárias](#required-permissions) no Active Directory do Azure para registrar um aplicativo. Normalmente, você verá esse erro somente quando os usuários administradores no Active Directory do Azure puderem registrar aplicativos e sua conta não for um administrador. Solicite ao administrador para lhe atribuir uma função de administrador ou para permitir que os usuários registrem aplicativos.

* Sua conta **"não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' no escopo '/subscriptions/{guid}'".**  – Você verá esse erro quando sua conta não tiver permissões suficientes para atribuir uma função a uma identidade. Solicite ao administrador da assinatura para adicioná-lo à função Administrador de Acesso do Usuário.

## <a name="sample-applications"></a>Aplicativos de exemplo
Os aplicativos de exemplo a seguir mostram como fazer logon como a entidade de serviço.

**.NET**

* [Implantar uma VM Habilitada para SSH com um Modelo com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [Gerenciar recursos e grupos de recursos do Azure com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [Introdução aos Recursos - Implantar Usando o Modelo do Azure Resource Manager - em Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [Introdução aos Recursos - Gerenciar o Grupo de Recursos - em Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Implantar uma VM Habilitada para SSH com um Modelo no Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Gerenciando o Recurso e Grupos de Recursos do Azure com o Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

* [Implantar uma VM Habilitada para SSH com um Modelo no Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Gerenciar recursos e grupos de recursos do Azure com Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Implantar uma VM Habilitada para SSH com um Modelo no Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Gerenciando o Recurso e Grupos de Recursos do Azure com Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Próximas etapas
* Para ver as etapas detalhadas sobre como integrar um aplicativo no Azure para gerenciar os recursos, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).
* Para obter uma explicação mais detalhada de aplicativos e entidades de serviço, consulte [Objetos de aplicativo e de entidade de serviço](../active-directory/active-directory-application-objects.md). 
* Para obter mais informações sobre a autenticação do Active Directory do Azure, consulte [Cenários de Autenticação do Azure AD](../active-directory/active-directory-authentication-scenarios.md).


