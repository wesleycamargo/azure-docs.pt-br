---
title: Estender o HDInsight com a Rede Virtual | Microsoft Docs
description: Saiba como usar a Rede Virtual do Azure para conectar o HDInsight a outros recursos de nuvem ou recursos no seu datacenter
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/20/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a3052e53c642ef3e6d9bb0489476274987707f91
ms.lasthandoff: 04/27/2017


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Estender os recursos do HDInsight usando a Rede Virtual do Azure

Saiba como usar redes virtuais do Azure com HDInsight para habilitar os seguintes cenários:

* Restrinja o acesso ao HDInsight. Por exemplo, impeça o tráfego de entrada da Internet.

* Acesse diretamente os serviços no HDInsight que não são expostos pela Internet. Por exemplo, trabalhe diretamente com os agentes Kafka ou use a API de Java do HBase.

* Conectar diretamente os serviços ao HDInsight. Por exemplo, use o Oozie para importar ou exportar dados para o SQL Server em seu data center.

* Crie soluções que envolvem vários clusters HDInsight. Por exemplo, use o Spark ou Storm para analisar os dados armazenados em Kafka.

## <a name="prerequisites"></a>Pré-requisitos

* CLI do Azure 2.0: para obter mais informações, consulte [Install and Configure Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) (Instalar e configurar a CLI do Azure 2.0).

* Azure PowerShell: para obter mais informações, consulte [Install and Configure Azure PowerShell](/powershell/azure/overview) (Instalar e configurar o Azure PowerShell).

> [!NOTE]
> As etapas neste documento requerem a versão mais recente da CLI do Azure e do Azure PowerShell. Se você estiver usando uma versão mais antiga, os comandos poderão ser diferentes. Para obter melhores resultados, use os links anteriores para instalar as versões mais recentes.

## <a id="whatis"></a>O que é a Rede Virtual do Azure

[Rede Virtual do Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permite que você crie uma rede segura e persistente contendo os recursos necessários para sua solução.

Veja a seguir uma lista de considerações ao usar o HDInsight em uma rede virtual:

* __Redes virtuais clássicas e do Resource Manager__: use a tabela a seguir para determinar o tipo da rede a ser usada com base no sistema operacional do cluster do HDInsight:

    | Sistema operacional do HDInsight | Rede virtual clássica | Rede virtual do Resource Manager |
    | ---- | ---- | ---- |
    | Linux | não | sim |
    | Windows | sim | não |

    Para acessar recursos em uma rede virtual incompatível, una as duas redes. Para obter mais informações sobre como conectar Redes Virtuais clássicas e do Resource Manager, consulte [Connecting classic VNets to new VNets](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) (Conectando Redes Virtuais clássicas a Redes Virtuais novas).

* __DNS personalizado__: o Azure fornece resolução de nomes para os serviços do Azure que são instalados em uma Rede Virtual do Azure. Essa resolução de nome não se estende para fora da rede virtual. Para habilitar a resolução de nomes para recursos fora da rede virtual, você deve usar um servidor DNS personalizado. Para obter mais informações sobre como usar um servidor DNS personalizado, consulte o documento [Resolução de nomes de máquinas virtuais e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

* __Túnel forçado__: o HDInsight não dá suporte à configuração de túnel forçada da Rede Virtual do Azure.

* __Restringir o tráfego de rede__: o HDInsight dá suporte ao uso de Grupos de Segurança de Rede para restringir o tráfego de rede, mas requer acesso irrestrito a vários IPs do Azure. Para obter mais informações, consulte a seção [Redes virtuais protegidas](#secured-virtual-networks).

### <a name="connect-cloud-resources-together-in-a-private-network-cloud-only"></a>Conectar recursos da nuvem juntos em uma rede privada (somente nuvem)

![diagrama da configuração somente nuvem](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

Usar a Rede Virtual para vincular os serviços do Azure ao Azure HDInsight habilita os seguintes cenários:

* **Invocar serviços ou trabalhos do HDInsight** de sites do Azure ou de serviços em execução em máquinas virtuais do Azure.

* **Transferência direta de dados** entre o HDInsight e o Banco de Dados SQL do Azure, o SQL Server ou outra solução de armazenamento de dados em execução em uma máquina virtual.

* **Combinar vários servidores HDInsight** em uma única solução. Há vários tipos de clusters HDInsight que correspondem à carga de trabalho ou à tecnologia para a qual o cluster está ajustado. Não há nenhum método com suporte para criar um cluster que combina vários tipos, como o Storm e HBase em um cluster. O uso de uma rede virtual permite que vários clusters comuniquem-se diretamente entre si.

### <a name="connect-cloud-resources-to-a-local-datacenter-network"></a>Conectar recursos de nuvem a uma rede de datacenter local

A configuração de site a site permite que você conecte vários recursos do seu datacenter à rede virtual do Azure. A conexão pode ser feita usando um dispositivo VPN de hardware ou o serviço de Roteamento e Acesso Remoto.

![diagrama da configuração site a site](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

A configuração ponto a site permite conectar um recurso específico à rede virtual do Azure usando VPN de software.

![diagrama da configuração ponto a site](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

Usar a Rede Virtual para vincular a nuvem e seu datacenter habilita cenários semelhantes à configuração somente nuvem. Porém, você não fica limitado a trabalhar com recursos na nuvem – também pode trabalhar com os recursos em seu datacenter.

* **Transferência direta de dados** entre o HDInsight e seu datacenter. Um exemplo é usar o Sqoop para transferir dados para ou do SQL Server, ou a leitura de dados gerados por um aplicativo LOB (linha de negócios).

* **Invocar serviços ou trabalhos do HDInsight** de um aplicativo LOB. Um exemplo é usar APIs do HBase Java para armazenar e recuperar dados de um cluster HDInsight HBase.

Para obter mais informações sobre os recursos, benefícios e capacidades das redes virtuais, consulte a [Visão geral da rede virtual do Azure](../virtual-network/virtual-networks-overview.md).

> [!NOTE]
> Crie a Rede Virtual do Azure antes de provisionar um cluster HDInsight e, em seguida, especifique a rede ao criar o cluster. Para obter mais informações, consulte [Tarefas de configuração de rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="secured-virtual-networks"></a>Redes virtuais protegidas

O serviço do HDInsight é um serviço gerenciado e requer acesso aos serviços de gerenciamento do Azure durante o provisionamento e a execução. O gerenciamento do Azure executa os seguintes serviços:

* Monitorar a integridade do cluster
* Iniciar o failover dos recursos do cluster
* Alterar o número de nós do cluster por meio de operações de colocação em escala
* Outras tarefas de gerenciamento

> [!NOTE]
> Essas operações não exigem acesso completo à Internet. Ao restringir o acesso à Internet, permita o acesso de entrada na porta 443 para os seguintes endereços IP. Isso permite que o Azure gerencie o HDInsight:

Os endereços IP que devem ser permitidos são específicos à região em que o cluster HDInsight e a Rede Virtual residem. Use a tabela a seguir para encontrar os endereços IP para a região que você está usando.

| País | Região | Endereços IP permitidos | Porta permitida |
| ---- | ---- | ---- | ---- |
| Brasil | Sul do Brasil | 191.235.84.104</br>191.235.87.113 | 443 |
| Canadá | Leste do Canadá | 52.229.127.96</br>52.229.123.172 | 443 |
| &nbsp; | Canadá Central | 52.228.37.66</br>52.228.45.222 | 443 |
| Alemanha | Alemanha Central | 51.4.146.68</br>51.4.146.80 | 443 |
| &nbsp; | Nordeste da Alemanha | 51.5.150.132</br>51.5.144.101 | 443 |
| Índia | Índia Central | 52.172.153.209</br>52.172.152.49 | 443 |
| Reino Unido | Oeste do Reino Unido | 51.141.13.110</br>51.141.7.20 | 443 |
| &nbsp; | Sul do Reino Unido | 51.140.47.39</br>51.140.52.16 | 443 |
| Estados Unidos | Centro-Oeste dos EUA | 52.161.23.15</br>52.161.10.167 | 443 |
| &nbsp; | Oeste dos EUA 2 | 52.175.211.210</br>52.175.222.222 | 443 |

__Se a região não estiver listada na tabela__, permita o tráfego para a porta __443__ nos endereços IP a seguir:

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> O HDInsight não permite a restrição do tráfego de saída, somente o tráfego de entrada. Ao definir regras do Grupo de Segurança de Rede para a sub-rede que contém o HDInsight, __use somente regras de entrada__.

### <a name="working-with-hdinsight-in-secured-virtual-networks"></a>Trabalhando com o HDInsight em redes virtuais protegidas

Se você bloquear o acesso à Internet, não será possível usar os serviços do HDInsight que normalmente são expostos por meio do gateway público para um cluster. Eles incluem Ambari e SSH. Em vez disso, você deve acessar serviços usando o endereço IP interno dos nós principais do cluster.

Para localizar o endereço IP interno dos nós principais, use os scripts na seção [IPs e FQDNs interno](#internal-ips-and-fqdns).

### <a name="example-secured-virtual-network"></a>Exemplo: rede virtual protegida

Os exemplos a seguir demonstram como criar um Grupo de Segurança de Rede que permite tráfego de entrada na porta 443 do endereço IP a seguir:

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> Esses endereços são regiões que não têm endereços IP específicos listados. Para localizar os endereços IP para a sua região, use as informações na seção [Redes virtuais protegidas](#secured-virtual-networks).

Essas etapas pressupõem que você já tenha criado uma Rede Virtual e a sub-rede na qual deseja instalar o HDInsight. Consulte [Criar uma Rede Virtual usando o Portal do Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

> [!WARNING]
> As regras são testadas no tráfego de rede em ordem de __prioridade__. Depois que uma regra corresponde aos critérios de teste, ela é aplicada e nenhuma outra regra é testada para essa solicitação. Se você tiver uma regra que bloqueia amplamente o tráfego de entrada (como uma regra **negar tudo**), ela __deverá__ vir após as regras que permitem o tráfego.
>
> Para obter mais informações sobre as regras do Grupo de Segurança de Rede, consulte o documento [O que é um Grupo de Segurança de Rede](../virtual-network/virtual-networks-nsg.md).

**Exemplo: usando o modelo do Azure Resource Manager**

Usando o seguinte modelo do Gerenciamento de Recursos nos [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/) para criar um cluster HDInsight em uma VNet com as configurações de rede segura:

[Implantar uma VNet segura do Azure e um cluster HDInsight Hadoop na VNet](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

**Exemplo: Azure PowerShell**

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that HDInsight will be installed into"
# Get the Virtual Network object
$vnet = Get-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName
# Get the region the Virtual network is in.
$location = $vnet.Location
# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzureRmNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound
# Set the changes to the security group
Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
# Apply the NSG to the subnet
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

**Exemplo: CLI do Azure**

1. Use o seguinte comando para criar um novo grupo de segurança de rede chamado `hdisecure`. Substitua **RESOURCEGROUPNAME** pelo grupo de recursos que contém a Rede Virtual do Azure. Substitua **LOCATION** pelo local (região) em que o grupo foi criado.

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    Após a criação do grupo, você receberá informações sobre o novo grupo.

2. Use o seguinte para adicionar regras ao novo grupo de segurança de rede que permitem a comunicação de entrada na porta 443 por meio do serviço de integridade e gerenciamento do Azure HDInsight. Substitua **RESOURCEGROUPNAME** pelo nome do grupo de recursos que contém a Rede Virtual do Azure.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    ```

3. Após a criação das regras, use o seguinte para obter identificador exclusivo para este grupo de segurança de rede:

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    Esse comando retorna um valor semelhante ao texto a seguir:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Use aspas duplas em torno da identificação no comando se você não obtiver os resultados esperados.

4. Use o comando a seguir para aplicar o grupo de segurança de rede a uma sub-rede. Substitua os valores __GUID__ e __RESOURCEGROUPNAME__ por aqueles retornados na etapa anterior. Substitua __VNETNAME__ e __SUBNETNAME__ pelo nome da rede virtual e sub-rede que você deseja usar ao criar um cluster HDInsight.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Após a conclusão desse comando, é possível instalar com êxito o HDInsight na Rede Virtual protegida, na sub-rede usada nessas etapas.

> [!IMPORTANT]
> O uso das etapas anteriores abre somente o acesso ao serviço de integridade e gerenciamento do HDInsight na nuvem do Azure. Qualquer outro acesso ao cluster HDInsight de fora da Rede Virtual permanecerá bloqueado. Para habilitar o acesso de fora da rede virtual, você deve acrescentar regras de Grupo de Segurança de Rede adicionais.
>
> O exemplo a seguir demonstra como habilitar o acesso SSH por meio da Internet:
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound
> ```
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
> ```

Para saber mais sobre os Grupos de Segurança de Rede, confira [Visão geral dos Grupos de Segurança de Rede](../virtual-network/virtual-networks-nsg.md). Para saber mais sobre como controlar o roteamento em uma Rede Virtual do Azure, confira [Encaminhamento IP e rotas definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md).

## <a name="retrieve-internal-ips-and-fqdns"></a>Recuperar IPs e FQDNs internos

Ao se conectar ao HDInsight usando uma rede virtual, você pode se conectar diretamente aos nós do cluster. Use os scripts a seguir para determinar o endereço IP interno e nomes de domínio totalmente qualificado (FQDN) para os nós no cluster:

**Azure PowerShell**

```powershell
$resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

__CLI do Azure__

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

> [!IMPORTANT]
> No exemplo da CLI 2.0 do Azure, substitua `<resourcegroupname>` pelo nome do grupo de recursos que contém a rede virtual.

Os scripts funcionam consultando as NICs (placas de interface de rede) virtuais para o cluster. As NICs existem no grupo de recursos que contém a rede virtual usada pelo HDInsight.

## <a id="nextsteps"></a>Próximas etapas

Os exemplos a seguir demonstram como usar o HDInsight com a Rede Virtual do Azure:

* [Clusters HBase na rede virtual do Azure](hdinsight-hbase-provision-vnet.md)

* [Analisar dados de sensor com o Storm e o HBase no HDInsight](hdinsight-storm-sensor-data-analysis.md)

* [Provisionar clusters do Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md)

* [Usar Sqoop com o Hadoop no HDInsight](hdinsight-use-sqoop-mac-linux.md)

Para saber mais sobre redes virtuais do Azure, consulte [Visão geral da Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).


