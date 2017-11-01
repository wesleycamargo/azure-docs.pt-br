---
title: Criar um cluster do Service Fabric do Windows no Azure | Microsoft Docs
description: Saiba como implantar um cluster do Service Fabric do Windows em uma rede de virtual do Azure existente usando o PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: d1eabaa1a2f1f8ba8102d567fee97c65d04ca5f7
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2017
---
# <a name="deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Implantar um cluster do Windows do Service Fabric em uma rede virtual do Azure
Este tutorial é a primeira parte de uma série. Você aprenderá como implantar um cluster do Service Fabric do Windows em uma rede e sub-rede de virtual do Azure (VNET) existente usando o PowerShell. Ao terminar, você terá um cluster em execução na nuvem no qual você poderá implantar aplicativos.  Para criar um cluster do Linux usando o CLI do Azure, consulte [Create a secure Linux cluster on Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) (Criar um cluster seguro do Linux no Azure).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma VNET no Azure usando o PowerShell
> * Criar um cofre de chaves e carregar um certificado
> * Criar um cluster do Service Fabric seguro no Azure PowerShell
> * Proteger o cluster com um certificado X.509
> * Conectar o cluster usando o PowerShell
> * Remover um cluster

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * Criar um cluster seguro no Azure
> * [Implantar o Gerenciamento de API com o Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instale o [SDK do Service Fabric e o módulo do PowerShell](service-fabric-get-started.md)
- Instale o [módulo do Azure PowerShell versão 4.1 ou superior](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

Os procedimentos a seguir criam um cluster de cinco nós do Service Fabric. Para calcular o custo incorrido ao executar um cluster do Service Fabric no Azure, use a [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/).

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
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc
```

## <a name="deploy-the-network-topology"></a>Implante a topologia da rede
Em seguida, configure a topologia de rede para a qual o Gerenciamento de API e o cluster do Service Fabric serão implantados. O modelo do Resource Manager [network.json][network-arm] foi configurado para criar uma VNET (rede virtual) e também um NSG (grupo de segurança de rede) e uma sub-rede para o Service Fabric e um NSG e uma sub-rede para o Gerenciamento de API. Saiba mais sobre VNETs, sub-redes e NSGs [aqui](../virtual-network/virtual-networks-overview.md).

O arquivo de parâmetros [network.parameters.json][network-parameters-arm] contém os nomes das sub-redes e NSGs em que o Service Fabric e o Gerenciamento de API são implantados.  O Gerenciamento de API é implantado no [seguinte tutorial](service-fabric-tutorial-deploy-api-management.md). Para este guia, os valores de parâmetros não precisam ser alterados. Os modelos do Resource Manager do Service Fabric usam esses valores.  Se os valores forem modificados aqui, será necessário modificá-los em outros modelos do Resource Manager usados neste tutorial e em [Implantar tutorial de Gerenciamento de API](service-fabric-tutorial-deploy-api-management.md). 

Baixe o seguinte modelo do Resource Manager e arquivo de parâmetros:
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

Use o seguinte comando do PowerShell para implantar o modelo do Resource Manager e os arquivos de parâmetros para a configuração de rede:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Implantar o cluster do Service Fabric
Uma vez que os recursos de rede concluíram a implantação, a próxima etapa é implantar um cluster do Service Fabric na VNET na sub-rede e o NSG designado para o cluster do Service Fabric. Implantar um cluster em uma VNET e em uma sub-rede existentes (implantada anteriormente neste artigo) requer um modelo do Resource Manager.  Para obter mais informações, consulte [Como criar um cluster usando o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Para esta série de tutoriais, o modelo é pré-configurado para usar os nomes da VNET, da sub-rede e do NSG que você configurou em uma etapa anterior.  Baixe o seguinte modelo do Resource Manager e arquivo de parâmetros:
- [cluster.json][cluster-arm]
- [cluster.parameters.json][cluster-parameters-arm]

O certificado é utilizado para proteger a comunicação do nó a nó para o seu cluster e gerenciar o acesso do usuário ao seu cluster do Service Fabric. O Gerenciamento de API também utiliza esse certificado para acessar o Serviço de Nomenclatura do Service Fabric para a descoberta de serviço. 

O script a seguir usa o cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) para implantar um novo cluster no Azure. O cmdlet também cria um cofre de chaves no Azure, cria um cofre de chaves e certificado autoassinado e baixa o arquivo de certificado localmente.   

```powershell
# Certificate variables.
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"

# Variables for VM admin.
$adminuser="vmadmin"
$adminpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 

# Variables for common values
$clustername = "mysfcluster"
$vmsku = "Standard_D2_v2"
$vaultname = "clusterkeyvault"
$vaultgroupname="clusterkeyvaultgroup"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Set the number of cluster nodes. Possible values: 1, 3-99
$clustersize=5 

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
-ClusterSize $clustersize -VmUserName $adminuser -VmPassword $adminpwd -CertificateSubjectName $subname `
-CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
-OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname
```

## <a name="connect-to-the-secure-cluster"></a>Conectar-se ao cluster seguro
Conecte-se ao cluster usando o módulo do PowerShell do Service Fabric instalado com o SDK do Service Fabric.  Primeiro, instale o certificado no repositório pessoal (Meu) do usuário atual no seu computador.  Execute o seguinte comando do PowerShell:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
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

## <a name="clean-up-resources"></a>Limpar recursos
Os outros artigos nesta série de tutoriais usam o cluster que você acabou de criar. Se você não estiver prosseguindo imediatamente para o próximo artigo, talvez queira excluir o cluster e o cofre de chaves para evitar a cobrança de taxas. A maneira mais simples de excluir o cluster e todos os recursos que ele consume é excluir o grupo de recursos.

Exclua o grupo de recursos e todos os recursos de cluster usando o [cmdlet Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).  Também exclua o grupo de recursos que contém o cofre de chaves.

```powershell
Remove-AzureRmResourceGroup -Name $groupname -Force
Remove-AzureRmResourceGroup -Name $vaultgroupname -Force
```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma VNET no Azure usando o PowerShell
> * Criar um cofre de chaves e carregar um certificado
> * Criar um cluster do Service Fabric seguro no Azure usando o PowerShell
> * Proteger o cluster com um certificado X.509
> * Conectar o cluster usando o PowerShell
> * Remover um cluster

Em seguida, avance para o próximo tutorial para saber como implantar um Gerenciamento de API com o Service Fabric.
> [!div class="nextstepaction"]
> [Implantar o Gerenciamento de API](service-fabric-tutorial-deploy-api-management.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json
