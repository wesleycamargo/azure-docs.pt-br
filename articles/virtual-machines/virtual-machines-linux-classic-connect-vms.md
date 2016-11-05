---
title: Conectar VMs Linux em um serviço de nuvem | Microsoft Docs
description: Conectar máquinas virtuais Linux criadas com o modelo clássico de implantação a um serviço de nuvem ou de rede virtual do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cynthn

---
# Conectar máquinas virtuais Linux criadas com o modelo clássico de implantação com um serviço de nuvem ou de rede virtual
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

As máquinas virtuais Linux criadas com o modelo de implantação clássico sempre são colocadas em um serviço de nuvem. O serviço de nuvem funciona como um contêiner e fornece um nome DNS público exclusivo, um endereço IP público e um conjunto de pontos de extremidade para acessar a máquina virtual pela Internet. O serviço de nuvem pode estar em uma rede virtual, mas isso não é um requisito. Você também pode [Conectar máquinas virtuais do Windows a uma rede virtual ou serviço de nuvem](virtual-machines-windows-classic-connect-vms.md).

Se um serviço de nuvem não estiver em uma rede virtual, ele será chamado de serviço de nuvem *autônomo*. As máquinas virtuais em um serviço de nuvem autônomo só poderão se comunicar com outras máquinas virtuais usando os nomes DNS públicos de outras máquinas virtuais, e o tráfego viajará pela Internet. Se um serviço de nuvem estiver em uma rede virtual, as máquinas virtuais no serviço de nuvem podem se comunicar com todas as outras máquinas virtuais na rede virtual sem enviar tráfego pela Internet.

Ao colocar as máquinas virtuais no mesmo serviço de nuvem autônomo, você ainda pode usar o balanceamento de carga e os conjuntos de disponibilidade. Para obter detalhes, consulte [Máquinas virtuais de balanceamento de carga](virtual-machines-linux-load-balance.md) e [Gerenciar a disponibilidade das máquinas virtuais](virtual-machines-linux-manage-availability.md). No entanto, você não pode organizar as máquinas virtuais em sub-redes ou conectar um serviço de nuvem autônomo à sua rede local. Aqui está um exemplo:

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## Próximas etapas
Após criar uma máquina virtual, é uma boa ideia [adicionar um disco de dados](virtual-machines-linux-classic-attach-disk.md) para que seus serviços e cargas de trabalho tenham um local para armazenar dados.

<!---HONumber=AcomDC_0706_2016-->