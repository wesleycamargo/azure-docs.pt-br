---
title: Criar um cluster do Service Fabric no Azure | Microsoft Docs
description: Saiba como criar um cluster do Windows no Azure usando um modelo.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5d56fd468998ee4b1253b47aa133812e0141062b
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---

# <a name="deploy-a-secure-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Implantar um cluster seguro do Windows do Service Fabric em uma rede virtual do Azure
Este tutorial é a primeira parte de uma série. Você aprenderá como criar um cluster do Service Fabric (Windows) em execução no Azure e como implantá-lo em uma VNET (rede virtual) e em uma sub-rede existentes. Ao terminar, você terá um cluster em execução na nuvem no qual você poderá implantar aplicativos.  Para criar um cluster do Linux, consulte [Create a secure Linux cluster on Azure using a template](service-fabric-tutorial-create-vnet-and-linux-cluster.md) (Criar um cluster seguro do Linux no Azure usando um modelo).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma VNET no Azure usando um modelo
> * Criar um cofre de chaves e carregar um certificado
> * Criar um cluster seguro do Service Fabric no Azure usando um modelo
> * Proteger o cluster com um certificado X.509
> * Conectar o cluster usando o PowerShell
> * Remover um cluster

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * Criar um cluster seguro no Azure usando um modelo
> * [Implantar o Gerenciamento de API com o Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instale o [SDK do Service Fabric e o módulo do PowerShell](service-fabric-get-started.md)
- Instale o [módulo do Azure PowerShell versão 4.1 ou superior](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

Os procedimentos a seguir criam um cluster de cinco nós do Service Fabric. O cluster é protegido por um certificado autoassinado colocado em um cofre de chaves. 

Para calcular o custo incorrido ao executar um cluster do Service Fabric no Azure, use a [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/).
Para obter mais informações sobre a criação de clusters do Service Fabric, confira [Criar um cluster do Service Fabric usando o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Entre no Azure e selecione sua assinatura
Este guia usa o Azure PowerShell. Ao iniciar uma nova sessão do PowerShell, entre em sua conta do Azure e selecione sua assinatura antes de executar comandos do Azure.
 
Execute o seguinte script para entrar em sua conta do Azure e selecione sua assinatura:

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um novo grupo de recursos para sua implantação e dê um nome e um local a ela.

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
New-AzureRmResourceGroup -Name $ResourceGroupName -Location centralus
```

## <a name="deploy-the-network-topology"></a>Implante a topologia da rede
Em seguida, configure a topologia de rede para a qual o Gerenciamento de API e o cluster do Service Fabric serão implantados. O modelo do Resource Manager [network.json][network-arm] foi configurado para criar uma VNET (rede virtual) e também um NSG (grupo de segurança de rede) e uma sub-rede para o Service Fabric e um NSG e uma sub-rede para o Gerenciamento de API. Saiba mais sobre VNETs, sub-redes e NSGs [aqui](../virtual-network/virtual-networks-overview.md).

O arquivo de parâmetros [network.parameters.json][network-parameters-arm] contém os nomes das sub-redes e NSGs em que o Service Fabric e o Gerenciamento de API são implantados.  O Gerenciamento de API é implantado no [seguinte tutorial](service-fabric-tutorial-deploy-api-management.md). Para este guia, os valores de parâmetros não precisam ser alterados. Os modelos do Resource Manager do Service Fabric usam esses valores.  Se os valores forem modificados aqui, será necessário modificá-los em outros modelos do Resource Manager usados neste tutorial e em [Implantar tutorial de Gerenciamento de API](service-fabric-tutorial-deploy-api-management.md). 

Baixe o seguinte modelo do Resource Manager e arquivo de parâmetros:
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

Use o seguinte comando do PowerShell para implantar o modelo do Resource Manager e os arquivos de parâmetros para a configuração de rede:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="create-a-key-vault-and-upload-a-certificate"></a>Criar um cofre de chaves e carregar um certificado
O modelo do Resource Manager do cluster do Service Fabric na próxima etapa é configurado para criar um cluster seguro com segurança de certificado. O certificado é utilizado para proteger a comunicação do nó para nó para o seu cluster e gerenciar o acesso do usuário ao seu cluster do Service Fabric. O Gerenciamento de API também utiliza esse certificado para acessar o Serviço de Nomenclatura do Service Fabric para a descoberta de serviço. Isso requer um certificado no Key Vault para que haja segurança do cluster.

O script a seguir cria um cofre de chaves no Azure, cria um certificado autoassinado e carrega o certificado para o cofre de chaves.  Se você desejar usar um certificado existente, defina **$CreateSelfSignedCertificate** como "$false" e especifique o local em **$ExistingPfxFilePath**.

```powershell
$VaultResourceGroupName = 'ryanwikeyvaultgroup'
$VaultName= 'ryanwikeyvault'
$Location = "westus"
$CertificateName = "ryanwicertificate1"
$Password = 'mypa$$word!'
$DnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$OutputPath = "C:\MyCertificates" # location where you want the .PFX to be stored
$CreateSelfSignedCertificate = $true
$ExistingPfxFilePath = 'C:\MyCertificates\ryanwicertificate1.pfx'

$ErrorActionPreference = 'Stop'

Write-Host "Switching context to SubscriptionId $SubscriptionId"
Set-AzureRmContext -SubscriptionId $SubscriptionId | Out-Null

# New-AzureRmResourceGroup is idempotent as long as the location matches
Write-Host "Ensuring ResourceGroup $VaultResourceGroupName in $Location"
New-AzureRmResourceGroup -Name $VaultResourceGroupName -Location $Location -Force | Out-Null
$resourceId = $null

try
{
    $existingKeyVault = Get-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultResourceGroupName
    $resourceId = $existingKeyVault.ResourceId

    Write-Host "Using existing vault $VaultName in $($existingKeyVault.Location)"
}
catch
{
}

if(!$existingKeyVault)
{
    Write-Host "Creating new vault $VaultName in $location"
    $newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultResourceGroupName -Location $Location -EnabledForDeployment
    $resourceId = $newKeyVault.ResourceId
}

if($CreateSelfSignedCertificate)
{
    $securePassword = ConvertTo-SecureString -String $password -AsPlainText -Force

    $NewPfxFilePath = Join-Path $OutputPath $($CertificateName+".pfx")

    Write-Host "Creating new self signed certificate at $NewPfxFilePath"
    
    ## Changes to PSPKI version 3.5.2 New-SelfSignedCertificate replaced by New-SelfSignedCertificateEx
    $PspkiVersion = (Get-Module PSPKI).Version
    if($PSPKIVersion.Major -ieq 3 -And $PspkiVersion.Minor -ieq 2 -And $PspkiVersion.Build -ieq 5) {
        New-SelfsignedCertificateEx -Subject "CN=$DnsName" -EKU "Server Authentication", "Client authentication" -KeyUsage "KeyEncipherment, DigitalSignature" -Path $NewPfxFilePath -Password $securePassword -Exportable
    }
    else {
        New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName $DnsName | Export-PfxCertificate -FilePath $NewPfxFilePath -Password $securePassword | Out-Null
    }

    $ExistingPfxFilePath = $NewPfxFilePath
}

Write-Host "Reading pfx file from $ExistingPfxFilePath"
$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2 $ExistingPfxFilePath, $Password

$bytes = [System.IO.File]::ReadAllBytes($ExistingPfxFilePath)
$base64 = [System.Convert]::ToBase64String($bytes)

$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $Password
   } | ConvertTo-Json

    $contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
    $content = [System.Convert]::ToBase64String($contentbytes)

    $secretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

Write-Host "Writing secret to $CertificateName in vault $VaultName"
$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $CertificateName -SecretValue $secretValue

$output = @{};
$output.SourceVault = $resourceId;
$output.CertificateURL = $secret.Id;
$output.CertificateThumbprint = $cert.Thumbprint;

Write-Host "Source vault: " $output.SourceVault
Write-Host "Certificate URL: " $output.CertificateURL
Write-Host "Certificate Thumbprint: " $output.CertificateThumbprint
```

## <a name="deploy-the-service-fabric-cluster"></a>Implantar o cluster do Service Fabric
Depois que os recursos de rede tiverem concluído a implantação e você tiver carregado um certificado para um cofre de chaves, a próxima etapa será implantar um cluster do Service Fabric na VNET na sub-rede e no NSG designado para o cluster do Service Fabric. Para este tutorial, o modelo do Resource Manager do Service Fabric é pré-configurado para usar os nomes da VNET, da sub-rede e do NSG que você configurou na etapa anterior.

Baixe o seguinte modelo do Resource Manager e arquivo de parâmetros:
- [cluster.json][cluster-arm]
- [cluster.parameters.json][cluster-parameters-arm]

Preencha os parâmetros vazios no arquivo `cluster.parameters.json` para sua implantação, incluindo as [informações do Key Vault](service-fabric-cluster-creation-via-arm.md#set-up-a-key-vault) para o certificado de cluster.

Utilize o seguinte comando do PowerShell para implantar o modelo do Resource Manager e arquivos de parâmetros para criar o cluster do Service Fabric:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\cluster.json -TemplateParameterFile .\cluster.parameters.json -Verbose
```

## <a name="modify-the-certificate--access-service-fabric-explorer"></a>Modificar o certificado e acessar o Gerenciador do Service Fabric 

1. Clique duas vezes no certificado para abrir o Assistente para Importação de Certificados.

2. Use as configurações padrão, mas marque a caixa de seleção **Marcar esta chave como exportável.** , na etapa **proteção de chave privada**. O Visual Studio precisa exportar o certificado ao configurar o Registro de Contêiner do Azure para autenticação do Cluster do Service Fabric, mais adiante neste tutorial.

3. Agora você pode abrir o Service Fabric Explorer em um navegador. Para fazer isso, navegue até o **ManagementEndpoint** URL para o cluster usando um navegador da web e selecione o certificado que foi salvo no seu computador.

>[!NOTE]
>Ao abrir o Service Fabric Explorer, você verá um erro de certificado, pois está usando um certificado autoassinado. No Edge, você precisa clicar em *Detalhes* e no link *Ir para a página da Web*. No Chrome, você precisa clicar em *Avançado* e no link *Continuar*.

>[!NOTE]
>Se a criação do cluster falhar, você sempre poderá executar novamente o comando, o que atualiza os recursos já implantados. Se um certificado tiver sido criado como parte da implantação com falha, um novo será gerado. Para solucionar problemas de criação do cluster, consulte [Criar um cluster do Service Fabric usando o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="connect-to-the-secure-cluster"></a>Conectar-se ao cluster seguro
Conecte-se ao cluster usando o módulo do PowerShell do Service Fabric instalado com o SDK do Service Fabric.  Primeiro, instale o certificado no repositório pessoal (Meu) do usuário atual no seu computador.  Execute o seguinte comando do PowerShell:

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Agora você está pronto para se conectar ao seu cluster seguro.

O módulo do PowerShell do **Service Fabric** fornece vários cmdlets para gerenciar clusters, aplicativos e serviços do Service Fabric.  Use o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) para se conectar ao cluster seguro. Os detalhes de ponto de extremidade de conexão e de impressão digital do certificado podem ser encontrados na saída da etapa anterior.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Verifique se você está conectado e se o cluster está íntegro, usando o cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Limpar recursos

Um cluster é composto por outros recursos do Azure, além do próprio recurso do cluster. A maneira mais simples de excluir o cluster e todos os recursos que ele consume é excluir o grupo de recursos.

Faça logon no Azure e selecione a ID de assinatura com a qual você deseja remover o cluster.  Você pode encontrar sua ID de assinatura fazendo logon no [Portal do Azure](http://portal.azure.com). Exclua o grupo de recursos e todos os recursos de cluster usando o [cmdlet Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId "Subcription ID"

$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma VNET no Azure usando um modelo
> * Criar um cofre de chaves e carregar um certificado
> * Criar um cluster seguro do Service Fabric no Azure usando um modelo
> * Proteger o cluster com um certificado X.509
> * Conectar o cluster usando o PowerShell
> * Remover um cluster

Em seguida, avance para o próximo tutorial para saber como implantar um aplicativo existente.
> [!div class="nextstepaction"]
> [Implantar o Gerenciamento de API](service-fabric-tutorial-deploy-api-management.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json

