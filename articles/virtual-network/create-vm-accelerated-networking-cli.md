---
title: "Criar uma máquina virtual do Azure com Rede Acelerada | Microsoft Docs"
description: "Saiba como criar uma máquina virtual Linux com Rede Acelerada."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 908d81c363a556917d211e0bcc92188f849fb690
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2018
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Criar uma máquina virtual Linux com Rede Acelerada

> [!IMPORTANT] 
> Máquinas virtuais devem ser criadas com a Rede Acelerada habilitada. Este recurso não pode ser habilitado em máquinas virtuais existentes. Você pode seguir as etapas abaixo para habilitar a Rede Acelerada:
>   1. Excluir a máquina virtual.
>   2. Recriar uma máquina virtual com Rede Acelerada habilitada.
>

Neste tutorial, você aprenderá a criar uma VM (máquina virtual) do Linux com Rede Acelerada. Rede acelerada permite SR-IOV (virtualização de E/S de raiz única) para uma VM, melhorando muito seu desempenho de rede. Esse caminho de alto desempenho ignora o host do caminho de dados, reduzindo a latência, a tremulação e a utilização da CPU para uso com as cargas de trabalho de rede mais exigentes nos tipos de VM compatíveis. A figura abaixo mostra a comunicação entre duas VMs com e sem rede acelerada:

![Comparação](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sem rede acelerada, todo o tráfego de rede que entra e sai da VM deve cruzar o host e o comutador virtual. O comutador virtual fornece toda a imposição de política, como grupos de segurança de rede, listas de controle de acesso, isolamento e outros serviços virtualizados de rede para tráfego de rede. Para saber mais sobre comutadores virtuais, leia o artigo [Virtualização de rede Hyper-V e comutador virtual](https://technet.microsoft.com/library/jj945275.aspx).

Com Rede Acelerada, o tráfego de rede chega à NIC (interface de rede) da VM e então é encaminhado para a VM. Todas as políticas de rede que o comutador virtual aplica agora são descarregadas e aplicadas em hardware. Aplicar a política de hardware permite que a NIC encaminhe tráfego de rede diretamente à VM, ignorando o host e o comutador virtual, mantendo toda a política que aplicou no host.

Os benefícios da rede acelerada aplicam-se somente à VM em que ela está habilitada. Para obter melhores resultados, é ideal habilitar esse recurso em pelo menos duas VMs conectadas à mesma VNet (rede virtual) do Azure. Ao se comunicar entre VNets ou fazer conexão local, esse recurso tem impacto mínimo sobre a latência geral.

## <a name="benefits"></a>Benefícios
* **Latência menor/mais pps (pacotes por segundo):** remover o comutador virtual do caminho de dados elimina o tempo que os pacotes gastam no host para processamento da política e aumenta o número de pacotes que podem ser processados dentro da VM.
* **Tremulação reduzida:** processamento de comutador virtual depende da quantidade de política que precisa ser aplicada e da carga de trabalho da CPU que está fazendo o processamento. O descarregamento da imposição de política para o hardware remove essa variabilidade ao entregar pacotes diretamente à VM, removendo a comunicação do host para a VM e todas as interrupções e mudanças de contexto de software.
* **Menor utilização da CPU:** ignorar o comutador virtual no host resulta em menor utilização da CPU para processar o tráfego de rede.

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte
* **Ubuntu 16.04**: 4.11.0-1013 ou versão do kernel superior
* **SLES SP3**: 4.4.92-6.18 ou versão do kernel superior
* **RHEL**: 7.4.2017120423 ou versão do kernel superior
* **CentOS**: 7.4.20171206 ou versão do kernel superior

## <a name="supported-vm-instances"></a>Instâncias de VM compatíveis
A Rede Acelerada é compatível com os tamanhos de instância de uso geral e de computação otimizada com 4 ou mais vCPUs. Em instâncias como D/DSv3 ou E/ESv3 que são compatíveis com hyperthreading, a Rede Acelerada é compatível com instâncias de VM com 8 ou mais vCPUs.  As séries compatíveis são: D/DSv2, D/DSv3, E/ESv3, F/Fs/Fsv2 e Ms/Mms. 

Para obter mais informações sobre instâncias de VM, consulte [Tamanhos de VM do Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>Regiões
Disponível em todas as regiões do Azure públicas com exceção da Ásia Oriental.   A Nuvem do Azure Governamental ainda não é compatível.

## <a name="limitations"></a>Limitações
Existem as seguintes limitações ao usar essa funcionalidade:

* **Criação de interface de rede:** rede acelerada só pode ser habilitada para uma NIC nova. Não pode ser habilitada para uma NIC existente.
* **Criação da VM:** uma NIC com rede acelerada habilitada somente poderá ser conectada a uma VM quando a VM for criada. A NIC não pode ser anexada a uma VM existente. Se adicionar a máquina virtual a um grupo de disponibilidades definida, todas as VMs no conjunto de disponibilidades também deverão a rede acelerada habilitada.
* **Implantação somente por meio do Azure Resource Manager:** máquinas virtuais (clássicas) não podem ser implantadas com Rede Acelerada.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Instale a [CLI 2.0 do Azure](/cli/azure/install-az-cli2) mais recente e faça logon em uma conta do Azure usando [az login](/cli/azure/#login). Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem *myResourceGroup*, *myNic* e *myVm*.

Crie um grupo de recursos com [az group create](/cli/azure/group#create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *centralus*:

```azurecli
az group create --name myResourceGroup --location centralus
```

Você deve selecionar uma região do Linux compatível listada em [Rede acelerada Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet#create). O exemplo a seguir cria uma rede virtual chamada *myVnet* com uma sub-rede:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede
Crie um grupo de segurança de rede com [az network nsg create](/cli/azure/network/nsg#create). O exemplo a seguir cria um grupo de segurança de rede denominado *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

O grupo de segurança de rede contém várias regras padrão, uma das quais desabilita todo o acesso de entrada proveniente da Internet. Abra uma porta para permitir o acesso SSH à máquina virtual com [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Criar um adaptador de rede com rede acelerada

Crie um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). Você não precisa de um endereço IP público se não planeja acessar a máquina virtual por meio da Internet, mas precisa para concluir as etapas deste artigo.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Crie um adaptador de rede com [az network nic create](/cli/azure/network/nic#create) com a rede acelerada habilitada. O exemplo a seguir cria um adaptador de rede denominado *myNic* na sub-rede *mySubnet* da rede virtual *myVnet* e associa o grupo de segurança de rede *myNetworkSecurityGroup* ao adaptador de rede:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nic"></a>Criar uma VM e anexar o NIC
Ao criar a VM, especifique o NIC que criou com `--nics`. Você deve selecionar um tamanho e uma distribuição listados em [Rede acelerada Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Crie uma VM com [az vm create](/cli/azure/vm#create). O exemplo a seguir cria uma VM denominada *myVM* com a imagem UbuntuLTS e um tamanho que é compatível com a Rede Acelerada (*Standard_DS4_v2*):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

Para obter uma lista de todos os tamanhos e características de VM, consulte [Tamanhos de VM do Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Depois que a VM é criada, é retornada uma saída semelhante ao exemplo a seguir. Anote o **publicIpAddress**. Esse endereço é usado para acessar a VM em etapas subsequentes.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="confirm-that-accelerated-networking-is-enabled"></a>Confirmar se a rede acelerada está habilitada

Use o comando a seguir para criar uma sessão SSH com a VM. Substitua `<your-public-ip-address>` pelo endereço IP público atribuído à máquina virtual que você criou e substitua *azureuser* se você usou um valor diferente para `--admin-username` ao criar a VM.

```bash
ssh azureuser@<your-public-ip-address>
```

No shell Bash, insira `uname -r` e confirme se a versão do kernel é uma das seguintes versões, ou superior:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Confirme se o dispositivo Mellanox VF está exposto à VM com o comando `lspci`. A saída retornada é semelhante à seguinte saída:

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Verifique se há atividade na VF (função virtual) com o comando `ethtool -S eth0 | grep vf_`. Se você receber uma saída semelhante ao seguinte exemplo de saída, a rede acelerada estará habilitada e funcionando.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
A Rede Acelerada agora está habilitada para sua VM.
