---
title: Implantar um aplicativo de pilha dupla de IPv6 na rede virtual do Azure - CLI
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implanta um aplicativo de pilha dupla de IPv6 na rede virtual do Azure usando a CLI do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 12fbf2ae5387ac0a9350cc203f4a6f2587c8dafe
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130829"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli-preview"></a>Implantar um aplicativo de pilha dupla de IPv6 na rede virtual do Azure - CLI (versão prévia)

Este artigo mostra como implantar um aplicativo de pilha dual (IPv4 + IPv6) no Azure que inclui uma rede virtual de pilha dupla com uma sub-rede de pilha dual, um balanceador de carga com configurações de front-end dual (IPv4 + IPv6), VMs com NICs que têm uma configuração de IP dual, regras de grupo de segurança de rede de dupla e IPs públicos duplo.

> [!Important]
> Pilha dupla do IPv6 para a rede Virtual do Azure está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

Caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você decidir instalar e usar a CLI do Azure localmente em vez disso, este guia de início rápido exige que você usar a CLI do Azure versão 2.0.49 ou posterior. Execute `az --version` para localizar a versão instalada. Para informações sobre como instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Pré-requisitos
Para usar o IPv6 para o recurso de rede virtual do Azure, você deve configurar sua assinatura usando o Azure PowerShell da seguinte maneira:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
Demora até 30 minutos para a conclusão do registro de recursos. Você pode verificar o status do registro, executando o seguinte comando da CLI do Azure:

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
Após a conclusão do registro, execute o seguinte comando:

```azurelci
az provider register --namespace Microsoft.Network
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar sua rede virtual de pilha dual, você deve criar um grupo de recursos com [criar grupo de az](/cli/azure/group). O exemplo a seguir cria um grupo de recursos denominado *myRGDualStack* na *eastus* local:

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Criar IPv4 e IPv6 endereços IP públicos para o balanceador de carga
Para acessar seus pontos de extremidade IPv4 e IPv6 na Internet, você precisa de endereços IP públicos IPv4 e IPv6 para o balanceador de carga. Crie um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip). O exemplo a seguir cria o IPv4 e IPv6 endereço IP público denominado *dsPublicIP_v4* e *dsPublicIP_v6* no *myRGDualStack* grupo de recursos:

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Criar endereços IP públicos para máquinas virtuais

Para acessar remotamente as VMs na internet, você precisa de endereços IP públicos IPv4 para as VMs. Crie um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip).

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Criar o balanceador de carga básico

Nesta seção, você configurará o IP de front-end dual (IPv4 e IPv6) e o pool de endereços de back-end para o balanceador de carga e, em seguida, cria um balanceador de carga básico.

### <a name="create-load-balancer"></a>Criar um balanceador de carga

Crie o balanceador de carga básico com [criar az network lb](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) denominado **dsLB** que inclua um pool de front-end chamado **dsLbFrontEnd_v4**, um pool de back-end chamado  **dsLbBackEndPool_v4** que está associado com o endereço IP público IPv4 **dsPublicIP_v4** que você criou na etapa anterior. 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Crie front-end do IPv6

Criar um IP de front-end IPV6 com [ip de front-end de balanceamento de carga de rede de az criar](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). O exemplo a seguir cria uma configuração de IP de front-end denominada *dsLbFrontEnd_v6* e anexa o *dsPublicIP_v6* endereço:

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Configurar o pool de endereços de back-end IPv6

Criar pools com um endereço de back-end IPv6 [Criar pool de endereços de balanceamento de carga de rede de az](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). O exemplo a seguir cria o pool de endereços de back-end denominado *dsLbBackEndPool_v6* para incluir as VMs com configurações de NIC de IPv6:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra de balanceador de carga é usada para definir como o tráfego é distribuído para as VMs. Defina a configuração de IP de front-end para o tráfego de entrada e o pool de IPs de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. 

Crie uma regra de balanceador de carga com [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create). O exemplo a seguir cria regras de Balanceador de carga denominadas *dsLBrule_v4* e *dsLBrule_v6* e equilibra o tráfego *TCP* porta *80* para os IPv4 e IPv6 front-end configurações de IP:

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \ 
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Criar recursos da rede
Antes de implantar algumas VMs, você deve criar recursos de rede de suporte - conjunto de disponibilidade, o grupo de segurança de rede, a rede virtual e NICs virtuais. 
### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade
Para melhorar a disponibilidade de seu aplicativo, coloque suas VMs em um conjunto de disponibilidade.

Crie um conjunto de disponibilidade com [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). O exemplo a seguir cria uma conjunto de disponibilidade chamado *dsAVset*:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede para as regras que controlará a comunicação de entrada e saída em sua rede virtual.

#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede com [criar az network nsg](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Criar uma regra de grupo de segurança de rede para conexões de entrada e saídas

Criar uma regra de grupo de segurança de rede para permitir conexões de RDP por meio de conexão de internet 3389, da porta por meio da porta 80 e para conexões de saída com [criar regra de nsg de rede az](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

```azurecli
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie a rede virtual com [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). O exemplo a seguir cria uma rede virtual denominada *dsVNET* com sub-redes *dsSubNET_v4* e *dsSubNET_v6*:

```azurecli
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Criar NICs

Crie NICs virtuais para cada VM com [az network nic criar](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). O exemplo a seguir cria uma NIC virtual para cada VM. Cada NIC tem duas configurações de IP (configuração de IPv4 1, 1 configuração de IPv6). Criar a configuração de IPV6 com [criar configuração de az network nic ip-](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).
 
```azurecli
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie as VM com [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). O exemplo a seguir cria duas VMs e os componentes de rede virtual necessários, caso ainda não existam. 

Criar a máquina virtual *dsVM0* da seguinte maneira:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest  
```
Criar a máquina virtual *dsVM1* da seguinte maneira:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Rede virtual de pilha dupla do IPv6 de modo de exibição no portal do Azure
Você pode exibir a rede virtual de pilha dupla do IPv6 no portal do Azure da seguinte maneira:
1. Na barra de pesquisa do portal, insira *dsVnet*.
2. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-o. Isso inicia o **visão geral** página da rede virtual de pilha dupla denominada *dsVnet*. A rede virtual de pilha dupla mostra as duas NICs com configurações de IPv4 e IPv6 localizadas na sub-rede de pilha dupla denominada *dsSubnet*.

  ![Rede virtual de pilha dupla de IPv6 no Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> O IPv6 para a rede virtual do Azure está disponível no portal do Azure em somente leitura para esta versão de visualização.


## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
 az group delete --name DsRG1
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um balanceador de carga básico com uma configuração de IP de front-end dual (IPv4 e IPv6). Você também criou um duas máquinas virtuais que incluídos NICs com duas configurações de IP (IPV4 + IPv6) que foram adicionadas ao pool de back-end do balanceador de carga. Para saber mais sobre o suporte ao IPv6 em redes virtuais do Azure, consulte [What ' s IPv6 para a rede Virtual do Azure?](ipv6-overview.md)