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
ms.date: 01/22/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 76fcdff6804cd1fa66c846597218d351eb6f4c77
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Implantar um cluster do Windows do Service Fabric em uma rede virtual do Azure
Este tutorial é a primeira parte de uma série. Você aprenderá como implantar um cluster do Service Fabric executando o Windows em uma [rede virtual do Azure (VNET)](../virtual-network/virtual-networks-overview.md) e em [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) usando o PowerShell e um modelo. Ao terminar, você terá um cluster em execução na nuvem no qual você poderá implantar aplicativos.  Para criar um cluster do Linux usando o CLI do Azure, consulte [Create a secure Linux cluster on Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) (Criar um cluster seguro do Linux no Azure).

Este tutorial descreve um cenário de produção.  Se você quiser criar rapidamente um cluster pequeno para fins de teste rapidamente, consulte [Criar um cluster de teste de três nós](./scripts/service-fabric-powershell-create-test-cluster.md).

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
> * [Reduzir ou escalar um cluster horizontalmente](service-fabric-tutorial-scale-cluster.md)
> * [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Implantar o Gerenciamento de API com o Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>pré-requisitos
Antes de começar este tutorial:
- Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instale o [SDK do Service Fabric e o módulo do PowerShell](service-fabric-get-started.md)
- Instale o [módulo do Azure PowerShell versão 4.1 ou superior](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

Os procedimentos a seguir criam um cluster de cinco nós do Service Fabric. Para calcular o custo incorrido ao executar um cluster do Service Fabric no Azure, use a [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="key-concepts"></a>Principais conceitos
Um [cluster do Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto de máquinas físicas ou virtuais conectadas em rede, no qual os microsserviços são implantados e gerenciados. Os clusters podem ser dimensionados para milhares de máquinas. Uma máquina ou VM que faz parte de um cluster é chamado de nó. Cada nó recebe um nome de nó (uma cadeia de caracteres). Os nós têm características como propriedades de posicionamento.

Um tipo de nó define o tamanho, o número e as propriedades de um conjunto de máquinas virtuais no cluster. Cada tipo de nó definido é configurado como um [conjunto de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets/), um recurso de computação do Azure usado para implantar e gerenciar conjuntamente um grupo de máquinas virtuais. Cada tipo de nó pode ser escalado verticalmente para cima ou para baixo de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente. Os tipos de nós são usados na definição de funções para um conjunto de nós de cluster, como "front-end" ou "back-end".  Seu cluster pode ter mais de um tipo de nó, mas o tipo de nó primário deve ter pelo menos cinco VMs para clusters de produção (ou pelo menos três VMs para clusters de teste).  [Os serviços de sistema do Service Fabric](service-fabric-technical-overview.md#system-services) são colocados nos nós do tipo primário.

O cluster é protegido por um certificado de cluster. Um certificado de cluster é um certificado x.509 usado para proteger a comunicação de nó para nó e autenticar os pontos de extremidade de gerenciamento de cluster para um cliente de gerenciamento.  Esse certificado também fornece um SSL para a API de gerenciamento de HTTPS e para o Service Fabric Explorer por HTTPS. Certificados autoassinados são somente para clusters de teste.  Para clusters de produção, use um certificado de uma autoridade de certificação (CA) como o certificado de cluster.

O certificado de cluster deve:

- conter uma chave privada.
- ser criado para troca de chaves, que deve ser exportável para um arquivo Troca de Informações Pessoais (.pfx).
- ter um nome de entidade que corresponda ao domínio usado para acessar o cluster do Service Fabric. Essa correspondência é necessária para fornecer SSL para pontos de extremidade de gerenciamento de HTTPS e Service Fabric Explorer do cluster. Você não pode obter um certificado SSL de uma AC (autoridade de certificação) para o domínio cloudapp.azure.com. Você deve obter um nome de domínio personalizado para seu cluster. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para seu cluster.

O Cofre de Chaves do Azure é usado para gerenciar certificados para clusters do Service Fabric no Azure.  Quando um cluster é implantado no Azure, o provedor de recursos do Azure responsável pela criação de clusters do Service Fabric recebe certificados do Cofre de Chaves e os instala nas VMs do cluster.

Este tutorial exibe um cluster com cinco nós em um único tipo de nó. No entanto, em qualquer implantação de cluster de produção, o [planejamento da capacidade](service-fabric-cluster-capacity.md) é uma etapa importante. Aqui estão alguns pontos a serem considerados como parte desse processo.

- O número de nós e tipos de nó que o seu cluster precisa 
- As propriedades de cada tipo de nó (por exemplo, tamanho, primário, voltado para a Internet e número de VMs)
- As características de confiabilidade e durabilidade do cluster

## <a name="download-and-explore-the-template"></a>Baixar e explorar o modelo
Baixe os seguintes arquivos do modelo do Resource Manager:
- [vnet-cluster.json][template]
- [vnet-cluster.parameters.json][parameters]

O [vnet-cluster.json][template] implanta diversos recursos, incluindo os seguintes. 

### <a name="service-fabric-cluster"></a>Cluster do Service Fabric
Um cluster do Windows é implantado com as seguintes características:
- um tipo de nó único 
- cinco nós no tipo de nó principal (configurável nos parâmetros de modelo)
- Sistema operacional: Windows Server 2016 Datacenter com Contêineres (configuráveis nos parâmetros de modelo)
- certificado protegidos (configurável nos parâmetros de modelo)
- [proxy reverso](service-fabric-reverseproxy.md) está habilitado
- [Serviço DNS](service-fabric-dnsservice.md) está habilitado
- [Nível de durabilidade](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) de Bronze (configurável nos parâmetros de modelo)
- [Nível de confiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) de Prata (configurável nos parâmetros de modelo)
- ponto de extremidade de conexão de cliente: 19000 (configurável nos parâmetros de modelo)
- ponto de extremidade de gateway HTTP: 19080 (configurável nos parâmetros de modelo)

### <a name="azure-load-balancer"></a>Azure Load Balancer
Um balanceador de carga é implantado e investigações e regras são configurados para as seguintes portas:
- ponto de extremidade de conexão do cliente: 19000
- ponto de extremidade de gateway HTTP: 19080 
- porta do aplicativo: 80
- porta do aplicativo: 443
- Proxy Inverso do Service Fabric: 19081

Se forem necessárias outras portas de aplicativo, você precisará ajustar o recurso de Microsoft.Network/loadBalancers e o recurso de Microsoft.Network/networkSecurityGroups para permitir o tráfego de entrada.

### <a name="virtual-network-subnet-and-network-security-group"></a>Rede virtual, sub-rede e grupo de segurança de rede
Os nomes de rede virtual, sub-rede e grupo de segurança de rede são declarados nos parâmetros de modelo.  Espaços de endereço da rede virtual e sub-rede também são declarados nos parâmetros do modelo:
- espaço de endereço da rede virtual: 172.16.0.0/20
- espaço de endereço de sub-rede do Service Fabric: 172.16.2.0/23

As seguintes regras de tráfego de entrada estão habilitadas no grupo de segurança de rede. Você pode alterar os valores de porta, alterando as variáveis de modelo.
- ClientConnectionEndpoint (TCP): 19000
- HttpGatewayEndpoint (HTTP/TCP): 19080
- SMB : 445
- Internodecommunication - 1025, 1026, 1027
- Intervalo de portas efêmeras – 49152 a 65534 (necessário um mínimo de 256 portas)
- Portas para o uso do aplicativo: 80 e 443
- Intervalo de portas do aplicativo – 49152 a 65534 (usada para comunicação de serviços e que não são abertas no balanceador de carga)
- Bloquear todas as outras portas

Se forem necessárias outras portas de aplicativo, você precisará ajustar o recurso de Microsoft.Network/loadBalancers e o recurso de Microsoft.Network/networkSecurityGroups para permitir o tráfego de entrada.

## <a name="set-template-parameters"></a>Definir os parâmetros do modelo
O arquivo de parâmetros [vnet cluster.parameters.json][parameters] declara muitos valores usados para implantar o cluster e os recursos associados. Alguns dos parâmetros que você talvez precise modificar para sua implantação:

|Parâmetro|Valor de exemplo|Observações|
|---|---||
|adminUserName|vmadmin| Um nome de usuário de administrador para as VMs do cluster. |
|adminPassword|Password#1234| Uma senha de administrador para as VMs do cluster.|
|clusterName|mysfcluster123| Nome do cluster. |
|location|southcentralus| Localização do cluster. |
|certificateThumbprint|| <p>O valor deve estar vazio se for criado um certificado auto-assinado ou for fornecido um arquivo de certificado.</p><p>Para usar um certificado existente que foi previamente carregado para um cofre de chaves, preencha o valor da impressão digital do certificado. Por exemplo, "6190390162C988701DB5676EB81083EA608DCCF3"</p>. | 
|certificateUrlValue|| <p>O valor deve estar vazio se for criado um certificado auto-assinado ou for fornecido um arquivo de certificado. </p><p>Para usar um certificado existente que foi previamente carregado para um cofre de chaves, preencha a URL do certificado. Por exemplo, "https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>O valor deve estar vazio se for criado um certificado auto-assinado ou for fornecido um arquivo de certificado.</p><p>Para usar um certificado existente que foi previamente carregado para um cofre de chaves, preencha o valor do cofre de origem. For example, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|


<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Implantar a rede virtual e o cluster
Em seguida, configure a topologia de rede e implante o cluster do Service Fabric. O modelo do Resource Manager [vnet-cluster.json][template] cria uma rede virtual (VNET) e também uma sub-rede e um grupo de segurança de rede (NSG) e para o Service Fabric. O modelo também implanta um cluster com a segurança de certificado habilitada.  Para clusters de produção, use um certificado de uma autoridade de certificação (CA) como o certificado de cluster. Um certificado autoassinado pode ser usado para proteger clusters de teste.

### <a name="create-a-cluster-using-an-existing-certificate"></a>Criar um cluster usando um certificado existente
O script a seguir usa o cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) e um modelo para implantar um novo cluster no Azure. O cmdlet também cria um novo cofre de chaves no Azure e carrega o certificado. 

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # must match the clusterName parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\vnet-linuxcluster.json" `
-ParameterFile "$templatepath\vnet-linuxcluster.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Criar um cluster usando um novo certificado autoassinado
O script a seguir usa o cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) e um modelo para implantar um novo cluster no Azure. O cmdlet também cria um cofre de chaves no Azure, adiciona um certificado autoassinado a ele e baixa o arquivo de certificado localmente. 

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\vnet-linuxcluster.json" `
-ParameterFile "$templatepath\vnet-linuxcluster.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateSubjectName $subname

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
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
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
Os outros artigos nesta série de tutoriais usam o cluster que você criou. Se você não estiver prosseguindo imediatamente para o próximo artigo, talvez queira excluir o cluster e o cofre de chaves para evitar a cobrança de taxas. A maneira mais simples de excluir o cluster e todos os recursos que ele consume é excluir o grupo de recursos.

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

Em seguida, avance para o próximo tutorial para saber como dimensionar o cluster.
> [!div class="nextstepaction"]
> [Dimensionar um cluster](service-fabric-tutorial-scale-cluster.md)


[template]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-cluster.json
[parameters]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-cluster.parameters.json
