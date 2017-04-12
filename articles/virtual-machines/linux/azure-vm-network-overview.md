---
title: "Visão geral de rede de VM do Azure e do Linux | Microsoft Docs"
description: "Uma visão geral da rede de VM do Azure e do Linux."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: b5420e35-0463-4456-9803-6142db150f2e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/25/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1ff4a0482d6dc6ec0eceaa89ca4b87ba1e2f89a1
ms.lasthandoff: 04/03/2017


---
# <a name="azure-and-linux-vm-network-overview"></a>Visão geral de rede de VM do Azure e do Linux
## <a name="virtual-networks"></a>Redes Virtuais
Uma rede virtual do Azure (VNet) é uma representação da sua própria rede na nuvem. É um isolamento lógico da nuvem do Azure dedicada à sua assinatura. Você pode controlar os blocos de endereços IP, as configurações de DNS, as políticas de segurança e as tabelas de rotas na rede. Você também pode segmentar a VNet em sub-redes e iniciar as máquinas virtuais (VMs) de IaaS do Azure e/ou os Serviços de nuvem (instâncias de função de PaaS). Além disso, você pode conectar a rede virtual à sua rede local usando uma das opções de conectividade disponíveis no Azure. Em linhas gerais, você pode expandir sua rede no Azure, com controle total sobre os blocos de endereços IP, com benefícios de escala empresarial proporcionados pelo Azure.

* [Visão geral da Rede Virtual](../../virtual-network/virtual-networks-overview.md)

Para criar uma VNet usando a CLI do Azure, clique aqui para seguir o passo a passo.

* [Como criar uma VNet usando a CLI do Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md)

## <a name="network-security-groups"></a>Grupos de segurança de rede
O NSG (Grupo de segurança de rede) contém uma lista de regras de ACL (Lista de Controle de Acesso) que permitem ou negam o tráfego de rede para suas instâncias de VM em uma Rede Virtual. Os NSGs podem ser associados a sub-redes ou instâncias de VM individuais dentro dessa sub-rede. Quando um NSG é associado uma sub-rede, as regras de ACL se aplicam a todas as instâncias de VM na sub-rede. Além disso, o tráfego para uma VM individual pode ser restrito ainda mais por meio da associação de um NSG diretamente à VM.

* [O que é um NSG (grupo de segurança de rede)?](../../virtual-network/virtual-networks-nsg.md)
* [Como criar NSGs na CLI do Azure](../../virtual-network/virtual-networks-create-nsg-arm-cli.md)

## <a name="user-defined-routes"></a>Rotas definidas pelo usuário
Ao adicionar VMs (máquinas virtuais) a uma VNet (rede virtual) no Azure, você observará que as VMs podem se comunicar automaticamente com outras VMs na rede. Não é necessário especificar um gateway, mesmo que as VMs estejam em sub-redes diferentes. O mesmo vale para a comunicação entre as VMs para a Internet pública e até mesmo em suas instalações de rede quando houver uma conexão híbrida do Azure para o seu próprio datacenter.

* [O que são Rotas Definidas pelo Usuário e Encaminhamento de IP?](../../virtual-network/virtual-networks-udr-overview.md)
* [Criar uma UDR na CLI do Azure](../../virtual-network/virtual-network-create-udr-arm-cli.md)

## <a name="associating-a-fqdn-to-your-linux-vm"></a>Associando um FQDN à sua VM do Linux
Quando você cria uma VM (máquina virtual) no portal do Azure usando o modelo de implantação do Resource Manager, um recurso de IP público para a máquina virtual é criado automaticamente. Use esse endereço IP para acessar a VM remotamente. Embora o portal não crie um nome de domínio totalmente qualificado, ou FQDN, por padrão, você pode adicionar um depois que a VM for criada.

* [Criar um nome de domínio totalmente qualificado no portal do Azure](portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="network-interfaces"></a>Interfaces de rede
Uma NIC (adaptador de rede) é a interconexão entre uma VM (máquina virtual) e a rede do software subjacente. Este artigo explica o que é um adaptador de rede e como ele é usado no modelo de implantação do Azure Resource Manager.

* [Interfaces de Rede Virtual](../../virtual-network/virtual-network-network-interface.md)

## <a name="virtual-nics-and-dns-labeling"></a>NICs virtuais o rotulação de DNS
Se você tiver um servidor que precisa que seja persistente, mas esse servidor é tratado como burro e é interrompido e implantado com frequência, você vai querer usar rotulação de DNS no seu NIC para manter o nome na VNET.  Com a seguinte explicação passo a passo, você configurará uma NIC persistentemente nomeada com um IP estático.

* [Criar um ambiente Linux completo usando a CLI do Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network-gateways"></a>Gateways de Rede Virtual
Um gateway de rede virtual é usado para enviar o tráfego de rede entre as redes virtuais do Azure e locais, bem como entre as redes virtuais no Azure (VNet a VNet). Quando você configura um gateway de VPN, é necessário criar e configurar um gateway de rede virtual e uma conexão de gateway de rede virtual.

* [Sobre o Gateway de VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

## <a name="internal-load-balancing"></a>Balanceamento de carga interno
Um Azure Load Balancer é um balanceador de carga de Camada 4 (TCP, UDP). O balanceador de carga fornece alta disponibilidade, distribuindo o tráfego de entrada entre instâncias do serviço de integridade em serviços de nuvem ou máquinas virtuais em um conjunto de balanceadores de carga. O Azure Load Balancer também pode apresentar esses serviços em várias portas, vários endereços IP ou ambos.

* [Criar um balanceador de carga interno usando a CLI do Azure](../../load-balancer/load-balancer-get-started-internet-arm-cli.md)


