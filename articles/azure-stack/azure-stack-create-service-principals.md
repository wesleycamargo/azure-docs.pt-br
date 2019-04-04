---
title: Gerenciar uma entidade de serviço para o Azure Stack | Microsoft Docs
description: Descreve como gerenciar uma nova entidade de serviço que pode ser usada com o controle de acesso baseado em função no Azure Resource Manager para gerenciar o acesso aos recursos.
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: sethm
ms.lastreviewed: 12/18/2018
ms.openlocfilehash: 0f5a4dc76830740d69547a01ce40b5e10cf4a74b
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499401"
---
# <a name="provide-applications-access-to-azure-stack"></a>Fornecer acesso a aplicativos para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Quando um aplicativo precisa acessar implanta nem configura recursos por meio do Azure Resource Manager no Azure Stack, você pode criar uma entidade de serviço, que é uma credencial para o seu aplicativo. Em seguida, você pode delegar apenas as permissões necessárias para essa entidade de serviço.  

Por exemplo, você pode ter uma ferramenta de gerenciamento de configuração que usa o Azure Resource Manager para fazer o inventário de recursos do Azure. Nesse cenário, você pode criar uma entidade de serviço, atribua a função de leitor a essa entidade de serviço e limitar a ferramenta de gerenciamento de configuração para acesso somente leitura. 

As entidades de serviço são preferíveis para executar o aplicativo com suas próprias credenciais porque:

 - Você pode atribuir permissões à entidade que são diferentes de suas próprias permissões de conta de serviço. Normalmente, essas permissões são restritas a exatamente o que o aplicativo precisa fazer.
 - Você não precisa alterar as credenciais do aplicativo se alterar suas responsabilidades.
 - Você pode usar um certificado para automatizar a autenticação ao executar um script autônomo.  

## <a name="getting-started"></a>Introdução

Dependendo de como você implantou o Azure Stack, você começa ao criar um serviço principal. Este documento descreve a criação de um serviço principal para:

- Azure AD (Azure Active Directory). Azure AD é um diretório multilocatário baseado em nuvem e o serviço de gerenciamento de identidade. Você pode usar o Azure AD com uma pilha do Azure conectados.
- Serviços de Federação do Active Directory (AD FS). O AD FS fornece federação de identidade simplificada e segura e recursos de logon único (SSO) da Web. Você pode usar o AD FS com instâncias do Azure Stack conectadas ou desconectadas.

Depois de criar a entidade de serviço, um conjunto de etapas comuns para AD FS e Azure Active Directory são usados para delegar permissões para a função.

## <a name="manage-service-principal-for-azure-ad"></a>Gerenciar a entidade de serviço do Azure AD

Se você tiver implantado o Azure Stack com o Azure Active Directory (Azure AD) como seu serviço de gerenciamento de identidade, você pode criar entidades de serviço exatamente como faria para o Azure. Esta seção mostra como executar as etapas por meio do portal. Verifique se você tem o [permissões do AD do Azure necessárias](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) antes de começar.

### <a name="create-service-principal"></a>Criar uma entidade de serviço

Nesta seção, você criará um aplicativo (entidade de serviço) no Azure AD que representa o aplicativo.

1. Entre sua conta do Azure através do [portal do Microsoft Azure](https://portal.azure.com).
2. Selecione **Azure Active Directory** > **registros do aplicativo** > **novo registro de aplicativo**
3. Forneça um nome e uma URL para o aplicativo. Selecione **aplicativo Web/API** ou **Nativo** para o tipo de aplicativo que você deseja criar. Depois de definir os valores, selecione **Criar**.

Você criou uma entidade de serviço para seu aplicativo.

### <a name="get-credentials"></a>Obter credenciais

Ao fazer logon por meio de programação, você usar a ID do aplicativo e para um aplicativo Web / API, uma chave de autenticação. Para obter esses valores, use as seguintes etapas:

1. De **Registros do Aplicativo** no Active Directory, selecione seu aplicativo.

2. Copie a **ID do aplicativo** e armazene-a no código do aplicativo. Os aplicativos na seção de aplicativos de exemplo se referir a esse valor como a ID do cliente.

     ![ID do cliente](./media/azure-stack-create-service-principal/image12.png)
3. Para gerar uma chave de autenticação para um aplicativo Web / API, selecione **as configurações** > **chaves**. 

4. Forneça uma descrição da chave e uma duração para a chave. Ao terminar, escolha **Salvar**.

Após salvar a chave, o valor da chave é exibido. Copie esse valor para o bloco de notas ou em outro local temporário, porque você não pode recuperar a chave mais tarde. Você pode fornecer o valor da chave com a ID do aplicativo para entrar como o aplicativo. Store o valor da chave em um local em que seu aplicativo possa recuperá-lo.

![Chave salva](./media/azure-stack-create-service-principal/image15.png)

Uma vez concluído, você pode atribuir seu aplicativo em uma função.

## <a name="manage-service-principal-for-ad-fs"></a>Gerenciar a entidade de serviço do AD FS

Se você tiver implantado o Azure Stack com os serviços de Federação do Active Directory (AD FS) como seu serviço de gerenciamento de identidade, use o PowerShell para criar uma entidade de serviço, atribuir uma função de acesso e entrar com essa identidade.

Você pode usar um dos dois métodos para criar a entidade de serviço com o AD FS. Você pode:
 - [Criar uma entidade de serviço usando um certificado](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate)
 - [Criar uma entidade de serviço usando um segredo do cliente](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret)

Entidades de serviço de tarefas de gerenciamento do AD FS.

| Type | Ação |
| --- | --- |
| Certificado do AD FS | [Criar](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate) |
| Certificado do AD FS | [Atualização](azure-stack-create-service-principals.md#update-certificate-for-service-principal-for-ad-fs) |
| Certificado do AD FS | [Remover](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |
| Segredo do cliente FS AD | [Criar](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Segredo do cliente FS AD | [Atualização](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Segredo do cliente FS AD | [Remover](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |

### <a name="create-a-service-principal-using-a-certificate"></a>Criar uma entidade de serviço usando um certificado

Ao criar uma entidade de serviço durante o uso do AD FS para a identidade, você pode usar um certificado.

#### <a name="certificate"></a>Certificado

É necessário um certificado.

**Requisitos de certificado**

 - O provedor de serviços de criptografia (CSP) deve ser herdado provedor da chave.
 - O formato do certificado deve estar no arquivo PFX, como as chaves públicas e privadas são necessárias. Servidores do Windows usam arquivos. pfx que contém o arquivo de chave pública (arquivo de certificado SSL) e o arquivo de chave privada associado.
 - Para a produção, o certificado deve ser emitido de uma autoridade de certificação interna ou uma autoridade de certificação pública. Se você usar uma autoridade de certificação pública, você deve incluído a autoridade na imagem base do sistema operacional como parte do que o programa de autoridade de raiz confiável do Microsoft. Você pode encontrar a lista completa em [Microsoft Trusted Root Certificate Program: Os participantes](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
 - Sua infraestrutura do Azure Stack deve ter acesso à rede local de lista de revogação de certificados (CRL) da autoridade de certificação publicado no certificado. Essa CRL deve ser um ponto de extremidade HTTP.

#### <a name="parameters"></a>parâmetros

As informações a seguir são necessárias como entrada para os parâmetros de automação:

|Parâmetro|DESCRIÇÃO|Exemplo|
|---------|---------|---------|
|NOME|Nome da conta SPN|MyAPP|
|ClientCertificates|Matriz de objetos de certificado|X509 certificado|
|ClientRedirectUris<br>(Opcional)|URI de redirecionamento do aplicativo|-|

#### <a name="use-powershell-to-create-a-service-principal"></a>Usar o PowerShell para criar uma entidade de serviço

1. Abra uma sessão do Windows PowerShell com privilégios elevados e execute os seguintes cmdlets:

   ```powershell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $Creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # If you have a managed certificate use the Get-Item command to retrieve your certificate from your certificate location.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

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

    Add-AzureRmAccount -EnvironmentName "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```
   > [!Note]  
   > Para fins de validação de um certificado autoassinado pode ser criado usando o exemplo abaixo:

   ```powershell  
   $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange
   ```


2. Após a automação, ele exibe os detalhes necessários para usar o SPN. É recomendável armazenar a saída para uso posterior.

   Por exemplo: 

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="update-certificate-for-service-principal-for-ad-fs"></a>Atualizar o certificado para entidade de serviço do AD FS

Se você tiver implantado o Azure Stack com o AD FS, você pode usar o PowerShell para atualizar o segredo para uma entidade de serviço.

O script é executado do ponto de extremidade com privilégios em uma máquina virtual ERCS.

#### <a name="parameters"></a>parâmetros

As informações a seguir são necessárias como entrada para os parâmetros de automação:

|Parâmetro|DESCRIÇÃO|Exemplo|
|---------|---------|---------|
|NOME|Nome da conta SPN|MyAPP|
|ApplicationIdentifier|Identificador exclusivo|S-1-5-21-1634563105-1224503876-2692824315-2119|
|ClientCertificate|Matriz de objetos de certificado|X509 certificado|

#### <a name="example-of-updating-service-principal-for-ad-fs"></a>Exemplo de atualização de entidade de serviço do AD FS

O exemplo cria um certificado autoassinado. Quando você executa os cmdlets em uma implantação de produção, use [Get-Item](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Management/Get-Item) para recuperar o objeto de certificado para o certificado que você deseja usar.

1. Abra uma sessão do Windows PowerShell com privilégios elevados e execute os seguintes cmdlets:

     ```powershell
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $RemoveServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ClientCertificates $NewCert}

          $Session | Remove-PSSession
     ```

2. Após a automação, ele exibe o valor de impressão digital atualizado necessário para a autenticação SPN.

     ```Shell  
          ClientId              : 
          Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
          ApplicationName       : Azurestack-ThomasAPP-3e5dc4d2-d286-481c-89ba-57aa290a4818
          ClientSecret          : 
          RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-using-a-client-secret"></a>Criar uma entidade de serviço usando um segredo do cliente

Ao criar uma entidade de serviço durante o uso do AD FS para a identidade, você pode usar um certificado. Você usará o ponto de extremidade com privilégios para executar os cmdlets.

Esses scripts são executados do ponto de extremidade com privilégios em uma máquina virtual ERCS. Para obter mais informações sobre o ponto de extremidade com privilégios, consulte [usando o ponto de extremidade privilegiado no Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).

#### <a name="parameters"></a>parâmetros

As informações a seguir são necessárias como entrada para os parâmetros de automação:

| Parâmetro | DESCRIÇÃO | Exemplo |
|----------------------|--------------------------|---------|
| NOME | Nome da conta SPN | MyAPP |
| GenerateClientSecret | Criar um segredo |  |

#### <a name="use-the-ercs-privilegedendpoint-to-create-the-service-principal"></a>Use o PrivilegedEndpoint ERCS para criar a entidade de serviço

1. Abra uma sessão do Windows PowerShell com privilégios elevados e execute os seguintes cmdlets:

     ```powershell  
      # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Creating a SPN with a secre
     $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Output the SPN details
     $ServicePrincipal
     ```

2. Após a execução de cmdlets, o shell exibe os detalhes necessários para usar o SPN. Verifique se que você armazenar o segredo do cliente.

     ```powershell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUZLRoBw3EebMDgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : 192.168.200.224
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

#### <a name="update-client-secret-for-a-service-principal-for-ad-fs"></a>Atualizar o segredo do cliente para uma entidade de serviço do AD FS

Um novo segredo do cliente é automaticamente gerado pelo cmdlet do PowerShell.

O script é executado do ponto de extremidade com privilégios em uma máquina virtual ERCS.

##### <a name="parameters"></a>parâmetros

As informações a seguir são necessárias como entrada para os parâmetros de automação:

| Parâmetro | DESCRIÇÃO | Exemplo |
|-----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------|
| ApplicationIdentifier | Identificador exclusivo. | S-1-5-21-1634563105-1224503876-2692824315-2119 |
| ChangeClientSecret | Altera o segredo do cliente com um período de sobreposição de 2.880 minutos em que o segredo antigo ainda é válido. |  |
| ResetClientSecret | Alterar o segredo do cliente imediatamente |  |

##### <a name="example-of-updating-a-client-secret-for-ad-fs"></a>Exemplo de atualização de um segredo do cliente para o AD FS

O exemplo usa o **ResetClientSecret** parâmetro, que altera imediatamente o segredo do cliente.

1. Abra uma sessão do Windows PowerShell com privilégios elevados e execute os seguintes cmdlets:

     ```powershell  
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ResetClientSecret}

          $Session | Remove-PSSession
     ```

2. Após a automação, ele exibe o segredo recém-gerado necessário para a autenticação SPN. Verifique se que você armazenar o novo segredo do cliente.

     ```powershell  
          ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2120
          ClientId              :  
          Thumbprint            : 
          ApplicationName       : Azurestack-Yourapp-6967581b-497e-4f5a-87b5-0c8d01a9f146
          ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
          RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal-for-ad-fs"></a>Remover uma entidade de serviço do AD FS

Se você tiver implantado o Azure Stack com o AD FS, você pode usar o PowerShell para excluir uma entidade de serviço.

O script é executado do ponto de extremidade com privilégios em uma máquina virtual ERCS.

#### <a name="parameters"></a>parâmetros

As informações a seguir são necessárias como entrada para os parâmetros de automação:

|Parâmetro|DESCRIÇÃO|Exemplo|
|---------|---------|---------|
| Parâmetro | DESCRIÇÃO | Exemplo |
| ApplicationIdentifier | Identificador exclusivo | S-1-5-21-1634563105-1224503876-2692824315-2119 |

> [!Note]  
> Para exibir uma lista de todas as entidades de serviço existentes e seu identificador de aplicativo, o comando get-graphapplication pode ser usado.

#### <a name="example-of-removing-the-service-principal-for-ad-fs"></a>Exemplo de remoção a entidade de serviço do AD FS

```powershell  
     Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier S-1-5-21-1634563105-1224503876-2692824315-2119}

     $Session | Remove-PSSession
```

## <a name="assign-a-role"></a>Atribuir uma função

Para acessar recursos em sua assinatura, você deve atribuir o aplicativo a uma função. Decida qual função representa as permissões corretas para o aplicativo. Para saber mais sobre as funções disponíveis, consulte [RBAC: Funções internas](../role-based-access-control/built-in-roles.md).

Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissão são herdadas para níveis inferiores do escopo. Por exemplo, adicionar um aplicativo à função Leitor de um grupo de recursos significa que ele pode ler o grupo de recursos e todos os recursos que ele contiver.

1. No portal do Azure Stack, navegue até o nível de escopo que você deseja atribuir o aplicativo. Por exemplo, para atribuir uma função no escopo da assinatura, escolha **Assinaturas**. Em vez disso, você pode selecionar um grupo de recursos ou um recurso.

2. Escolha a assinatura específica (grupo de recursos ou recurso) à qual atribuir o aplicativo.

     ![Selecione a assinatura para atribuição](./media/azure-stack-create-service-principal/image16.png)

3. Selecione **Controle de Acesso (IAM)**.

     ![Selecionar acesso](./media/azure-stack-create-service-principal/image17.png)

4. Selecione **Adicionar atribuição de função**.

5. Selecione a função que deseja atribuir ao aplicativo.

6. Procure seu aplicativo e selecione-o.

7. Selecione **OK** para finalizar a atribuição da função. Agora você vê o aplicativo na lista de usuários atribuídos a uma função para esse escopo.

Agora que você criou uma entidade de serviço e atribuído a uma função, você pode começar a usar isso dentro de seu aplicativo para acessar os recursos do Azure Stack.  

## <a name="next-steps"></a>Próximas etapas

[Adicionar usuários do AD FS](azure-stack-add-users-adfs.md)  
[Gerenciar permissões de usuário](azure-stack-manage-permissions.md)  
[Documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory)  
[Serviços de Federação do Active Directory (AD FS)](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
