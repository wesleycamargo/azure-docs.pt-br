---
title: Configurar várias NICs em uma VM Linux | Microsoft Docs
description: Saiba como criar uma VM com várias NICs anexadas usando a CLI do Azure ou modelos do Gerenciador de Recursos.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2016
ms.author: iainfou

---
# Criando uma VM com diversas NICs
Você pode criar uma VM (máquina virtual) no Azure que tenha várias NICs (interfaces de rede virtual) anexadas a ela. Um cenário comum seria ter sub-redes diferentes para conectividade de front-end e de back-end ou uma rede dedicada a uma solução de monitoramento ou de backup. Este artigo fornece comandos rápidos para criar uma VM com várias NICs anexadas a ela. Para obter informações detalhadas, incluindo como criar várias NICs dentro de seus próprios scripts Bash, leia mais sobre a [implantação de VMs com várias NICs](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Diferentes [tamanhos de VM](virtual-machines-linux-sizes.md) dão suporte a um número variável de NICs, sendo assim, dimensione sua VM adequadamente.

> [!WARNING]
> Você deverá anexar várias NICs quando criar a VM – não é possível adicionar NICs a uma VM existente. Você pode [criar uma nova VM com base nos discos virtuais originais](virtual-machines-linux-copy-vm.md) e criar várias NICs conforme implantar a VM.
> 
> 

## Comandos rápidos
Verifique se você tem a [CLI do Azure](../xplat-cli-install.md) conectada e está usando o modo do Gerenciador de Recursos (`azure config mode arm`).

Em primeiro lugar, crie um grupo de recursos:

```bash
azure group create TestRG --location WestUS
```

Crie uma conta de armazenamento para manter suas VMs:

```bash
azure storage account create teststorage --resource-group TestRG \
    --location WestUS --kind Storage --sku-name PLRS
```

Crie uma rede virtual à qual você conectará suas VMs:

```bash
azure network vnet create --resource-group TestRG --location WestUS \
    --name TestVNet --address-prefixes 192.168.0.0/16 
```

Crie duas sub-redes da rede virtual – uma para o tráfego de front-end e uma para o tráfego de back-end:

```bash
azure network vnet subnet create --resource-group TestRG --vnet-name TestVNet \
    --name FrontEnd --address-prefix 192.168.1.0/24
azure network vnet subnet create --resource-group TestRG --vnet-name TestVNet \
    --name BackEnd --address-prefix 192.168.2.0/24
```

Crie duas NICs, anexando uma NIC à sub-rede de front-end e uma NIC à sub-rede de back-end:

```bash
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC1 --subnet-vnet-name TestVNet --subnet-name FrontEnd
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC2 --subnet-vnet-name TestVNet --subnet-name BackEnd
```

Por fim, crie sua VM, anexando as duas NICs criadas anteriormente:

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location WestUS \
    --os-type linux \
    --nic-names NIC1,NIC2 \
    --vm-size Standard_DS2_v2
    --storage-account-name teststorage \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## Criando várias NICs usando a CLI do Azure
Se você já tiver criado uma VM usando a CLI do Azure, os comandos rápidos deverão ser familiares. O processo é o mesmo para criar uma NIC ou várias NICs. Você pode ler mais detalhes sobre a [implantação de várias NICs usando a CLI do Azure](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), incluindo o script do processo de loop para criar todas as NICs.

O exemplo a seguir cria duas NICs, com uma NIC se conectando a cada sub-rede:

```bash
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC1 --subnet-vnet-name TestVNet --subnet-name FrontEnd
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC2 --subnet-vnet-name TestVNet --subnet-name BackEnd
```

Normalmente, você também criaria um [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) ou [balanceador de carga](../load-balancer/load-balancer-overview.md) para ajudar a gerenciar e distribuir o tráfego entre suas VMs. Novamente, os comandos são os mesmos de quando se trabalha com várias NICs. As NICs que você cria são vinculadas a um grupo de segurança de rede ou a um balanceador de carga usando `azure network nic set`, como no exemplo a seguir:

```bash
azure network nic set --resource-group TestRG --name NIC1 \
    --network-security-group-name TestNSG
```

Ao criar a VM, agora você especifica várias NICs. Em vez de usar `--nic-name` para fornecer uma única NIC, você usa `--nic-names` e fornece uma lista de NICs separada por vírgulas. Você também precisa tomar cuidado ao selecionar o tamanho da VM. Há limites para o número total de NICs que podem ser adicionados a uma VM. Leia mais sobre [Tamanhos de VM Linux](virtual-machines-linux-sizes.md). O exemplo a seguir mostra como especificar várias NICs e, em seguida, um tamanho de VM que dê suporte ao uso de várias NICs (`Standard_DS2_v2`):

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location WestUS \
    --os-type linux \
    --nic-names NIC1,NIC2 \
    --vm-size Standard_DS2_v2
    --storage-account-name teststorage \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## Criando várias NICs usando modelos do Gerenciador de Recursos
Os modelos do Azure Resource Manager usam arquivos JSON declarativos para definir o seu ambiente. Você pode ler uma [visão geral do Azure Resource Manager](../resource-group-overview.md). Os modelos do Gerenciador de Recursos oferecem uma maneira de criar várias instâncias de um recurso durante a implantação, como a criação de várias NICs. Você usa *copiar* para especificar o número de instâncias a serem criadas:

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Leia mais sobre a [criação de várias instâncias usando *copiar*](../resource-group-create-multiple.md).

Você também pode usar um `copyIndex()` para acrescentar um número a um nome de recurso, o que lhe permite criar `NIC1`, `NIC2` etc. Veja a seguir um exemplo de como acrescentar o valor de índice:

```bash
"name": "[concat('NIC-', copyIndex())]", 
```

Você pode ler um exemplo completo em [Criando várias NICs usando modelos do Gerenciador de Recursos](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## Próximas etapas
Certifique-se de rever os [Tamanhos de VM Linux](virtual-machines-linux-sizes.md) ao tentar criar uma VM com várias NICs. Preste atenção ao número máximo de NICs a que cada VM dá suporte.

Lembre-se de que você não pode adicionar mais NICs a uma VM existente; você deve criar todas as NICs quando implantar a VM. Tome cuidado ao planejar suas implantações para certificar-se de que tenha toda a conectividade de rede necessária desde o início.

<!---HONumber=AcomDC_0817_2016-->