---
title: Adicionar Kubernetes para o Azure Stack Marketplace | Microsoft Docs
description: Saiba como adicionar o Kubernetes no Azure Stack Marketplace.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 7b73a0efbe20b43b3ba8e94fce3aabf12ea918e8
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2019
ms.locfileid: "56986049"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Adicionar Kubernetes para o Azure Stack Marketplace

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!note]  
> Kubernetes no Azure Stack está em visualização. Cenário desconectado da pilha do Azure não é suportado pelo preview.

Você pode oferecer Kubernetes como um item do Marketplace para seus usuários. Os usuários podem, em seguida, implante o Kubernetes em uma única operação coordenada.

O seguinte artigo examinar usando um modelo do Azure Resource Manager para implantar e provisionar os recursos para um cluster de Kubernetes autônomo. Antes de começar, verifique suas configurações de locatário do Azure global e o Azure Stack. Colete as informações necessárias sobre o Azure Stack. Adicione recursos necessários para seu locatário e o Azure Stack Marketplace. O cluster depende de um servidor do Ubuntu, o script personalizado e o item do Marketplace de Cluster do Kubernetes seja no marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Criar um plano, uma oferta e uma assinatura

Crie um plano, uma oferta e uma assinatura para o item do Marketplace do Kubernetes. Você também pode usar um plano existente e oferecem.

1. Entrar para o [portal de administração.](https://adminportal.local.azurestack.external)

1. Crie um plano que o plano base. Para obter instruções, consulte [criar um plano no Azure Stack](azure-stack-create-plan.md).

1. Crie uma oferta. Para obter instruções, consulte [criar uma oferta no Azure Stack](azure-stack-create-offer.md).

1. Selecione **oferece**e localize a oferta que você criou.

1. Selecione **visão geral** na folha da oferta.

1. Selecione **alterar estado**. Selecione **Público**.

1. Selecione **+ criar um recurso** > **planos e oferece** > **assinatura** para criar uma assinatura.

     a. Insira um **nome de exibição**.

    b. Insira um **usuário**. Use conta do Azure AD associada ao locatário.

    c. **Descrição do provedor**

    d. Defina a **locatário do diretório** ao locatário do Azure AD para o Azure Stack. 

    e. Selecione **oferecem**. Selecione o nome da oferta que você criou. Anote a ID da assinatura.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Criar uma entidade de serviço e as credenciais no AD FS

Se você usar o Active Directory Federated Services (AD FS) para seu serviço de gerenciamento de identidade, você precisará criar um serviço principal para usuários que estão implantando um cluster Kubernetes.

1. Criar e exportar um certificado autoassinado usado para criar a entidade de serviço. 

    - Você precisa que as seguintes partes de informações:

       | Valor | DESCRIÇÃO |
       | ---   | ---         |
       | Senha | Digite uma nova senha do certificado. |
       | Caminho de certificado local | Insira o nome de arquivo e caminho do certificado. Por exemplo: `c:\certfilename.pfx` |
       | Nome do certificado | Insira o nome do certificado. |
       | Local do repositório de certificados |  Por exemplo, `Cert:\LocalMachine\My` |

    - Abra o PowerShell com um prompt com privilégios elevados. Execute o script a seguir com os parâmetros atualizados para seus valores:

        ```PowerShell  
        # Creates a new self signed certificate 
        $passwordString = "<password>"
        $certlocation = "<local certificate path>.pfx"
        $certificateName = "CN=<certificate name>"
        $certStoreLocation="<certificate store location>"
        
        $params = @{
        CertStoreLocation = $certStoreLocation
        DnsName = $certificateName
        FriendlyName = $certificateName
        KeyLength = 2048
        KeyUsageProperty = 'All'
        KeyExportPolicy = 'Exportable'
        Provider = 'Microsoft Enhanced Cryptographic Provider v1.0'
        HashAlgorithm = 'SHA256'
        }
        
        $cert = New-SelfSignedCertificate @params -ErrorAction Stop
        Write-Verbose "Generated new certificate '$($cert.Subject)' ($($cert.Thumbprint))." -Verbose
        
        #Exports certificate with password in a .pfx format
        $pwd = ConvertTo-SecureString -String $passwordString -Force -AsPlainText
        Export-PfxCertificate -cert $cert -FilePath $certlocation -Password $pwd
        ```

2.  Anote a nova ID de certificado exibida na sua sessão do PowerShell, `1C2ED76081405F14747DC3B5F76BB1D83227D824`. A ID será usada ao criar a entidade de serviço.

    ```PowerShell  
    VERBOSE: Generated new certificate 'CN=<certificate name>' (1C2ED76081405F14747DC3B5F76BB1D83227D824).
    ```

3. Crie a entidade usando o certificado de serviço.

    - Você precisa que as seguintes partes de informações:

       | Valor | DESCRIÇÃO                     |
       | ---   | ---                             |
       | ERCS IP | No ASDK, o ponto de extremidade com privilégios é normalmente `AzS-ERCS01`. |
       | Nome do aplicativo | Insira um nome simples para a entidade de serviço de aplicativo. |
       | Local do repositório de certificados | O caminho no computador em que você armazenou o certificado. Isso é indicado pelo local do repositório e a ID do certificado gerado na primeira etapa. Por exemplo: `Cert:\LocalMachine\My\1C2ED76081405F14747DC3B5F76BB1D83227D824` |

       Quando solicitado, use as seguintes credenciais para se conectar ao ponto de extremidade de privilégio. 
        - Nome de usuário: Especifique a conta CloudAdmin, no formato <Azure Stack domain>\cloudadmin. (Para ASDK, o nome de usuário é azurestack\cloudadmin.)
        - Senha: Insira a mesma senha que foi fornecida durante a instalação para a conta de administrador de domínio AzureStackAdmin.

    - Execute o script a seguir com os parâmetros atualizados para seus valores:

        ```PowerShell  
        #Create service principal using the certificate
        $privilegedendpoint="<ERCS IP>"
        $applicationName="<application name>"
        $certStoreLocation="<certificate location>"
        
        # Get certificate information
        $cert = Get-Item $certStoreLocation
        
        # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
        $creds = Get-Credential

        # Creating a PSSession to the ERCS PrivilegedEndpoint
        $session = New-PSSession -ComputerName $privilegedendpoint -ConfigurationName PrivilegedEndpoint -Credential $creds

        # Get Service principal Information
        $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name "$using:applicationName" -ClientCertificates $using:cert}

        # Get Stamp information
        $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }

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

    - Os detalhes da entidade de serviço se parecer com o trecho a seguir

        ```Text  
        ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
        ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
        Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
        ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
        PSComputerName        : azs-ercs01
        RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
        ```

## <a name="add-an-ubuntu-server-image"></a>Adicionar uma imagem de servidor do Ubuntu

Adicione a seguinte imagem do Ubuntu Server no Marketplace:

1. Entrar para o [portal de administração](https://adminportal.local.azurestack.external).

1. Selecione **todos os serviços**e, em seguida, sob o **administração** categoria, selecione **gerenciamento Marketplace**.

1. Selecione **+ adicionar do Azure**.

1. Digite `Ubuntu Server`.

1. Selecione a versão mais recente do servidor. Verifique a versão completa e garantir que você tenha a versão mais recente:
    - **Editor**: Canônico
    - **Oferta**: UbuntuServer
    - **Versão**: 16.04.201806120 (ou versão mais recente)
    - **SKU**: 16.04-LTS

1. Selecione **baixar.**

## <a name="add-a-custom-script-for-linux"></a>Adicionar um script personalizado para Linux

Adicione o Kubernetes do Marketplace:

1. Abra o [portal de administração](https://adminportal.local.azurestack.external).

1. Selecione **todos os serviços** e, em seguida, sob o **administração** categoria, selecione **gerenciamento Marketplace**.

1. Selecione **+ adicionar do Azure**.

1. Digite `Custom Script for Linux`.

1. Selecione o script com o seguinte perfil:
    - **Oferta**: Script personalizado para Linux 2.0
    - **Versão**: 2.0.6 (ou versão mais recente)
    - **Editor**: Microsoft Corp

    > [!Note]  
    > Mais de uma versão de Script personalizado para Linux pode ser listada. Você precisará adicionar a última versão do item.

1. Selecione **baixar.**


## <a name="add-kubernetes-to-the-marketplace"></a>Adicionar o Kubernetes no Marketplace

1. Abra o [portal de administração](https://adminportal.local.azurestack.external).

1. Selecione **todos os serviços** e, em seguida, sob o **administração** categoria, selecione **gerenciamento Marketplace**.

1. Selecione **+ adicionar do Azure**.

1. Digite `Kubernetes`.

1. Selecione `Kubernetes Cluster`.

1. Selecione **baixar.**

    > [!note]  
    > Ele pode levar cinco minutos para que o item do marketplace aparecer no Marketplace.

    ![kubernetes](user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Atualizar ou remover o Kubernetes 

Ao atualizar o item de Kubernetes, você removerá o item anterior no Marketplace. Siga as instruções neste artigo para adicionar que os Kubernetes de atualizações no Marketplace.

Para remover o item de Kubernetes:

1. Conecte-se ao Azure Stack com o PowerShell como um operador. Para obter instruções, consulte [conectar-se ao Azure Stack com o PowerShell como um operador](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-admin).

2. Localize o item atual do Kubernetes Cluster na Galeria.

    ```PowerShell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Anote o nome do item atual, como `Microsoft.AzureStackKubernetesCluster.0.3.0`

4. Use o seguinte cmdlet do PowerShell para remover o item:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Próximas etapas

[Implantar um Kubernetes no Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

[Visão geral da oferta de serviços no Azure Stack](azure-stack-offer-services-overview.md)
