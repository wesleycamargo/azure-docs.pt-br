---
title: Criar uma Rede Virtual do Azure com várias sub-redes - CLI do Azure | Microsoft Docs
description: Saiba como criar uma rede virtual com várias sub-redes usando a CLI do Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 0b0bfae02147910d98231d7c93f5ab260f26364f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880526"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-cli"></a>Criar uma rede virtual com várias sub-redes usando a CLI do Azure

Uma rede virtual permite que vários tipos de recursos do Azure comuniquem-se com a Internet e em modo privado. Criar várias sub-redes em uma rede virtual permite segmentar a rede para que seja possível filtrar ou controlar o fluxo de tráfego entre as sub-redes. Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar uma sub-rede
> * Testar a comunicação de rede entre máquinas virtuais

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.4 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli.md). 

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). O exemplo a seguir cria uma rede virtual nomeada *myVirtualNetwork* com o prefixo de endereço *10.0.0.0/16*. O comando cria uma sub-rede nomeada *Pública*, com o prefixo de endereço *10.0.0.0/24*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Como um local não foi especificado no comando anterior, o Azure cria a rede virtual na mesma localização do grupo de recursos *myResourceGroup* existente. Os **address-prefixes** e **subnet-prefix** são especificados na notação CIDR. O prefixo de endereço especificado inclui os endereços IP 10.0.0.0-10.0.255.254. O prefixo especificado para a sub-rede deve estar dentro do prefixo de endereço definido para a rede virtual. O DHCP do Azure atribui endereços IP de um prefixo de endereço de sub-rede aos recursos implantados em uma sub-rede. O Azure atribui apenas os endereços 10.0.0.4-10.0.0.254 aos recursos implantados na sub-rede **Pública**, porque o Azure reserva os quatro primeiros endereços (10.0.0.0-10.0.0.3 para a sub-rede, neste exemplo) e o último endereço (10.0.0.255 para a sub-rede, neste exemplo) em cada sub-rede.

## <a name="create-a-subnet"></a>Criar uma sub-rede

Crie uma sub-rede com [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). O exemplo a seguir cria uma sub-rede nomeada *Privada* na rede virtual *myVirtualNetwork* com o prefixo de endereço *10.0.1.0/24*. O prefixo de endereço deve estar dentro do prefixo de endereço definido para a rede virtual e não poderá sobrepor o prefixo do endereço de qualquer outra sub-rede na rede virtual.

```azurecli-interactive 
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24
```

Antes de implantar sub-redes e redes virtuais do Azure para uso em produção, é recomendável que você esteja familiarizado com as [considerações](manage-virtual-network.md#create-a-virtual-network) de espaço de endereço e os [limites da rede virtual](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Quando os recursos forem implantados em sub-redes, algumas mudanças na rede virtual e na sub-rede, como a alteração de intervalos de endereços, poderão exigir a reimplantação dos recursos do Azure existentes implantados nas sub-redes.

## <a name="test-network-communication"></a>Testar comunicação de rede

Uma rede virtual permite que vários tipos de recursos do Azure comuniquem-se com a Internet e em modo privado. Um tipo de recurso que pode ser implantado em uma rede virtual é uma máquina virtual. Crie duas máquinas virtuais na rede virtual para que seja possível testar a comunicação de rede entre elas e a Internet em uma etapa posterior.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma máquina virtual com [az vm create](/cli/azure/vm#az_vm_create). O exemplo a seguir cria uma máquina virtual nomeada *myVmWeb* na sub-rede *Pública*. O parâmetro `--no-wait` permite que o Azure execute o comando em segundo plano para que você possa continuar com o próximo comando. Para simplificar este tutorial, uma senha é utilizada. Normalmente, chaves são usadas em implantações de produção. Se você usar chaves, será necessário configurar também o encaminhamento de agente SSH para concluir as etapas restantes. Para obter mais informações sobre o encaminhamento de agente SSH, consulte a documentação do cliente SSH. Substitua `<replace-with-your-password>` no comando a seguir com uma senha de sua escolha.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

O Azure atribuiu automaticamente 10.0.0.4 como o endereço IP privado da máquina virtual, porque 10.0.0.4 é o primeiro endereço IP disponível na sub-rede *Pública*. 

Crie uma máquina virtual na sub-rede *Privada*.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```
A criação da máquina virtual demora alguns minutos. Depois que a máquina virtual é criada, a CLI do Azure retorna saída semelhante ao exemplo a seguir: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmMgmt",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Na saída do exemplo, é possível ver que **privateIpAddress** é *10.0.1.4*. O Azure criou uma [interface de rede](virtual-network-network-interface.md), anexou-a à máquina virtual, atribuiu à interface de rede um endereço IP privado e um **macAddress**. O DHCP do Azure atribui automaticamente 10.0.1.4 à interface de rede porque é o primeiro endereço IP disponível na sub-rede *Privada*. Os endereços MAC e IP privado permanecerão atribuídos à interface de rede até que a interface de rede seja excluída. 

Anote o **publicIpAddress**. Esse endereço será usado para acessar a máquina virtual da Internet em uma etapa posterior. Embora uma máquina virtual não seja necessária para ter um endereço IP público atribuído a ela, o Azure atribui um endereço IP público a cada máquina virtual criada, por padrão. Para comunicar-se da Internet para uma máquina virtual, um endereço IP público deverá ser atribuído à máquina virtual. Todas as máquinas virtuais podem se comunicar com a Internet, tendo ou não um endereço IP público atribuído à máquina virtual. Para saber mais sobre as conexões com a Internet de saída no Azure, consulte [Conexões de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

As máquinas virtuais criadas neste artigo possuem uma [interface de rede](virtual-network-network-interface.md) com um endereço IP dinamicamente atribuído à interface de rede. Após implantar a VM, será possível [adicionar vários endereços IP públicos e privados ou alterar o método de atribuição de endereço IP para estático](virtual-network-network-interface-addresses.md#add-ip-addresses). É possível [adicionar interfaces de rede](virtual-network-network-interface-vm.md#vm-add-nic), até o limite suportado pelo [tamanho da VM](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que você seleciona ao criar uma máquina virtual. Além disso, é possível [habilitar a SR-IOV (virtualização de E/S de raiz única)](create-vm-accelerated-networking-cli.md) para uma VM, mas somente quando você cria uma VM com um tamanho da VM que suporta a capacidade.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Comunicação entre máquinas virtuais e Internet

Use o comando a seguir para criar uma sessão SSH com a máquina virtual *myVmMgmt*. Substitua `<publicIpAddress>` pelo endereço IP público de sua máquina virtual. No exemplo anterior, o endereço IP público é *13.90.242.231*. Quando uma senha for solicitada, insira a senha que você inseriu em [Criar máquinas virtuais](#create-virtual-machines).

```bash 
ssh azureuser@<publicIpAddress>
```

Por razões de segurança, é comum limitar o número de máquinas virtuais que podem ser conectadas remotamente em uma rede virtual. Neste tutorial, a máquina virtual *myVmMgmt* é usada para gerenciar a máquina virtual *myVmWeb* na rede virtual. Use o comando a seguir para SSH para a máquina virtual *myVmWeb* da máquina virtual *myVmMgmt*:

```bash 
ssh azureuser@myVmWeb
```

Para comunicar-se com a máquina virtual *myVmMgmt* a partir da máquina virtual *myVmWeb*, insira o comando a seguir em um prompt de comando:

```
ping -c 4 myvmmgmt
```

Você receberá uma saída semelhante à saída de exemplo a seguir:
    
```
PING myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net (10.0.1.4) 56(84) bytes of data.
64 bytes from 10.0.1.4: icmp_seq=1 ttl=64 time=1.45 ms
64 bytes from 10.0.1.4: icmp_seq=2 ttl=64 time=0.628 ms
64 bytes from 10.0.1.4: icmp_seq=3 ttl=64 time=0.529 ms
64 bytes from 10.0.1.4: icmp_seq=4 ttl=64 time=0.674 ms

--- myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3029ms
rtt min/avg/max/mdev = 0.529/0.821/1.453/0.368 ms
```
      
É possível ver que o endereço da máquina virtual *myVmMgmt* é 10.0.1.4. 10.0.1.4 foi o primeiro endereço IP disponível no intervalo de endereços da sub-rede *Privada* implantada na máquina virtual *myVmMgmt* em uma etapa anterior.  Observe que o nome de domínio totalmente qualificado da máquina virtual é *myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net*. Embora a parte *hxehizax3z1udjnrx1r4gr30pg* do nome de domínio seja diferente para a máquina virtual, as partes restantes do nome de domínio serão as mesmas. Por padrão, todas as máquinas virtuais do Azure usam o serviço DNS do Azure padrão. Todas as máquinas virtuais dentro de uma rede virtual podem resolver os nomes de todas as outras máquinas virtuais na mesma rede virtual usando o serviço DNS padrão do Azure. Em vez de usar o serviço DNS do Azure padrão, é possível usar seu próprio servidor DNS ou a capacidade de domínio privado do serviço DNS do Azure. Para obter detalhes, consulte [Resolução de nome usando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) ou [Usar DNS do Azure para domínios privados](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Use os comandos a seguir para instalar o servidor Web nginx na máquina virtual *myVmWeb*:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Quando o nginx estiver instalado, feche a sessão SSH da *myVmWeb*, que direcionará para o prompt da máquina virtual *myVmMgmt*. Insira o comando a seguir para recuperar a tela de boas-vindas do nginx da máquina virtual *myVmWeb*.

```bash
curl myVmWeb
```

A tela de boas-vindas do nginx é retornada.

Feche a sessão SSH com a máquina virtual *myVmMgmt*.

Quando o Azure criou a máquina virtual *myVmWeb*, um endereço IP público nomeado *myVmWebPublicIP* também foi criado e atribuído à máquina virtual. Obter o endereço público do Azure atribuído com [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show).

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myVmWebPublicIP \
  --query ipAddress
```

Do seu próprio computador, insira o comando a seguir, substituindo `<publicIpAddress>` pelo endereço retornado do comando anterior:

```bash
curl <publicIpAddress>
```

A tentativa de ondulação da tela de boas-vindas do nginx do seu próprio computador falha. A tentativa falha porque, quando as máquinas virtuais foram implantadas, o Azure criou um grupo de segurança de rede para cada máquina virtual, por padrão. 

Um grupo de segurança de rede contém regras de segurança que permitem ou negam tráfego de entrada e saída pela porta e pelo endereço IP. O grupo de segurança de rede padrão do Azure criado permite a comunicação entre todas as portas entre recursos na mesma rede virtual. Para máquinas virtuais do Linux, o grupo de segurança de rede padrão nega todo o tráfego de entrada da Internet em todas as portas, aceita a porta TCP 22 (SSH). Como resultado, por padrão, também é possível criar uma sessão SSH diretamente na *myVmWeb* da Internet, mesmo que você não queira a porta 22 aberta para um servidor Web. Como o comando `curl` comunica-se através da porta 80, a comunicação falha a partir da Internet porque não há nenhuma regra no grupo de segurança de rede padrão que permite o tráfego na porta 80.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, use [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos e todos os recursos que ele contém.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como implantar uma rede virtual com várias sub-redes. Adicionalmente, aprendeu que ao criar uma máquina virtual do Linux, o Azure cria uma interface de rede que anexa à máquina virtual e cria um grupo de segurança de rede que permite somente tráfego na porta 22, da Internet. Avance para o próximo tutorial para aprender como filtrar tráfego para sub-redes, e não para máquinas virtuais individuais.

> [!div class="nextstepaction"]
> [Filtrar tráfego para sub-redes](./virtual-networks-create-nsg-arm-cli.md)
