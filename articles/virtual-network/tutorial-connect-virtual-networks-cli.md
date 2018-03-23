---
title: Conectar redes virtuais com o emparelhamento de rede virtual – CLI do Azure | Microsoft Docs
description: Saiba como conectar redes virtuais com o emparelhamento de rede virtual.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: df56f2e3e13f80e7ce2c2b6c9cffeac3d03776e5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Conectar redes virtuais com o emparelhamento de rede virtual usando a CLI do Azure

Você pode conectar redes virtuais entre si com o emparelhamento de rede virtual. Depois que as redes virtuais são emparelhadas, os recursos de ambas as redes virtuais podem se comunicar entre si, com a mesma latência e largura de banda como se os recursos estivessem na mesma rede virtual. Este artigo aborda a criação e o emparelhamento de duas redes virtuais. Você aprenderá como:

> [!div class="checklist"]
> * Criar duas redes virtuais
> * Criar um emparelhamento entre redes virtuais
> * Testar o emparelhamento

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.4 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Criar redes virtuais

Antes de criar uma rede virtual, você precisa criar um grupo de recursos para a rede virtual e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). O exemplo a seguir cria uma rede virtual chamada *myVirtualNetwork1* com o prefixo de endereço *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Crie uma rede virtual chamada *myVirtualNetwork2* com o prefixo de endereço *10.1.0.0/16*. O prefixo de endereço não se sobrepõe ao prefixo de endereço da rede virtual *myVirtualNetwork1*. Não é possível emparelhar redes virtuais com prefixos de endereço sobrepostos.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Emparelhar redes virtuais

Os emparelhamentos são estabelecidos entre IDs de rede virtual e, portanto, você precisa primeiro obter a ID de cada rede virtual com o comando [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show) e armazenar a ID em uma variável.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Crie um emparelhamento de *myVirtualNetwork1* para *myVirtualNetwork2* com [az network vnet peering create](/cli/azure/network/vnet/peering#az_network_vnet_peering_create). Se o parâmetro `--allow-vnet-access` não for especificado, um emparelhamento será estabelecido, mas nenhuma comunicação poderá fluir nele.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet-id $vNet2Id \
  --allow-vnet-access
```

Na saída retornada após a execução do comando anterior, você vê que o **peeringState** é *Iniciado*. O emparelhamento permanece no estado *Iniciado* até que você crie o emparelhamento de *myVirtualNetwork2* para *myVirtualNetwork1*. Crie um emparelhamento de *myVirtualNetwork2* para *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet-id $vNet1Id \
  --allow-vnet-access
```

Na saída retornada após a execução do comando anterior, você vê que o **peeringState** é *Conectado*. O Azure também alterou o estado de emparelhamento do emparelhamento *myVirtualNetwork1-myVirtualNetwork2* para *Conectado*. Confirme se o estado de emparelhamento do emparelhamento *myVirtualNetwork1-myVirtualNetwork2* foi alterado para *Conectado* com [az network vnet peering show](/cli/azure/network/vnet/peering#az_network_vnet_peering_show).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Os recursos de uma rede virtual não podem se comunicar com os recursos da outra rede virtual até que o **peeringState** dos emparelhamentos de ambas as redes virtuais seja *Conectado*. 

Os emparelhamentos são feitos entre duas redes virtuais, mas não são transitivos. Assim, por exemplo, se você desejar emparelhar *myVirtualNetwork2* com *myVirtualNetwork3*, precisará criar um emparelhamento adicional entre as redes virtuais *myVirtualNetwork2* e *myVirtualNetwork3*. Embora *myVirtualNetwork1* esteja emparelhada com *myVirtualNetwork2*, os recursos de *myVirtualNetwork1* só podem acessar os recursos de *myVirtualNetwork3* se *myVirtualNetwork1* também foi emparelhada com *myVirtualNetwork3*. 

Antes de emparelhar redes virtuais de produção, é recomendável que você se familiarize por completo com a [visão geral do emparelhamento](virtual-network-peering-overview.md), o [gerenciamento do emparelhamento](virtual-network-manage-peering.md) e os [limites da rede virtual](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Embora este artigo ilustre um emparelhamento entre duas redes virtuais na mesma assinatura e no mesmo local, você também pode emparelhar redes virtuais em [regiões diferentes](#register) e [assinaturas diferentes do Azure](create-peering-different-subscriptions.md#cli). Você também pode criar [designs de rede de hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com o emparelhamento.

## <a name="test-peering"></a>Testar o emparelhamento

Para testar a comunicação de rede entre máquinas virtuais em diferentes redes virtuais por meio de um emparelhamento, implante uma máquina virtual em cada sub-rede e, em seguida, estabeleça a comunicação entre as máquinas virtuais. 

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma máquina virtual com [az vm create](/cli/azure/vm#az_vm_create). O exemplo a seguir cria uma máquina virtual chamada *myVm1* na rede virtual *myVirtualNetwork1*. Se as chaves SSH ainda não existirem em uma localização de chave padrão, o comando criará. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`. A opção `--no-wait` cria a máquina virtual em segundo plano para que você continue na próxima etapa.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

O Azure atribui automaticamente 10.0.0.4 como o endereço IP privado da máquina virtual, porque 10.0.0.4 é o primeiro endereço IP disponível na *Subnet1* de *myVirtualNetwork1*. 

Crie uma máquina virtual na rede virtual *myVirtualNetwork2*.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

A criação da máquina virtual demora alguns minutos. Depois que a máquina virtual é criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Na saída de exemplo, você vê que **privateIpAddress** é *10.1.0.4*. O DHCP do Azure atribuiu 10.1.0.4 automaticamente à máquina virtual, porque ele é o primeiro endereço disponível na *Subnet1* de *myVirtualNetwork2*. Anote o **publicIpAddress**. Esse endereço será usado para acessar a máquina virtual da Internet em uma etapa posterior.

### <a name="test-virtual-machine-communication"></a>Testar a comunicação entre máquinas virtuais

Use o comando a seguir para criar uma sessão SSH com a máquina virtual *myVm2*. Substitua `<publicIpAddress>` pelo endereço IP público de sua máquina virtual. No exemplo anterior, o endereço IP público é *13.90.242.231*.

```bash 
ssh <publicIpAddress>
```

Execute ping da máquina virtual em *myVirtualNetwork1*.

```bash 
ping 10.0.0.4 -c 4
```

Você receberá quatro respostas. Se você executar ping pelo nome da máquina virtual (*myVm1*) em vez de pelo endereço IP, o ping falhará, porque *myVm1* é um nome de host desconhecido. A resolução de nomes padrão do Azure funciona entre máquinas virtuais na mesma rede virtual, mas não entre máquinas virtuais em redes virtuais diferentes. Para resolver nomes entre redes virtuais, você precisa [implantar seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) ou usar [domínios privados do DNS do Azure](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Feche a sessão SSH da máquina virtual *myVm2*. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, use [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos e todos os recursos que ele contém.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

**<a name="register"></a>Registrar-se para a versão prévia de emparelhamento de rede virtual global**

O emparelhamento de redes virtuais na mesma região está disponível ao público em geral. Emparelhar redes virtuais em diferentes regiões está em versão prévia no momento. Consulte [Atualizações de rede virtual](https://azure.microsoft.com/updates/?product=virtual-network) para as regiões disponíveis. Para emparelhar redes virtuais entre regiões, primeiro registre-se para versão prévia concluindo as seguintes etapas (na assinatura de cada rede virtual que você deseja emparelhar):

1. Registre-se para a versão prévia inserindo os seguintes comandos:

  ```azurecli-interactive
  az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
  az provider register --name Microsoft.Network
  ```

2. Digite o seguinte comando para confirmar que você está registrado para a versão prévia:

  ```azurecli-interactive
  az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
  ```

  Caso você tente emparelhar redes virtuais em regiões diferentes antes que a saída de **RegistrationState** recebida após a inserção do comando anterior seja **Registrado** para ambas as assinaturas, o emparelhamento falhará.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a conectar duas redes com o emparelhamento de rede virtual. Você pode [conectar seu próprio computador a uma rede virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) por meio de uma VPN e interagir com os recursos de uma rede virtual ou de redes virtuais emparelhadas.

Continue lendo para obter amostras de scripts reutilizáveis para concluir muitas das tarefas abordadas nos artigos sobre redes virtuais.

> [!div class="nextstepaction"]
> [Amostras de script de rede virtual](../networking/cli-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
