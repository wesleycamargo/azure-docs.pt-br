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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7f63344455fafafc3a966c45742ba52d23177fa5
ms.openlocfilehash: 8c25c33535ce942ac63b9a259aa9e61765129d0a


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Estender os recursos do HDInsight usando a Rede Virtual do Azure
A Rede Virtual do Azure permite que você estenda suas soluções Hadoop para incorporar recursos locais como SQL Server, combinar vários tipos de cluster HDInsight ou criar redes privadas seguras entre recursos na nuvem.

## <a name="prerequisites"></a>Pré-requisitos

* CLI do Azure 2.0 (Visualização): veja [Instalar e configurar a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) para obter mais informações.

* Azure PowerShell: veja [Instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para obter mais informações.

> [!NOTE]
> As etapas neste documento requerem a versão mais recente da CLI do Azure e do Azure PowerShell. Se você estiver usando uma versão mais antiga, os comandos poderão ser diferentes. Para obter melhores resultados, use os links anteriores para instalar as versões mais recentes.

## <a name="a-idwhatisawhat-is-azure-virtual-network"></a><a id="whatis"></a>O que é a Rede Virtual do Azure?

[Rede Virtual do Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permite que você crie uma rede segura e persistente contendo os recursos necessários para sua solução. Uma rede virtual permite que você:

* Conecte recursos da nuvem juntos em uma rede privada (somente nuvem).
  
    ![diagrama da configuração somente nuvem](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)
  
    Usar a Rede Virtual para vincular os serviços do Azure ao Azure HDInsight habilita os seguintes cenários:
  
    * **Invocar serviços ou trabalhos do HDInsight** de sites do Azure ou de serviços em execução em máquinas virtuais do Azure.
    * **Transferência direta de dados** entre o HDInsight e o Banco de Dados SQL do Azure, o SQL Server ou outra solução de armazenamento de dados em execução em uma máquina virtual.
    * **Combinar vários servidores HDInsight** em uma única solução. Clusters HDInsight são fornecidos em uma variedade de tipos que correspondem à carga de trabalho ou à tecnologia para a qual o cluster está ajustado. Não há nenhum método com suporte para criar um cluster que combina vários tipos, como o Storm e HBase em um cluster. O uso de uma rede virtual permite que vários clusters comuniquem-se diretamente entre si.

* Conecte seus recursos de nuvem à sua rede de datacenter local (site a site ou ponto a site), usando uma VPN (rede privada virtual).
  
    A configuração site a site permite conectar vários recursos do datacenter à rede virtual do Azure usando uma VPN de hardware ou o serviço de Roteamento e Acesso Remoto.
  
    ![diagrama da configuração site a site](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)
  
    A configuração ponto a site permite conectar um recurso específico à rede virtual do Azure usando VPN de software.
  
    ![diagrama da configuração ponto a site](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)
  
    Usar a Rede Virtual para vincular a nuvem e seu datacenter habilita cenários semelhantes à configuração somente nuvem. Porém, você não fica limitado a trabalhar com recursos na nuvem – também pode trabalhar com os recursos em seu datacenter.
  
    * **Transferência direta de dados** entre o HDInsight e seu datacenter. Um exemplo é usar o Sqoop para transferir dados para ou do SQL Server, ou a leitura de dados gerados por um aplicativo LOB (linha de negócios).
    * **Invocar serviços ou trabalhos do HDInsight** de um aplicativo LOB. Um exemplo é usar APIs do HBase Java para armazenar e recuperar dados de um cluster HDInsight HBase.

Para obter mais informações sobre os recursos, benefícios e capacidades das redes virtuais, consulte a [Visão geral da rede virtual do Azure](../virtual-network/virtual-networks-overview.md).

> [!NOTE]
> Você deve criar a Rede Virtual do Azure antes de provisionar um cluster HDInsight. Para obter mais informações, consulte [Tarefas de configuração de rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="virtual-network-requirements"></a>Requisitos de Rede Virtual

> [!IMPORTANT]
> Criar um cluster HDInsight em uma Rede Virtual exige configurações de Rede Virtual específicas, que são descritas nesta seção.

### <a name="location-based-virtual-networks"></a>Redes virtuais baseadas em local

O Azure HDInsight dá suporte apenas a redes virtuais baseadas em local e atualmente não funciona com redes virtuais baseadas em grupo de afinidade.

### <a name="classic-or-v2-virtual-network"></a>Rede virtual clássica ou v2

Os clusters baseados em Windows exigem uma rede virtual clássica, enquanto os clusters baseados em Linux exigem uma rede virtual do Azure Resource Manager. Se você não tiver o tipo correto de rede, ele não poderá ser usado durante a criação do cluster.

Se você tiver recursos em uma Rede Virtual que não pode ser usada pelo cluster que você planejar criar, é possível criar uma nova Rede Virtual que pode ser usada pelo cluster e conectá-la à Rede Virtual incompatível. Em seguida, você pode criar o cluster na versão de rede exigida, e ele poderá acessar os recursos na outra rede, pois as duas foram unidas. Para obter mais informações sobre como conectar Redes Virtuais clássicas e novas, veja [Conectando Redes Virtuais clássicas a Redes Virtuais novas](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

### <a name="custom-dns"></a>DNS personalizado

Ao criar uma rede virtual, o Azure fornece resolução de nomes padrão para os serviços do Azure, como o HDInsight que é instalado na rede. No entanto, você pode ter que usar seu próprio DNS (sistema de nomes de domínio) para situações como resolução de nomes de domínio entre redes. Por exemplo, na comunicação entre serviços localizados em duas redes virtuais unidas. O HDInsight dá suporte tanto à resolução de nomes do Azure padrão como ao DNS personalizado quando usado com a rede virtual do Azure.

Para saber mais sobre como usar seu próprio servidor DNS com a Rede Virtual do Azure, confira a seção **Resolução de nomes usando o seu próprio servidor DNS** no documento [Resolução de nomes de máquinas virtuais e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) .

### <a name="secured-virtual-networks"></a>Redes virtuais protegidas

O serviço do HDInsight é um serviço gerenciado e requer acesso à Internet durante o provisionamento e a execução. Isso é necessário para que o Azure possa monitorar a integridade do cluster, iniciar o failover dos recursos de cluster, alterar o número de nós no cluster por meio de operações de escala, bem como outras tarefas de gerenciamento.

Se você precisar instalar o HDInsight em uma Rede Virtual protegida, será necessário permitir o acesso de entrada pela porta 443 para os seguintes endereços IP, que permitem ao Azure gerenciar o cluster HDInsight.

> [!IMPORTANT]
> Os endereços IP que devem ser permitidos são específicos à região em que o cluster HDInsight e a Rede Virtual residem. Use os valores a seguir para encontrar os endereços IP para a região que você está usando.

Região __Leste do Canadá__:

* 52.229.127.96
* 52.229.123.172

Região __Canadá Central__:

* 52.228.37.66
* 52.228.45.222

Região __Centro-Oeste dos EUA__:

* 52.161.23.15
* 52.161.10.167

Região __Oeste dos EUA 2__:

* 52.175.211.210
* 52.175.222.222

__Todas as outras regiões__:

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

Permitir o acesso de entrada desses endereços por meio da porta 443 permitirá a instalação bem-sucedida do HDInsight em uma rede virtual protegida.

> [!IMPORTANT]
> O HDInsight não permite a restrição do tráfego de saída, somente o tráfego de entrada. Ao definir regras do Grupo de Segurança de Rede para a sub-rede que contém o HDInsight, use somente regras de entrada.

Os exemplos a seguir demonstram como criar um novo Grupo de Segurança de Rede que permite os endereços necessários e que aplica o grupo de segurança a uma sub-rede em sua Rede Virtual. Os endereços usados neste exemplo são da lista __Todas as outras regiões__ acima. Se você estiver em uma das regiões especificamente listadas, como __Centro-Oeste dos EUA__, modifique o script para usar os endereços IP da sua região.

Essas etapas pressupõem que você já tenha criado uma Rede Virtual e a sub-rede na qual deseja instalar o HDInsight.

> [!IMPORTANT]
> Observe o valor `priority` usado nesses exemplos. As regras são testadas no tráfego de rede na ordem de prioridade. Depois que uma regra corresponde aos critérios de teste e é aplicada, nenhuma outra regra é testada.
> 
> Se você tiver regras personalizadas que bloqueiam amplamente tráfego de entrada (como uma regra **negar tudo**), talvez seja necessário ajustar os valores de prioridade nesses exemplos ou suas regras personalizadas para que as regras nos exemplos ocorram antes das regras que bloqueiam o acesso. Caso contrário, a regra **negar tudo** será testada primeiro e as regras neste exemplo nunca serão aplicadas. Você também deve tomar cuidado para não bloquear as regras padrão para uma Rede Virtual do Azure. Por exemplo, você não deve criar uma regra **negar tudo** que é aplicada antes da regra **ALLOW VNET INBOUND** padrão (que tem uma prioridade 65000.)
> 
> Para obter mais informações sobre como as regras são aplicadas e as regras de entrada e saída padrão, consulte [O que é Grupo de Segurança da Rede?](../virtual-network/virtual-networks-nsg.md).

**Usando o PowerShell do Azure**

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
    # Create a new Network Security Group.
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

**Usando a CLI do Azure**

1. Use o seguinte comando para criar um novo grupo de segurança de rede chamado `hdisecure`. Substitua **RESOURCEGROUPNAME** e **LOCATION** pelo grupo de recursos que contém a Rede Virtual do Azure e pelo local (região) em que o grupo foi criado.
   
        az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION

    Após a criação do grupo, você receberá informações sobre o novo grupo.

2. Use o seguinte para adicionar regras ao novo grupo de segurança de rede que permitem a comunicação de entrada na porta 443 por meio do serviço de integridade e gerenciamento do Azure HDInsight. Substitua **RESOURCEGROUPNAME** pelo nome do grupo de recursos que contém a Rede Virtual do Azure.
    
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"

3. Após a criação das regras, use o seguinte para obter identificador exclusivo para este grupo de segurança de rede:

        az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'

    Isso retorna um valor semelhante ao texto a seguir:

        "/subscriptions/55b1016c-0f27-43d2-b908-b8c373d6d52e/resourceGroups/mygroup/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

4. Use o comando a seguir para aplicar o grupo de segurança de rede a uma sub-rede. Substitua os valores __GUID__ e __RESOURCEGROUPNAME__ por aqueles retornados na etapa anterior. Substitua __VNETNAME__ e __SUBNETNAME__ pelo nome da rede virtual e sub-rede que você deseja usar ao criar um cluster HDInsight.
   
        az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
   
    Após a conclusão desse comando, é possível instalar com êxito o HDInsight na Rede Virtual protegida, na sub-rede usada nessas etapas.

> [!IMPORTANT]
> Usar as etapas acima abre o acesso somente ao serviço de integridade e gerenciamento do HDInsight na nuvem do Azure. Isso permite uma instalação bem-sucedida de um cluster HDInsight na sub-rede; no entanto, o acesso ao cluster HDInsight de fora da Rede Virtual é bloqueado por padrão. Você terá de adicionar mais regras do Grupo de Segurança de Rede se desejar habilitar o acesso de fora da Rede Virtual.
> 
> Por exemplo, para permitir o acesso SSH da Internet, você precisará adicionar uma regra semelhante à seguinte: 
> 
> * Azure PowerShell - ```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * CLI do Azure - ```az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"```

Para saber mais sobre os Grupos de Segurança de Rede, confira [Visão geral dos Grupos de Segurança de Rede](../virtual-network/virtual-networks-nsg.md). Para saber mais sobre como controlar o roteamento em uma Rede Virtual do Azure, confira [Rotas e encaminhamento IP definidos pelo usuário](../virtual-network/virtual-networks-udr-overview.md).

## <a name="a-idtasksatasks-and-information"></a><a id="tasks"></a>Tarefas e informações

Esta seção contém informações sobre tarefas comuns e informações que podem ser necessárias ao usar o HDInsight com uma rede virtual.

### <a name="determine-the-fqdn"></a>Determinar o FQDN

Um FQDN (nome de domínio totalmente qualificado) será atribuído ao cluster HDInsight para a interface da Rede Virtual. Esse é o endereço que você deve usar ao se conectar ao cluster por meio de outros recursos na rede virtual. Para determinar o FQDN, use a seguinte URL para consultar o serviço de gerenciamento Ambari:

    https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [!NOTE]
> Para obter mais informações sobre como usar o Ambari com o HDInsight, consulte [Monitorar clusters Hadoop no HDInsight usando a API do Ambari](hdinsight-monitor-use-ambari-api.md).

Você deve especificar o nome do cluster e um serviço e componente em execução no cluster, como o gerenciador de recursos YARN.

> [!NOTE]
> Os dados retornados são um documento JSON (JavaScript Object Notation) que contém muitas informações sobre o componente. Para extrair apenas o FQDN, você deve usar um analisador JSON para recuperar o valor `host_components[0].HostRoles.host_name` .

Por exemplo, para retornar o FQDN de um cluster Hadoop no HDInsight, você pode usar um dos métodos a seguir para recuperar os dados para o gerenciador de recursos YARN:

* [PowerShell do Azure](/powershell/azureps-cmdlets-docs)
  
        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix
  
        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
  
        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/        components/resourcemanager"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        $FQDN = $JsonObject.host_components[0].HostRoles.host_name
        Write-host $FQDN

* [cURL](http://curl.haxx.se/) e [jq](http://stedolan.github.io/jq/)
  
        curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

### <a name="connecting-to-hbase"></a>Conectando-se ao HBase

Para se conectar ao HBase remotamente usando a API do Java, você deve determinar os endereços de quorum ZooKeeper para o cluster HBase e especificá-los no seu aplicativo.

Para obter o endereço de quorum ZooKeeper, use um dos seguintes métodos para consultar o serviço de gerenciamento Ambari:

* [PowerShell do Azure](/powershell/azureps-cmdlets-docs)
  
        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix
  
        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
  
        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [cURL](http://curl.haxx.se/) e [jq](http://stedolan.github.io/jq/)
  
        curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [!NOTE]
> Para obter mais informações sobre como usar o Ambari com o HDInsight, consulte [Monitorar clusters Hadoop no HDInsight usando a API do Ambari](hdinsight-monitor-use-ambari-api.md).

Uma vez que as informações estejam reunidas, use-as em seu aplicativo cliente.

Por exemplo, para um aplicativo Java que usa a API do HBase, você adicionaria um arquivo **hbase-site.htm** ao projeto e especificaria as informações de quorum no arquivo da seguinte maneira:

```xml
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

### <a name="verify-network-connectivity"></a>Verificar a conectividade de rede

Alguns serviços, como o SQL Server, podem limitar conexões de rede. Isso impedirá que HDInsight trabalhe com êxito com esses serviços.

Se você encontrar problemas ao acessar um serviço do HDInsight, consulte a documentação para o serviço para garantir que você habilitou o acesso à rede. Você também pode verificar o acesso à rede criando uma máquina virtual do Azure na mesma rede virtual e usar utilitários de cliente para verificar se a máquina virtual consegue se conectar ao serviço através da rede virtual.

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Próximas etapas

Os exemplos a seguir demonstram como usar o HDInsight com a Rede Virtual do Azure:

* [Analisar dados de sensor com o Storm e o HBase no HDInsight](hdinsight-storm-sensor-data-analysis.md) : demonstra como configurar um cluster Storm e HBase em uma rede virtual e como gravar dados remotamente no HBase pelo Storm.
* [Provisionar clusters Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md) : fornece informações sobre como provisionar clusters Hadoop, inclusive informações sobre como usar a Rede Virtual do Azure.
* [Usar o Sqoop com o Hadoop no HDInsight](hdinsight-use-sqoop-mac-linux.md) : fornece informações sobre como usar o Sqoop para transferir dados com o SQL Server em uma rede virtual.

Para saber mais sobre redes virtuais do Azure, consulte [Visão geral da Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).




<!--HONumber=Jan17_HO2-->


