---
title: "Conectar-se ao Kafka no HDInsight usando redes virtuais – Azure | Microsoft Docs"
description: "Saiba como se conectar remotamente ao Kafka no HDInsight usando o cliente kafka-python. A configuração neste documento usa HDInsight em uma Rede Virtual do Azure. O cliente remoto conecta-se à rede virtual por meio de um gateway de VPN ponto a site."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.custom: hdinsightactive
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/18/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 9ddf19c008c35525419a357436b1a969a4b19205
ms.lasthandoff: 04/27/2017


---

# <a name="connect-to-kafka-on-hdinsight-preview-through-an-azure-virtual-network"></a>Conectar-se ao Kafka no HDInsight (preview) por meio de uma Rede Virtual do Azure

Saiba como se conectar ao Kafka no HDInsight usando Redes Virtuais do Azure. Os clientes do Kafka (produtores e consumidores) podem executar diretamente no HDInsight ou em sistemas remotos. Os clientes remotos devem se conectar ao Kafka no HDInsight por meio de uma Rede Virtual do Azure. Use as informações neste documento para entender como os clientes remotos podem se conectar ao HDInsight usando Redes Virtuais do Azure.

> [!IMPORTANT]
> Várias das configurações abordadas neste documento podem ser usadas com clientes Windows, macOS ou Linux. No entanto, o exemplo ponto a site incluído fornece apenas um cliente VPN para Windows.
>
> O exemplo também usa um cliente Python ([kafka-python](http://kafka-python.readthedocs.io/en/master/)) para verificar a comunicação com o Kafka no HDInsight.

## <a name="architecture-and-planning"></a>Arquitetura e planejamento

Os clusters HDInsight são protegidos dentro de uma Rede Virtual do Azure e permitem apenas tráfego de entrada SSH e HTTPS. O tráfego chega por meio de um gateway público, que não roteia o tráfego de clientes do Kafka. Para acessar o Kafka de um cliente remoto, você deve criar uma Rede Virtual do Azure que forneça um gateway de VPN (rede virtual privada). Depois que você tiver configurado a rede virtual e o gateway, instale o HDInsight na rede virtual e conecte-se a ele usando o gateway de VPN.

![Um diagrama do HDInsight em uma Rede Virtual do Azure com um cliente conectados por VPN](media/hdinsight-apache-kafka-connect-vpn-gateway/hdinsight-in-virtual-network.png)

A lista a seguir contém informações sobre o processo de usar o Kafka no HDInsight com uma rede virtual:

1. Crie uma rede virtual. Para obter informações específicas sobre como usar o HDInsight com Redes Virtuais do Azure, veja o documento [Estender o HDInsight usando a Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md).

2. (Opcional) Crie uma Máquina Virtual do Azure na rede virtual e instale um servidor DNS personalizado nela. Esse servidor DNS é usado para permitir a resolução de nomes para clientes remotos em uma configuração site a site ou vnet a vnet. Para saber mais, confira o documento [Resolução de nomes para VMs e serviços de nuvem](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

3. Crie um Gateway de VPN para a rede virtual. Para saber mais sobre configurações de gateway de VPN, confira o documento [Sobre o gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

4. Crie o HDInsight na rede virtual. Se você tiver configurado um servidor DNS personalizado para a rede, o HDInsight será automaticamente configurado para usá-lo.

5. (Opcional) Se você não tiver usado um servidor DNS personalizado e não tem a resolução de nomes entre clientes e a rede virtual, será preciso configurar o Kafka para anúncio de IP. Para saber mais, veja a seção [Configurar Kafka para anúncio de IP](#configure-kafka-for-ip-advertising) neste documento.

## <a name="create-using-powershell"></a>Criar usando o PowerShell

As etapas desta seção criam a seguinte configuração usando o [Azure PowerShell](/powershell/azure/overview):

* Rede Virtual do Azure
* Gateway de VPN ponto a site
* Conta de Armazenamento do Azure (usada pelo HDInsight)
* Kafka no HDInsight

1. Siga as etapas do documento [Trabalhando com certificados autoassinados para conexões ponto a site](../vpn-gateway/vpn-gateway-certificates-point-to-site.md) de modo a criar os certificados necessários para o gateway.

2. Abra um prompt do PowerShell e use o seguinte código para fazer logon em sua assinatura do Azure:

    ```powershell
    Add-AzureRmAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzureRmSubscription -SubscriptionName "name of your subscription"
    ```

3. Use o seguinte código para criar variáveis que contenham informações de configuração:

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.5"
    $hdiType = "Kafka"
    ```

4. Use o seguinte código para criar o grupo de recursos do Azure e a rede virtual:

    ```powershell
    # Create the resource group that contains everything
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzureRmPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzureRmVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzureRmVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]
    > Esse processo pode demorar para ser concluído.

5. Use o seguinte código para criar a Conta de Armazenamento do Azure e o contêiner de blob:

    ```powershell
    # Create the storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -Type Standard_GRS `
        -Location $location

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzureStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Use o seguinte código para criar o cluster HDInsight:

    ```powershell
    # Create the HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

  > [!WARNING]
  > Esse processo leva cerca de 20 minutos para ser concluído.

8. Use o seguinte cmdlet para recuperar a URL do cliente VPN do Windows para a rede virtual:

    ```powershell
    Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName `
        -VirtualNetworkGatewayName $vpnName `
        -ProcessorArchitecture Amd64
    ```

    Para baixar o cliente VPN do Windows, use o URI retornado no navegador da Web.

## <a name="configure-kafka-for-ip-advertising"></a>Configurar Kafka para anúncio de IP

Por padrão, o Zookeeper retorna o nome de domínio dos agentes do Kafka aos clientes. Essa configuração não funciona para o cliente VPN, pois não é possível usar a resolução de nomes para entidades na rede virtual. Use as seguintes etapas para configurar o Kafka no HDInsight a fim de anunciar endereços IP no lugar de nomes de domínio:

1. Usando um navegador da Web, acesse https://NOMEDOCLUSTER.azurehdinsight.net. Substitua __NOMEDOCLUSTER__ pelo nome do Kafka no cluster HDInsight.

    Quando solicitado, use o nome de usuário e a senha HTTPS para o cluster. A Interface de Usuário Ambari Web para o cluster é exibida.

2. Para exibir informações sobre o Kafka, selecione __Kafka__ na lista à esquerda. 

    ![Lista de serviços com Kafka realçado](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Para exibir a configuração do Kafka, selecione __Configurações__ na parte central superior.

    ![Links de Configurações para Kafka](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. Para localizar a configuração __kafka-env__, digite `kafka-env` no campo __Filtro__ na parte superior direita.

    ![Configuração do Kafka, para kafka-env](./media/hdinsight-apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Para configurar o Kafka para anunciar endereços IP, adicione o seguinte texto à parte inferior do campo __kafka-env-template__:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Para configurar a interface na qual o Kafka escuta, digite `listeners` no campo __Filtro__ na parte superior direita.

7. Para configurar o Kafka para escutar em todas as interfaces de rede, altere o valor no campo __ouvintes__ para `PLAINTEXT://0.0.0.0:92092`.

8. Para salvar as alterações de configuração, use o botão __Salvar__. Digite uma mensagem de texto que descreva as alterações. Selecione __OK__ assim que as alterações tiverem sido salvas.

    ![Botão Salvar configuração](./media/hdinsight-apache-kafka-connect-vpn-gateway/save-button.png)

9. Para evitar erros ao reiniciar o Kafka, use o botão __Ações de Serviço__ e selecione __Ativar o Modo de Manutenção__. Selecione OK para concluir essa operação.

    ![Ações de serviço, com ativar manutenção realçada](./media/hdinsight-apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Para reiniciar o Kafka, use o botão __Reiniciar__ e selecione __Reiniciar Todos os Afetados__. Confirme a reinicialização e, em seguida, use o botão __OK__ depois que a operação for concluída.

    ![Botão Reiniciar com reiniciar todos os afetados realçada](./media/hdinsight-apache-kafka-connect-vpn-gateway/restart-button.png)

11. Para desabilitar o modo de manutenção, use o botão __Ações de Serviço__ e selecione __Ativar o Modo de Manutenção__. Selecione **OK** para concluir essa operação.

## <a name="connect-to-the-vpn-gateway"></a>Conectar-se ao gateway de VPN

Para se conectar ao gateway de VPN usando um __cliente Windows__, use a seção __Conectar ao Azure__ do documento [Configurar uma conexão Ponto a Site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#a-nameconnectapart-7---connect-to-azure).

## <a name="remote-kafka-client"></a>Cliente remoto do Kafka

Para se conectar ao Kafka do computador cliente, você deve usar o endereço IP dos agentes do Kafka ou nós do Zookeeper (o que o cliente exigir). Use as etapas a seguir para recuperar o endereço IP dos agentes do Kafka e use-os de um aplicativo Python

1. Use o seguinte script para recuperar os endereços IP dos nós no cluster:

    ```powershell
    # Get the NICs for the HDInsight workernodes (names contain 'workernode').
    $nodes = Get-AzureRmNetworkInterface `
        -ResourceGroupName $resourceGroupName `
        | where-object {$_.Name -like "*workernode*"}

    # Loop through each node and get the IP address
    foreach($node in $nodes) {
        $node.IpConfigurations.PrivateIpAddress
    }
    ```

    Esse script supõe que `$resourceGroupName` seja o nome do grupo de recursos do Azure que contém a rede virtual. A saída do script é semelhante ao seguinte texto:

        10.0.0.12
        10.0.0.6
        10.0.0.13
        10.0.0.5

    > [!NOTE]
    > Se seu cliente do Kafka usar nós do Zookeeper em vez de agentes do Kafka, substitua `*workernode*` por `*zookeepernode*` no script do PowerShell.

    > [!WARNING]
    > Se você dimensionar o cluster, ou os nós falharem e forem substituídos, os endereços IP podem mudar. Atualmente, não há maneira de pré-atribuir endereços IP específicos para nós em um cluster HDInsight.

2. Use o seguinte para instalar o cliente [kafka-python](http://kafka-python.readthedocs.io/):

        pip install kafka-python

3. Para enviar dados ao Kafka, use o seguinte código Python:

  ```python
  from kafka import KafkaProducer
  # Replace the `ip_address` entries with the IP address of your worker nodes
  producer = KafkaProducer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'])
  for _ in range(50):
      producer.send('testtopic', b'test message')
  ```

    Substitua as entradas `'ip_address'` pelos endereços retornados da etapa 1 nesta seção.
    
    > [!NOTE]
    > Esse código envia a cadeia de caracteres `test message` para o tópico `testtopic`. A configuração padrão do Kafka no HDInsight é criar o tópico se ele não existir.

4. Para recuperar as mensagens do Kafka, use o seguinte código Python:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Substitua as entradas `'ip_address'` pelos endereços retornados da etapa 1 nesta seção. A saída contém a mensagem de teste enviada ao produtor na etapa anterior.

## <a name="troubleshooting"></a>Solucionar problemas

Se você tiver problemas para se conectar à rede virtual ou ao HDInsight por meio da rede, confira o documento [Como solucionar problemas de conexões e gateway de rede virtual](../network-watcher/network-watcher-troubleshoot-manage-powershell.md) para obter orientação.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como criar uma Rede Virtual do Azure com o gateway de VPN Ponto a Site, confira os seguintes documentos:

* [Configurar uma conexão Ponto a Site usando o portal do Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Configurar uma conexão Ponto a Site usando o Azure PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Para saber mais sobre como trabalhar com o Kafka no HDInsight, veja os seguintes documentos:

* [Introdução ao Kafka no HDInsight](hdinsight-apache-kafka-get-started.md)
* [Usar o espelhamento com o Kafka no HDInsight](hdinsight-apache-kafka-mirroring.md)

