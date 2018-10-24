---
title: Criar uma entidade de serviço para o Azure Stack | Microsoft Docs
description: Descreve como criar uma nova entidade de serviço que pode ser usada com o controle de acesso baseado em função no Azure Resource Manager para gerenciar o acesso aos recursos.
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2018
ms.author: sethm
ms.openlocfilehash: 96137b95f46f24bca6a4ee6a39d93a490a03c431
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49958441"
---
# <a name="provide-applications-access-to-azure-stack"></a>Fornecer acesso a aplicativos para o Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Quando um aplicativo precisa acessar implanta nem configura recursos por meio do Azure Resource Manager no Azure Stack, você pode criar uma entidade de serviço, que é uma credencial para o seu aplicativo. Em seguida, você pode delegar apenas as permissões necessárias para essa entidade de serviço.  

Por exemplo, você pode ter uma ferramenta de gerenciamento de configuração que usa o Azure Resource Manager para fazer o inventário de recursos do Azure. Nesse cenário, você pode criar uma entidade de serviço, atribua a função de leitor a essa entidade de serviço e limitar a ferramenta de gerenciamento de configuração para acesso somente leitura. 

As entidades de serviço são preferíveis para executar o aplicativo com suas próprias credenciais porque:

* Você pode atribuir permissões à entidade que são diferentes de suas próprias permissões de conta de serviço. Normalmente, essas permissões são restritas a exatamente o que o aplicativo precisa fazer.
* Você não precisa alterar as credenciais do aplicativo se alterar suas responsabilidades.
* Você pode usar um certificado para automatizar a autenticação ao executar um script autônomo.  

## <a name="getting-started"></a>Introdução

Dependendo de como você implantou o Azure Stack, você começa ao criar um serviço principal. Este documento descreve como criar uma entidade de serviço para ambos [Azure Active Directory (AD do Azure)](#create-service-principal-for-azure-ad) e [Active Directory Federation Services(AD FS)](#create-service-principal-for-ad-fs). Depois de criar a entidade de serviço, um conjunto de etapas comuns para AD FS e Azure Active Directory são usados para [delegar permissões](#assign-role-to-service-principal) à função.     

## <a name="create-service-principal-for-azure-ad"></a>Criar entidade de serviço do AD do Azure

Se você implantou o Azure Stack usando o Azure AD como o repositório de identidades, você pode criar entidades de serviço exatamente como faria para o Azure. Esta seção mostra como executar as etapas por meio do portal. Verifique se você tem o [permissões do AD do Azure necessárias](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) antes de começar.

### <a name="create-service-principal"></a>Criar uma entidade de serviço
Nesta seção, você criará um aplicativo (entidade de serviço) no Azure AD que representa o aplicativo.

1. Entre sua conta do Azure através do [portal do Microsoft Azure](https://portal.azure.com).
2. Selecione **Azure Active Directory** > **registros do aplicativo** > **novo registro de aplicativo**   
3. Forneça um nome e uma URL para o aplicativo. Selecione **aplicativo Web/API** ou **Nativo** para o tipo de aplicativo que você deseja criar. Depois de definir os valores, selecione **Criar**.

Você criou uma entidade de serviço para seu aplicativo.

### <a name="get-credentials"></a>Obter credenciais
Ao fazer logon por meio de programação, você usar a ID do aplicativo e para um aplicativo Web / API, uma chave de autenticação. Para obter esses valores, use as seguintes etapas:

1. De **Registros do Aplicativo** no Active Directory, selecione seu aplicativo.

2. Copie a **ID do aplicativo** e armazene-a no código do aplicativo. Os aplicativos na seção [aplicativos de exemplo](#sample-applications) referem-se a esse valor como a ID do cliente.

     ![ID do CLIENTE](./media/azure-stack-create-service-principal/image12.png)
3. Para gerar uma chave de autenticação para um aplicativo Web / API, selecione **as configurações** > **chaves**. 

4. Forneça uma descrição da chave e uma duração para a chave. Ao terminar, escolha **Salvar**.

Após salvar a chave, o valor da chave é exibido. Copie esse valor para o bloco de notas ou em outro local temporário, porque você não pode recuperar a chave mais tarde. Você pode fornecer o valor da chave com a ID do aplicativo para entrar como o aplicativo. Store o valor da chave em um local em que seu aplicativo possa recuperá-lo.

![chave salva](./media/azure-stack-create-service-principal/image15.png)

Uma vez concluído, vá para [atribuindo uma função de seu aplicativo](#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Criar entidade de serviço do AD FS
Se você tiver implantado o Azure Stack com o AD FS, você pode usar o PowerShell para criar uma entidade de serviço, atribuir uma função de acesso e entrar no PowerShell usando essa identidade.

O script é executado do ponto de extremidade com privilégios em uma máquina virtual ERCS.

Requisitos:
- É necessário um certificado.

#### <a name="parameters"></a>parâmetros

As informações a seguir são necessárias como entrada para os parâmetros de automação:


|Parâmetro|DESCRIÇÃO|Exemplo|
|---------|---------|---------|
|NOME|Nome da conta SPN|MyAPP|
|ClientCertificates|Matriz de objetos de certificado|X509 certificado|
|ClientRedirectUris<br>(Opcional)|URI de redirecionamento do aplicativo|-|

#### <a name="example"></a>Exemplo

1. Abra uma sessão do Windows PowerShell com privilégios elevados e execute os seguintes comandos:

   > [!NOTE]
   > Este exemplo cria um certificado autoassinado. Quando você executa esses comandos em uma implantação de produção, use [Get-Certificate](/powershell/module/pkiclient/get-certificate) para recuperar o objeto de certificado para o certificado que você deseja usar.

   ```PowerShell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

    # This produces a self signed cert for testing purposes. It is prefered to use a managed certificate for this.
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange

    $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name '<yourappname>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }
    $session|remove-pssession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
    $TenantID = $AzureStackInfo.AADTenantID

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

    # Set the GraphEndpointResourceId value
    Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

    Add-AzureRmAccount -EnvironmentName "azurestackuser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```

2. Após a automação, ele exibe os detalhes necessários para usar o SPN. 

   Por exemplo: 

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="assign-a-role"></a>Atribuir uma função
Depois que a entidade de serviço é criada, você deve [atribuí-lo a uma função](#assign-role-to-service-principal).

### <a name="sign-in-through-powershell"></a>Entrar por meio do PowerShell
Depois que você atribuiu uma função, você pode entrar no Azure Stack usando a entidade de serviço com o seguinte comando:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ClientId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>Atribuir função à entidade de serviço
Para acessar recursos em sua assinatura, você deve atribuir o aplicativo a uma função. Decida qual função representa as permissões corretas para o aplicativo. Para saber mais sobre as funções disponíveis, consulte [RBAC: funções internas](../role-based-access-control/built-in-roles.md).

Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissão são herdadas para níveis inferiores do escopo. Por exemplo, adicionar um aplicativo à função Leitor de um grupo de recursos significa que ele pode ler o grupo de recursos e todos os recursos que ele contiver.

1. No portal do Azure Stack, navegue até o nível de escopo que você deseja atribuir o aplicativo. Por exemplo, para atribuir uma função no escopo da assinatura, escolha **Assinaturas**. Em vez disso, você pode selecionar um grupo de recursos ou um recurso.

2. Escolha a assinatura específica (grupo de recursos ou recurso) à qual atribuir o aplicativo.

     ![selecionar assinatura para atribuição](./media/azure-stack-create-service-principal/image16.png)

3. Selecione **Controle de Acesso (IAM)**.

     ![selecionar acesso](./media/azure-stack-create-service-principal/image17.png)

4. Selecione **Adicionar**.

5. Selecione a função que deseja atribuir ao aplicativo.

6. Procure seu aplicativo e selecione-o.

7. Selecione **OK** para finalizar a atribuição da função. Agora você vê o aplicativo na lista de usuários atribuídos a uma função para esse escopo.

Agora que você criou uma entidade de serviço e atribuído a uma função, você pode começar a usar isso dentro de seu aplicativo para acessar os recursos do Azure Stack.  

## <a name="next-steps"></a>Próximas etapas

[Adicionar usuários do AD FS](azure-stack-add-users-adfs.md)
[gerenciar permissões de usuário](azure-stack-manage-permissions.md)
