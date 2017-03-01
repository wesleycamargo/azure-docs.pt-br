---
title: "Vários endereços IP para máquinas virtuais do Azure – CLI do Azure | Microsoft Docs"
description: "Saiba como atribuir diversos endereços IP a uma máquina virtual usando a CLI do Azure | Resource Manager."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 7e99731f6826e563109da734a80fcccfff85676a
ms.openlocfilehash: 3deb0e1668aae15a7d9abe00791f4524ed49d77b
ms.lasthandoff: 02/21/2017


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli"></a>Atribuir vários endereços IP a máquinas virtuais usando a CLI do Azure

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Este artigo explica como criar uma máquina virtual (VM) por meio do Modelo de implantação do Azure Resource Manager usando a CLI do Azure. Múltiplos endereços IP não podem ser atribuídos a recursos criados por meio do modelo de implantação clássico. Para saber mais sobre modelos de implantação do Azure, leia o artigo [Compreender os modelos de implantação](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Criar uma VM com vários endereços IP

As etapas a seguir explicam como criar uma VM de exemplo com vários endereços IP, como descrito no cenário. Altere os nomes da variável e os tipos de endereço IP como exigido por sua implementação.

1. Instale e configure a CLI do Azure seguindo as etapas do artigo [Instalar e configurar a CLI do Azure](../xplat-cli-install.md) e faça logon em sua conta do Azure.

2. Registre-se para obter a prévia executando os seguintes comandos do PowerShell após o logon e selecione a assinatura apropriada:
    ```
    Register-AzureRmProviderFeature -FeatureName AllowMultipleIpConfigurationsPerNic -ProviderNamespace Microsoft.Network

    Register-AzureRmProviderFeature -FeatureName AllowLoadBalancingonSecondaryIpconfigs -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
    Não tente concluir as etapas restantes até ver a saída a seguir ao executar o comando ```Get-AzureRmProviderFeature```:
        
    ```powershell
    FeatureName                            ProviderName      RegistrationState
    -----------                            ------------      -----------------      
    AllowLoadBalancingOnSecondaryIpConfigs Microsoft.Network Registered       
    AllowMultipleIpConfigurationsPerNic    Microsoft.Network Registered       
    ```
        
    >[!NOTE] 
    >Isso pode levar alguns minutos.

3. [Crie um grupo de recursos](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations) seguido por uma [rede virtual e uma sub-rede](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet). Altere os campos ``` --address-prefixes ``` e ```--address-prefix``` para o seguinte para seguir o cenário exato descrito neste artigo:

    ```azurecli
    --address-prefixes 10.0.0.0/16
    --address-prefix 10.0.0.0/24
    ```
    >[!NOTE] 
    >O artigo mencionado acima usa Europa Ocidental como o local para criar recursos, mas este artigo usa Centro-Oeste dos EUA. Faça as alterações de localização de forma adequada.

4. [Crie uma nova conta de armazenamento](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account) para sua VM.

5. Crie a NIC e as configurações do IP que você deseja atribuir à NIC. Você pode adicionar, remover ou alterar as configurações conforme necessário. As configurações a seguir são descritas no cenário:

    **IPConfig-1**

    Insira os comandos a seguir para criar:

    - Um recurso de endereço IP público com um endereço IP público estático
    - Uma configuração de IP com o recurso de endereço IP público e um endereço IP privado dinâmico

    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP --domain-name-label mypublicdns --allocation-method Static
    ```
    > [!NOTE]
    > Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).

    ```azurecli
    azure network nic create --resource-group myResourceGroup --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 --public-ip-name myPublicIP
    ```

    **IPConfig-2**

     Insira os seguintes comandos para criar um novo recurso de endereço IP público e uma nova configuração de IP com um endereço IP público estático e um endereço IP privado estático:
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns2 --allocation-method Static

    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-2 --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
    ```

    **IPConfig-3**

    Insira os comandos a seguir para criar uma configuração de IP com um endereço IP privado dinâmico e nenhum endereço IP público:

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3
    ```

    >[!NOTE] 
    >Embora este artigo atribua todas as configurações de IP a uma única NIC, você também pode atribuir várias configurações de IP para qualquer NIC em uma VM. Para saber como criar uma VM com várias NICs, leia o artigo Criar uma VM com várias NICs.

6. Artigo [Criar uma VM Linux](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms). Remova a propriedade ```  --availset-name myAvailabilitySet \ ``` porque ela não é necessária neste cenário. Use a localização apropriada com base em seu cenário. 

    >[!WARNING] 
    > A Etapa 6 do artigo Criar uma VM falhará se o tamanho da VM não tiver suporte na localização selecionada. Execute o comando a seguir para obter uma lista completa de VMs no Centro-Oeste dos EUA, por exemplo: `azure vm sizes --location westcentralus`. Esse nome de local pode ser alterado dependendo do seu cenário.

    Para alterar o tamanho da VM para Standard DS2 v2, por exemplo, basta adicionar a seguinte propriedade ```  --vm-size Standard_DS3_v2``` ao comando ``` azure vm create ``` na etapa 6.

7. Insira o comando a seguir para exibir a NIC e as configurações de IP associadas:

    ```azurecli
    azure network nic show --resource-group myResourceGroup --name myNic1
    ```
8. Adicione os endereços IP privados ao sistema operacional da VM executando as etapas para seu sistema operacional na seção [Adicionar endereços IP ao sistema operacional de uma VM](#os-config) deste artigo.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>Adicionar endereços IP a uma VM

Você pode adicionar endereços IP públicos e privados adicionais para um NIC existente ao concluir as etapas a seguir. Os exemplos baseiam-se no [cenário](#Scenario) descrito neste artigo.

1. Abra a CLI do Azure e conclua as etapas restantes nesta seção dentro de uma única sessão da CLI. Se você ainda não tiver a CLI do Azure instalada e configurada, conclua as etapas do artigo [Instalar e configurar a CLI do Azure](../xplat-cli-install.md) e faça logon em sua conta do Azure.

2. Registre-se para a visualização pública seguindo a etapa 2 na seção **Criar uma VM com vários endereços IP**.

3. Conclua as etapas em uma das seções a seguir, com base em seus requisitos:

    **Adicionar um endereço IP privado**
    
    Para adicionar um endereço IP privado a uma NIC, você deve criar uma configuração de IP usando o comando a seguir.  Se você deseja adicionar um endereço IP privado dinâmico, remova ```-PrivateIpAddress 10.0.0.7``` antes de inserir o comando. Ao especificar um endereço IP estático, ele deve ser um endereço não usado para a sub-rede.

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --private-ip-address 10.0.0.7 --name IPConfig-4
    ```
    Crie quantas configurações forem necessárias, usando nomes de configuração exclusivos e endereços IP privados (para configurações com endereços IP estáticos).

    **Adicionar um endereço IP público**
    
    Um endereço IP público é adicionado por meio de sua associação a uma nova configuração de IP ou de uma configuração de IP existente. Conclua as etapas em uma das seções a seguir, quando você precisar.

    > [!NOTE]
    > Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).
    >

    **Associar o recurso a uma nova configuração de IP**
    
    Sempre que você adicionar um endereço IP público em uma nova configuração de IP, também deverá adicionar um endereço IP privado, pois todas as configurações de IP devem ter um endereço IP privado. Você pode adicionar um recurso de endereço IP público existente ou criar um novo. Para criar um novo, insira o seguinte comando:
    
    ```azurecli
      azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3
    ```

     Para criar uma nova configuração de IP com um endereço IP privado dinâmico e o recurso de endereço IP público *myPublicIP3* associado, insira o seguinte comando:

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 --public-ip-name myPublicIP3
    ```

    **Associar o recurso a uma configuração de IP existente**
    Um recurso de endereço IP público só pode ser associado a uma configuração de IP que ainda não tenha um associado. Você pode determinar se uma configuração de IP tem um endereço IP público associado inserindo o seguinte comando:

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

    Procure uma linha semelhante à seguinte na saída retornada:
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet
     
    Como a coluna **IP Público** para *IpConfig&3;* está em branco, nenhum recurso de endereço IP público está associado a ele no momento. Você pode adicionar um recurso de endereço IP público existente como IpConfig-3 ou inserir o seguinte comando para criar um:

    ```azurecli
    azure network public-ip create --resource-group  myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
    ```
    
    Insira o comando a seguir para associar o recurso de endereço IP público à configuração de IP existente chamada *IPConfig-3*:
    
    ```azurecli
    azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 --public-ip-name myPublicIP3
    ```

7. Exiba os endereços IP privados e o recurso de endereço IP público atribuído à NIC, digitando o seguinte comando:

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```
    Você deverá ver uma saída semelhante ao seguinte: 
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet  myPublicIP3
     
9. Adicione os endereços IP privados que você adicionou à NIC para o sistema operacional da VM seguindo as instruções da seção [Adicionar endereços IP a um sistema operacional de VM](#os-config) deste artigo. Não adicione os endereços IP públicos ao sistema operacional.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

